# 03 – Modul-Aufbau: Startbedingung, Version, Moduleditor

← [Index](00_INDEX.md) · verwandt: [02 Bibliotheken](02_bibliotheken-kriteriumssaetze.md) · [04 Formate](04_formate-referenz.md)

Ein **Modul** = Struktur aus **Modul-Kopf** → **Startbedingung(en)** → **Version(en)** →
**Modulcode** (Formate). Reihenfolge des Anlegens spiegelt genau diese Hierarchie.

## 1. Modul anlegen

**Aufruf:** `Stammdaten → Administration → Verwaltungstools → Module`.

- In der **Auswahlmaske** werden die **Kriterien** definiert, auf die sich der LI beziehen
  soll. Je nach Bibliothek + Kriteriumssatz zeigt die Tabelle „Kriterium / Wert /
  Bezeichnung“ die relevanten Kriterien (teils vorgegeben).
- Mit **[OK]/[F12]** werden passende (bzw. auf den Wert eingeschränkte) Module angezeigt.
  Gibt es noch keine → leere Übersicht.
- **[Neu]/[F8]** legt ein neues Modul an. Der gewählte **Kriteriumssatz wird
  automatisch vorbefüllt**.
- **Bezeichnung** (Name) frei wählbar – **sprechend wählen** (soll Inhalt andeuten).
- Speichern mit **[Speichern]/[F12]**.

## 2. Startbedingung

Eine **Startbedingung ist selbst ein Mini-Modul**, das entscheidet, **ob** das zugeordnete
Modul (in seiner gültigen Version) durchgeführt wird.

- Aufruf: passende Zeile in der Übersicht markieren →
  **[Startbedingung / Version (Detail)]** bzw. **[F9]** → „Startbedingungen/Versionen Übersicht“.

  > ⚠️ Wichtig: die **korrekte Zeile** markieren, bevor man Startbedingung/Version aufruft.

- **Checkbox „Modul“** öffnet den Moduleditor; dort werden Einschränkungen definiert.
- Innerhalb der Entscheidung wird in einem Zweig das **Format Start**, im anderen das
  **Format Stopp** verwendet:
  - **Format Start** → Startbedingung trifft zu → **Modul wird ausgeführt**.
  - **Format Stopp** → trifft nicht zu → **nächste Startbedingung** wird geprüft.

### Bedingungslose Startbedingung (wichtig!)
Zusätzlich zu den bedingten Startbedingungen wird meist eine **„bedingungslose“
Startbedingung** mit gültiger Version hinterlegt, um zu **garantieren, dass ein Modul
ausgeführt werden kann**. Fehlt sie, kann eine **Fehlermeldung den Vorgang im TMS
blockieren**. Das System legt beim Anlegen bereits eine bedingungslose Startbedingung an.

### Priorität der Startbedingungen
Ist eine Startbedingung so definiert, dass die **Stopp**-Bedingung zutreffen kann, muss es
eine **weitere** Startbedingung mit ausführbarer Version geben. Reihenfolge der Prüfung =
**Priorität**:
- Startbedingung mit **Priorität 1** wird zuerst geprüft.
- Trifft sie zu → Version wird ausgeführt → **LIS ist mit dem Modul fertig** (Rest wird
  nicht mehr geprüft).

Speichern mit **[F12]/[Speichern]**.

## 3. Version

Module unterliegen ständigem Wandel → daher **Versionen**.

- Ein Modul kann **mehrere Versionen** haben; sie unterscheiden sich im **Gültigkeitszeitraum**.
- **[Neue Version]** anlegen: die zuletzt gültige, nicht gesperrte Version wird in die neue
  **kopiert**.
- **Gültigkeitszeiträume dürfen sich NICHT überschneiden.**
- Defaults bei erster Version: „Gültig von“ = **01.01.1900**, „Gültig bis“ = **31.12.2099**
  (wenn ohne Datum gespeichert).

> ⚠️ **Merksatz:** Jede Änderung/Erweiterung an einem Modul → **vorher immer eine neue
> Version** mit passendem Zeitraum anlegen (nie die alte, evtl. produktive Version direkt
> überschreiben).

- Nach dem Speichern ist **„Kompiliere mit Zeilennummer“** automatisch aktiv → Zeilennummern
  im Bytecode → erleichtert Fehlersuche im LI-Debug.
- **Moduleditor öffnen:** Button **[Moduleditor öffnen]** oder **[STRG+M]**.

## 4. Der Moduleditor (ME)

Zentrales Werkzeug für Erweiterungen/Anpassungen. Aufbau:

| Bereich | Beschreibung |
|---|---|
| 1 | **Programmzeilen** mit Zeilennummerierung (Spalte „Line“) |
| 2 | **Liste der Formate** (Befehle) |
| 3 | **Parameter** zum gewählten Format |

- **„Gehe zu Zeile“**: Zeilennummer eingeben → Cursor springt hin. **[Alt+G]** selektiert
  das Feld. Eingabe **-1** springt ans **Modulende** (und trägt die Zeilennummer ein).
- **Kompilieren** direkt im ME möglich (ohne ihn zu verlassen). Bei Fehlern öffnet sich
  automatisch das `fglctest`-Ergebnis im Dialog **„Debugfiles anzeigen“**.
- **„Zeige 3GL-Code“**: öffnet „Debugfiles Anzeigen“ mit Modul-ID-Register; automatische
  Synchronisation 4GL ↔ 3GL (markierte Zeile bleibt synchron). Alle `#include`-Zeilen
  werden **aufgelöst** → tatsächlich ausführbarer Code einsehbar. Nach Neukompilieren wird
  der zugehörige 3GL-Code in der DB gespeichert (letzter Stand der Kompilation).

### Formate im ME anwenden
- Format aus der Liste wählen → mit **Tab** bestätigen → Felder mit Variablen/Zahlen/Text
  füllen (viele Felder mit Suchfunktion **[F5]**).
- Übernahme in den Anzeigebereich: **[Übernehmen]**, Icon oder **[Strg+I]**.
- Pro Modul wird definiert, **welche Formate in welcher Reihenfolge** verfügbar sind.

→ Alle wichtigen Formate im Detail: [04 Formate-Referenz](04_formate-referenz.md).
