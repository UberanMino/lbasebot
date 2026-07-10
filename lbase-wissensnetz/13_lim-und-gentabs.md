# 13 – LIM (geführte Sendungserfassung) & GenTabs

← [Index](00_INDEX.md) · verwandt: [12 LBase 21st](12_lbase-21st-bedienung.md) · [14 LOGBATT Abrechnung](14_logbatt-abrechnung-konditionen.md) · [02 Bibliotheken](02_bibliotheken-kriteriumssaetze.md)

Quellen: 21st-Handbuch Allgemein (S.19–24, 43) + Niederlassungsspezifisch + Konditions-Doku.

## Was ist ein LIM?

**LIM = Logikinterpreter-Modul**, das die **Sendungserfassung als geführten Dialog**
umsetzt. Es ist die in LBase 21st bevorzugte Art der **manuellen Sendungsanlage** – garantiert
schnelle, **einheitliche** Erfassung und einen reibungslosen Folge-Workflow.

Technisch ist ein LIM ein Modul in der LI-Welt (Bibliothek/Kriteriumssatz/Startbedingung/
Version wie in [02]/[03]); der Kriteriumssatz „**LI-Modul und Abteilung**“ wird über das Feld
**LIM** angesprochen (vgl. T102-Übungen, [09]).

## LIM aufrufen & bedienen (S.19–24)

1. In der Sendungsmaske Feld **„LIM“** → **[F5]** zeigt die möglichen Module → auswählen.
2. Mit **TAB** oder **ENTER** startet der LIM und führt per **Dialogabfrage** durch die Erfassung.
3. **Dialoge:** Pflichtfelder mit **`(*)`** markiert. Häufige Werte (>50 %) sind als
   **Vorschläge** vorbelegt (durch die IT anpassbar). **[F5]** hilft beim Befüllen. Sind alle
   `*`-Felder gefüllt → **[OK]**.

### Adressauswahl im LIM (S.21–23)
Drei Varianten (durch IT konfiguriert; gesteuert über **Zusatzattribute auf der Adresse**):
1. **Fix gesetzt** – keine Abfrage/Auswahl.
2. **Dialogauswahl** – Liste; ist die Adresse nicht dabei → **„999999 Andere Adresse
   auswählen/erfassen“** → Kundennummer eintragen oder **[F5]** suchen (Kriterien +
   „Tabelle aktualisieren“).
3. **Abfrage im Dialog** – Kundennummer direkt oder [F5].
- **Textadresse (S.23):** wenn keine Kundennummer/[F5]-Adresse → Textadresse mit `*`-Pflichtfeldern.

Nach Abschluss wird die **Sendungsmaske befüllt**; Korrekturen jederzeit möglich; Speichern
mit **„Fertig“**.

## Beispiele für LIMs (aus Niederlassungen)
- **LIM LOGBATT** (Plochingen): Batterie-/Entsorgungserfassung; JA = eine Batterie, NEIN =
  mehrere Positionen. Greift auf **Batteriedatenbank** ([F5]) und **LMX_STD_VAR** zu. → [15]
- **LIM Blanko / LIM Bestandskunden** (Salzburg-Timesped): Blanko-Standard 1 LDG 24.000 kg;
  Bestandskunden über **GenTab LMX_SDG_LIM** (Standard je Kunde). → [15]

---

## GenTabs (generische Tabellen) – das Steuerungssystem von 21st

**GenTabs** sind konfigurierbare Tabellen, über die **Key-User/IT das Systemverhalten
steuern**, ohne Modulcode zu ändern. Sie sind der zentrale „Stellhebel“ in LBase 21st.
Verwandt: **Wertebereiche** (z. B. `X_LOGB_BER`, `X_LBATT_EINH`) und **Komponenten**
(z. B. `Cust_LOGBATT`, `Cust_LMX`) für die Übertragung/Zuordnung von Objekten.

### GenTab-Register (aus den Quellen)

| GenTab | Steuert | Quelle |
|---|---|---|
| **LMX_SDG_VAR** | Sendungs-Pflichtfelder (Verkehr, TPV, Übernahme, Weiterleitung) | 21st Allg. S.24 |
| **LMX_STD_VAR** | Standard-Vorgaben Sendung (Rest nach LIM/LOGBATT-Auswahl) | 21st NL S.6 |
| **LMX_SDG_LIM** | Standard je Kunde für LIM Bestandskunden (AG_ID, ADRIDs) | 21st NL S.24 |
| **LMX_SDG_AUTO** | automatische Fahrtanlage | 21st Allg. S.28 |
| **LMX_FAR_AUTO** | automatische Sendungsanlage | 21st Allg. S.28 |
| **LMX_FAR_SDGA** | automatische Disposition | 21st Allg. S.28 |
| **LMX_FAR_DR** | Anlage der Dokumente auf der **Fahrt** | 21st Allg. S.38 |
| **LMX_SDG_DR** | Anlage der Dokumente auf der **Sendung** | 21st Allg. S.38 |
| **LMX_SOT_VKM** | Zuordnung VKM/Frächter/Handy je ORGID (Driver App) | 21st Allg. S.53 |
| **LMX_SCANNER** | Wertebereich Scanner/Handy (Driver App) | 21st Allg. S.51 |
| **LMX_ZOLLDIEN** | SPC-Abfragen (Zolldienstleistungen) im MFD je ORGID/Sprache | 21st NL S.21 |
| **LMX_LBATT_KO** | LOGBATT: welche **Berechnungs-Elemente** je Adresse/RV abgerechnet werden | Kondition |
| **LMX_LBATT_TX** | LOGBATT: **Texte/Einheiten** je Berechnungs-Element (SPC-Text) | Kondition |

> Für Key-User gilt durchgängig: **„Das kann euer Key-User in den GenTabs hinterlegen; für
> Rückfragen steht die IT bereit.“** Regeln (z. B. wann Automatik greift) im Team/mit
> Gruppenleiter abstimmen.

### Vollständiges GenTab-Verzeichnis (aus System-Export, 134 Tabellen)

Quelle: Export der GenTab-**Definition** aus MD DEV (`LBATT_KO1.xlsx`, Stand Test 2026). Zeigt
alle vorhandenen generischen Tabellen mit Bezeichnung. *(Hinweis: Dies ist die Definition/Liste,
nicht der Zeileninhalt der Tabellen.)*

**LOGBATT-spezifisch (Komponente `Cust_LOGBATT` u. a.):**
| Tabelle | Bezeichnung |
|---|---|
| `LMX_LBATT_KO` | Zu berechnende Einstellungen in Konditionen (→ [14]) |
| `LMX_LBATT_TX` | Logbatt Konditionstexte (→ [14]) |
| `LMX_LB_RV` | Rahmenverträge Logbatt |
| `LMX_LB_KV` | Kopfverträge Logbatt |
| `LMX_BATT_DB` | Batteriedatenbank LogBATT |
| `LB_SBOX_TEST` | Prüfung SafetyBATTbox Modelle |

**Compliance (→ [17]):** `LMX_COM_FREI` (User zur Freigabe der Compliance-Treffer) ·
`COM_AUSNAHME` (Compliance Ausnahmen) · `COM_BUCCHECK` / `COM_SATCHECK` (Adresstypen-Prüfung).

**Sendung/Fahrt-Steuerung:** `LMX_SDG_VAR`*→ n. i. Export, s. u.* · `LMX_STD_VAR`
(Standardvarianten) · `LMX_SDG_LIM` (GenTab für LIMs) · `LMX_SDG_AUTO` (autom. tägl. Sendungen) ·
`LMX_FAR_AUTO` (autom. Fahrtanlage) · `LMX_FAR_SDGA` (autom. Fahrt aus Sendung) ·
`LMX_SDG_DR` (Druck-LA’s Sendung) · `LMX_FAR_DR` (Ausdrucke Fahrt) · `LMX_SDG_AB`
(Abrechnungs-LA’s) · `LMX_SDG_TR` (Transport-LA’s) · `LMX_SDG_REL` (Relationsermittlung) ·
`LMX_SDG_KST` / `LMX_FAR_KST` (Kostenstellenermittlung) · `LMX_SDG_STAT` (Statusvergabe) ·
`LMX_SDG_STOP` (Sendung stoppen) · `LMX_SDG_HWT` (Hinweistexte) · `LMX_STD_VKM`
(Standardverkehrsmittel) · `LMX_SOT_VKM` (SOT aktive Fahrzeuge) · `LMX_FAR_EV` /
`LMX_EV_PLAN` (EV-Anlage/-Kalkulation) · `LMX_WF_FARID` / `LMX_WF_ADRID` / `LMX_KD_BASIS`
(Workflows) · `LMX_STAT_WF` (Statusworkflow) · `LMX_FAR_MAIL` (Mailversand bei FSW).

**Abrechnung/Steuer/Zoll:** `LMX_ABR_GGEW` (Grenzgewicht) · `LMX_ABR_SPER` (Sperrigkeit) ·
`LMX_ABR_FUEL` (Treibstoffzuschlag) · `LMX_ABR_ZUSA` (Zusatzleistungen) · `LMX_ABR_LAG`
(Lagerabrechnung) · `LMX_ABR_BTY` (Belegtyp) · `LMX_ZOLLDIEN` (Zolldienstleistungen, → [15]) ·
`KVAT_VANID`/`SVAT_VANID` (USt-Ermittlung) · `LB_STC_KBEZ` (Steuercode-Kurzbez.) ·
`SKZK_KRIT` (Konditionen Zusatzkriterien) · `SYS_KTYPVAR` (Konditionstyp-Variablen).

**Driver App / Scan:** `LMX_SOT_VKM` · `LMX_TOR_SCAN` (gültige Tore Gegenscan) · `LMX_SCAN_AUS`
(Ausnahmen Auto-Dispo) · `LBASE_APP_NA` (Driver-App-Namen) · `LMX_FAR_LAUF` (Laufzeit bei Entladescan).

> Der komplette Export mit allen 134 Definitionen liegt vor; die obigen sind die für LogBATT
> relevanten. Weitere u. a.: `LMX_MAUT_PLZ`, `LMX_FEIERTAG`, `LMX_LINIEN`, `LMX_PRODUKT`,
> `LMX_ADR_SUCH`, `LMX_EDI*`, `LMX_NV_GEB`, `LBASE_GEWPFL` (frachtpfl. Gewicht), `LBASE_EINV`.

> ⚠️ **Noch offen:** Der **Zeileninhalt** von `LMX_LBATT_KO` und `LMX_LBATT_TX` (die eigentliche
> Abrechnungs-Steuerung) ist damit **noch nicht** exportiert – dazu die **Daten** (nicht die
> Definition) ausgeben (siehe `ANLEITUNG_logik-export.md`, Abschnitt B).

## Wertebereiche & Komponenten (LOGBATT-relevant, → [14])
- **Wertebereich `X_LOGB_BER`** – Katalog aller LOGBATT-Berechnungs-Elemente (neue Elemente
  müssen hier zuerst aufgenommen werden).
- **Wertebereich `X_LBATT_EINH`** – erlaubte Einheiten für den Zusatztext.
- **Komponente `Cust_LOGBATT`** – jede neue Stammdaten-Matrix/überarbeitete GenTab/Modul muss
  hierüber zugeordnet/übertragen werden.
- **Komponente `Cust_LMX`** – z. B. Scanner-Geräte (Driver App).
