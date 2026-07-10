# 06 – Bedienung: Funktionstasten, Shortcuts, Suche, Filter

← [Index](00_INDEX.md) · verwandt: [08 Digitaler Sendungsakt](08_digitaler-sendungsakt.md)

Quelle: **lBase Basis-Schulung – Tipps & Tricks** (Unterlagen für User nach Basis-Schulung).

## Funktionstasten [F1]–[F12]

| Taste | Funktion |
|---|---|
| **F1** | Hilfe (Online-Hilfe **zur aktuellen Maske**) |
| **F2** | Maske leeren (in Auswahlmaske) · **Zurückblättern** (z. B. Sendungs-/Fahrtanzeige) |
| **F3** | Weiterblättern (z. B. Sendungs-/Fahrtanzeige) |
| **F4** | Allg. Modulaufruf (Sendungsübersicht) / **Vorgabemodul** (Sendungserfassung) |
| **F5** | Suchen |
| **F6** | Anzeigen |
| **F7** | Ändern |
| **F8** | Neu |
| **F9** | Storniert *(im Modulkontext: Startbedingung/Version-Detail)* |
| **F10** | Fertig |
| **F11** | Statuswechsel |
| **F12** | Speichern |
| **ESC** | Abbrechen |

> Hinweis: **[F2]** und **[F9]** sind kontextabhängig doppelt belegt (siehe Klammern).

## Steuerungs-Shortcuts (Strg)

| Shortcut | Funktion |
|---|---|
| **Strg + B** | Weiterspringen von Adresszeile in Sendungsbeschreibung (Sendungserfassung) |
| **Strg + E** | LA-Erfassen |
| **Strg + I** | Neue Zeile *(auch: Format übernehmen im Moduleditor)* |
| **Strg + U** | LA-Übersicht |
| **Strg + Y** | Gefahrgut (Sendungserfassung) |
| **Strg + <** | Ebene tiefer (Sendungserfassung) |
| **Strg + Shift + <** | Ebene höher (Sendungserfassung) |
| **Strg + Alt + C** | Sendung kopieren |
| **Strg + M** | Moduleditor öffnen (aus Versionen-Maske) |
| **Alt + G** | Feld „Gehe zu Zeile“ selektieren (Moduleditor) |

## Datumseingabe (Kurzschreibweisen)

| Eingabe | Ergebnis |
|---|---|
| **„ “ (Leerzeichen/SPACE)** | aktuelles Datum wird übernommen |
| **„14“** | Tag 14 im aktuellen Monat/Jahr |
| **„+2“** | aktuelles Datum **+ 2 Tage** (z. B. 05.01. → 07.01.) |
| **„-5“** | aktuelles Datum **− 5 Tage** (z. B. 05.01. → 31.12.) |

## Zeiteingabe (Kurzschreibweisen)

| Eingabe | Ergebnis |
|---|---|
| **„ 12“** | aktuelles Datum um **12:00** ergänzt |
| **„28 17“** | Tag 28 (akt. Monat/Jahr) + **17:00** |
| **„-1 10“** | aktuelles Datum **− 1 Tag** + **10:00** |

## Suche mit Platzhaltern

- **`%`** = Platzhalter (mehrere Zeichen), vor und/oder nach dem Wort einsetzbar.
  - `%Spedition%` (Feld Name) → alle Datensätze, die **„Spedition“ enthalten**.
  - `%Sped` → alle, die **mit „Sped“ enden**; `Sped%` → die **mit „Sped“ beginnen**.
- **`?`** = Platzhalter für **genau ein** beliebiges Zeichen (v. a. in Modul-Parametern / PLZ).
- **Bereich `:`** (in Parametern) = von–bis, z. B. `6800:6900`.
- **Aufzählung `;`** (in Parametern) = mehrere Werte, z. B. `6800;6850;6900`.
- Laufende **Abfrage unterbrechen**: Klick auf das **rote „X“**.

## Druckerfilter (LA drucken)

1. Zu druckende **LA** auswählen → **[LA ausführen]** → Druckdialog.
2. Druckdialog einstellen.
3. **[Filter]** → Filtermaske.
4. **Filterbezeichnung** eingeben; bei **Benutzer** sich selbst wählen → Filter **nur für
   dich** sichtbar. *(Ohne Benutzer gilt der Filter für die gesamte Arbeitsgruppe!)*
5. Anzeige-Verhalten wählen: **manuell** (im Dropdown), **Überspringen** (Dialog erscheint
   nicht mehr – **wird abgeraten**), **Vorschlag** (Filter wird vorgeschlagen).
6. Mit **[OK]** bestätigen & speichern. Bestehende Filter über Auswahl in Schritt 2 anpassen.

## Filter für Auswahl (Beispiel Disposition)

Für **jedes Auswahlfenster** anwendbar:
1. In den **Dispotopf** klicken → **[Auswahl]**.
2. In der **LA-Auswahl** Kriterien erfassen. Beispiel: nur Sendungen mit Empfänger in AT →
   Feld **Typ** = `EMPF` (sonst ins Feld klicken + **[F5]**), Feld **Staat** = `AT`.
   Weitere Kriterien beliebig ergänzbar.
3. **[Filter verwalten]** → Filterbezeichnung ergänzen → **Benutzer: eigenen Namen wählen**
   → Ausführungsart wählen → **[OK]**.
