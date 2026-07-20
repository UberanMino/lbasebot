# 18 – Kopfvertrag & Rahmenvertrag in lBase anlegen (LOGBATT)

← [Index](00_INDEX.md) · verwandt: [14 LOGBATT Abrechnung & Konditionen](14_logbatt-abrechnung-konditionen.md) · [13 GenTabs](13_lim-und-gentabs.md) · [15 Niederlassungen – Verträge anlegen (S.14–15)](15_niederlassungsspezifisch.md) · [16 Aktueller Prozess](16_aktueller-prozess-handbuch.md) · [07 Prozess L3, Schritt III](07_prozesse-logbatt.md)

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
| Kundenreferenznr. / Abrufbestellnr. | *(vom **Kunden** – dessen Bestell-/Abrufnummer; eintragen)* |
| Volumen Abruf in € | *(eintragen)* |
| Laufzeit von / bis | *(eintragen; ≤ KV-Laufzeit)* |
| Bisherige RV-Nummer | *(nur bei bestehendem RV; bei **Neuanlage leer**)* |

> ### ⚠️ Das Dialogfeld „Vertragsnummer erfassen" – **nicht einfach ausdenken**
> Beim Anlegen fragt das Vertragsmodul eine **Vertragsnummer** ab (kein Auto-Wert → **ihr vergebt
> sie nach Schema**). Dieser Wert ist der **permanente Schlüssel**: er geht in den **Matrixnamen**
> `PLO_<Adr>_<RV>_<Element>` **und** später **in jede Sendung** (Feld RV). Falsch/willkürlich =
> **keine Preise** oder **Kollision** mit bestehendem Vertrag; nachträglich kaum änderbar. Er muss
> **eindeutig** sein **und dem Hausschema folgen**. Woher der richtige Wert kommt:
>
> - **Kopfvertrag:** Vertragsnummer = **`Kopfvertragsnr./Einkaufsbeschluss`** aus Abschnitt 2 –
>   kommt vom **Kunden/Einkauf** (Jungheinrich), nicht erfunden. Fehlt sie → Vertrieb/FiBu fragen.
> - **Rahmenvertrag:** interne **L-Base-/Axians-RV-ID** (Format wie BMWs `F41946L`, → [14]).
>   **Muster + freie Nummer finden** über bestehende RV: **„Adressen" + Filter** (RV-Stand, → [16]),
>   GenTab **`LMX_LB_RV`** (Generische Tabellen → Daten) oder bestehende **`PLO_…`-Matrizen** –
>   am einfachsten **einen bestehenden RV kopieren** und hochzählen.
>
> **Nicht verwechseln:** **Kundenreferenz-/Abrufbestellnr.** = Nummer, die *Jungheinrich* aus
> seiner Bestellung mitgibt (eigenes Feld); **Bisherige RV-Nummer** = nur beim Ablösen eines
> alten RV, bei Neuanlage **leer**. Steht **nicht im Preisblatt**.
>
> 👉 Vor dem Speichern **mit FiBu/IT** (bzw. wer die Axians-Vertragsnummern vergibt) abklären, ob
> die Nummer frei im Schema wählbar ist oder von einer Stelle vergeben wird.

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

> **Wo arbeitest du?** Anlage auf **L-Base DEV** (Umgebung für RV-/Vertragsanlage, → [07]).
> Alle Werkzeuge liegen im Ribbon-Reiter **ADMINISTRATION** (Gruppen *Verwaltungstools*,
> *Generische Tabellen*, *Konfigurationen*) bzw. **ABRECHNUNG** (→ `ANLEITUNG_logik-export.md`).

### Bedienungs-Grundmuster (gilt für alle GenTab-Schritte)
lBase-Masken werden **immer gleich** bedient — diese Tasten brauchst du durchgängig (→ [06]):

| Taste | Funktion | | Taste | Funktion |
|---|---|---|---|---|
| **F5** | Suchen (in ein Feld klicken → F5 = Auswahlliste) | | **F8** | **Neu** (neuer Datensatz/Zeile) |
| **F6** | Anzeigen (nur lesen) | | **F7** | **Ändern** |
| **F12** | **Speichern** | | **F10** | Fertig |
| **ESC** | Abbrechen | | **F2 / F3** | zurück-/weiterblättern |

- **Datum** schnell: `SPACE` = heute, `31 12` = 31.12. akt. Jahr, `+30` = heute+30 Tage (→ [06]).
- **Suchen** mit Platzhaltern: `%` (mehrere Zeichen), `?` (ein Zeichen) — z. B. `PLO_%JUNG%`.
- **Kopieren statt neu tippen:** bestehenden Datensatz **anzeigen → „Datensatz kopieren"** →
  anpassen → speichern (dokumentiert für Abrechnungs-LA in [16]; gleiches Prinzip bei Matrizen).

### Schritt 1 – Vorlage vervollständigen
Die Intake-Vorlage „Vorlage_neuer Rahmenvertrag" (= `Preisliste_Jungheinrich.xlsx`) füllen:
Abschnitt 1–3 (Kunde / Kopfvertrag / Rahmenvertrag) **vollständig**, Preisblatt beilegen.
Übergabe an **FiBu** (vergibt SAP-Projektnr.) / Anlage durch Key-User.

> ### 🟢 Der richtige Anlageweg: das **Vertragsmodul** (nicht die GenTab direkt)
> KV und RV werden über das **Vertrags-Modul** angelegt (bei euch „Verträge und Projekte anlegen"),
> **nicht** durch Hand-Editieren der GenTab (S.14–15, → [15]):
> **Allgemeiner Modulaufruf [F4]** → in der Modulliste **[F5]** → nach **„Vertrag"** suchen →
> **Vertragsart wählen** (Kopfvertrag bzw. Rahmenvertrag) → **[OK]** → Maske ausfüllen → **[F12]**.
>
> Die GenTabs **`LMX_LB_KV`** / **`LMX_LB_RV`** sind der **Speicher dahinter** – dort landen die
> Daten automatisch. GenTab **direkt** öffnen (ADMINISTRATION → Generische Tabellen → **Daten**)
> brauchst du nur zum **Prüfen/Nachkorrigieren** (Experten-Weg), nicht für die normale Anlage.

### Schritt 2 – Kopfvertrag (KV) anlegen
1. **[F4] Allgemeiner Modulaufruf** → **[F5]** → **„Vertrag"** suchen → **Vertragsart = Kopfvertrag**
   → **[OK]**.
2. Maske füllen (Feld anklicken, bei Auswahlfeldern **[F5]**): **Kunde Jungheinrich (10988 /
   Rechnungs-ADRID `810…`)**, **Kopfvertragsnr./Einkaufsbeschluss**, **Volumen (€)**,
   **Laufzeit von** / **bis 31.12.2026** (Datum kurz: `31 12` tippen, → [06]).
3. **[F12] Speichern**. Der KV ist jetzt die Klammer für die Abrufe darunter.
4. *(Kontrolle optional: ADMINISTRATION → Generische Tabellen → Daten → `LMX_LB_KV` → Zeile da?)*

### Schritt 3 – Rahmenvertrag (RV / Abruf) anlegen
1. Wieder **[F4] → [F5] „Vertrag"** → **Vertragsart = Rahmenvertrag** → **[OK]** (unter dem KV
   aus Schritt 2).
2. Maske füllen: **Verweis auf den KV**, **Kundenreferenz/Abrufbestellnr.** (die Nummer vom
   Kunden), **Volumen Abruf (€)**, **Laufzeit** (≤ KV), **Rechnungsadresse (ADRID)**;
   „Bisherige RV-Nummer" bleibt **leer**. **[F12]**.
3. Die vom System vergebene / gesetzte **RV-Nummer notieren** (interne L-Base-ID, s. Kasten oben) –
   sie wird gleich im **Matrixnamen** und später **in der Sendung** gebraucht.

### Schritt 4 – abzurechnende Elemente in `LMX_LBATT_KO`
1. **ADMINISTRATION → Generische Tabellen → Daten → `LMX_LBATT_KO`**.
2. **[F8] Neu** → Zeile für **Rechnungsadresse (`LAF_ADRID`) + RV** und die **Elemente**
   eintragen, die für Jungheinrich gelten: **TRANS, BEHG, VERW, VERL, RECYC, STAPAB, WARTE,
   ggf. MAUT** → **[F12]** (→ [14], Schritt 5).
3. **Element fehlt im Katalog?** → erst im Wertebereich anlegen:
   **ADMINISTRATION → Verwaltungstools → Wertebereiche → `X_LOGB_BER` → [Bereichsview]** →
   neuen Wert + Bezeichnung + Komponente **`Cust_LOGBATT`** (→ `ANLEITUNG_logik-export.md` C).

### Schritt 5 – Texte in `LMX_LBATT_TX`
1. **ADMINISTRATION → Generische Tabellen → Daten → `LMX_LBATT_TX`**.
2. **[F8] Neu** → je Element **Rechnungstext + Einheit** (€/Tag, €/kg, €/Beauftragung, Pauschal),
   **[F12]**. Kommt ein Text nur einmal vor → keine Bedingung. Braucht ein Element je Parameter
   unterschiedliche Texte (Behältergröße L/XL, Chemie NMC/LTO/LFP, sicher/unsicher) →
   **Bedingung exakt** eintragen; bestehenden Eintrag als Vorlage nehmen (→ [14], Schritt 6).
   Sprache beim RV kommt aus dieser Tabelle.

### Schritt 6 – Preis-Matrizen `PLO_<Adr>_<RV>_<Element>`
1. Ribbon **ABRECHNUNG → Abrechnung mit Konditionen → Stammdaten-Matrix**
   (→ `ANLEITUNG_logik-export.md` D).
2. Anzeigeart **„MD Matrix – Standard"**. **Zuerst eine passende bestehende Matrix suchen**
   (`PLO_%…` mit **[F5]** / `%`-Platzhalter) — z. B. dieselbe Element-Matrix eines anderen RV.
3. Diese Matrix **anzeigen → „Datensatz kopieren"** → **umbenennen exakt nach Konvention**
   (→ [14], „kritisch!"):
   ```
   PLO_<ADRID Rechnungsempfänger>_<RV-Nummer>_<Element>
   z. B.  PLO_<JungheinrichADRID>_<RV>_TRANS
          PLO_<JungheinrichADRID>_<RV>_BEHG
          PLO_<JungheinrichADRID>_<RV>_RECYC   …
   ```
4. **Preise aus Abschnitt 3 eintragen** → **[F12]**. **TRANS** je Standort, **BEHG/RECYC** je
   Größe/Chemie/Zustand über die X-/Y-Variable, alles Übrige als Konstante. **Weil fast alles
   gleich ist:** meist nur die **TRANS-Werte** ändern, Rest bleibt aus der Kopiervorlage stehen.
5. Jede **neue Matrix** auf Komponente **`Cust_LOGBATT`** zuordnen (sonst wird sie nicht
   übertragen/gefunden).

### Schritt 7 – Übertragen & testen
1. Geänderte GenTabs/Matrizen (und ggf. Module) über die Komponente **`Cust_LOGBATT`** von
   **DEV → PROD** übertragen (→ [14]/[13]).
2. **Testsendung** mit gesetztem **RV** anlegen (über LIM, Feld **LIM → [F5]**, → [13]) →
   **LA-Übersicht** (**Strg+U**) → **Kalkulation → „LA ausführen"** (→ [16]) → prüfen, ob alle
   Elemente mit den Preisen aus dem Preisblatt ziehen.
3. **Kommt „kein Preis"** → fast immer **Matrixbezeichnung** falsch: `PLO_<Adr>_<RV>_<Element>`
   **Zeichen für Zeichen** gegen ADRID/RV/Element prüfen (häufigster Fehler, → [14]).
4. Passt alles → Freigabe auf **PROD**.

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
- **RV-Nummernvergabe:** vergibt das Vertragsmodul die interne RV-Nummer **automatisch**, oder
  wird sie nach eigenem Schema gesetzt? (steht in keiner der Quelldateien – s. Kasten in §2).
- **Genaue Bezeichnung der Vertragsarten** im Modul „Verträge/Projekte anlegen" (welcher Eintrag
  = Kopfvertrag, welcher = Rahmenvertrag/Abruf, → [15] S.14–15).
- Ob **Behälter (L/XL)** und **Entsorgung (NMC/LTO/LFP, sicher/unsicher)** je eine Matrix mit
  X-/Y-Parametern oder getrennte Elemente sind — an bestehendem LOGBATT-RV orientieren.
- Zuordnung **Standort → Element/Parameter** bei TRANS (Köln/Dortmund/Mülheim/Rundlauf): ob über
  Absenderadresse (`ADRID_WA`) oder eigenes Element modelliert.
- Behandlung **Maut Ausland** und **Storno < 24 h (50 %)** — ob als Element oder manueller Zuschlag.
