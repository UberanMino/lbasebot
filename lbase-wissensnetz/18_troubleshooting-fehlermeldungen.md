# 18 – Troubleshooting: lBase-Fehlermeldungen (Ursachen & Abhilfe)

← [Index](00_INDEX.md) · verwandt: [12 LBase 21st](12_lbase-21st-bedienung.md) (FSW-Workflows) ·
[16 Aktueller Prozess](16_aktueller-prozess-handbuch.md) (Dispo/Abrechnung) ·
[07 Prozesse LogBATT](07_prozesse-logbatt.md) (Support/Ticket) · [10 Glossar](10_glossar.md)

Sammelknoten für **konkrete lBase-Fehlermeldungen** aus dem Echtbetrieb – mit Ursache,
Diagnose und Abhilfe. Wird laufend erweitert (löst den To-Do-Punkt „Troubleshooting-Knoten“
aus [00] ein).

---

## 0) So liest man einen lBase-„SQL-Fehler“

- Der Dialog **„SQL-Fehler“** zeigt oben eine **Nr** (z. B. `ORA-20994`) und darunter einen
  Kurztext. **[Details]** öffnet das vollständige **Fehlerprotokoll (XML)** mit
  `<REASON>`, `<REMEDY>` (das auslösende SQL), Login-/Org-Kontext und **SQL-History**.
- **`ORA-20000` bis `ORA-20999`** sind **keine DB-Systemfehler**, sondern **anwendungsseitig
  ausgelöste Fehler** (`RAISE_APPLICATION_ERROR`) – d. h. eine **Prüf-/Geschäftslogik bricht
  bewusst ab**. Der Text nach `ORA-20xxx:` ist die **Klartextbegründung**.
- Die Zeile **`ORA-06512: in "<SCHEMA.PACKAGE>", Zeile N`** nennt das **auslösende
  PL/SQL-Objekt** – der wichtigste Hinweis auf die Ursache.
- Für ein Ticket immer aus dem Detail-XML mitschicken: `ERROR`, `REASON`, das `<REMEDY>`-SQL,
  **Org-Kontext** (`FA_ID`/`NL_ID`/`AB_ID`/`AG_ID`), `USRID`, `SESSION/ID` und die
  **LBase-Version** (`CLIENT/VERSION`).

---

## 1) `ORA-20994` – `get_kurs(...) IS NULL` beim Fahrtstatuswechsel „in Kontrolle“

**Kurzdiagnose:** Die vom FSW „in Kontrolle“ angestoßene interne Verrechnung will einen
**Beleg-Kurs** ermitteln und findet **keine Kurstabellen-ID** → Abbruch. **Meist ist die
Ursache ein Objekt auf der Fahrt in verfrühtem Status** (z. B. Sendung noch im **Angebotsstatus**
oder **LA nicht „fertig zum Drucken“**), das keinen sauberen Beleg liefert – **nicht** eine
generell fehlende Kurstabelle. Erst Objekt-Stati prüfen (operativ), Konfiguration nur, wenn
**alle** vergleichbaren Fahrten scheitern.

### Symptom
- Der **Fahrtstatuswechsel (FSW) „auf Fahrt“ → „in Kontrolle“** lässt sich nicht setzen; es
  erscheint der SQL-Fehler `ORA-20994`.
- Auslöser im Prozess: „in Kontrolle“ stößt die **automatische interne Verrechnung** an
  (→ [12](12_lbase-21st-bedienung.md) FSW-Workflows). Dabei wird der **Währungskurs auf den
  Beleg** geschrieben (`SBEL_KURS_UPDATE`).

### Meldungstext (Beispiel)
```
ORA-20994: ERROR: get_kurs(, EUR, EUR, 2026.08.26, Y, 0) IS NULL!
ORA-06512: in "SPED.SBEL_KURS_UPDATE", Zeile 96
```
Auslösendes SQL (aus `<REMEDY>` / SQL-History):
```sql
SELECT sbel_kurs_update.get_kurs(MAX(kut_kutid), :p0 /*von-Wrg*/, :p1 /*nach-Wrg*/,
                                 slaf.li_to_date(:p2 /*Datum*/), 'Y', 0)
FROM   skut_t_kurstab
JOIN  (SELECT org_orgid FROM sorg_t_einheit
        START WITH org_orgid = :p3 /*Org*/ CONNECT BY PRIOR org_orgid = org_orgidh) ON kut_orgid = org_orgid
```

### Ursache – Symptom vs. eigentlicher Auslöser
**Technisch (Symptom):** `get_kurs` erwartet als 1. Argument die **Kurstabellen-ID `kut_kutid`**.
Im Fehlertext ist diese **leer**. Der Join sucht über die **Org-Hierarchie**
(`START WITH org_orgid = :p3 CONNECT BY …`) eine gültige Kurstabelle (`skut_t_kurstab`);
`MAX(kut_kutid)` kommt **NULL** zurück → `get_kurs` liefert NULL → `ORA-20994`.

> ⚠️ Das scheitert **sogar bei `EUR → EUR`** – der Fehler heißt also **nicht** „Fremdwährungs­kurs
> fehlt“, sondern die **Kurstabellen-ID konnte nicht aufgelöst werden**.

**Eigentlicher Auslöser (Praxis – wichtig!):** In der Regel ist **nicht** die Kurstabelle
grundsätzlich falsch konfiguriert. Meist ist **`:p3` (die Org des Belegs/der Sendung) leer oder
unbrauchbar**, weil ein **Objekt auf der Fahrt in einem verfrühten/unvollständigen Status** hängt
und daher **keinen sauberen Beleg mit gültiger Org-/Währungszuordnung** hat. Die interne
Verrechnung versucht trotzdem, dafür einen Kurs zu ermitteln → Baumsuche findet nichts → NULL.

Der `get_kurs`-Fehler ist damit ein **Folgesymptom**. **Zuerst die Objekt-Stati auf der Fahrt
prüfen**, nicht die Kurstabellen-Konfiguration.

**Im Feld bestätigte Auslöser:**
| Auslöser | woran erkennbar | Abhilfe (Ebene) |
|---|---|---|
| **Sendung noch im Angebotsstatus** (nicht in Einzelsendung übertragen, → [07] Proz. IX) | Sendung auf der Fahrt hat Angebots-/Angebotsstatus statt Auftrag | **Operativ** (Auftragsmgmt/Dispo) |
| **LA (Leistungsanforderung) nicht auf „fertig zum Drucken“** | Abrechnungs-/Druck-LA hängt in unfertigem Status | **Operativ** (Auftragsmgmt) |
| Kurstabelle tatsächlich nicht zugeordnet | **alle** Fahrten der Firma/Periode scheitern gleich | **Konfiguration** (Lagermax/Axians) |

> Faustregel: Scheitert **nur diese eine Fahrt/Sendung** → verfrühter Objekt-Status (operativ
> lösbar). Scheitern **alle** vergleichbaren → Kurstabellen-Konfiguration (2nd Level).

### Nebeneffekt: Status-Inkonsistenz Fahrt ↔ Übersicht
Ein zuvor abgebrochener „in Kontrolle“-Versuch kann eine **Inkonsistenz** hinterlassen:
- **In der Fahrt** (führender Datensatz, `frmFahrtBearb`) steht noch **„auf Fahrt“**,
- in der **Sammelübersicht** wird bereits **„in Kontrolle“** angezeigt.

Der FSV rollt bei `ORA-20994` zurück; der Übersichts-/Anzeigewert kann vorher gesetzt worden
sein. Folge-Symptome:
- Erneutes „in Kontrolle“ → wieder `ORA-20994` (bzw. „schon dort“).
- Direkt „abgeschlossen“ → **„Der Fahrtstatus kann nicht von '' auf '' übersprungen werden.“**
  (echter Status ist „auf Fahrt“, „abgeschlossen“ würde „in Kontrolle“ überspringen; die
  leeren `''` entstehen, weil der widersprüchliche Status nicht aufgelöst werden kann).

Die **richtige Statuskette** bleibt: `… → auf Fahrt → in Kontrolle → abgeschlossen`
(→ [10](10_glossar.md) FSW). Die Inkonsistenz ist ein **Symptom**, nicht die Ursache – sie
verschwindet i. d. R., sobald der FSW nach dem Kurs-Fix sauber durchläuft.

### Diagnose (schnell) – erst Objekt-Stati, dann Konfiguration
1. **[Details]** öffnen → `REASON` und `ORA-06512`-Objekt lesen: bestätigt `get_kurs` /
   `SBEL_KURS_UPDATE`.
2. **Sendungen der Fahrt durchgehen:** Ist eine Sendung **noch im Angebotsstatus** (nicht in
   eine **Einzelsendung/Auftrag** übertragen, → [07](07_prozesse-logbatt.md) Proz. IX)? Diese
   Sendung hat keinen sauberen Beleg → sie ist der wahrscheinliche Auslöser.
3. **LA-Übersicht prüfen:** Hängt eine Abrechnungs-/Druck-**LA** in unfertigem Status (nicht
   **„fertig zum Drucken“**)? → ebenfalls typischer Auslöser.
4. **Echten Fahrtstatus** in der geöffneten Fahrt ablesen (nicht die Sammelübersicht; nicht die
   Spalten *Status Beladung/Entladung* – das sind Sendungs-/Scanstatus).
5. **Systemisch oder Einzelfall?** Eine vergleichbare **andere** Fahrt derselben Firma/Periode
   testweise auf „in Kontrolle“ setzen:
   - **Nur diese Fahrt scheitert** → verfrühter Objekt-Status (Schritt 2/3) → **operativ** lösen.
   - **Alle scheitern gleich** → **Kurstabelle grundsätzlich nicht zugeordnet** → Konfiguration
     (2nd Level).

### Abhilfe

**A) Regelfall – verfrühter Objekt-Status (operativ, Auftragsmgmt/Dispo):**
1. **Sendung aus dem Angebotsstatus holen:** Angenommenes Angebot in eine **Einzelsendung/
   Auftrag übertragen** (→ [07] Proz. IX; Achtung: irreversibel) → damit entstehen saubere
   Belegdaten inkl. Org/Währung. Gehört die Sendung gar nicht auf die Fahrt → **von der Fahrt
   nehmen** (de-disponieren) statt übertragen.
2. **LA fertigstellen:** Abrechnungs-/Druck-LA auf **„fertig zum Drucken“** bringen.
3. **Erneut versuchen:** FSW **aus der geöffneten Fahrt** `auf Fahrt → in Kontrolle`. Läuft die
   Verrechnung durch, gleichen sich Fahrt und Übersicht ab → anschließend `→ abgeschlossen`.

**B) Ausnahme – Kurstabelle wirklich nicht zugeordnet (Konfiguration):**
Nur wenn Schritt 5 zeigt, dass **alle** vergleichbaren Fahrten scheitern.
1. **Key-User:** In den **Währungs-/Kurs-Stammdaten (MD PROD)** prüfen, ob der Firma/Org (oder
   einer übergeordneten Einheit) eine **Kurstabelle** zugeordnet ist und diese fürs Belegdatum
   gültige Einträge hat. *(Technisch: `sfir_firma.fir_kutid`, `skut_t_kurstab`.)*
2. **Eskalation (2nd Level):** Ist keine Kurstabelle konfiguriert / nicht über die Oberfläche
   herstellbar → **Ticket an Lagermax/Axians** mit dem technischen Detail unten.

> **Status-Inkonsistenz** (Fahrt „auf Fahrt“, Übersicht „in Kontrolle“) nach erfolgreichem FSW
> noch vorhanden → separat über Lagermax/Axians im Backend geradeziehen lassen.

### Beteiligte DB-Objekte (für IT/Key-User)
| Objekt | Bedeutung |
|---|---|
| `SBEL_KURS_UPDATE.get_kurs(...)` | Beleg-Kurs-Ermittlung (wirft hier `ORA-20994`) |
| `skut_t_kurstab` (`kut_kutid`, `kut_orgid`) | **Kurstabelle** je Organisation |
| `sorg_t_einheit` (`org_orgid`, `org_orgidh`) | Org-Hierarchie (Baumsuche der gültigen Kurstabelle) |
| `sfir_firma.fir_kutid` / `sfiw_firwrg` | der Firma zugeordnete **Kurstabelle** / **Firmenwährung** |

### Ticket-Vorlage (nur für Fall **B** – systemische Kurstabellen-Konfig; Eskalation an Lagermax/Axians – 2nd Level, → [07](07_prozesse-logbatt.md) Support VII)
> Bei Fall **A** (verfrühter Objekt-Status) **kein** Ticket – Sendung aus dem Angebotsstatus
> holen bzw. LA fertigstellen und FSW wiederholen.
```
Betreff: LogBATT – Fahrt lässt sich nicht auf „in Kontrolle“ setzen (ORA-20994 get_kurs)

Fahrt:       PLO-1989 „KW35-Donald“
Fehler:      ORA-20994: get_kurs(, EUR, EUR, 2026.08.26, Y, 0) IS NULL
Objekt:      SPED.SBEL_KURS_UPDATE, Zeile 96
Deutung:     Keine gültige Kurstabelle (kut_kutid = NULL) für die Org zum Belegdatum 26.08.2026;
             Beleg-Kurs-Ermittlung im FSW „in Kontrolle“ bricht ab.
Bitte prüfen: Zuordnung/Gültigkeit der Kurstabelle (fir_kutid) für LogBATT GmbH bzw. Org-Hierarchie;
             ggf. Kurs für 26.08.2026 nachpflegen/importieren.
Nebenbefund: Fahrt-Datensatz = „auf Fahrt“, Sammelübersicht = „in Kontrolle“ (Inkonsistenz nach Abbruch).
Kontext:     FA 8001 LogBATT GmbH · NL 8002 PLO Plochingen · AB 8003 PLO Landverkehr · AG 8009 PLO National
             User thorwr · Session 1363/39975 · LBase 7.7.1_4 (2026.06.12) · DB LMXLB1P (Oracle 19c)
```

### Merksätze
- `ORA-20994 … get_kurs … IS NULL` ist ein **Folgesymptom**, nicht die Ursache: die interne
  Verrechnung findet keinen Beleg-Kurs.
- Tritt beim **FSW „in Kontrolle“** auf, weil dieser die **interne Verrechnung** anstößt.
- **Zuerst Objekt-Stati prüfen:** Sendung noch im **Angebotsstatus**? **LA nicht „fertig zum
  Drucken“**? → operativ lösbar (Auftragsmgmt/Dispo). Das ist der Regelfall.
- **Gegentest** (andere Fahrt gleicher Firma/Periode): scheitert **nur diese** → Objekt-Status;
  scheitern **alle** → Kurstabellen-Konfiguration (dann 2nd Level Lagermax/Axians).
- Zeigt die Übersicht „in Kontrolle“, die Fahrt aber „auf Fahrt“ → **Abbruch-Nebenwirkung**,
  kein zweiter, eigener Fehler.

---

## 2) `ORA-01403` „Keine Daten gefunden“ im Trigger `SBEL_BEF_SAEUMNISZA` (Angebot → Einzelsendung)

**Kurzdiagnose:** Beim Umwandeln **Angebot → Einzelsendung** legt lBase einen **Beleg** an
(`insert into sbel_beleg`). Ein **Before-Insert-Trigger** darauf macht ein `SELECT … INTO` für
eine **Zahlungs-/Säumnis-Angabe (Zahlungsbedingung/Zahlungsziel)** und findet **keine Zeile** →
`ORA-01403` → als Trigger-Fehler `ORA-04088`. Meist **fehlender Stammsatz** (Zahlungsbedingung
am Rechnungsempfänger/Debitor), sonst **Trigger-Robustheitslücke** (2nd Level).

> ⚠️ Unabhängig vom `get_kurs`-Fall (1): anderer Trigger, anderer Mechanismus, anderer
> Prozessschritt (hier **Angebot → Einzelsendung**, nicht der FSW).

### Symptom
- Der Statuswechsel einer Sendung von **„Angebot“ → „Einzelsendung/Auftrag“** bricht mit einem
  SQL-Fehler ab (Beispiel `ORA-1403`).

### Meldungstext (Beispiel)
```
ORA-01403: Keine Daten gefunden
ORA-06512: in "SPED.SBEL_BEF_SAEUMNISZA", Zeile 7
ORA-04088: Fehler bei der Ausführung von Trigger 'SPED.SBEL_BEF_SAEUMNISZA'
```
Auslösendes SQL (Beleg-Insert, gekürzt):
```sql
insert into sbel_beleg (BEL_BELID, BEL_ORGID, BEL_ADRID, BEL_LASID, BEL_BTYID, BEL_BETNTO,
  BEL_VORZ, BEL_ZZ, BEL_ZZVAR, BEL_ABC, BEL_INTERN, BEL_BESID, BEL_UID_RA, BEL_UID_RE,
  BEL_AEND, BEL_USRID, BEL_ACLID, BEL_ERFDAT)
values (:p0, :p1, :p2 /*Adr*/, '210', 'AB', :p3, '1', :p4, 1, 'A', 0, 'E',
  :p5 /*UID_RA*/, :p6 /*UID_RE*/, sysdate, :p7, :p8, sysdate);
```

### Ursache
- Der Insert in `sbel_beleg` löst den **Trigger `SBEL_BEF_SAEUMNISZA`** aus (Namensteile:
  *SBEL* = Beleg, *BEF* = Before-Insert, *SAEUMNIS/ZA* = **Säumnis/Zahlung** → Zahlungsziel/
  -bedingung). Der Trigger führt in Zeile 7 ein `SELECT … INTO` aus, das **keine Zeile** trifft.
- `ORA-01403 (NO_DATA_FOUND)` wird im Trigger **nicht abgefangen** → der Insert bricht ab
  (`ORA-04088`). *(Die genaue gesuchte Zeile ist ohne Trigger-Quelltext nicht belegbar; der
  Trigger-Name legt eine **Zahlungsbedingung/Säumnis-Angabe** nahe.)*
- **Wahrscheinlichster Auslöser:** dem beteiligten **Rechnungsempfänger/Debitor** (Beleg-Adresse
  `BEL_ADRID` / UID-Felder) fehlt eine **Zahlungsbedingung/Zahlungsziel**, die der Trigger
  zwingend erwartet.
- **Sonderkonstellation beachten:** Ist die Sendung **„Dienstgut ohne Berechnung“** (interne
  Sendung, Frankatur `991`) mit **Rechnungsempfänger = eigene Firma** (z. B. LogBATT GmbH
  `80000001`), existiert für diesen „Debitor“ evtl. **gar kein Zahlungs-/Debitorstammsatz** –
  dann läuft der Zahlungs-Trigger ins Leere. Das ist ein starker Kandidat für diesen Fall.

### Diagnose (schnell)
1. **[Details]** öffnen → bestätigt `SBEL_BEF_SAEUMNISZA` / `sbel_beleg`-Insert.
2. **Nur diese Sendung oder alle?** Ein **anderes Angebot** testweise in eine Einzelsendung
   wandeln:
   - **Andere gehen** → diese Sendung/ihre Adressen sind der Unterschied → Schritt 3.
   - **Alle scheitern** → generelle Konfig-/Trigger-Lücke → 2nd Level.
3. **Adressen der Sendung vergleichen** (RECH/Rechnungsempfänger, Auftraggeber, Empfänger):
   Hat der **Rechnungsempfänger/Debitor** eine **Zahlungsbedingung/Zahlungsziel** hinterlegt?
   Fehlt sie bei genau dieser Adresse → Auslöser gefunden.
4. **Konstellation prüfen:** „Dienstgut ohne Berechnung“ + interne Rechnung an eigene Firma?
   → gehört hier überhaupt ein (Abrechnungs-)Beleg zu entstehen?

### Abhilfe
**A) Regelfall – fehlende Zahlungs-Stammdaten (Key-User/Stammdaten):**
1. Am **Rechnungsempfänger/Debitor** die **Zahlungsbedingung/Zahlungsziel** ergänzen (analog zu
   funktionierenden Debitoren) → Umwandlung erneut versuchen.

**B) Interne No-Charge-Sendung / unklare Konstellation:**
2. Ist die Sendung „Dienstgut ohne Berechnung“ und soll gar nicht fakturiert werden, aber das
   System will trotzdem einen Zahlungs-Beleg bauen → **Konfiguration/Trigger** passt nicht zur
   Konstellation → **Lagermax/Axians (2nd Level)**.

**C) Immer prüfenswert – Trigger-Robustheit:**
3. Ein **nicht abgefangenes `ORA-01403` in einem Insert-Trigger** ist eine Robustheitslücke: der
   Trigger sollte den „keine Zahlungsbedingung“-Fall sauber behandeln statt hart abzubrechen.
   Das gehört von **Lagermax/Axians** gefixt (mit Beispiel-Sendung melden).

### Beteiligte DB-Objekte
| Objekt | Bedeutung |
|---|---|
| `sbel_beleg` (`BEL_ADRID`, `BEL_BTYID='AB'`, `BEL_LASID='210'`, `BEL_UID_RA/RE`) | Beleg, der bei Angebot→Einzelsendung entsteht |
| Trigger `SPED.SBEL_BEF_SAEUMNISZA` | Before-Insert-Trigger; wirft hier `ORA-01403`/`ORA-04088` |

### Ticket-Vorlage (Eskalation an Lagermax/Axians – 2nd Level, → [07](07_prozesse-logbatt.md) Support VII)
```
Betreff: LogBATT – Angebot lässt sich nicht in Einzelsendung wandeln (ORA-01403, Trigger SBEL_BEF_SAEUMNISZA)

Sendung:     PLO-5495-PLO-1 (Angebot → Einzelsendung schlägt fehl)
Fehler:      ORA-01403 Keine Daten gefunden → ORA-04088 Trigger SPED.SBEL_BEF_SAEUMNISZA, Zeile 7
Vorgang:     insert into sbel_beleg (BTYID='AB', LASID='210') beim Statuswechsel Angebot→Einzelsendung
Deutung:     SELECT INTO im Zahlungs-/Säumnis-Trigger findet keine Zeile (fehlende Zahlungsbedingung?);
             ORA-01403 wird im Trigger nicht abgefangen.
Konstellation: „Dienstgut ohne Berechnung“ (Frankatur 991), Rechnungsempfänger = LogBATT GmbH (80000001, intern)
Bitte prüfen: erwartete Zahlungs-/Debitor-Stammdaten für diese Konstellation; Trigger sollte den
             Fall „keine Zahlungsbedingung“ robust behandeln.
Kontext:     User kuzmea · Session 1213/59771 bzw. 217/13720 · Sendung PLO-5495-PLO-1
```

### Merksätze
- `ORA-01403` + `ORA-04088` = ein **Trigger** bricht ab, weil ein `SELECT … INTO` **keine Zeile**
  findet – **fehlender/erwarteter Stammsatz**, nicht „Sendung kaputt“.
- Hier beim Schritt **Angebot → Einzelsendung** (Beleg-Insert), Trigger `SBEL_BEF_SAEUMNISZA`
  (Zahlung/Säumnis).
- Zuerst **Zahlungsbedingung am Rechnungsempfänger/Debitor** prüfen; Sonderfall interne
  No-Charge-Sendung an eigene Firma beachten.
- Nicht abgefangenes `ORA-01403` im Trigger = **Robustheitslücke** → an Lagermax/Axians.

---

## 3) „Abrechnungsbedingungen NICHT erfüllt“ (STATUS = NOTOK) beim „Abrechnung starten“

**Kurzdiagnose:** **Keine DB-Fehlermeldung**, sondern ein **Prüfmodul der Abrechnungslogik** (LI)
blockiert die Abrechnung bewusst, weil die **Abrechnungsbedingungen nicht erfüllt** sind.
Häufigste Ursache: der abzurechnende **Partner ist kein vollständiger Kunde/Debitor** (noch
**Interessent** oder Pflichtfelder fehlen) – oft nach einer **SAP-Änderung, die noch nicht
synchronisiert** ist.

> Unabhängig von den `ORA`-Fällen (1)/(2): kein Absturz, sondern eine **bewusste Sperre**. Es
> entsteht keine Abrechnungs-LA/Rechnung, bis die Bedingung erfüllt ist.

### Symptom
- Beim **„Abrechnung starten“** (Typ Abrechnung, Subtyp z. B. „An den Kunden“) erscheint eine
  **Meldung** statt eines SQL-Fehlers.

### Meldungstext (Beispiel)
```
Abrechnungsbedingungen NICHT erfüllt NOTOK am 2026.09.11 11:00:33
von jungit
FSW = 410, STATUS = NOTOK
```
- `von <user>` = User/Terminal; `FSW`/`STATUS = NOTOK` = **Ergebnis des Prüfmoduls** (OK/NOTOK).

### Ursache
- Ein **Abrechnungs-Prüfmodul** wertet die Bedingungen aus und liefert **NOTOK** → die Abrechnung
  wird nicht gestartet (analog zu den *Sendungs-Prüfmodulen*, → [02]/[09]).
- **Stärkster Kandidat:** Der abzurechnende Partner ist **kein vollständiger Kunde/Debitor**.
  Für „Abrechnung an den Kunden“ braucht es einen **echten Kunden** mit **Rechnungsanschrift,
  gültiger UID, Zahlungsbedingung, Vertriebsmitarbeiter, USt-Satz** (→ [07] Proz. I, [16] Vertrieb).

> **Richtung wichtig (häufiges Missverständnis):** Ein **Interessent ist NICHT abrechenbar** –
> das Prozess-Handbuch sagt sogar: Neukunde *direkt als Kunde anlegen, „Interessent-Status
> entfällt“* ([16] Vertrieb Pkt. 1). Das NOTOK kommt also, wenn der Partner **(noch) Interessent
> bzw. unvollständig** ist – **nicht**, weil er „schon Kunde“ ist. Ein sauberer Kunde ist die
> **Voraussetzung**, damit es klappt.

- **SAP-Sync beachten:** Kundenstammdaten sind in **SAP führend** und laufen nur **~alle 15 Min**
  nach lBase (→ [07]/[16]). Frisch in SAP geänderte Partner (Interessent→Kunde, ergänzte Felder)
  sind evtl. **noch nicht** in lBase → bis dahin NOTOK.
- Kundennummern-Konvention: **Kunden „81…“**, Rechnungsempfänger „810…“ (→ [16]). Eine vergebene
  „81…“-Nummer heißt „Kunde“, garantiert aber **nicht**, dass alle Debitorfelder befüllt sind.
- Andere mögliche NOTOK-Gründe (nachrangig): fehlende **Kondition/Preis/Rahmenvertrag**,
  **Spesencode**, **Leistungsdatum/Buchungsperiode**.

### Diagnose (schnell)
1. **Partner in lBase prüfen** (Adresse/Debitor, im Beispiel Kußmaul **811729000**): vollständiger
   **Kunde** mit Rechnungsanschrift, UID, Zahlungsbedingung, Vertriebler?
2. War der Partner zuletzt **Interessent** oder wurde in SAP gerade geändert? → **~15 Min Sync**
   abwarten bzw. prüfen, ob der neue Stand in lBase angekommen ist.
3. **Gegenprobe:** Gleiche Abrechnung bei einem sauber angelegten Kunden → läuft die durch, ist
   das Kundenstammbild die Differenz.

### Abhilfe
1. **Fehlende Kundenfelder ergänzen (Vertrieb, in SAP)** – Rechnungsanschrift/UID/Zahlungsbedingung/
   Vertriebler; **direkt als Kunde**, nicht als Interessent. Nach dem **Sync** erneut „Abrechnung
   starten“.
2. Zeigt sich, dass die Bedingung an etwas anderem hängt (Kondition/Preis/Spesencode/Periode) →
   entsprechend nachziehen; bei unklarer Prüfregel den Detailtext des Prüfmoduls heranziehen bzw.
   Key-User/2nd Level.

### Merksätze
- `STATUS = NOTOK` = **bewusste Sperre** eines Abrechnungs-Prüfmoduls, kein Absturz.
- „An den Kunden“ abrechnen geht **nur mit vollständigem Kunden/Debitor**; **Interessent = nicht
  abrechenbar**.
- **SAP ist führend**, Sync ~15 Min – frische Kundenänderungen brauchen einen Moment.
