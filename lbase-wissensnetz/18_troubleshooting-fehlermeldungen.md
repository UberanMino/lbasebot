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

**Kurzdiagnose:** Fehlende/nicht zugeordnete **Kurstabelle (Wechselkurs)** für die
Organisation zum Belegdatum → die von der internen Verrechnung angestoßene
**Beleg-Kurs-Ermittlung** findet keinen Kurs und bricht ab. **Kein Dispo-/Fahrtfehler –
Stammdatenproblem (IT/Key-User/FiBu).**

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

### Ursache
- Die Funktion `get_kurs` erwartet als **1. Argument die Kurstabellen-ID `kut_kutid`**. Im
  Fehlertext ist diese **leer** (`get_kurs(` **leer** `, EUR, EUR, …)`).
- Grund: Der Join sucht über die **Org-Hierarchie** (`START WITH … CONNECT BY …`) eine
  **gültige Kurstabelle** (`skut_t_kurstab`). `MAX(kut_kutid)` liefert **NULL** →
  **es ist keine Kurstabelle für die betroffene Organisation zum Belegdatum zugeordnet/gültig**.
- Deshalb liefert `get_kurs` NULL – und die Logik wirft `ORA-20994`.

> ⚠️ Das scheitert **sogar bei `EUR → EUR`**: Ohne zugeordnete Kurstabelle gibt es überhaupt
> keinen Kurs (auch nicht den trivialen 1:1). Der Fehler heißt also **nicht**, dass ein
> Fremdwährungskurs fehlt, sondern dass die **Kurstabelle selbst fehlt/nicht zugeordnet** ist.

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

### Diagnose (schnell)
1. **[Details]** öffnen → `REASON` und `ORA-06512`-Objekt lesen: bestätigt `get_kurs` /
   `SBEL_KURS_UPDATE`.
2. **Echten Fahrtstatus** in der geöffneten Fahrt ablesen (nicht die Sammelübersicht;
   nicht die Spalten *Status Beladung/Entladung* – die sind Sendungs-/Scanstatus).
3. **Belegdatum** aus der Meldung notieren (im Beispiel `2026.08.26`) – dafür fehlt der Kurs.
4. **Systemisch oder Einzelfall?** Eine **vergleichbare andere Fahrt** derselben Firma/Periode
   testweise auf „in Kontrolle“ setzen:
   - **Andere scheitern auch** (gleicher Fehler) → **Kurstabelle grundsätzlich nicht
     zugeordnet/kaputt** → Konfiguration, i. d. R. Fall für **Lagermax/Axians**.
   - **Andere funktionieren** → das Problem ist **Fahrt-/datumsspezifisch** (z. B. Kurs für
     diesen Tag fehlt, oder diese Fahrt/ihr Beleg hängt an einer Org ohne Kurstabelle).

### Abhilfe – Ebene **Stammdaten/Konfiguration** (nicht durch Dispo lösbar)
> Das ist **kein** Dispo-/Fahrtfehler und aus der Fahrt heraus nicht behebbar. Es liegt auf
> **Stammdaten-/Konfigurationsebene** – als **Key-User (1st Level)** zuerst selbst prüfen,
> sonst an **Lagermax/Axians (2nd Level)** eskalieren.

1. **Kurstabelle zuordnen/prüfen (Key-User):** In den **Währungs-/Kurs-Stammdaten (MD PROD)**
   prüfen, ob der **Firma „LogBATT GmbH“** bzw. der betroffenen Org-Einheit (oder einer
   **übergeordneten** Einheit in der Org-Hierarchie) eine **Kurstabelle** zugeordnet ist
   und ob diese für das Belegdatum **gültige Einträge** hat.
   *(Technisch: Zuordnung `sfir_firma.fir_kutid`, Kurstabelle `skut_t_kurstab`.)*
2. **Kurs nachpflegen/importieren:** Fehlt der Tages-/Periodenkurs für das Datum → **Kurs
   pflegen** bzw. Kurstabelle der Org zuordnen. Prüfen, ob ein automatischer Kursimport
   ausgefallen ist.
3. **Eskalation (2nd Level):** Ist gar keine Kurstabelle konfiguriert / ist die Zuordnung
   nicht über die Oberfläche herstellbar → **Ticket an Lagermax/Axians** mit dem
   technischen Detail unten (Betreiber = Lagermax, Hersteller = Axians/lBase, → [00]).
4. **Erneut versuchen:** Danach den FSW **aus der geöffneten Fahrt** `auf Fahrt → in Kontrolle`
   setzen. Läuft die Verrechnung durch, gleichen sich Fahrt und Übersicht ab → anschließend
   `→ abgeschlossen`.
5. **Bleibt die Status-Inkonsistenz** nach erfolgreichem FSW bestehen → separat über
   Lagermax/Axians im Backend geradeziehen lassen.

### Beteiligte DB-Objekte (für IT/Key-User)
| Objekt | Bedeutung |
|---|---|
| `SBEL_KURS_UPDATE.get_kurs(...)` | Beleg-Kurs-Ermittlung (wirft hier `ORA-20994`) |
| `skut_t_kurstab` (`kut_kutid`, `kut_orgid`) | **Kurstabelle** je Organisation |
| `sorg_t_einheit` (`org_orgid`, `org_orgidh`) | Org-Hierarchie (Baumsuche der gültigen Kurstabelle) |
| `sfir_firma.fir_kutid` / `sfiw_firwrg` | der Firma zugeordnete **Kurstabelle** / **Firmenwährung** |

### Ticket-Vorlage (Eskalation an Lagermax/Axians – 2nd Level, → [07](07_prozesse-logbatt.md) Support VII)
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
- `ORA-20994 … get_kurs … IS NULL` = **Kurstabelle/Wechselkurs fehlt**, nicht „Fahrt kaputt“.
- Tritt beim **FSW „in Kontrolle“** auf, weil dieser die **interne Verrechnung** (Beleg-Kurs)
  anstößt.
- **Stammdaten-/Konfig-Fix**: Key-User prüft Kurstabellen-Zuordnung, sonst Eskalation an
  **Lagermax/Axians (2nd Level)**; aus der Fahrt/Dispo heraus nicht behebbar.
- **Gegentest** mit einer anderen Fahrt derselben Firma/Periode trennt „systemisch fehlende
  Kurstabelle“ von „nur diese Fahrt/dieses Datum“.
- Zeigt die Übersicht „in Kontrolle“, die Fahrt aber „auf Fahrt“ → **Abbruch-Nebenwirkung**,
  kein zweiter, eigener Fehler.
