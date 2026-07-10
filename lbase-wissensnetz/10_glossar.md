# 10 – Glossar & Abkürzungen

← [Index](00_INDEX.md)

## Begriffe (LIS / Logikinterpreter)

| Begriff | Bedeutung |
|---|---|
| **lBase / L-Base** | Logistik-/Speditionssoftware (Hersteller Axians ICT Austria). Bei LogBATT im Einsatz |
| **LIS** | Logikinterpreter-System – steuert den Workflow über Module ([01]) |
| **LI** | Logikinterpreter – Kern des LIS, führt Modul-Anweisungen aus |
| **ME** | Moduleditor – Werkzeug zum Schreiben von Modulen |
| **Compiler** | Wandelt Modul in Bytecode um |
| **TMS** | Transport Management System (Trägerapplikation von lBase) |
| **WMS** | Warehouse Management System (Trägerapplikation von lBase) |
| **Modul** | Struktur aus Startbedingung(en) + Version(en) + Modulcode ([03]) |
| **Bibliothek** | Vom System vorgegebene Gruppierung von Modulen je Tätigkeit ([02]) |
| **Kriteriumssatz** | Zusammenfassung von Kriterien zur Eingrenzung der Modulsuche ([02]) |
| **Fixe Suche (F)** | Alle passenden Module laufen (können sich überschreiben) ([02]) |
| **Alternative Suche (A)** | Erster passender Kriteriumssatz gewinnt ([02]) |
| **Startbedingung** | Mini-Modul (Start/Stopp), entscheidet ob Modul läuft; **bedingungslose** als Fallback ([03]) |
| **Version** | Modulstand mit Gültigkeitszeitraum (dürfen sich nicht überschneiden) ([03]) |
| **Format** | „Befehl“ der LI-Sprache im Moduleditor ([04]) |
| **Variable** | Wertespeicher, beginnt mit `#`, GROSS ([05]) |
| **Hilfsvariable** | Globale Zwischenspeicher `#ZUFELD…`, `#AKKU…` ([05]) |
| **Resultatset** | Ergebnis eines Abrechnungslaufs (Basis für Rechnung) ([04]) |
| **Spesencode** | Leistungsart, Basis der Verrechnung ([04]) |
| **Spesencodemodul** | Pflichtmodul je Spesencode; ohne kann nicht abgerechnet werden ([04]) |
| **Matrix** | Ein-/zweidim. Anordnung von Berechnungselementen (Tarife) ([04]) |
| **Akkumulationszeile / AKKU-Zeichen** | Fasst Spesencodes auf der Rechnung zusammen ([04]) |
| **MFD** | Mehrfelddialog (4 Formate: Anlegen/Setzen Feld/Start/Löschen) ([04]) |
| **LIM** | Aufruf eines LI-Moduls über das Feld „LIM“ (Kriteriumssatz LI-Modul und Abteilung) ([09]) |
| **LI-Testmodus** | Debug-Fenster mit Hintergrundinfos beim Modulablauf ([01]) |
| **LI-Debug in Datei** | Umleitung der Debug-Infos in eine Datei ([01]) |
| **3GL/4GL-Code** | 4GL = Modulsprache; 3GL = aufgelöster ausführbarer Code (`#include` aufgelöst) ([03]) |

## Sendungs-/Speditionsbegriffe

| Begriff | Bedeutung |
|---|---|
| **Sendung / SDG** | Transportauftrag |
| **SDB / Sendungsbeschreibungszeile** | Positionszeile einer Sendung; Ebenen: Colli (1) / Verpackung (2) / Artikel (3). Variablen mit `ZEILE_` ([05]) |
| **LA / Leistungsanforderung** | Zentrales Workflow-Instrument; Typen: Abrechnung, Transport, Ausdruck; + Statuswechsel ([04]) |
| **LA-Untertyp** | Feinklassifizierung einer LA / Beschlagwortung im E-Akt ([08]) |
| **Frankatur** | Frachtzahlungsbedingung (z. B. 900 „frei Haus“) – `#FRANK` |
| **Übernahme(code)** | Art der Übernahme (z. B. Direktabholung) – `#UEBCODE` |
| **Weiterleitung(scode)** | Art der Weiterleitung/Zustellung (z. B. Direktzustellung, Selbstabholer) – `#WLCODE` |
| **Relation** | Verkehrsrelation/Tarifzone – `#RELATION` |
| **Verkehr** | Verkehrsart (z. B. Inland ausgehend, EU ausgehend, Ladung) – `#VERKEHR` |
| **Ladung / LDG** | Zusammenfassung von Sendungen (Schleifen LDG_FAHRT, SDG_LDG_U) |
| **Fahrt / FAR** | Transportfahrt |
| **FSW** | Fahrtstatuswechsel (geplant → in Vorbereitung → auf Fahrt → in Kontrolle) ([07]) |
| **Frachtpflichtiges Gewicht** | Zur Verrechnung herangezogenes Gewicht (Mindestgewichte je Verpackungsart) ([09]) |
| **Verpackungsarten** | PAL Palette, EUP Europalette, PRP Privatpalette, EWP/EWP Einwegpalette, PRP, EUP, Colli … |
| **Entfernungswerk** | Datenbasis zur Distanz-/Mautberechnung ([04]) |

## Prozess-/Organisationsbegriffe (LogBATT)

| Begriff | Bedeutung |
|---|---|
| **LogBATT GmbH** | Kunde/Betreiber; Batterie-Logistik. Konzern **Lagermax AG** |
| **Lagermax** | Dienstleister/Projektpartner; Support & Betrieb |
| **Key-User** | 1st-Level-Support, Anlage/Datenpflege/Schulung ([07]) |
| **SAP** | Führendes Stammdatensystem; Schnittstelle zu L-Base alle 15 Min. ([07]) |
| **L-Base PROD / DEV / MD-PROD** | Produktiv- / Entwicklungs- / Stammdaten-Umgebung ([07]) |
| **Driver App** | App der Fahrer (Scannen, Infos) ([07]) |
| **DocuWare** | Belegarchiv ([07]) |
| **OTOBO::ITSM** | Ticket-/Supportsystem (`support@lagermax.com`) ([07]) |
| **FiBu** | Finanzbuchhaltung |
| **VID** | Vertriebsinnendienst |
| **E-Akt / EPaper** | Digitaler Sendungsakt – Dokumentenablage in L-Base ([08]) |
| **BI-Dashboard** | Business-Intelligence-Auswertung über L-Base-Daten ([07]) |

## LA-Untertypen (E-Akt-Pilot) & Betreff-Kürzel

Siehe ausführlich [08]:
- **Untertypen:** SAB (Ablieferbeleg) · BELE (Beleg) · EX (Extern) · SSD (Schaden).
- **Betreff-Kürzel (Auswahl):** POD, LMS, LDL, ER, AR, GFG, ZOL, LDA, FRB, MES, UMZ, PIC,
  DOK, FIM, SVB, ANG, KUD, KOR.

## LBase 21st & LOGBATT (Batch 2)

| Begriff | Bedeutung |
|---|---|
| **LBase 21st** | Aktuelle Systemgeneration bei Lagermax/LogBATT (→ [12]) |
| **LIM** | Logikinterpreter-Modul – geführte Sendungserfassung per Dialog (→ [13]) |
| **GenTab** | Generische Tabelle zur Verhaltenssteuerung durch Key-User/IT (→ [13]) |
| **Wertebereich** | Zulässige-Werte-Liste, z. B. `X_LOGB_BER`, `X_LBATT_EINH` (→ [13]/[14]) |
| **Komponente** | Übertragungs-/Zuordnungseinheit, z. B. `Cust_LOGBATT`, `Cust_LMX` (→ [13]) |
| **TPV** | Transportvariante (Sendungs-Pflichtfeld, z. B. FTL/LTL/FCL) (→ [12]) |
| **Kostenstelle** | In 21st das Feld, das die **alten „Relationen“** ersetzt (→ [12]) |
| **Verkehr(styp)** | INA/EUA/EXP/LAD… (→ [12]) |
| **Frankatur/INCOTERM** | numerischer Code der Abrechnungsart, z. B. 900 Frei Haus (→ [12]) |
| **Fahrttyp** | Eigen-/Partner-/Charterfuhrpark, Interne Fahrt, Verteilung (→ [12]) |
| **FSW** | Fahrtstatuswechsel: in Vorbereitung → auf Fahrt → in Kontrolle → abgeschlossen (→ [12]) |
| **EV / Eingangsvorschau** | erwartete Kosten je Fahrt, später mit Beleg abgeglichen (→ [12]/[16]) |
| **DFÜ** | Datenfernübertragung (wird bei FSW „auf Fahrt“ ausgelöst) (→ [12]) |
| **CMR** | Internationaler Frachtbrief (1:1 aus Sendung) (→ [12]) |
| **Sendungsowner** | Eigentümer der Sendung, rechnet Leistungen ab (→ [12]) |
| **Sammelfaktura** | Sammelrechnung über mehrere Sendungen (Kennzeichen S/M/L + Trennkennz.) (→ [16]) |
| **AZA / Anzeigenart** | Auswertungs-/Anzeigeform, z. B. „LogBATT Sendungsauswertung“ (→ [16]) |
| **Kondition** | Preis-/Abrechnungsregelwerk; „PLO – allgemeine Kondition“ = LOGBATT-Start (→ [14]) |
| **Spesenblock (SPB)** | Modulblock der Abrechnung, z. B. LOGBATT_SDG_BESCH_DATEN (→ [14]) |
| **Berechnungs-Element** | LOGBATT-Abrechnungsbaustein (X_LOGB_BER), z. B. TRANS, VERP, RECYC (→ [14]) |
| **Stammdaten-Matrix** | Preis-Matrix `PLO_<Adr>_<RV>_<Element>` (→ [14]) |
| **RV / Rahmenvertrag** | Kundenvertrag mit hinterlegten Konditionen/Preisen (→ [14]/[16]) |
| **Einmalangebot** | Abrechnung ohne RV (Stückpreis per Dialog anpassbar) (→ [14]) |
| **VEPID** | Verpackungs-/Behälter-ID (z. B. TRO Trockeneis, HEBGE Hegegestell, VERMI) (→ [14]) |
| **ORGID / AG_ID / ADRID** | Organisations-/Arbeitsgruppen-/Adress-ID (→ [12]/[13]) |
| **DBI** | Deckungsbeitrag I (Tagespreis-Berechnung) (→ [15]) |
| **ZO / SV (Zoll)** | Zollgut / Selbstverzoller (Sperrgründe) (→ [15]) |

## GenTab-Kürzel (Schnellindex → [13])
LMX_SDG_VAR (Pflichtfelder) · LMX_STD_VAR · LMX_SDG_LIM · LMX_SDG_AUTO · LMX_FAR_AUTO ·
LMX_FAR_SDGA · LMX_FAR_DR · LMX_SDG_DR · LMX_SOT_VKM · LMX_SCANNER · LMX_ZOLLDIEN ·
LMX_LBATT_KO · LMX_LBATT_TX.

## Codierungen zum Nachschlagen

- **Wochentage (Zeitberechnung Wochentag):** 1=So, 2=Mo, 3=Di, 4=Mi, 5=Do, 6=Fr, 7=Sa. ([04])
- **1 Tag = 1440 Minuten** (Zeitberechnung addieren). ([04])
- **Variablen-Typ-Präfixe:** `ln_/ls_/ld_` (lokal num/string/datum), `gn_/gs_/gd_` (global). ([05])
- **Dialog-Art:** `A` = alphanumerisch, `N` = numerisch. ([04])
