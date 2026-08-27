# Quellenverzeichnis & Änderungsprotokoll

Dieses Wissensnetz wird aus vom Nutzer bereitgestellten Handbüchern, Anleitungen und
(künftig) Schulungstranskripten aufgebaut. Hier werden alle eingearbeiteten Quellen
dokumentiert, damit Aussagen nachvollziehbar bleiben.

## Eingearbeitete Quellen (Stand: 2026-07-10)

| Kürzel | Dokument | Typ (physische Seiten) | Eingearbeitet in |
|---|---|---|---|
| **T102-HB** | T102 – Logikinterpreter (Handbuch für Anfänger/Administratoren), Axians ICT Austria 2024 | PDF, **76 S.** (gedr. -1- bis -71-, Offset +5) | [01], [02], [03], [04], [05], [11] |
| **T102-UE** | T102 – Logikinterpreter, Übungen | PDF, **51 S.** (gedr. -1- bis -48-, Offset +3) | [09], [11] (+ Kriteriumssatz-Prioritäten in [02]) |
| **PROZ-L3** | L3 – LBase Prozessdokumentation V1 (Stand 04.12.2024) | DOCX (Tabelle) | [07] |
| **SAKT** | Handout zum Start Pilotprojekt „Digitaler Sendungsakt“ (Maik Großhäuser, LMX Salzburg) | PDF, 4 S. | [08], [11] |
| **BASIS** | Unterlagen für User nach Basis-Schulung (Tipps & Tricks, Funktionstasten, Filter, Beschlagwortung) | PDF, 8 S. | [06], [08], [11] |

> **Hinweis zur PDF-Verarbeitung:** PDFs werden zur Analyse mit **PyMuPDF** seitenweise zu
> Bildern gerendert und **visuell** ausgewertet (Screenshots/Masken/Modulcode) – nicht nur
> als Text. Dadurch sind **seitengenaue Referenzen inkl. Abbildungshinweisen** möglich (→ [11]).
> Die anfangs vom System gemeldeten Seitenzahlen (46/13) waren zu niedrig; maßgeblich sind
> die oben genannten **physischen Seitenzahlen**.

## Eingearbeitete Quellen – Batch 2 (Stand: 2026-07-10)

| Kürzel | Dokument | Typ | Eingearbeitet in |
|---|---|---|---|
| **KONDITION** | KONDITION_LOGBATT – Beschreibung & Anpassungen | DOCX (Text + Tabelle X_LOGB_BER) | [14], [13] |
| **21ST-ALLG** | Handbuch LMX 21st – Allgemein (Version 4, Stand 09/2024) | PDF, 56 S. (Seite = PDF-Seite) | [12], [13], [11] |
| **21ST-NL** | Handbuch LMX 21st – Niederlassungsspezifisch | PDF, 26 S. | [15], [13], [11] |
| **EINF** | LBase_Einführung | PPTX, 27 Folien | [16], [11] |
| **PROZ-1.7** | LBase_Prozessschritte v1.7 (Stand 12.11.2025) | PPTX, 68 Folien | [16], [11] |

> **Format-Handling Batch 2:** PPTX werden mit **python-pptx** ausgelesen (Text + Tabellen +
> Notizen) und per **Foliennummer** referenziert; PDF-Seiten der 21st-Handbücher entsprechen
> 1:1 der gedruckten „Seite N von …“. Wichtige Screenshots visuell geprüft (u. a.
> 21st-Sendungsmaske, LOGBATT-Entsorgungsdialoge).
>
> **Generationen-Hinweis:** Batch 1 = generischer **T102-Logikinterpreter** + älterer Prozess
> (2024). Batch 2 = **LBase 21st** (real eingesetztes System) + konkrete **LOGBATT-Abrechnung**
> + aktueller Prozess (2025). Bei Prozess-Widersprüchen gilt der neuere Stand ([16] vor [07]).

## Eingearbeitete Quellen – Batch 3 (Stand: 2026-07-10)

| Kürzel | Dokument | Typ | Eingearbeitet in |
|---|---|---|---|
| **EINF-PDF** | LBase_Einführung (PDF-Fassung der PPTX) | PDF, 27 S. (Seite = Folie) | bereits in [16]; jetzt visuell referenzierbar ([11]) |
| **PROZ-1.7-PDF** | LBase_Prozessschritte v1.7 (PDF-Fassung der PPTX) | PDF, 68 S. (Seite = Folie) | bereits in [16]; jetzt visuell referenzierbar ([11]) |
| **ST-IOD** | 20250129_Sendungsstatus_IOD | DOCX | [17] |
| **ST-POD** | 20250327_Sendungsstatus_POD | PDF, 4 S. | [17] |
| **COMPL** | Anleitung Compliance-Prüfung LBase (Felix Bauer) | PDF, 3 S. | [17] |

> **Hinweis:** Die beiden Präsentationen wurden vom Nutzer als **PDF** nachgereicht → Folien
> jetzt **renderbar** (PDF-Seite = Foliennummer); Kerninhalte waren bereits über die PPTX in
> [16]. ST-IOD und ST-POD sind inhaltsgleich (dieselbe „Status setzen“-Anleitung, unterschiedl.
> Datum/Format) und gemeinsam in [17] abgebildet.

## Eingearbeitete Quellen – Batch 4 (Stand: 2026-08-27)

| Kürzel | Dokument | Typ | Eingearbeitet in |
|---|---|---|---|
| **CASE-ORA20994** | Realer Support-Fall: lBase-Fehlerprotokoll (XML aus „SQL-Fehler → Details“) + Nutzer-Schilderung; Fahrt PLO-1989 lässt sich nicht auf „in Kontrolle“ setzen | Fehlerprotokoll (XML) + Chat | **[18]** (neu), Querverweis in [12], Glossar [10], Index [00] |

> **Kontext des Falls:** `ORA-20994: get_kurs(, EUR, EUR, 2026.08.26, Y, 0) IS NULL` in
> `SPED.SBEL_KURS_UPDATE` beim FSW „in Kontrolle“ → **fehlende/nicht zugeordnete Kurstabelle**
> (`skut_t_kurstab` / `fir_kutid`). Begleitsymptom: Status-Inkonsistenz Fahrt („auf Fahrt“) ↔
> Sammelübersicht („in Kontrolle“). Fix ist **Stammdaten (IT/Key-User/FiBu)**, nicht Dispo.
> Damit ist der To-Do-Punkt „Troubleshooting-Knoten“ aus [00] eröffnet ([18]).

## Noch offen / angekündigt

- **Schulungstranskripte** (folgen laut Nutzer) → werden thematisch in [01]–[16]
  eingearbeitet und hier ergänzt.
- Weitere Handbücher/Anleitungen (Nutzer sendet in Blöcken zu je 5 Dokumenten).
- **PPTX-Screenshots**: Folien-Bilder konnten in dieser Umgebung nicht gerendert werden
  (LibreOffice-Profilproblem); Inhalte liegen als extrahierter Text/Foliennummer vor.

## Konventionen für die Einarbeitung neuer Quellen

1. Neues Dokument einem oder mehreren Bausteinen [01]–[10] zuordnen (oder neuen Baustein
   anlegen und im `00_INDEX.md` verlinken).
2. Fakten in Tabellen/Merksätze überführen; **wörtliche Codierungen/Werte exakt** übernehmen.
3. Querverweise (`→ [nn]`) setzen, damit das Netz zusammenhängt.
4. Diese Tabelle + „Stand“-Datum aktualisieren.
5. Bei Widersprüchen zwischen Quellen: beide Stände notieren und Datum/Quelle nennen.

## Ablage der Originaldateien

Die Originaldokumente wurden als Chat-Uploads bereitgestellt und liegen **nicht** im Repo.
Bei Bedarf können extrahierte Textfassungen hier abgelegt werden.
