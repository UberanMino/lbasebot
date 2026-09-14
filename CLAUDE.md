# CLAUDE.md — Arbeitsanweisung für dieses Repo (zuerst lesen)

Dieses Repo ist das **lBase-Wissensnetz** von LogBATT (`lbase-wissensnetz/`). Aufgabe von Claude:
Fragen zu lBase präzise beantworten **aus dem Wissensnetz** — nicht aus Allgemeinwissen raten.

## Grundregeln
1. **Erst lesen, dann antworten.** Vor jeder inhaltlichen Antwort die passende Doc im
   `lbase-wissensnetz/` öffnen (Einstieg: `00_INDEX.md`). Nicht aus dem Gedächtnis extrapolieren.
2. **Neuanlage ≠ Aktualisieren.** Das sind zwei verschiedene Prozesse. Bei „Rahmenvertrag
   **aktualisieren/verlängern**" **niemals** den Neuanlage-Weg beschreiben.
3. **Ehrlich bleiben:** Steht etwas nicht im Netz, sagen „steht nicht drin" — nicht erfinden.
   Belegte Fakten von Vermutungen trennen.

## ⭐ Rahmenvertrag (RV) aktualisieren/verlängern — Preise bleiben gleich → **4 Stellen**
Der **häufigste Regelfall**. Es werden **genau vier Stellen** angepasst, alle verschlüsselt auf
**RV + Rechnungs-ADRID**. Der Kopfvertrag **`LMX_LB_KV` gehört NICHT dazu**. Reihenfolge:

1. GenTab **`LMX_LB_RV`** — Rahmenverträge LogBATT (RV-Eintrag: Laufzeit/Volumen)
2. GenTab **`LMX_LBATT_KO`** — zu berechnende Einstellungen & Konditionen (Elemente je ADRID+RV)
3. GenTab **`LMX_LBATT_TX`** — LogBATT-Konditionstexte
4. **Matrizen `PLO_<ADRID>_<RV>_<Element>`** — Preise bleiben gleich (nur RV/Zeitraum)

→ je Stelle **[F7] Ändern → [F12]**, dann über Komponente **`Cust_LOGBATT`** DEV→PROD,
dann **Testsendung mit RV → LA ausführen**. Vollständig Stück für Stück:
**[18] „Bestehenden Rahmenvertrag aktualisieren/verlängern"** in
`lbase-wissensnetz/18_kopf-und-rahmenvertrag-anlegen.md`.

**Neuanlage** (KV + RV neu, mit Preisen): dieselbe Doc [18], Abschnitt „Schritt-für-Schritt".

## Belegte Schlüssel (Stand 2026)
- **BMW:** Rechnungs-ADRID **`810035000`**; RV-IDs im Format `F41946L`/`F3Y9KT1`/**`F4F691P`**;
  KV (Vertragskopfnummer) z. B. **89100**.
- **Daimler Buses:** RV/Abruf **`1260543850`**, KV/Einkaufsabschluss **`2010009171`**;
  Rechnungs-ADRID **noch offen** (nachschlagen).
- **Jungheinrich:** RV **`JH_OHNE`** (Hausschema `Kundenname_OHNE`, nur wenn keine offizielle
  Nummer existiert), Rechnungs-ADRID **`810988000`**.
- Konkrete Fälle BMW/Daimler 2026: `lbase-wissensnetz/19_rahmenvertraege-bmw-daimler-2026.md`.

## Noch offen (beim nächsten realen Durchgang eintragen, dann ist es feldscharf)
- **Exakte Feldnamen** je GenTab (`LMX_LB_RV`/`_KO`/`_TX`), die beim Verlängern geändert werden.
- Ob je Verlängerung eine **neue Gültigkeit/Zeile** oder ein **In-Place-Update** üblich ist.
- Rechnungs-ADRID Daimler Buses.
