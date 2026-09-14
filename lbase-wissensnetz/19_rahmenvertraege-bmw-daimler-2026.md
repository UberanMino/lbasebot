# 19 – Rahmenverträge 2026 anlegen/aktualisieren: BMW (F4F691P) & Daimler Buses (1260543850)

← [Index](00_INDEX.md) · **Grundanleitung:** [18 KV/RV anlegen](18_kopf-und-rahmenvertrag-anlegen.md) ·
verwandt: [14 Abrechnung & Konditionen](14_logbatt-abrechnung-konditionen.md) · [16 Aktueller Prozess](16_aktueller-prozess-handbuch.md)

> Konkreter Arbeitszettel für zwei reale Abrufbestellungen (Stand 09/2026). Das **Wie**
> (Modulwege, Tasten, Matrix-Konvention) steht vollständig in [18]; hier stehen nur die
> **fallbezogenen Werte** und **welcher Weg** je Fall gilt. Beide sind **Zellentsorgungs-/
> Batterieentsorgungs-Abrufe für 2026** — sehr wahrscheinlich **Verlängerungen bestehender
> Kundenbeziehungen** (BMW und Daimler Buses sind eingeführte LOGBATT-Kunden), also zuerst der
> **Prüf-/Aktualisieren-Pfad**, nicht blind neu anlegen.

> ⚠️ **Ehrlichkeitsvermerk (was belegt ist vs. was zu prüfen ist).** Alle Werte unten stammen
> **direkt aus den beiden Bestell-PDFs**. Drei Dinge sind **nicht** aus den PDFs oder dem
> Wissensnetz sicher ableitbar und **müssen im System / mit Key-User (Lisa H.) / FiBu** bestätigt
> werden — sie sind unten mit **🔎 prüfen** markiert:
> 1. die **ADRID des Rechnungsempfängers** (geht in jeden Matrixnamen ein),
> 2. **welche Nummer** genau als **RV-Schlüssel** in Sendung + Matrix gesetzt wird,
> 3. ob **KV/RV schon existieren** (dann aktualisieren statt neu anlegen) und ob die Summe ein
>    **Pauschal-Festpreis** oder ein **Budgetdeckel** über Einzelpreisen ist.

---

## 1. Die beiden Fälle im KV/RV-Schema

Erinnerung aus [18]: **Kopfvertrag (KV) = Budget-/Laufzeitklammer** (GenTab `LMX_LB_KV`),
**Rahmenvertrag (RV/Abruf) = abrechenbarer Abruf mit Preisen** (GenTab `LMX_LB_RV`).
Abgerechnet wird in der Sendung immer gegen den **RV**.

| | **Fall A — BMW AG** | **Fall B — Daimler Buses GmbH** |
|---|---|---|
| Bestell-/Dokumentart | Abrufbestellung | Abrufbestellung (über proQ) |
| Belegnummer (Kunde) | **F4F691P** (Bestellnummer) | **1260543850** (Dokumentennummer) |
| LogBATT-Lieferantennr. beim Kunden | **24403010** | **17146028** |
| **KV** (Kopfvertrag / Einkaufsbeschluss) | **89100** (Feld „Vertragskopfnummer") | **2010009171** (Einkaufsabschluss vom 10.03.2026) |
| **RV / Abruf** (Schlüssel in Sendung + Matrix) | **F4F691P** 🔎 prüfen | **1260543850** 🔎 prüfen |
| Volumen (Netto) | **48.000,00 EUR** | **20.000,00 EUR** |
| Laufzeit / Leistungszeitraum | **22.07.2026 – 31.12.2026** | Ende **31.12.2026**; Beginn nicht angegeben 🔎 (Abschluss datiert 10.03.2026) |
| Leistung | „Zellentsorgung" (ES-413_Zellentsorgung_LogBatt_2026_All) | „Entsorgung von Li-Ion-Batterien 2026" (Abholung + Entsorgung, Versuch Neu-Ulm) |
| Rechnungsempfänger | BMW AG, Kreditorenbuchhaltung, 80788 München | Daimler Buses GmbH, HPC R520, Postfach 9042, 89087 Neu-Ulm |
| **ADRID Rechnungsempfänger** | **810035000** (im Wissensnetz für BMW belegt) — trotzdem 🔎 gegen die reale Rechnungsanschrift bestätigen | **unbekannt** 🔎 zwingend nachschlagen |
| Liefer-/Verbrauchsort | BMW, Lemgostr. 7, 80935 München | Daimler Buses Neu-Ulm Werk 5, Otto-Hahn-Str. 5, 89231 Neu-Ulm |
| Lieferbedingung / Incoterm | CPT München (frachtfrei) | DAP benannter Lieferort (Incoterms 2020) |
| Zahlung | 30 Tage | ZG82 (Stichtagsregelung, s. PDF S. 3) |

> **Zur RV-Schlüssel-Wahl (🔎 der kritischste Punkt).**
> - **BMW:** Die Bestellnummer **`F4F691P`** hat exakt das Format der **internen L-Base-RV-IDs**,
>   die das Wissensnetz für BMW nennt (`F41946L`, `F3Y9KT1`, → [14]/[11]). Deshalb ist `F4F691P`
>   der **naheliegende RV-Schlüssel** (Matrix `PLO_810035000_F4F691P_<Element>`). Die
>   **Vertragskopfnummer 89100** ist die **Klammer = KV**.
> - **Daimler:** Der **Einkaufsabschluss `2010009171`** ist die Rahmen-/Budgetklammer → **KV**; die
>   **Abrufbestellung `1260543850`** ist der konkrete Abruf → **RV**.
> - In **beiden** Fällen gilt der Warnhinweis aus [14]/[18]: der RV-Wert muss **zeichengenau
>   identisch** im Vertragsdialog, im Matrixnamen `PLO_<ADRID>_<RV>_<Element>` **und** später in
>   jeder Sendung (Feld RV) stehen. Ein Tippfehler = **keine Preise**. Bevor Matrizen gebaut werden:
>   an einer **bestehenden BMW-/Daimler-Matrix** anschauen, welche Nummer dort tatsächlich als RV
>   steht (`PLO_%810035000%` bzw. `PLO_%<DaimlerADRID>%` mit **[F5]**/`%`), und die Wahl mit dem
>   Key-User bestätigen.

> **Kein `_OHNE`-Schema hier.** Das `Kundenname_OHNE`-Hausschema aus [18] §2 gilt **nur, wenn es
> keine offizielle Nummer gibt** (wie bei Jungheinrich). **Beide** Fälle hier **haben** offizielle
> Nummern (89100 bzw. 2010009171 + die Belegnummern), also werden **diese** verwendet — nicht
> `BMW_OHNE`/`Daimler_OHNE`.

---

## 2. Entscheidung zuerst: neu anlegen ODER aktualisieren?

Weil beides **2026-Verlängerungen** eingeführter Kunden sind, ist der **Regelfall
„aktualisieren"**. Vorgehen exakt nach [18] §4 (Sonderfall „KV/RV existieren schon"):

1. **Suchen, ob RV existiert:** Vertrags-/Adressliste → nach Kunde bzw. nach RV-Kandidat filtern
   (`%F4F691P%`, `%89100%` bzw. `%1260543850%`, `%2010009171%`, `%810035000%`) → RV markieren →
   **[F6] Anzeigen** → RV-Bezeichnung, ADRID, Laufzeit, Volumen, Verweis auf KV notieren.
2. **Existiert & passt** → **weiterverwenden**, nur veraltete Felder per **[F7] Ändern**
   aktualisieren (typisch: **Laufzeit → 31.12.2026** und **Volumen → 48.000 € / 20.000 €**).
   **Kein Duplikat daneben anlegen** (sonst keine Preise / Kollision).
3. **Existiert nicht** → **neu anlegen** nach [18] §Schritt 2–3 (KV zuerst, dann RV darunter).
4. Danach in **beiden** Fällen weiter mit §3 unten (Preise/Elemente prüfen bzw. ergänzen) und
   §Schritt 7 aus [18] (**Testsendung mit RV → LA ausführen**).

> **Was ich aus den PDFs NICHT weiß:** ob für BMW/Daimler bereits KV/RV in lBase liegen — das
> zeigt nur die Live-Suche in Schritt 1. Deshalb steht „aktualisieren" hier als **wahrscheinlich**,
> nicht als gesichert.

---

## 3. Preise / abzurechnende Elemente — hier anders als bei Jungheinrich

Wichtiger Unterschied zum Preisblatt-Fall in [18] §3: Beide PDFs enthalten **eine einzige
Pauschal-Position** mit **einem Gesamt-Nettobetrag** (48.000 € bzw. 20.000 €), **keine**
kg-/Staffel-Preisliste mit 8 Leistungsbausteinen.

Daraus folgt eine **offene, nicht aus dem PDF entscheidbare Frage** 🔎 (mit FiBu/Key-User klären):

- **Variante 1 – Pauschal-Festpreis:** Der Abruf wird als **eine** Element-Position abgerechnet
  (z. B. Entsorgung/`RECYC` bzw. ein Pauschal-Element) mit dem **Festbetrag**. Dann genügt in
  `LMX_LBATT_KO` **ein** Element je ADRID+RV und **eine** Matrix
  `PLO_<ADRID>_<RV>_<Element>` mit dem Pauschalbetrag.
- **Variante 2 – Budgetdeckel über Einzelpreisen:** Die 48.000 €/20.000 € sind nur das
  **KV-/Abruf-Budget**, tatsächlich wird **je kg/Leistung** gegen die bestehenden LOGBATT-Preise
  abgerechnet (wie im generellen Modell aus [14]). Dann Elemente/Matrizen wie bei einem normalen
  RV pflegen (TRANS, RECYC nach Chemie/Zustand, BEHG, VERW, … → [18] §3) und den Betrag nur als
  **Volumen** im RV führen.

**Bis das geklärt ist, nichts an Matrizen bauen.** Der Betrag gehört in **jedem** Fall als
**Volumen** in den RV (bzw. KV); nur die **Preis-Matrix-Struktur** hängt an der Antwort oben.

Element-Hinweise aus den PDFs (falls Variante 2 / Einzelabrechnung):
- **BMW:** reine „Zellentsorgung" → v. a. **RECYC** (Entsorgung); Transport ggf. über CPT bereits
  eingepreist.
- **Daimler:** „Abholung **und** Entsorgung" → **RECYC** + wahrscheinlich **TRANS** (Abholung
  Neu-Ulm Werk 5); Incoterm **DAP**. Verpackung „6250 einschließlich", Verp.Rücks. „51".

Matrix-/Element-/Text-Pflege exakt wie in [18] §Schritt 4–6 und [14] („Neuen RV erfassen"):
`LMX_LBATT_KO` (Elemente je ADRID+RV) → `LMX_LBATT_TX` (Texte/Einheiten) →
Matrizen `PLO_<ADRID>_<RV>_<Element>` auf Komponente **`Cust_LOGBATT`**.

---

## 4. Kurz-Arbeitsablauf (beide Fälle, verweist auf [18])

Bedienung/Tasten (F4 Modulaufruf, F5 Suchen, F6 Anzeigen, F7 Ändern, F8 Neu, F12 Speichern) und
Modulwege stehen in [18] §4 und [06].

1. **ADRID besorgen** 🔎 — BMW: `810035000` gegen reale Rechnungsanschrift bestätigen; Daimler:
   ADRID des Rechnungsempfängers (Daimler Buses, HPC R520 / Neu-Ulm) nachschlagen.
2. **RV-Existenz prüfen** (§2 Schritt 1). Existiert → **[F7] aktualisieren** (Laufzeit/Volumen).
   Existiert nicht → **KV** anlegen ([18] Schritt 2), dann **RV** darunter ([18] Schritt 3).
   - **KV-Werte:** BMW 89100 / Volumen 48.000 € / bis 31.12.2026 · Daimler 2010009171 /
     Volumen 20.000 € / bis 31.12.2026.
   - **RV-Werte:** BMW Kundenreferenz **F4F691P**, Volumen 48.000 €, 22.07.–31.12.2026 ·
     Daimler Kundenreferenz **1260543850**, Volumen 20.000 €, bis 31.12.2026.
     „Bisherige RV-Nummer" nur ausfüllen, wenn ein **Alt-RV abgelöst** wird (§2 Schritt 2), sonst leer.
3. **Preis-Frage klären** (§3, Variante 1 vs. 2) → dann Elemente/Texte/Matrizen nach [18] §4–6.
4. **Übertragen** (`Cust_LOGBATT`, DEV→PROD) und **Testsendung** mit gesetztem RV → **LA ausführen**
   → prüfen, ob die Preise ziehen ([18] Schritt 7). „Kein Preis" → Matrixname
   `PLO_<ADRID>_<RV>_<Element>` **Zeichen für Zeichen** prüfen.

---

## 5. Checkliste je Vertrag

**BMW – F4F691P**
- [ ] ADRID Rechnungsempfänger bestätigt (Kandidat `810035000`) 🔎
- [ ] RV-Existenz geprüft → aktualisieren **oder** neu
- [ ] KV `89100`: Volumen 48.000 €, bis 31.12.2026
- [ ] RV `F4F691P`: Volumen 48.000 €, 22.07.–31.12.2026, ADRID gesetzt
- [ ] Preis-Modell geklärt (Pauschal 48.000 € vs. Einzelpreise) 🔎
- [ ] Elemente/Texte/Matrizen `PLO_810035000_F4F691P_<Element>` (falls Variante 2)
- [ ] Testsendung mit RV → LA korrekt → PROD

**Daimler Buses – 1260543850**
- [ ] ADRID Rechnungsempfänger nachgeschlagen 🔎
- [ ] RV-Existenz geprüft → aktualisieren **oder** neu
- [ ] KV `2010009171`: Volumen 20.000 €, bis 31.12.2026
- [ ] RV `1260543850`: Volumen 20.000 €, Laufzeitbeginn geklärt 🔎, bis 31.12.2026, ADRID gesetzt
- [ ] Preis-Modell geklärt (Pauschal 20.000 € vs. Einzelpreise) 🔎
- [ ] Elemente/Texte/Matrizen `PLO_<DaimlerADRID>_1260543850_<Element>` (falls Variante 2)
- [ ] Testsendung mit RV → LA korrekt → PROD

---

## 6. Offen / zu bestätigen (nicht aus den PDFs ableitbar)
- **ADRID** beider Rechnungsempfänger (BMW `810035000` nur als belegter Kandidat; Daimler offen).
- **RV-Schlüsselwahl** (BMW `F4F691P`; Daimler `1260543850`) an bestehender Matrix gegenprüfen.
- **Pauschal vs. Budgetdeckel** — bestimmt, ob überhaupt Preis-Matrizen zu bauen sind.
- **KV/RV bereits vorhanden?** — entscheidet aktualisieren ([F7]) vs. neu anlegen.
- **Laufzeitbeginn Daimler** (nur Ende 31.12.2026 im PDF; Abschluss 10.03.2026).
