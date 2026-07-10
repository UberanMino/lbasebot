# lBase Wissensnetz – Index & Concept-Map

> Zentrale Wissensbasis für **lBase** (Logistik-/Speditionssoftware, eingesetzt bei
> **LogBATT**, betrieben mit **Lagermax** als Dienstleister, Software von **Axians / lBase**).
> Ziel: Fragen zu lBase möglichst präzise beantworten – vom Logikinterpreter über
> Bedienung bis zu den konkreten LogBATT-Prozessen.

Dieses Netz ist **modular und erweiterbar**. Weitere Dokumente (u. a. Schulungs­transkripte)
werden in die passenden Bausteine eingearbeitet und in `quellen/` protokolliert.

---

## Bausteine (thematische Knoten)

| # | Datei | Inhalt | Herkunft |
|---|-------|--------|----------|
| 00 | `00_INDEX.md` | Dieser Index, Concept-Map, Glossar-Einstieg | — |
| 01 | `01_grundlagen-lis.md` | Logikinterpreter-System (LIS): LI, ME, Compiler; Ablauf | T102 Handbuch |
| 02 | `02_bibliotheken-kriteriumssaetze.md` | Bibliotheken, Kriteriumssätze, fixe/alternative Suche | T102 Handbuch |
| 03 | `03_module-startbedingung-version.md` | Modul-Aufbau: Startbedingung, Version, Priorität, Moduleditor | T102 Handbuch |
| 04 | `04_formate-referenz.md` | Referenz aller wichtigen Formate (Befehle) im Moduleditor | T102 Handbuch |
| 05 | `05_variablen-referenz.md` | Variablen, Hilfsvariablen, lokale/globale Deklaration, Feld-Mapping | T102 Handbuch |
| 06 | `06_bedienung-shortcuts.md` | Funktionstasten, Shortcuts, Datums-/Zeiteingabe, Suche, Filter | Basis-Schulung |
| 07 | `07_prozesse-logbatt.md` | End-to-End-Prozess LogBATT (Stammdaten → Angebot → Dispo → Abrechnung) | Prozessdoku L3 |
| 08 | `08_digitaler-sendungsakt.md` | E-Akt / EPaper: Dokumente auf Sendung/Fahrt/Adresse archivieren | Handout Sendungsakt + Basis-Schulung |
| 09 | `09_uebungen-rezepte.md` | Praxis-Rezepte aus den Übungen (Vorgabe, Prüfung, Abrechnung, Fahrten) | T102 Übungen |
| 10 | `10_glossar.md` | Glossar aller Begriffe & Abkürzungen | alle |
| — | `quellen/README.md` | Quellenverzeichnis + Änderungsprotokoll | — |

---

## Concept-Map: Wie hängt alles zusammen?

```
                          ┌─────────────────────────────┐
                          │  Trägerapplikation (lbase)  │
                          │  TMS (Transport) / WMS (Lager)│
                          └──────────────┬──────────────┘
                                         │ Tätigkeit löst Aufruf aus
                                         │ (z. B. Sendung speichern, F4-Vorgabe,
                                         │  Abrechnungs-LA, Fahrtstatuswechsel)
                                         ▼
     ┌──────────────────────  LOGIKINTERPRETER-SYSTEM (LIS)  ──────────────────────┐
     │                                                                             │
     │   [01] LIS = Moduleditor (ME) + Compiler + Logikinterpreter (LI)            │
     │                                                                             │
     │   Aufruf →  [02] BIBLIOTHEK  (je Tätigkeit eine, z. B. "Sendungs-Vorgabe")   │
     │                    │                                                        │
     │                    ▼                                                        │
     │             [02] KRITERIUMSSATZ  (grenzt Modulsuche ein; Eckdaten-Vergleich)│
     │                    │  Suchart: FIX (alle passenden laufen) /                │
     │                    │           ALTERNATIV (erster Treffer gewinnt)          │
     │                    ▼                                                        │
     │             [03] MODUL  (Name + Kriteriumssatz-Werte)                        │
     │                    │                                                        │
     │                    ▼                                                        │
     │             [03] STARTBEDINGUNG  (eigenes Mini-Modul: Start/Stopp;          │
     │                    │              Priorität; "bedingungslose" als Fallback)  │
     │                    ▼                                                        │
     │             [03] VERSION  (Gültigkeitszeitraum, darf sich nicht überschneiden)│
     │                    │                                                        │
     │                    ▼                                                        │
     │             [03] MODULCODE im Moduleditor  =  Folge von                     │
     │                    [04] FORMATEN  (Entscheidung, Schleife, Vorgabe Feld,    │
     │                                     Berechnungselement, Spesencode, MFD …)   │
     │                    verarbeitet  [05] VARIABLEN  (#FRANK, #WERT, #ZUFELD…)    │
     │                    │                                                        │
     │                    ▼                                                        │
     │             ERGEBNIS: Maske befüllt / Sendung geprüft / RESULTATSET         │
     │                       (Abrechnung) / LA generiert / Fahrt angelegt          │
     └─────────────────────────────────────────────────────────────────────────────┘

  Anwendungsfälle (Bibliotheken):   [09] Übungs-Rezepte zeigen konkrete Umsetzung
    • Sendungs-Vorgabe (F4)          → Maske vorbefüllen
    • Sendungs-Prüfmodule            → beim Speichern prüfen/korrigieren/abbrechen
    • Abrechnung                     → Beträge berechnen (Spesencode + Berechnungselement/Matrix)
    • Fahrten / Fahrten-Statuswechsel→ wiederkehrende Fahrten & Sendungen automatisiert
    • Allgemeine Module              → wiederverwendbare Bausteine (#P1..#P6)

  Drumherum (Bedienung & Prozess):
    • [06] Funktionstasten/Shortcuts/Filter  – tägliche Bedienung
    • [07] LogBATT-Gesamtprozess             – wo im Geschäftsablauf das passiert
    • [08] Digitaler Sendungsakt (E-Akt)     – Belege/PODs zur Sendung archivieren (LA "Archiv Dokument")
```

---

## Schnell-Einstieg nach Frage-Typ

- **„Wie funktioniert der Logikinterpreter grundsätzlich?“** → [01](01_grundlagen-lis.md)
- **„Warum wird mein Modul (nicht) ausgeführt?“** → [02](02_bibliotheken-kriteriumssaetze.md) (Suche/Priorität) + [03](03_module-startbedingung-version.md) (Startbedingung/Version/Gültigkeit)
- **„Welches Format/welcher Befehl macht X?“** → [04](04_formate-referenz.md)
- **„Was bedeutet Variable #… / wie deklariere ich Variablen?“** → [05](05_variablen-referenz.md)
- **„Welche Taste/welcher Shortcut?“ / „Wie filtere/suche ich?“** → [06](06_bedienung-shortcuts.md)
- **„Wie läuft der Geschäftsprozess bei LogBATT?“** → [07](07_prozesse-logbatt.md)
- **„Wie speichere ich einen POD/Beleg zur Sendung?“** → [08](08_digitaler-sendungsakt.md)
- **„Wie baue ich konkret ein Vorgabe-/Prüf-/Abrechnungs-/Fahrtmodul?“** → [09](09_uebungen-rezepte.md)
- **„Was heißt Abkürzung X?“** → [10](10_glossar.md)

---

## Wichtige Kernaussagen (Merksätze)

1. **Ein Modul wird nie „nackt“ ausgeführt** – es braucht immer eine passende
   Bibliothek → Kriteriumssatz → Startbedingung → gültige Version. Fehlt eine
   gültige Version/Startbedingung, kann eine Fehlermeldung den Vorgang im TMS
   **blockieren**. Deshalb fast immer eine **„bedingungslose“ Startbedingung** als Fallback. → [03]
2. **Fixe Suche**: alle passenden Module laufen (spätere können frühere Werte
   überschreiben). **Alternative Suche**: nur der erste passende Kriteriumssatz gewinnt. → [02]
3. **Jede Änderung an einem Modul = neue Version** mit eigenem, nicht
   überschneidendem Gültigkeitszeitraum. → [03]
4. **Variablen beginnen mit `#` und werden GROSS geschrieben.** Hilfsvariablen
   (`#ZUFELD…`, `#AKKU…`) sind **global** und müssen vor Nutzung initialisiert werden. → [05]
5. **Abrechnung** basiert vollständig auf dem LI; Ergebnis ist das **Resultatset**.
   Ohne **Spesencodemodul** kann nicht abgerechnet werden. → [04]/[09]
6. **Fehlersuche**: `LI-Testmodus` (Fenster mit Hintergrundinfos) bzw. `LI-Debug in Datei`.
   Beim Kompilieren „mit Zeilennummer“ für nachvollziehbares Debugging. → [01]/[03]

---

## Offene Punkte / To-Do (wird mit neuen Quellen gefüllt)

- Schulungs-Transkripte einarbeiten (folgen laut Nutzer).
- Konkrete lBase-Fehlermeldungen + Ursachen/Abhilfe sammeln (Troubleshooting-Knoten).
- Screenshots/Masken werden in den PDFs referenziert, liegen hier aber nur als Text vor.
