# 12 – LBase 21st: Grundlagen & Bedienung

← [Index](00_INDEX.md) · verwandt: [13 LIM & GenTabs](13_lim-und-gentabs.md) · [16 Aktueller Prozess](16_aktueller-prozess-handbuch.md) · [06 Bedienung](06_bedienung-shortcuts.md)

Quelle: **Handbuch LMX 21st – Allgemein** (Version 4, Stand 09/2024, 56 S.). Das ist die
Doku der **aktuell eingesetzten Systemgeneration „LBase 21st“** (Lagermax-Konzern, damit
auch LogBATT). Ergänzt/aktualisiert die generischen T102-Grundlagen und den älteren
Prozess ([07]).

> **Wichtige Terminologie-Neuerungen ggü. den T102-Unterlagen:**
> - **Kostenstelle = alte „Relationen“** (Fahrt, S.31).
> - **TPV = Transportvariante** (neues Pflichtfeld der Sendung).
> - **LIM** = geführte Sendungserfassung per Dialog (zentral, → [13]).
> - Viel Verhalten wird über **GenTabs** (generische Tabellen) gesteuert (→ [13]).

## Grundsätze von LBase 21st (S.3)

1. Transport-Aufträge werden als **Sendung** erfasst.
2. Jede Sendung hat einen **Sendungsowner**, der alle Leistungen an Auftraggeber/
   Rechnungsempfänger abrechnet.
3. Interne Dienstleistungen → **interne Abrechnung**.
4. **Bruttonutzen** ist auf **Sendungsebene** sichtbar.
5. Alle Transportstrecken werden über eine **Transport-LA** auf eine **Fahrt** disponiert.
6. Für in Anspruch genommene Leistungen wird eine **Eingangsvorschau (EV)** erstellt.
7. Rechnungen haben **immer einen Träger** – Fahrt oder Sendung.

**Drei Wege der Sendungserfassung (S.3):** 1) **EDI**, 2) **ePortal**, 3) **Manuelle Anlage**
(im Projekt 21st so definiert, dass gängige Varianten über einen **LIM** erstellt werden).

## Anmeldung & Arbeitsgruppe (S.4, S.6)

- Jeder User meldet sich in der **richtigen Arbeitsgruppe** an ([OK]/ENTER).
- Mehrere Arbeitsgruppen → LBase-Applikationen **parallel** öffnen.
- Sprache im Reiter **Sprache** umschaltbar.
- Arbeitsgruppe wechseln: **Optionen → Arbeitsbereich ändern**.

## Startseite TMS (S.5)
1) Navigationsübersicht der Themengebiete · 2) Kategorien zum Themengebiet ·
3) Menüband · 4) Shortcuts.

## Filter (S.7–9)
Gleiches Prinzip wie [06], hier für 21st bestätigt:
- **Druckerfilter**: LA → [LA ausführen] → Druckdialog → [Filter] → Bezeichnung + **Benutzer
  = man selbst** (sonst gilt der Filter für die ganze Arbeitsgruppe). Anzeige: manuell /
  überspringen (abgeraten) / Vorschlag.
- **Auswahlfilter**: Auswahlfenster einstellen → **[Filter verwalten]** → Bezeichnung +
  Benutzer → [OK].

## Adresse (S.10–11)
- Suche: **Adresse → Öffnen** oder im Adressfeld **[F5]** → Dialog „Adressen-Auswahl“.
- Kriterien: **Suchbegriff** (= Sortierbegriff, Anfang), **Name** (mit `%`-Platzhalter),
  **Staat** ([Suchen]), **PLZ/Ort**. Beispiel: `%Test%` findet alles mit „Test“.

## Sendung (S.12–27)

Eine Sendung = Transport von Absender- zu Empfängeradresse; erhält bei Erst-Erfassung eine
**konstante, eindeutige Sendungsnummer**.

**Aufruf:** Datei → Neu → Sendung · Sendung → Neu · Shortcut · oder in der Disposition
direkt auf der Fahrt (**Strg-Alt-E**).

### Sendungsmaske (S.14) 🖼
Nummerierte Bereiche: 1) **Pflichtfelder = gelb hinterlegt** · 2) Adresszeilen (AUFG/ABS/
EMPF/RECH) · 3) Sendungsbeschreibungszeile · 4) Hinweistexte (Aviso/Dispo/FiBu/Abholtext/
Auflagen/Beladung/Zoll/Zusteller/Abrechnung/Intern) · 5) Zusatzattribute · 6) div. Kennzahlen.
Rechts: **TPV, Übernahme, Weiterleitung, Frankatur** (gelb) sowie Kostenstelle/Kostenträger.

### Pflichtfeld-Codes

**Verkehr (S.15):** EUA EU ausgehend · EUE EU eingehend · EXP Export (Drittland) ·
FEX FCL-Export · FIM FCL-Import · FLV Flächenverkehr · IMP Import · INA Inland ausgehend ·
INE Inland eingehend · LAA Lager ausgehend · LAD Ladungen · LAE Lager eingehend ·
LCE LCL-Export · LCI LCL-Import · LUE Luftfracht Export · LUI Luftfracht Import ·
NAT National · ZOL Zollabfertigung.

**TPV – Transportvariante (S.16):** ABH_PAR Abholauftrag Partner · FCL · LCL ·
SG_AUS Sammelgut ausgehend (mit EMKO/SA) · SG_EIN Sammelgut eingehend (mit ABKO) ·
ABH_GRP Abholgruppe · FTL Direktfahrt einer Sendung · LTL Direktfahrt mehrerer Sendungen ·
KEP Kurier Express Paket · FLV Rollfuhr · AIR / AIR_AWB / AIR_HWB / AIR_MWB Luftfracht.

**Übernahme (S.16):** AN Nahverkehr (andere NL) · GA Abholgruppe · GP Paketdienst ·
DA Direktabholung (Direkt-LKW) · DD Abgangskorrespondent (von Partner angeliefert) ·
IA andere Abteilung · LA Lager · NV Nahverkehr · OA ohne Anmeldung · SA Selbstanlieferer.

**Weiterleitung (S.17):** 24 24PLUS · AN Nahverkehr (andere NL) · DP Direktzustellung (DFÜ
an Korr/Partner) · DZ Direktzustellung (Direkt-LKW) · EW Empfangskorrespondent ·
GP Paketdienst · IW andere Abteilung · LA Lager · NV Nahverkehr · OW ohne Weiterleitung ·
SA Selbstabholer · SW Sammelverkehre.

**Frankatur / INCOTERM (S.18):** numerischer Code legt die Abrechnungsart fest. Auswahl:
100 Ab Werk · 101 Unfrei gegen Rechnung · 200 Ab Magazin · 400–404 Frei Grenze
(Abgangsland, …) · 600 FAS · 601 FCA · 610 FOB · 620 C+F · 625 CFR · 630 CIF · 635 CPT ·
640 CIP · 645 DAF · 650 DES · 660 DEQ · 670 DDU · 680 DDP · 700–704 Frei Grenze
(Empfangsland) · 800–806 Frei Ankunft/Geliefert · 850 Frei Terminal · **900 Frei Haus** ·
901–904 Frei Haus (verzollt …) · 912 Dreiecksgeschäft · 990 Dienstgut mit Kosten ·
991 Dienstgut ohne Berechnung · 992 Abholauftrag.

### Sendungssuche (S.25) & „Ich finde meine Sendung nicht“
Genauer einschränken (Arbeitsgruppe, Datum). Ohne Sendungs-ID wird z. B. das **Datum zum
Pflichtfeld**. Nach **Benutzer (Ersteller)** filterbar. Tipp: Arbeitsgruppen-Feld **leeren**,
wenn unklar, dann erneut suchen.

### Storno Sendung (S.26–27)
Nur möglich, wenn **alle LAs „angefordert“ oder „storniert“** sind. Beim Storno werden alle
LAs storniert, Lademittelbuchungen gelöscht, Status = „storniert“, Grund (User/Datum/Zeit)
in den internen Text geschrieben.

## Fahrt (S.28–35)

Fahrt = Strecke mit Verkehrsmittel; erhält eine unveränderliche **Fahrt-ID**. Die **meisten
Fahrten sollen automatisch angelegt** werden (Varianten: „einfache“ Fahrtanlage / automatische
Dispo mit Fahrtanlage; gesteuert über GenTabs → [13]).

**Fahrt-Pflichtfelder (S.31):** 1) Fahrtbezeichnung · 2) Beginn/Ende · 3) Frachtführer
([F5] ADRID) · 4) **Typ** · 5) **Kostenstelle (= alte Relationen)** · 6) Verkehrsmittel
(nur bei Partnerfuhrpark Pflicht).

**Fahrttypen:** Eigenfuhrpark (eigenes Fzg) · **Partnerfuhrpark** (fester Partner → VKM
Pflicht) · **Charterfuhrpark** (Spotmarkt → VKM kein Pflicht) · **Interne Fahrt** (nur
scannen) · **Verteilung** (Eingang des Partners zum Scannen).

**Storno Fahrt (S.34):** Dispoübersicht → Rechtsklick → Statuswechsel/[F11] → „storniert“.
Vorher **alle Transport-LAs stornieren**.

**Eingangsvorschau (EV) der Fahrt (S.35):** bei Charterfahrt vorläufige Frachtkosten
erfassen (Abfrage beim FSW „in Vorbereitung“) → EV-LA. Später Abgleich mit der Frächter-
rechnung → bei Übereinstimmung Freigabe.

## Leistungsanforderung (LA) (S.36–37)
Träger: **Sendung / Fahrt / Beleg**. Typen: Transport, Abrechnung, Ausdruck.
Erfassung: in Sendung/Fahrt über „Leistungsanforderung → LA-Erfassung“ oder **Strg+E**
(idealerweise immer auf dem Träger). Druck: „LA Ausführen“ (Typ Ausdruck) → Druckdialog.

## Disposition (S.37) & Fahrtstatuswechsel-Workflows (S.38–40)

- Dispo: **Transport-LA per Drag & Drop** auf die Fahrt; fertig → **[F11] „in Vorbereitung“**.
- **FSW „in Vorbereitung“**: Dokumente werden erstellt – eingehende Ladungen → Ladeauftrag;
  ausgehende Ladungen → Beladeliste, CMR (Fahrt), Gefahrgutpapiere. *(GenTabs LMX_FAR_DR /
  LMX_SDG_DR, → [13])*
- **FSW „auf Fahrt“**: 1) **DFÜ** ausgelöst, 2) VKM bei Partner-/Charterfuhrpark geprüft
  (Charter: Kennzeichen in „Bez. VKM 1“). Nicht umkehrbar.
- **FSW „in Kontrolle“**: setzen, wenn Transport abgeschlossen → stößt **automatische interne
  Verrechnung** an.
- **FSW „abgeschlossen“**: Druck-LAs (angefordert/geplant) auf der Sendung werden storniert;
  Sendung auf **„positiv erledigt“**. *(Keine Prüfung des Abrechnungsstatus!)*
- **FSW „storniert“**: siehe Storno Fahrt.

## CMR (S.41–42)
Daten beziehen sich **1:1 auf die Sendung** → Änderungen möglichst in der Sendung, nicht im
CMR. Anpassungen via **Dynamic Frame**. Status: Angefordert → Erfasst („LA ausführen“
bearbeitbar) → Gedruckt (gesperrt). Adresse ändern: reinklicken → [F5] → **[Adr. Übernahme]**.

## Modulaufruf (S.43–44)
**Erweiterte Funktionen → Allgemeiner Modulaufruf**: Firma wählen, Modul per Name/[F5];
Option **„nur Firmenmodule suchen“** (Kriterium „Firma“). Beispiel: **Wechsel der
Transportvariante** – Fahrt markieren, Modul aufrufen; JA = Sendungen zurück in Dispotopf,
NEIN = bleiben auf Fahrt.

## Weitere Funktionen
- **Scanfortschritt (S.45):** Anzeigenart „LMX-Dispo mit Scanfortschritt/Zustellstatus“ →
  Spalten *Status Beladung/Entladung*: **Warten / Aktiv / Fertig**.
- **Belege (S.47–50):** Eingangsbeleg über Datei → Neu → Beleg; Belegsteller über **UID-Nr.**
  suchen. Externe RE-Nr. kürzen (DCW: keine Buchstaben/Sonderzeichen, max. 7 Stellen; z. B.
  `AR2024/000071` → `71`). Freigabe: Kosten Sendung/Fahrt zuordnen (manuell oder via EV) →
  Status „offen/erfasst“ → „freigegeben“ → nächtlicher Job → „verbucht“.
  Faktura-Arten: **Einzelfaktura, Fahrtfaktura, Sammelfaktura**.
- **TrackTrace (S.54–55):** Modul „Adresse ändern“ Auswahl **7 „TrackTrace“** (NEU/Ändern/
  Löschen). Bei Auftraggeber-Attribut „Track and Trace aktiv? = Y“ wird beim Sendungsspeichern
  ein **TrackTraceKey** vergeben; URL `https://eportal.lagermax.com/tracktrace21/#/de/shipment/ + Key`.
- **Driver App (S.51–53):** Handy in Wertebereich **LMX_SCANNER** anlegen (Komponente
  Cust_LMX); VKM anlegen; GenTab **LMX_SOT_VKM** ordnet VKM/Frächter/Handy je ORGID zu
  (Scanner nur an PKW/Bus/Motorwagen/LKW-GIGA). App via Store + QR-Code-DB-Config.
- **Export (S.56):** Zeilen markieren (oder alle) → Exportvorgang.
- **Archivierung (S.56):** Datei per **Drag & Drop** auf das LBase-Fenster (Sendung/Adresse/
  Fahrt/Beleg) → Ablagefenster (Name + Ablage-Typ) → Abruf über LA-Übersicht. → vertiefend [08].

## Für Key-User relevante GenTab-Verweise
Sendungs-Pflichtfelder **LMX_SDG_VAR** · autom. Fahrtanlage **LMX_SDG_AUTO** · autom.
Sendungsanlage **LMX_FAR_AUTO** · autom. Dispo **LMX_FAR_SDGA** · Dokumente Fahrt
**LMX_FAR_DR** / Sendung **LMX_SDG_DR** · Scanner **LMX_SCANNER** / **LMX_SOT_VKM**.
Vollständiges GenTab-Register → [13].
