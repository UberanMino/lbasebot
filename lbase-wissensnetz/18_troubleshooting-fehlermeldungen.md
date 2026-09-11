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
