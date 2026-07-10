# 01 – Grundlagen: Logikinterpreter-System (LIS)

← [Index](00_INDEX.md) · verwandt: [02 Bibliotheken](02_bibliotheken-kriteriumssaetze.md) · [03 Module](03_module-startbedingung-version.md)

## Was ist das LIS?

Das **Logikinterpreter-System (LIS)** steuert den **Workflow (Arbeitsfluss)** in lBase
über **Module**, geschrieben in einer eigenen Programmiersprache mit **Formaten,
Variablen und Schleifen**.

**Zweck:** größtmögliche Flexibilität in allen Bereichen des Speditionssystems
(Sendungserfassung, Abrechnung, Vor-/Nachkalkulation …). Es gibt dem Endanwender ein
einfach handhabbares Werkzeug zur „Programmierung“ von **Sonderfallbehandlungen**.

## Die 3 Elemente des LIS

| Element | Kürzel | Aufgabe |
|---------|--------|---------|
| **Moduleditor** | ME | Erstellung der Programme (Module) auf einfache Weise; zentrale Schnittstelle Anwender ↔ LIS; transparenter Zugriff auf alle DB-Felder |
| **Compiler** | — | Wandelt das im ME erstellte Programm in **Bytecode** um |
| **Logikinterpreter** | LI | Kern des Systems: nimmt Anweisungen (aus Modulen) entgegen, **bewertet** und **führt** sie aus |

Der **LI ist ein eigenständiges Programm**, das entweder bereits läuft oder von der
Trägerapplikation bei Bedarf gestartet wird.

## Trägerapplikationen

Der LI läuft „unter“ einer Trägerapplikation:

- **lBase TMS** – Transport Management System (Speditions-/Transportabwicklung)
- **lBase WMS** – Warehouse Management System (Lager)

Ein **Modul** ist der logische Überbegriff für die gesamte Struktur aus
**Startbedingungen, Versionen und dem eigentlichen Modul (Programm)**. Module dienen der
**Automatisierung bzw. Halbautomatisierung** wiederkehrender Abläufe.

## Grundsätzlicher Ablauf (am Beispiel Sendungsvorgabemodul)

1. In der **Sendungserfassungsmaske** wird z. B. ein Absender (Adress-ID) eingegeben.
2. Aufruf des Vorgabemoduls über **Ribbon → Bearbeitung → Vorgabemodul** oder Taste **[F4]**.
3. Das LIS sucht in **Bibliothek / Kriteriumssatz** anhand von **Startbedingungen und
   Versionen** nach passenden Modulen.
4. **Modul gefunden** → der Client schreibt die Daten in die Maske (z. B. Empfänger,
   Beschreibung, Frankatur, Übernahmecode, Termine, Datum).
5. **Kein Modul gefunden** → die Maske bleibt (bis auf die vorher eingegebenen Daten) leer.

> Merksatz: **Aufruf → Bibliothek → Kriteriumssatz → Modul → Startbedingung → Version → Modulcode.**
> Details zu jeder Stufe in [02] und [03].

## Testen & Fehlersuche

- **LI-Testmodus**: `OPTIONEN → Logikinterpreter → LI-Testmodus`. Beim Modulablauf
  erscheint ein Fenster mit **detaillierten technischen Hintergrundinformationen** –
  hilfreich zur Kontrolle/Fehlersuche eines Modulablaufs.
- **LI-Debug in Datei**: leitet dieselben Hintergrundinfos in eine **Datei** um (für
  Entwicklung & Support, tiefergehende Fehlersuche).
- **Kompilieren „mit Zeilennummer“**: hinterlegt Zeilennummern im Bytecode → erleichtert
  die Fehlersuche im LI-Debug (siehe [03]).

## Import / Export von Modulen

- **Import**: `Ribbon MODUL → BEARBEITUNG → Moduleditor → Von Datei einfügen`.
- **Export**: alle Modulzeilen markieren → `Moduleditor → in Datei kopieren` → Dateinamen
  vergeben → Speichern.

## Online-Hilfe

Mit **[F1]** wird die lBase-Online-Hilfe **immer für die aktuell geöffnete Maske**
aufgerufen. Dort finden sich weitere Erklärungen zu Masken, Begriffen, Funktionen und
eine **vollständige Liste aller Formate** (das Handbuch nennt nur die wichtigsten).
