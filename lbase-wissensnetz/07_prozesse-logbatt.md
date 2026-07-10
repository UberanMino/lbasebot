# 07 – LogBATT-Gesamtprozess (Prozessdokumentation L3)

← [Index](00_INDEX.md) · verwandt: [08 Digitaler Sendungsakt](08_digitaler-sendungsakt.md) · [06 Bedienung](06_bedienung-shortcuts.md)

Quelle: **L3 – LBase Prozessdokumentation V1** (Stand 04.12.2024). Beschreibt den
End-to-End-Ablauf bei **LogBATT GmbH** (Konzern **Lagermax AG**), mit Rollen, Werkzeugen
und Systemen (**SAP**, **L-Base PROD/DEV/MD-PROD**, **Driver App**, **DocuWare**, Ticketsystem **OTOBO::ITSM**).

> ⚠️ **Aktualität:** Dieser Baustein gibt den Stand **04.12.2024** wieder. Der **neuere**
> operative Ablauf (Stand **12.11.2025**) steht in [16 Aktueller Prozess](16_aktueller-prozess-handbuch.md)
> und konkretisiert v. a. Angebot, Track & Trace, Sammelfaktura, Eingangsvorschau,
> Gruppenauswertung. Bei Abweichungen gilt **[16]**.

## Systemlandschaft (Kontext)

- **SAP** – führendes System für **Stammdaten** (Kunden, Lieferanten). Übertragung nach
  **L-Base via Schnittstelle automatisiert alle 15 Minuten**. Abrechnungen gehen aus
  L-Base zurück an SAP.
- **L-Base** – operatives TMS (Angebot, Sendung, Disposition, Abrechnung).
  Umgebungen: **PROD** (produktiv), **DEV** (Entwicklung/Anlage Rahmenverträge, Verkehrsmittel),
  **MD-PROD** (Stammdaten/Monatsabschluss).
- **Driver App** – Fahrer: Scannen der Boxen bei Aus-/Eingang, Infos zu Absender/Empfänger.
- **DocuWare** – Belegarchiv (Eingangsbelege).
- **Ticketsystem** – **OTOBO::ITSM 10**, Support über `support@lagermax.com`.

## Rollen

Vertrieb (Innen-/Außendienst) · Auftragsmanagement / Vertriebsinnendienst · Disposition ·
Produktion / Fahrer · FiBu (Finanzbuchhaltung) · **Key-User** (1st-Level-Support, Anlage,
Datenpflege, Schulung) · Lagermax (Projektteam) · Entwicklung.

## Prozessschritte I–XIX

| Nr. | Schritt | Kern | Verantwortlich |
|---|---|---|---|
| I | **Kundenstammdaten Anlage SAP** | Neukunden/Änderungen in SAP → alle 15 Min. an L-Base. Nötig: Name+Rechtsform, gültige UID, Adresse, Zahlungsbed., Branche+Vertriebler, USt-Satz | Vertrieb |
| II | **Lieferantenstammdaten Anlage SAP** | analog Kunden + gültige **Bankverbindung** | FiBu |
| III | **Rahmenverträge / Abrufaufträge** | Preisblätter + Budget/Laufzeit in L-Base; Übergabe per Vorlage „Vorlage_neuer Rahmenvertrag“; Excel-Übersichten Volumen/Parameter | FiBu, Vertrieb, Key-User (unterstützend) |
| IV | **Verkehrsmittel/Transportboxen, Angebotszusatztexte, Batteriedatenbank** (Datenpflege) | Neue Verkehrsmittel/Boxen; nötig: Kennzeichen+Dienstleister/Boxen-ID, Abmessungen, EK-Tagespauschale/Eigenkosten pro km | Key-User (Produktion, Dispo, Entwicklung) |
| V | **Weiterentwicklung/Anpassung System** | Überwachung + neue Funktionen; Abstimmung mit Lagermax-Projektteam | Key-User, Lagermax |
| VI | **Einweisungen** | Initiale Unterweisungen, Prozessunterlagen/Anleitungen; Schulungen bei neuen Features | Key-User, Anwender |
| VII | **Support** | Key-User = **1st Level**; Ticket per Mail oder Portal. Ablauf: *Ticket erstellen → Service „Logistics“ → „LogBATT“ → Betreff/Inhalt → immer Ticket-ID angeben* | Key-User |
| VIII | **Angebot erstellen** | Checkliste/Bestellung prüfen → **Modulabfrage** in L-Base → alle Parameter erfassen → Kalkulation über LA-Übersicht **„LA ausführen“** → Angebot senden. **Gültigkeit 14 Tage** | Auftragsmanagement / VID |
| IX | **Sendung/Beauftragung erfassen** | Angenommenes Angebot → in **Einzelsendung** übertragen (**irreversibel!**) → Auftragsbestätigung → Dispo | Auftragsmanagement / VID |
| X | **Angebot absagen** | Ablehnung → Status **„negativ erledigt“** + Ablehnungsgrund im Auswahlfeld | Auftragsmanagement / VID |
| XI | **Disposition** | Einzelsendung erscheint in Dispomaske, bereit zur Einplanung | Disposition |
| XII | **Tagesfahrt erstellen** | siehe Detailablauf unten | Disposition |
| XIII | **Transport & Doku in Driver App** | Boxen bei Aus-/Eingang scannen; Fahrer erhält Absender-/Empfängerinfos | Produktion / Fahrer |
| XIV | **Vorbereitung Abrechnung** | Fahrt → Status **„in Kontrolle“**; alle abrechnungsrel. Belege (Abliefernachweis, Entsorgungsnachweis, Abfalldoku …) sammeln + **auf der Sendung archivieren** (→ [08]); Prüfung; Info an FiBu | Dispo, Auftragsmanagement |
| XV | **Abrechnungserstellung** | Vier-Augen-Prinzip; Abrechnungs-LA über **„LA-nachbearbeiten“** im **Testdruck** prüfen → Typ **„Rechnungserstellung“** → automatisiert an SAP; Versand mit rechnungsrel. Dokumenten | FiBu |
| XVI | **Monatsabschluss** | Buchhalt. Monat **zuerst in L-Base, dann in SAP** schließen. In **L-Base MD-PROD**: *Organisation → Organisationseinheiten → Filter Konzern „Lagermax AG“ / Firma „LogBATT GmbH“ → Buchungs-/Kostenrechnungsperiode anpassen* | FiBu |
| XVII | **Eingangsbelege** | Lieferantenrechnungen in L-Base erfassen → Fahrt/Sendung zuordnen → freigeben; indirekte Kosten → dedizierte **Monatsfahrt**. Ablauf: *Belege erfassen → analog Eingangsrechnung → zuordnen → Spesencode prüfen* | Auftragsmanagement |
| XVIII | **Reporting** | Anzeigenart **„Sendungsauswertung“** → wöchentliches Excel für Vertrieb; zusätzlich **BI-Dashboard** | Vertrieb |
| XIX | **Gruppenauswertung** | Anzeigenart **„LOGBATT Abrechnungsgruppen“** → Auswertungen je Abrechnungsgruppe | FiBu |

## Detailablauf XII – Tagesfahrt erstellen

Rechtzeitig (spätestens Nachmittag des Vortags) alle geplanten Tagesfahrten anlegen:

1. In Dispositionsmaske **„neue Fahrt“** wählen.
2. Tagesfahrt benennen nach Schema **„Fahrer/Datum“**.
3. Korrektes **Verkehrsmittel** wählen.
4. **Eingangsvorschau** erstellen: Fahrt in Dispomaske wählen → über **„LA-Erfassung“**
   den Typ **„Eingangs-Vorschau“** erfassen.
5. Fahrt disponieren → **FSW „in Vorbereitung“** → **Transportbox** wählen.
6. Fahrt über **FSW „auf Fahrt“** setzen.
7. Nach Transport → **FSW „in Kontrolle“** + Hinweis an Auftragsmanagement (bereit zur
   Abrechnung). *(FSW = Fahrtstatuswechsel; löst Bibliothek „Fahrten – Statuswechsel“ aus, → [02])*

## Bezug zum Logikinterpreter

Viele dieser Schritte lösen **LI-Module** aus (siehe [02] Mapping):
- **Angebot/Sendung erfassen** → *Sendungs-Vorgabe* ([F4]) + beim Speichern *Sendungs-Prüfmodule* + *Sendungs-LA-Modul*.
- **LA ausführen / Abrechnung** → Bibliotheken *Abrechnung* / *Abrechnungs-Einstellungen* → **Resultatset**.
- **Disponieren / Fahrtstatuswechsel** → *Disposition* / *Fahrten – Statuswechsel*.
- **Tagesfahrten (wiederkehrend)** → *Fahrten*-Module ([09] Fahrten).
