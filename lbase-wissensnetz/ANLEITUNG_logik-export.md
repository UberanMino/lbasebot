# Anleitung: lBase-Logik für den Kontext exportieren

← [Index](00_INDEX.md)

Diese Anleitung beschreibt **Schritt für Schritt**, wie man die eigentliche Logik von lBase
aus dem System in Dateien exportiert, damit sie hier analysiert werden kann. Alle Menüpfade
sind aus den vorhandenen Handbüchern (mit Seitenangabe) verifiziert.

> **Wer:** Am einfachsten für **Key-User** (Modul-/GenTab-Pflege ist deren Aufgabe, → [16]).
> **Menüband-Hinweis:** In LBase 21st liegen die Werkzeuge im Ribbon-Reiter **ADMINISTRATION**
> (Gruppen *Verwaltungstools*, *Generische Tabellen*, *Konfigurationen*) bzw. **ABRECHNUNG**.
> *(Screenshot der Ribbon-Gruppen: 21st-Allg. S.51.)*

---

## Prioritäten (was den größten Nutzen bringt)

| Prio | Was | Format | Abschnitt |
|---|---|---|---|
| ⭐ 1 | **LOGBATT-Abrechnungsmodule** (LI-Code) | `.me` (Text) | A |
| ⭐ 2 | **Generische Tabellen** `LMX_LBATT_KO`, `LMX_LBATT_TX` | Excel | B |
| ⭐ 3 | **Wertebereich** `X_LOGB_BER` + eine Beispiel-**Matrix** `PLO_…` | Excel | C, D |
| 4 | **Kriteriumssätze**-Übersicht (welches Modul wann) | Excel | E |
| 5 | Bei konkretem Fehlerfall: **LI-Debug-Mitschnitt** | `.txt` | F |

**Minimal-Paket für den ersten Test:** A (4 Module) + B (2 GenTabs). Damit ist der Kern der
LOGBATT-Abrechnung abgedeckt.

---

## A) Module als Datei exportieren (der eigentliche Code) ⭐

*Quelle: T102-HB S.76 (gedr. -71-) „Import/Export Module".*

**Zielmodule (LOGBATT-Abrechnung):**
`LOGBATT_SDG_BESCH_DATEN`, `LOGBATT_BERECHNUNG_SPC`, `LOGBATT_SPC_TEXT`,
`LOGBATT_EINMALANGEBOT` — dazu ggf. das `LIM LOGBATT` (LPD-157822).

**Schritte:**
1. **ADMINISTRATION → Verwaltungstools → Module** (Auswahlmaske öffnet sich).
2. In der Auswahlmaske die **Bibliothek** wählen (LOGBATT-Abrechnung liegt im Abrechnungs-/
   Spesenblock-Bereich) bzw. das Modul **über den Namen suchen** (Feld antippen → **[F5]**).
   *Tipp: Der Key-User weiß, in welcher Bibliothek die Module liegen.*
3. **[OK] / [F12]** → Modul-Übersicht. Gewünschtes Modul **markieren**.
4. **[Startbedingung / Version (Detail)] / [F9]** → gültige **Version** markieren.
5. **[Moduleditor öffnen] / [Strg+M]**.
6. Im Moduleditor **alle Zeilen markieren** (erste Zeile anklicken, mit Shift bis zur letzten;
   Tipp: Feld „Gehe zu Zeile" = **-1** springt ans Ende, → [03]).
7. Ribbon **MODUL – BEARBEITUNG → Moduleditor → „in Datei kopieren"**.
8. Im Dialog **Dateiname** eingeben, Datei-Typ **„Moduleditor-File (\*.me)"**, **[Speichern]**.
9. Für jedes Modul wiederholen.

**Ergebnis:** je eine `*.me`-**Textdatei** pro Modul — direkt lesbar/analysierbar.
*(Gegenrichtung = „Von Datei einfügen".)*

> Optional für den lückenlosen Blick: im Moduleditor **„Zeige 3GL-Code"** aktivieren – löst alle
> `#include` auf und zeigt den tatsächlich ausführbaren Code *(T102-HB S.25 / gedr. -20-, → [03])*.

---

## B) Generische Tabellen (GenTabs) exportieren ⭐

*Ribbon-Gruppe „Generische Tabellen" mit **Definition** (Struktur) und **Daten** (Inhalt);
Screenshot 21st-Allg. S.51. Export-Weg 21st-Allg. S.56.*

**Zieltabellen:** `LMX_LBATT_KO` (welche Berechnungs-Elemente je Adresse/RV) und
`LMX_LBATT_TX` (Texte/Einheiten) — optional `LMX_SDG_VAR`, `LMX_SDG_LIM`, `LMX_FAR_DR`,
`LMX_SDG_DR` (→ [13]).

**Schritte:**
1. **ADMINISTRATION → Generische Tabellen → Daten**.
2. Die gewünschte Tabelle wählen/filtern (z. B. `LMX_LBATT_KO`) → Inhalt anzeigen.
3. **Exportieren:** entweder nichts markieren (= alle Zeilen) oder gewünschte Zeilen markieren,
   dann **DATEI → Senden an → MS Excel** *(21st-Allg. S.56)*.

**Ergebnis:** Excel-Tabelle je GenTab.

> Falls eine Tabelle unter „Generische Tabellen → Daten" nicht auftaucht, ist sie ggf. ein
> **Wertebereich** → siehe C.

---

## C) Wertebereiche exportieren (z. B. `X_LOGB_BER`) ⭐

*Screenshot 21st-Allg. S.51 („Wertebereich öffnen / Bereichsview").*

**Ziel:** `X_LOGB_BER` (Katalog der Berechnungs-Elemente), optional `X_LBATT_EINH` (Einheiten).

**Schritte:**
1. **ADMINISTRATION → Verwaltungstools → Wertebereiche**.
2. Den Wertebereich suchen/markieren (z. B. `X_LOGB_BER`) → **[Bereichsview]** (Icon „0-9AZ").
   Es erscheinen die Spalten *Wertebereich / Wert / Bezeichnung / Text / Komponente*.
3. **DATEI → Senden an → MS Excel** *(21st-Allg. S.56)*.

**Ergebnis:** Excel mit allen zulässigen Werten/Elementen.

---

## D) Stammdaten-Matrix (Preise) anzeigen/exportieren

*Ribbon-Screenshot: Prozessschritte v1.7, Folie/S.63 „Hinweis Matrizen finden".*

**Ziel:** eine Beispiel-Matrix, z. B. `PLO_810035000_F41946L_VERP` (Namenskonvention
`PLO_<Rechnungsempfänger>_<RV>_<Element>`, → [14]).

**Schritte:**
1. **ABRECHNUNG → Abrechnung mit Konditionen → Stammdaten-Matrix**.
2. Im Dialog **Filter/Anzeigeart** „MD Matrix – Standard", ggf. **Sprache** wählen.
3. Die Matrix **über den Namen** (`PLO_…`) suchen → **[Anzeigen]** (nicht Ändern).
4. Über die geöffnete Übersicht die Werte per **DATEI → Senden an → MS Excel** exportieren.

**Ergebnis:** die konkreten Preis-/Tarifwerte der Matrix.
*(Die Matrix-Logik selbst steckt außerdem im zugehörigen Abrechnungsmodul aus A.)*

---

## E) Kriteriumssätze / Bibliotheken-Übersicht (welches Modul wann läuft)

*Quelle: T102-HB S.13 (gedr. -8-), → [02].*

1. **ADMINISTRATION → Verwaltungstools → Bibliotheken** → **[OK]**.
2. Gewünschte Bibliothek **markieren** → Button **[Kriterium-Sätze]** → zeigt fixe/alternative
   Suche + Priorität.
3. Export bei Bedarf via **DATEI → Senden an → MS Excel**.

---

## F) LI-Debug-Mitschnitt (für einen konkreten Problemfall)

*Quelle: T102-HB S.74–75 (gedr. -69-,-70-), → [01].*

1. **OPTIONEN → Logikinterpreter → LI-Testmodus** aktivieren (Fenster mit Hintergrundinfos)
   **oder** **LI-Debug in Datei** aktivieren (schreibt den Ablauf in eine Datei).
2. Den **Problemfall reproduzieren** (z. B. betroffene Abrechnungs-LA ausführen / Sendung
   speichern).
3. Die erzeugte **Debug-Datei** sichern.

**Ergebnis:** ein Schritt-für-Schritt-Protokoll, was der LI mit den echten Daten getan hat –
ideal für „warum wird genau *dieser* Fall so behandelt?".

---

## Was am Ende zu mir kommt

- `*.me`-Dateien (Module) — **am wertvollsten**
- `*.xlsx` (GenTabs, Wertebereiche, Matrix, Kriteriumssätze)
- `*.txt` (LI-Debug), falls ein konkreter Fehlerfall analysiert werden soll

Alle Formate kann ich direkt auswerten. Danach kann ich die **reale Logik** (statt nur der
Beschreibung) ins Wissensnetz einarbeiten.

## ⚠️ Häufige Fehler (aus der Praxis)

1. **Modul-Export ist (fast) leer / nur eine Zeile.**
   Ursache: Beim „in Datei kopieren“ war **nur die Cursor-Zeile markiert**, nicht das ganze
   Modul. → Vor dem Export **alle Zeilen markieren**: erste Zeile anklicken, dann ans
   **Modulende springen** (Feld „Gehe zu Zeile“ = **-1**) und mit **Shift+Klick** auf die
   letzte Zeile den gesamten Bereich markieren (alternativ **Strg+A** im Zeilenbereich testen).
   Erst wenn **alle Zeilen blau hinterlegt** sind → „in Datei kopieren“.
   *Kontrolle: eine `.me` eines echten Moduls ist i. d. R. viele KB groß, nicht wenige Byte.*

2. **GenTab-Export enthält nur die Tabellenliste statt der Inhalte.**
   Ursache: Es wurde die **Definition** (Liste aller Tabellen: *Tabellen-ID / Bezeichnung /
   Komponente*) exportiert statt der **Daten** (Zeileninhalt). → In der Ribbon-Gruppe
   **Generische Tabellen** auf **„Daten“** (nicht „Definition“) gehen, die Tabelle
   (z. B. `LMX_LBATT_KO`) wählen/filtern, sodass die **einzelnen Konditionszeilen** erscheinen,
   und **diese** per **DATEI → Senden an → MS Excel** exportieren.
   *Kontrolle: die Datei enthält viele Zeilen mit Adress-/RV-/Element-Bezug, nicht nur 2–3 Zeilen
   mit Tabellennamen.*

## ⚠️ Vertraulichkeit
Modul- und GenTab-/Matrix-Exporte enthalten teils **echte Kundennummern, Rahmenverträge und
Preise**. Für die Analyse ist das unproblematisch, aber es sind vertrauliche Konditionen –
ggf. vorab auf **ein bis zwei Beispiel-Kunden/RV** eingrenzen oder Preise anonymisieren, wenn
das gewünscht ist.
