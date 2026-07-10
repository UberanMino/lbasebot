# 14 – LOGBATT Abrechnung & Konditionen

← [Index](00_INDEX.md) · verwandt: [04 Formate](04_formate-referenz.md) (Berechnungselement/Matrix/Spesencode) · [13 GenTabs](13_lim-und-gentabs.md) · [15 Niederlassungen](15_niederlassungsspezifisch.md)

Quelle: **KONDITION_LOGBATT – Beschreibung & Anpassungen** (Word). Beschreibt die **reale,
produktive Abrechnungslogik** für LogBATT – d. h. wie die generische LI-Abrechnung ([04]/[09])
konkret für LogBATT aufgebaut ist. Zielgruppe: Key-User, die Rahmenverträge (RV) pflegen.

## Gesamtablauf der LOGBATT-Abrechnung

1. **Abrechnungs-Start:** Kondition **„PLO – allgemeine Kondition“** (keine Einschränkung auf
   Adresse/Untertyp) → ruft Spesenblock **`LOGBATT_SDG_BESCH_DATEN`** auf.
2. Es wird geprüft, ob ein **Rahmenvertrag (RV)** in der Sendung gesetzt ist.
   - **Kein RV** → **Einmalangebot** wird berechnet.
   - **RV gesetzt** → abzurechnende Elemente werden aus **GenTab `LMX_LBATT_KO`** anhand von
     **RV** und **`LAF_ADRID` (Rechnungsadresse)** gelesen → Variable **`gs_lbatt_ko`**.
3. Sendungs- und Sendungsbeschreibungs-**Attribute** werden gelesen (z. B. `X_GG_EINST`).
4. **Berechnungselemente** werden je nach Attributen/RV abgearbeitet; pro Treffer wird der
   Spesenblock **`LOGBATT_BERECHNUNG_SPC`** aufgerufen.
5. Ergebnis = **Resultatset** (Rechnung). Vgl. generische Abrechnung [09-D].

## Die zentralen Module/Spesenblöcke

| Spesenblock | Aufgabe |
|---|---|
| **LOGBATT_SDG_BESCH_DATEN** | Einstieg: RV lesen / Einmalangebot-Prüfung; `LMX_LBATT_KO` lesen; Sendungs- & SDB-Attribute lesen; Berechnungselemente (Sendungs-, SDB-, Verpackungs-, Hebegestell-Ebene) auslösen. **Hier neue Elemente an der passenden Stelle einbauen** |
| **LOGBATT_BERECHNUNG_SPC** | Sucht **Matrix** anhand `gs_ber_von` (je RV+`LAF_ADRID` oder Einmalangebot ohne Adresse) → Ergebnis in **`ZUFELD20N`**; ruft bei Einmalangebot `LOGBATT_EINMALANGEBOT`; ruft `LOGBATT_SPC_TEXT`; ermittelt den **Spesencode (SPC)** je Element |
| **LOGBATT_SPC_TEXT** | Liest SPC-Text aus **`LMX_LBATT_TX`**; setzt Text + Mengenangabe (€/Menge) zusammen. `gs_text` = SPC-Text, `gs_zusatztext` = gebundener Text mit Mengen |
| **LOGBATT_EINMALANGEBOT** | Nur bei Einmalangebot: **Dialogabfrage**, ob der **Stückpreis** geändert werden soll (Ergebnis `ZUFELD20N`, Stückpreis `TXTWERT` → `gn_txtwrt`); danach Neuberechnung |

### Wichtige Variablen (LOGBATT-spezifisch)
`gs_lbatt_ko` (abzurechnende Elemente) · `gs_ber_von` (aktuell verglichenes Element) ·
`ln_ber` (=1 bei Treffer) · `ZUFELD20N` (Matrix-Ergebnis/Preis) · `TXTWERT`/`gn_txtwrt`
(Stückpreis Einmalangebot) · `gs_text` / `gs_zusatztext` · `ZUFELD11N` (immer = 1, für
„1 pro Sendung“/ST-Fälle) · `LAF_ADRID` (Rechnungsadresse) · `ADRID_WE`/`ADRID_WA`
(Warenempfänger/-absender).

## Stammdaten-Matrix – Namenskonvention (kritisch!)

Bei Rahmenverträgen setzt sich die **Matrix-Bezeichnung** immer so zusammen:

```
PLO_<Adresse Rechnungsempfänger>_<Rahmenvertrag>_<Berechnungs-Element>
Beispiel BMW, RV F41946L, Element VERP →  PLO_810035000_F41946L_VERP
```

> ⚠️ Die Bezeichnung muss **exakt** stimmen, sonst werden **keine Preise gezogen**.
> Bei Matrizen ohne Parameter (immer abrechnen, z. B. DOKU) wird **`ZUFELD11N`** (=1) als
> Variable verwendet. Bei mehreren Ländern (z. B. SEA) X-/Y-Variable entsprechend erfassen.
> **Jede neue Matrix** muss auf die Komponente **`Cust_LOGBATT`** zugeordnet werden.
> Tipp: bei neuen RV bestehende Matrix mit ähnlicher Logik **kopieren** und Preise eintragen.

## Neuen Rahmenvertrag erfassen – Vorgehen (Kurzrezept)

1. **RV analysieren:** genau festlegen, wann/was berechnet wird; Artikel des RV je
   **Berechnungs-Element** zusammenfassen (mit `X_LOGB_BER`-Liste vergleichen).
2. **Fehlt ein Element?** → neues Element im Wertebereich **`X_LOGB_BER`** anlegen und der
   Komponente zuordnen (sonst Schritt 3 überspringen).
3. Element in **`LOGBATT_SDG_BESCH_DATEN`** an passender Stelle ergänzen (Absatz kopieren,
   `gs_ber_von = <ELEMENT>` setzen; z. B. Einlagerung je Kiste → `EINL` pro Beschreibungszeile).
4. In **`LOGBATT_BERECHNUNG_SPC`** die SPC-Ermittlung ergänzen (z. B. `ENTL` → Spesencode `1016`).
5. **GenTab `LMX_LBATT_KO`**: Zeile für Adresse + RV + zu berechnende Elemente anlegen.
6. **GenTab `LMX_LBATT_TX`**: Texte je Element erfassen. Kommt ein Text nur einmal vor →
   keine Bedingung nötig (z. B. DOKU, ENTL, ZOLL, EINL, TRANS). Werden je Parameter
   unterschiedliche Texte gebraucht → **Bedingung** hinterlegen (z. B. SEA/AIR je Absender-/
   Empfängerland). **Bedingung exakt** eintragen (sonst Fehlberechnung) – bestehende Einträge
   als Vorlage nehmen.
7. **Stammdaten-Matrix** je Element mit korrekter Bezeichnung anlegen (s. o.), auf
   `Cust_LOGBATT` zuordnen. Module/GenTabs bei Änderung ebenfalls über die Komponente übertragen.

## Sprache der Texte (LOGBATT_SPC_TEXT)
- **Einmalangebot:** Land des **Rechnungsempfängers** entscheidet – **DACH → Deutsch**, sonst
  **Englisch**.
- **Rahmenvertrag:** Sprache wird aus der Tabelle `LMX_LBATT_TX` gezogen.
- **Einheiten-Ausnahmen im Zusatztext:** `TRANS_KMV` nutzt **`X_VOLL_KM`** (Vollgut-km) statt
  Gesamt-km; **TO** = kg→Tonnen umgerechnet; **ST** = `X_CLL_ANZ`, außer „1 pro Sendung“ →
  `ZUFELD11N`. Neue Einheit → im Entscheidungsblock + Wertebereich **`X_LBATT_EINH`** ergänzen.

## Wichtige LOGBATT-Attribute (aus Sendung/SDB)
`X_GG_EINST` (Gefahrgut-Einstufung: `NBTSY`, `BTSY`, `BTSN`) · `X_ENTSORGUNG` (`ENTSORGUNG`,
`ANALYSE`, `STANDARD`) · `X_VOLL_KM` / `X_LEER_KM` / `X_GES_KM` / `X_GES_KM_GRENZ` (km-Logik) ·
`X_CLL_ANZ` (Colli) · `X_TREIS` (Trockeneis verwendet) · `X_STAPL_AB` (Stapler Abholung) ·
`X_WARTZEIT`/`X_WARTZEIT_Z` (Wartezeit, Staffel 15 min = 1) · `VEPID` (Verpackungs-/Behälter-ID,
z. B. `TRO`, `VERMI`, `HEBGE`).

## Berechnungs-Elemente `X_LOGB_BER` (Katalog)

Auszug der wichtigsten Elemente (Spalten: *Element | Beschreibung | Einschränkung | Häufigkeit*).
Vollständige Liste in der Quelle; hier die Struktur + prägnante Fälle:

**Häufigkeit:** `1` = einmal pro Sendung · `pro **` = pro 2. Beschreibungsebene · `pro *` =
pro 1. Beschreibungsebene · `pro erfasster VEPID` = je Verpackungs-ID.

| Element | Beschreibung | Einschränkung / Logik |
|---|---|---|
| ABF_ABS / ABF_EMPF | Abfertigung Absender/Empfänger | keine · 1× |
| ABFPA | Erstellung Abfall-/Transportpapiere | keine · 1× |
| AIR / SEA / FAEHRE / EXPR | Transport Luft/Schiff/Fähre/Express | keine · 1× |
| AUSLSP | Auslandsspesen | keine · 1× |
| BEHG / BEHG_1 | Behälterbereitstellung | pro ** bzw. pro * |
| BEHG_TREIS | Behälter mit Trockeneis | wenn VEPID `TRO` · pro VEPID |
| BEHG_BED | Behälter mit Bedingung | Matrix `PLO_BEHG_BED` (RV + X_ENTSORGUNG) |
| BEHG1 / BEHG2 | Behälter je Empfänger | Matrix `PLO_BEHG_EMPF` (RV + ADRID_WE); gehören zusammen |
| EINL | Einlagerungskosten | keine · pro ** |
| ENTL / ENTP (+ _TREIS) | Entladen/Entpacken (Trockeneis) | pro ** bzw. pro VEPID |
| DOKU | Zusätzliche Dokumente | wenn neben ABFPA weitere Doku-Kosten |
| HEBGE | Hebegestell LogBATT | wenn VEPID `HEBGE` · pro Hebegestell |
| **TRANS** | Transport (Basis) | wenn `PLO_TRANS_KM` kein Wert bzw. `X_GES_KM < KM_GRENZ` · pro ** |
| **KMV / TRANS_KMV** | Transport nach km Vollgut | wenn `X_GES_KM > X_GES_KM_GRENZ` → mit `X_VOLL_KM` |
| **KML / TRANS_KML** | Transport nach km Leergut | analog → mit `X_LEER_KM` |
| TRANS_RUND | Transport Rundlauf | wenn `X_VOLL_KM` **und** `X_LEER_KM` gesetzt (sonst KMV/KML) |
| TRANS_TRO / TRANS_TREIS | Transport Trockeneis | `X_TREIS` bzw. VEPID `TRO` |
| TRANS1 / TRANS2 | Transport je Empfänger | Matrix `PLO_TRANS_EMPF` (RV + ADRID_WE) |
| TRANSABI/ABN, TRANSANABI/ABN, TRANSANZUI/ZUN, TRANSZUI/ZUN | Transport An-/Abfahrt/Zustellung IO/NIO | je `X_ENTSORGUNG` (ANALYSE/STANDARD) + `X_GG_EINST` + ADRID_WE/WA=8000014 |
| RECYC / RECYC_BED | Recycling/Entsorgung (mit Bed.) | keine · pro ** / Matrix `PLO_RECYC_BED` |
| RECYC_BATT / RECYC_GG / RECYC_BMZ | Recycling für Starterbatterie/GG/Entsorgung LOGBATT | nur bei `X_ENTSORGUNG = ENTSORGUNG` |
| RECM | Recycling div. Verpackungsmaterial | VEPIDs KALO/KAKU/HOKIKU/HOKILU/FOLIE/VERMI/STYRO/KUBEKU |
| VERL / VERL_TREIS | Verladen/Verpacken (Trockeneis) | pro ** bzw. VEPID |
| VERP / VERP_1 / VERP_2 | Verpackungspauschale (Ebenen) | keine; VERP_1 = 2. SDB-Ebene, VERP_2 = 1. SDB-Ebene |
| VERP_BED / VERP1 / VERP2 | Verpackung mit Bed. / je Empfänger | Matrix `PLO_VERP_BED` / `PLO_VERP_EMPF` |
| VERM / VERPBEHG | Verpackungsmaterial / Kombi Verpackung+Behälter | VEPIDs VERMI/BRAL/BRAXL / keine |
| TREIS | Trockeneis | wenn VEPID `TRO` · pro VEPID |
| MAUT / WOEND | Maut / Wochenendzuschlag | keine · 1× |
| STAPAB | Stapler Abholung | wenn ZA `X_STAPL_AB = N` · 1× |
| WARTE | Wartezeit | wenn `X_WARTZEIT` gesetzt (Staffel 15 min → `X_WARTZEIT_Z`) |
| FACHAR | Facharbeiter Stunden | keine · 1× |
| LOGIS / VERW | Zusatz-Logistikdienstl. / Verwaltungskosten | keine |
| ZOLL / ZOLL_ABS / ZOLL_EMPF | Zollabwicklung (Abs/Empf) | keine · 1× |
| LEER / LEER_1 | Leertransport | keine / Logik wie TRANS_1 |
| TRANS_KM / VOLL | (nicht in Verwendung) | Häufigkeit 0 |

> Suffix **`_1`/`_2`** = Varianten je Beschreibungsebene; **`_BED`** = mit Bedingung (Matrix);
> **`_TREIS`/`_TRO`** = Trockeneis; **`1`/`2`** (ohne Unterstrich) = Empfänger-Paare (gehören
> zusammen, Matrix `_EMPF`).

## km-Logik (TRANS vs. KMV/KML) – Merksatz
In Matrix **`PLO_TRANS_KM`** wird je RV eine **km-Grenze** gesetzt. Ist
`X_GES_KM (= X_VOLL_KM + X_LEER_KM) > X_GES_KM_GRENZ` → **KMV** (mit `X_VOLL_KM`) bzw. **KML**
(mit `X_LEER_KM`). Andernfalls → **TRANS**.
