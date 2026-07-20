# 18 – Kopfvertrag & Rahmenvertrag in lBase anlegen (LOGBATT)

← [Index](00_INDEX.md) · verwandt: [14 LOGBATT Abrechnung & Konditionen](14_logbatt-abrechnung-konditionen.md) · [13 GenTabs](13_lim-und-gentabs.md) · [16 Aktueller Prozess](16_aktueller-prozess-handbuch.md) · [07 Prozess L3, Schritt III](07_prozesse-logbatt.md)

Quellen: GenTab-Definition `LMX_LB_KV` / `LMX_LB_RV` (→ [13]), Abrechnungslogik (→ [14]),
Prozessschritt III „Rahmenverträge/Abrufaufträge" (→ [07]), Intake-Vorlage „**Vorlage_neuer
Rahmenvertrag**" + Preisblatt (Fallbeispiel **Jungheinrich**, Stand 2026).

> Zielgruppe: Key-User / Auftragsmanagement, die für einen Kunden einen **Kopfvertrag (KV)**
> und darunter einen oder mehrere **Rahmenverträge (RV, Abruf)** mit Preisen anlegen.

---

## 1. Konzept: Kopfvertrag vs. Rahmenvertrag

In lBase (LOGBATT) hängen Kundenverträge zweistufig zusammen:

```
KUNDE (SAP-Stammdaten, z. B. Jungheinrich 10988)
   └── KOPFVERTRAG (KV)   ── Klammer / Einkaufsbeschluss + Gesamt-Budget + Laufzeit
          └── RAHMENVERTRAG (RV, „Abruf")  ── konkreter Abruf: eigenes Volumen,
                 └── PREISE je Berechnungs-Element (Matrix PLO_<Adr>_<RV>_<Element>)
```

| Ebene | Was | GenTab | Analog Formular |
|---|---|---|---|
| **Kopfvertrag (KV)** | Übergeordnete Vertragsklammer: Kopfvertrags-/Einkaufsbeschlussnr., **Gesamt-Volumen in €**, Laufzeit. Ein KV kann **mehrere RV/Abrufe** tragen. | **`LMX_LB_KV`** (Kopfverträge Logbatt) | Abschnitt **2** der Vorlage |
| **Rahmenvertrag (RV / Abruf)** | Konkreter Abruf unter dem KV: Kundenreferenz/Abrufbestellnr., **Volumen Abruf in €**, Laufzeit, ggf. bisherige RV-Nr. Der **RV** ist der Schlüssel, der später **in der Sendung** gesetzt wird und über den die Preise gezogen werden. | **`LMX_LB_RV`** (Rahmenverträge Logbatt) | Abschnitt **3** der Vorlage |
| **Preise** | Je Berechnungs-Element eine Preis-Matrix; welche Elemente überhaupt gelten, steht in `LMX_LBATT_KO`, die Texte in `LMX_LBATT_TX`. | `LMX_LBATT_KO` / `LMX_LBATT_TX` + Matrizen `PLO_…` | Preisblatt |

> **Merksatz:** Der **Kopfvertrag ist die Budget-/Laufzeitklammer**, der **Rahmenvertrag der
> abrechenbare Abruf mit Preisen**. Abgerechnet wird immer gegen den **RV** in der Sendung
> (kein RV in der Sendung → Einmalangebot, → [14]).

Anlage passiert auf **L-Base DEV** (Anlage RV/Verträge, → [07]); GoLive-Hinweis: RV-Anlage extern
über Axians möglich (**1 BT = 1.200 €**, → [16]). Verantwortlich: **FiBu + Vertrieb**, Key-User
unterstützend.

---

## 2. Fallbeispiel Jungheinrich – die Formulardaten

Aus der Vorlage `Preisliste_Jungheinrich.xlsx` (= „Vorlage_neuer Rahmenvertrag"):

**Abschnitt 1 – Kunde**
| Feld | Wert |
|---|---|
| Kunde | **Jungheinrich** |
| Kundennr. SAP | **10988** |
| Rechnungsempfängeradresse | siehe SAP (→ ADRID der `810…`-Rechnungsadresse, → [16]) |
| SAP Projektnr. | kommt von Finance auf Basis dieses Formulars |

**Abschnitt 2 – Kopfvertrag** (`LMX_LB_KV`)
| Feld | Wert |
|---|---|
| Kopfvertragsnr. / Einkaufsbeschluss | *(vom Kunden/Einkauf; eintragen)* |
| Kopfvertrag Volumen in € | *(Gesamtbudget; eintragen)* |
| Laufzeit von / bis | von … / **bis 31.12.2026** |

**Abschnitt 3 – Rahmenvertrag / Abruf** (`LMX_LB_RV`)
| Feld | Wert |
|---|---|
| Kundenreferenznr. / Abrufbestellnr. | *(eintragen)* |
| Volumen Abruf in € | *(eintragen)* |
| Laufzeit von / bis | *(eintragen; ≤ KV-Laufzeit)* |
| Bisherige RV-Nummer | *(nur bei bestehendem RV; bei Neuanlage leer)* |

> ⚠️ Die **exakten Spaltennamen** in den GenTabs `LMX_LB_KV`/`LMX_LB_RV` liegen im Wissensnetz
> nur als Definition, nicht als Zeilenlayout vor (→ [13]). Die **Felder oben** entsprechen der
> offiziellen Intake-Vorlage — bei der Ersterfassung die Spaltenzuordnung einmal mit der IT
> bestätigen, danach ist es reine Routine.

---

## 3. Preisblatt → Berechnungs-Elemente (das Mapping)

Das Preisblatt `Preisblatt_Jungheinrich_V3_2026` besteht aus **8 Leistungsbausteinen** =
Standort × Transportsicherheit:

| LB | Standort | Sicherheit | → steuert v. a. |
|---|---|---|---|
| LB1 | Köln | transportsicher | TRANS + Behälter „grün" + Entsorgung n.beschädigt |
| LB2 | Köln | transportunsicher | TRANS + Behälter „rot" + Entsorgung beschädigt |
| LB3 | Dortmund | transportsicher | " |
| LB4 | Dortmund | transportunsicher | " |
| LB5 | Mülheim | transportsicher | " |
| LB6 | Mülheim | transportunsicher | " |
| LB7 | Rundlauf (3 Standorte) | transportsicher | " |
| LB8 | Rundlauf (3 Standorte) | transportunsicher | " |

Jede Preisspalte im Preisblatt entspricht einem **Berechnungs-Element `X_LOGB_BER`** (→ [14]):

| Preisblatt-Spalte | Einheit | Berechnungs-Element (Vorschlag) | Bemerkung |
|---|---|---|---|
| Behälterbereitstellung L (grün i.O / rot n.i.O) | €/Tag | **BEHG** | Preis je nach i.O/n.i.O + Größe |
| Behälterbereitstellung XL | €/Tag | **BEHG** (Größe XL) | " |
| Verwaltungs-Pauschalpreis | €/Beauftragung | **VERW** | konstant 85 |
| Verpacken/Verladen pro Zelle | Pauschal | **VERL** (Zelle) | konstant 7,88 |
| Verpacken/Verladen pro Modul | Pauschal | **VERL** (Modul) | konstant 49 |
| Verpacken/Verladen pro Pack (BEV) | Pauschal | **VERL** (Pack) | konstant 169 |
| Transport Pauschalpreis | Pauschal | **TRANS** | **variiert je LB** (s. u.) |
| Entsorgung NMC/NMA | €/kg | **RECYC** (Chemie NMC/NMA) | 0,49 / 0,99 / stark 2,50 |
| Entsorgung LTO | €/kg | **RECYC** (Chemie LTO) | 3,00 / 5,50 |
| Entsorgung LFP | €/kg | **RECYC** (Chemie LFP) | 3,00 / 7,50 |
| Bereitstellung Stapler | €/Beauftragung | **STAPAB** | 890,00 DE |
| Standzeit > 1 h, je 15 min | 25,00 € | **WARTE** | Staffel `X_WARTZEIT_Z` (→ [14]) |
| Maut (inkl. DE, Ausland separat) | — | **MAUT** | nur außerhalb DE |

> Die konkrete Element-Benennung mit der bestehenden `X_LOGB_BER`-Liste abgleichen (→ [14],
> Schritt „RV analysieren"). Ob Behälter/Entsorgung über **eine** parametrisierte Matrix
> (Größe / Chemie / Zustand als X-/Y-Variable) oder mehrere Elemente laufen, richtet sich nach
> der bestehenden LOGBATT-Modellierung — bevorzugt an einem **bestehenden RV** orientieren.

### Die konkreten Preise (aus dem Preisblatt)

**Konstant über alle 8 LB** (→ nur **einmal** hinterlegen, gilt für jeden Abruf gleich):
- Verwaltung **85 €** · Verpacken Zelle **7,88 €** · Modul **49 €** · Pack **169 €**
- Stapler **890 € (DE)** · Standzeit **25 €/15 min** (ab > 1 h)

**Behälter & Entsorgung – nur nach Transportsicherheit unterschiedlich (nicht nach Standort):**
| | transportsicher (LB1/3/5/7) | transportunsicher (LB2/4/6/8) |
|---|---|---|
| Behälter L | **45 €/Tag** (grün) | **100 €/Tag** (rot) |
| Behälter XL | **100 €/Tag** (grün) | **300 €/Tag** (rot) |
| Entsorgung NMC/NMA | **0,49 €/kg** | **0,99 €/kg** (stark beschädigt 2,50) |
| Entsorgung LTO | **3,00 €/kg** | **5,50 €/kg** |
| Entsorgung LFP | **3,00 €/kg** | **7,50 €/kg** |

**Transport (TRANS) – der einzige Preis, der je Standort abweicht:**
| Standort | transportsicher | transportunsicher |
|---|---|---|
| Köln (LB1/2) | **1.670 €** | **2.160 €** |
| Dortmund (LB3/4) | **1.670 €** | **2.010 €** |
| Mülheim (LB5/6) | **1.670 €** | **2.140 €** |
| Rundlauf (LB7/8) | **1.960 €** | **2.910 €** |

> 💡 **„Die meisten Preise sind identisch"** heißt hier konkret: **alles außer TRANS** ist über
> die Standorte gleich. Beim transportsicheren Fall ist selbst TRANS für Köln/Dortmund/Mülheim
> identisch (1.670 €) — nur Rundlauf weicht ab. **Praktische Folge:** einen bestehenden
> LOGBATT-RV **kopieren** und im Wesentlichen nur die **TRANS-Preise** (und die
> Behälter-/Entsorgungsstaffel sicher/unsicher) eintragen (→ [14]-Tipp „bestehende Matrix
> kopieren").

---

## 4. Schritt-für-Schritt-Anleitung

> Vorab: Kunde muss in **SAP** als Kunde inkl. Rechnungsanschrift existieren (Jungheinrich 10988)
> und per 15-Min-Schnittstelle in lBase angekommen sein (→ [16]/[07-I]). ADRID der
> Rechnungsadresse (`810…`) bereithalten — sie geht in den Matrixnamen ein.

### Schritt 1 – Vorlage vervollständigen
Die Intake-Vorlage „Vorlage_neuer Rahmenvertrag" (= `Preisliste_Jungheinrich.xlsx`) füllen:
Abschnitt 1–3 (Kunde / Kopfvertrag / Rahmenvertrag) **vollständig**, Preisblatt beilegen.
Übergabe an **FiBu** (vergibt SAP-Projektnr.) / Anlage durch Key-User.

### Schritt 2 – Kopfvertrag in `LMX_LB_KV` anlegen
1. In lBase **DEV** die GenTab **`LMX_LB_KV`** öffnen (GenTab-Pflege, → [13]).
2. Neue Zeile: **Kunde/ADRID Jungheinrich**, **Kopfvertragsnr./Einkaufsbeschluss**,
   **Volumen (€)**, **Laufzeit von/bis** (bis **31.12.2026**).
3. Speichern. Der KV ist die Klammer für die darunter liegenden Abrufe.

### Schritt 3 – Rahmenvertrag in `LMX_LB_RV` anlegen
1. GenTab **`LMX_LB_RV`** öffnen.
2. Neue Zeile: **RV-/Abrufnummer**, **Verweis auf den KV** aus Schritt 2, **Kundenreferenz/
   Abrufbestellnr.**, **Volumen Abruf (€)**, **Laufzeit** (≤ KV), **Rechnungsadresse (ADRID)**.
3. Diese **RV-Nummer** ist der Wert, der später **in der Sendung** gesetzt wird und die
   Preisfindung auslöst — sauber dokumentieren.

### Schritt 4 – abzurechnende Elemente in `LMX_LBATT_KO`
Zeile für **Rechnungsadresse (`LAF_ADRID`) + RV** anlegen und die **Berechnungs-Elemente**
eintragen, die für Jungheinrich gelten (aus dem Mapping oben): **TRANS, BEHG, VERW, VERL, RECYC,
STAPAB, WARTE, ggf. MAUT** (→ [14], Schritt 5). Fehlt ein Element im Wertebereich `X_LOGB_BER`
→ erst dort anlegen und `Cust_LOGBATT` zuordnen (→ [14]/[13]).

### Schritt 5 – Texte in `LMX_LBATT_TX`
Je Element den **Rechnungstext + Einheit** hinterlegen (€/Tag, €/kg, €/Beauftragung,
Pauschal). Kommt ein Text nur einmal vor → keine Bedingung. Werden je Parameter unterschiedliche
Texte gebraucht (z. B. Behältergröße L/XL, Chemie NMC/LTO/LFP, sicher/unsicher) → **Bedingung
exakt** hinterlegen, bestehende Einträge als Vorlage (→ [14], Schritt 6). Sprache beim RV kommt
aus dieser Tabelle.

### Schritt 6 – Preis-Matrizen `PLO_<Adr>_<RV>_<Element>`
Für jedes Element eine **Stammdaten-Matrix** anlegen — Bezeichnung **exakt** nach Konvention
(→ [14], „kritisch!"):

```
PLO_<ADRID Rechnungsempfänger>_<RV-Nummer>_<Element>
z. B.  PLO_<JungheinrichADRID>_<RV>_TRANS
       PLO_<JungheinrichADRID>_<RV>_BEHG
       PLO_<JungheinrichADRID>_<RV>_RECYC   …
```
- Preise aus Abschnitt 3 oben eintragen; **TRANS** je Standort, **BEHG/RECYC** je Größe/Chemie/
  Zustand als X-/Y-Variable, alles Übrige als Konstante.
- Jede neue Matrix auf Komponente **`Cust_LOGBATT`** zuordnen.
- **Effizient:** bestehenden RV mit ähnlicher Logik als Vorlage **kopieren** und nur die
  abweichenden (v. a. TRANS-)Werte überschreiben.

### Schritt 7 – Übertragen & testen
1. Geänderte Module/GenTabs/Matrizen über **`Cust_LOGBATT`** von DEV übertragen (→ [14]/[13]).
2. **Test:** Testsendung mit gesetztem **RV** anlegen (LIM, → [13]) → **LA-Übersicht →
   „LA ausführen"** (→ [16]) → prüfen, ob alle Elemente mit den Preisen aus dem Preisblatt
   ziehen. Bei „keine Preise" → Matrixbezeichnung Zeichen für Zeichen prüfen (häufigster Fehler).
3. Freigabe → auf **PROD**.

---

## 5. Checkliste

- [ ] Kunde in SAP + in lBase vorhanden (Jungheinrich 10988, Rechnungs-ADRID `810…`)
- [ ] Intake-Vorlage vollständig (Kunde / KV / RV), SAP-Projektnr. von Finance
- [ ] **KV** in `LMX_LB_KV` (Volumen, Laufzeit bis 31.12.2026)
- [ ] **RV/Abruf** in `LMX_LB_RV` (Volumen, Laufzeit ≤ KV, ADRID, Verweis auf KV)
- [ ] `LMX_LBATT_KO`: Elemente je ADRID+RV
- [ ] `LMX_LBATT_TX`: Texte/Einheiten (Bedingungen für L/XL, Chemie, sicher/unsicher)
- [ ] Matrizen `PLO_<Adr>_<RV>_<Element>` mit Preisen, auf `Cust_LOGBATT`
- [ ] Testsendung mit RV → LA ausführen → Preise korrekt → PROD

---

## 6. Offen / mit IT bzw. FiBu klären
- Exaktes **Spaltenlayout** von `LMX_LB_KV` / `LMX_LB_RV` (Definition bekannt, Zeilenlayout nicht
  exportiert, → [13]).
- Ob **Behälter (L/XL)** und **Entsorgung (NMC/LTO/LFP, sicher/unsicher)** je eine Matrix mit
  X-/Y-Parametern oder getrennte Elemente sind — an bestehendem LOGBATT-RV orientieren.
- Zuordnung **Standort → Element/Parameter** bei TRANS (Köln/Dortmund/Mülheim/Rundlauf): ob über
  Absenderadresse (`ADRID_WA`) oder eigenes Element modelliert.
- Behandlung **Maut Ausland** und **Storno < 24 h (50 %)** — ob als Element oder manueller Zuschlag.
