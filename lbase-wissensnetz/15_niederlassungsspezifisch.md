# 15 – Niederlassungsspezifische Abläufe

← [Index](00_INDEX.md) · verwandt: [13 LIM & GenTabs](13_lim-und-gentabs.md) · [14 LOGBATT Abrechnung](14_logbatt-abrechnung-konditionen.md)

Quelle: **Handbuch LMX 21st – Niederlassungsspezifisches Kapitel** (26 S.). Enthält
Spezialabläufe je Standort: **Plochingen (LOGBATT)**, **SENEC (Zoll)**, **Salzburg-Timesped**.

## Plochingen (LOGBATT) – Batterie-/Entsorgungserfassung

### Hebevorrichtung auf Adressebene hinterlegen (S.4–5)
1. Adresse suchen → Modul **„ADRESSE ÄNDERN“** ausführen.
2. **Zusatzattribut** ändern → neues Attribut setzen → Attribut suchen/bestätigen →
   Wert **`Y`** (= YES).
> Im LIM wird nach Adresseingabe geprüft, ob **Absender oder Empfänger einen Stapler** haben →
> es erscheint ein extra **Stapler-Fenster** (Default `N`).

### Sendungserfassung PLO National (S.6–13)
**Support-Module:** `LIM LOGBATT = LPD-157822` · `LMX_SBOX_PRUEF = LPD-157826`
(über 80000001 aus LPD-157730).

**Ablauf:** **keinen Verkehr vorgeben**, direkt in **LIM** und **LOGBATT** auswählen; der Rest
läuft über **`LMX_STD_VAR`**. Mit **TAB** Feld verlassen → LIM startet.
- **JA** = nur **eine** Batterie wird erfasst.
- **NEIN** = **mehrere** Positionen werden erfasst.

**Workflow JA (S.7–10):**
- Über **[F5]** auf die **Batteriedatenbank** zugreifen (oder Variante wählen) → Hauptdialog.
- Adresse **AUFG** z. B. `SON` + [F5] → Sonnen Batterie GmbH; übrige Felder nach Checkliste,
  mit **OK** bestätigen. Bei **ENTSORGUNG** den **Empfänger leer lassen**.
- Bei **ENTSORGUNG** erscheinen zwei weitere Dialoge 🖼:
  - **Mehrfelddialog „Abfalldetails“**: **Abfallerzeugernummer\*** · **AVV-Nummer**
    (z. B. `160121` → „16 01 21“) · **Abfall Papiere** (z. B. `EN` = Einzelentsorgungsnachweis).
  - **LI-Auswahl „Entsorgungsfahrt – Entsorger auswählen“** (Nickelhütte Aue, Duesenfeld,
    Primobius, ROTH, ERLOS, Rekular, Li-Cycle …).
- Ergebnis-Maske trägt Attribute (z. B. `X_ENTSORGUNG = ENTSORGUNG`, `Gefahrgutrechtliche =
  BTSN`, `Zellchemie = LFP`, `Zellformat = POUCH`, `AVV-Nummer`, `Nennenergie kWh`, km).
- **Gewichte:** `1830 = TARA Box + 330 Batterie`; `330 = Batterie + TARA Hebevorrichtung`
  (falls verwendet).

**Workflow NEIN (S.11–13):** zuerst **verkürzte Kopfdaten**, dann Zeilenerfassung (nach
Batterieauswahl nur verkürzter Dialog). **Boxen** werden hier **nicht** automatisch ermittelt →
vor dem Speichern **manuell vorgeben** (eine nach der anderen; `*` = Platzhalter für echte Box).
> Hinweis: [F5] zeigt hier noch **alle** Einträge (nicht nur Boxen) → Kürzel eingeben oder suchen.

### Verträge / Rahmenverträge anlegen (S.14–15)
Neue RV über **Allgemeinen Modulaufruf** → in der Modulliste mit **[F5]** nach „Vertrag“
suchen → Vertragsart wählen → OK. *(Fachliche RV-Konditionslogik → [14].)*

## SENEC – Sendungsfreigabe nach Zollabwicklung (S.17–22)

**Ausgangslage:** Sendungen mit Zollbehandlungscode **`ZO` (Zollgut)** oder **`SV`
(Selbstverzoller)** werden für Bearbeitung/Disposition **gesperrt** (Sperrgrund = **ZOLL**,
Kennz.1/2). LA-Status bleibt „angefordert“, **manuell nicht änderbar**. Freigabe nur durch
**Zoll-AG**.
- **ZO:** Freigabe nach durchgeführter Zollabfertigung.
- **SV:** wenn direkt vom EMPF (oder Beauftragtem) verzollt wird.

**Suche gesperrter Sendungen:** Filter **„gesperrte Sendungen Zoll“** in der LA-Auswahlmaske.

**Freigabe:** Sendung markieren → **„Gesperrte Sendung freigeben“** (auch über Modulaufruf) →
Dialogfolge:
- **A. „Freigabe mit Abrechnung“** – Anzahl der Verzollungsleistungen abfragen → Preis gemäß
  Preisliste der **Kondition** (wenn LMX-Zollabteilung abfertigt). Zollbelegnummern/Texte →
  in SO/XP-LA. Varianten: (1) Transport zum ZA + Direktlieferung (**TR/ZO** + **TR/ZU**, beide
  auf Fahrt disponieren), (2) Transport zum ZA mit Rücktransport (TR/ZU nach Rückscannen
  freigeben), (3) Direktzustellung ohne ZA-Fahrt. ZA/Agent (alphabetisch nach Städten;
  `ZA` = Zollamt, `AG` = Zollagent) wird als **ZOLL-Adresstyp** automatisch hinzugefügt.
- **B. „Freigabe ohne Rechnung“** – Zollleistungen werden nicht berechnet (EMPF/Beauftragter
  verzollt). Ablauf bis Punkt C analog A.
- **C. Positionserfassung (S.21):** SPC-Abfragen im **Mehrfelddialog** – Konfiguration über
  **GenTab `LMX_ZOLLDIEN`** (je ORGID/Sprache). Nach Speichern: Werte in Attribute; interne
  Abrechnung nach Tarifen der Kondition; LA-Status automatisch **„fertig zum Drucken“**.

**Verrechnung (S.22):** Sendungskosten → KST des Sendungseigentümers (Tagespreis/DBI);
Fahrteinnahmen → monatliche interne Fahrt + KST Zollabteilung (DBI).

## Salzburg – Timesped: Sendungserfassung LIM (S.24–26)

- **LIM Blanko** – für Einzelkunden; Standard **1 LDG 24.000 kg**.
- **LIM Bestandskunden** – greift auf **GenTab `LMX_SDG_LIM`** (Standard je Kunde). Für
  Neuanlagen sind **nur Arbeitsgruppe und Auftraggeber Pflichtfelder**; es müssen nur
  **AG_ID und ADRIDs** eingegeben werden (Bezeichnungen holt sich die Tabelle per **Cronjob**).
- Ablauf: LIM starten → Auftraggeber abgefragt. Eine Zeile → Infos aus GenTab übernommen;
  mehrere Zeilen (verschiedene Absender/Empfänger) → Kombination wählen. Danach Abhol-/
  Zustelldatum + optionale Textadresse → Maske füllt sich → mit **„FERTIG“** speichern.
