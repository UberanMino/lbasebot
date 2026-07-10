# 16 – Aktueller LogBATT-Prozess (Handbuch 2025)

← [Index](00_INDEX.md) · verwandt: [07 Prozess L3 (2024)](07_prozesse-logbatt.md) · [12 LBase 21st](12_lbase-21st-bedienung.md) · [14 LOGBATT Abrechnung](14_logbatt-abrechnung-konditionen.md)

Quellen: **LBase_Einführung** (PPTX, 27 Folien, „Das ist neu / GoLive“) + **LBase_Prozess­schritte
v1.7** (PPTX „L-Base Handbuch“, **Stand 12.11.2025**, 68 Folien). Dies ist der **aktuellste**
operative Ablauf und **aktualisiert** die ältere Prozessdoku L3 (Stand 04.12.2024, → [07]).

> Referenzen als *„Einf. F<n>“* (Einführung) bzw. *„Proz. F<n>“* (Prozessschritte-Folie).

## Kernaussage: SAP → L-Base Verlagerung (Einf. F3–4, F9–17)

Vieles wandert **von SAP nach L-Base**. **SAP bleibt** führend für **FiBu** und
**Kundenstammdaten**. **Neu in L-Base:**
- **Artikel → Spesencodes** (statt SAP-Artikel 4…/8…).
- **Angebote, Auftragsbestätigung, Rahmenverträge, Auswertungen** in L-Base (früher SAP).
- Auswertungen jetzt auf **Spesencode-Ebene** (statt Artikelebene).
- Kostenstelle **KST 4000** = Transportdienstleistungen.

## Rollen: User & Key-User (Einf. F5–6, Proz. F60)

**Key-User-Verantwortung:** 1st-Level-Support inhouse · Weiterentwicklung/Optimierung ·
**Stammdatenpflege** (Behälter, Scanner/Driver App, User, Verkehrsmittel, Reportings) ·
Face-to-IT Lagermax (KeyUser/Monatsmeetings) · **Ticketerstellung** bei Bugs/Weiterentwicklung ·
Unterstützung bei RV-Anlage. *(Key-User u. a.: Lisa Hoffmann, Rosa De Rosa, Elias Neumann.)*

## Vertrieb (Proz. F2–11)

1. **Kundenstammdaten (SAP):** Neukunde direkt als **Kunde inkl. Rechnungsanschrift** anlegen
   (Interessent-Status entfällt). **Immer Rechnungsanschrift + Vertriebsmitarbeiter** zuordnen;
   Branchen ergänzen.
2. **E-Mail** für digitalen Rechnungsversand hinterlegen.
3. **Rechnungsadresse:** Textfeld **max. 35 Zeichen** – alle vorgesehenen Felder nutzen.
4. **Bemerkungen** für Rechnungsversand hinterlegen.
- **Reporting:** wöchentlich über Sendungsübersicht **AZA „LogBATT Sendungsauswertung“**;
  Kommentare/Stornogründe in Spalte **„Interner Text“**.
- **Stand Rahmenverträge:** über **„Adressen“ + Filter** selbst prüfbar; auslaufende RV **gelb**,
  Restbudget **< 20 %** zusätzlich hervorgehoben.
- **Entsorgerübersicht:** Auswertungen zu Entsorgermengen, Gefahrstufe, Batteriezustand.

## Logistik / Auftragsmanagement (Proz. F12–25)

- **Vorholung / Angebot anlegen:** in der Maske mit **Enter** die LIM-Abfrage starten
  (Batterien/Module/Zellen? Batterie wählen? Einstufung?). **Referenz:** bei RV **„-“**,
  bei Einmalangebot die **echte Referenz** eintragen.
- **Zeilenlogik LogBATT-Kisten:** je LogBATT-Kiste eine Zeile; je Zeile `*` eine zugehörige
  **Inhaltszeile `**`** (Box darf auf Sendung nicht leer sein). **Fremdkisten** dürfen
  zusammengefasst werden.
- **Gewicht:** Gesamt-**Netto**gewicht der Einheiten (nicht Einzelgewicht der Zellen).
- **Angebot prüfen:** „Fertig“ → **F2** erneut einsehen → **LA-Übersicht** → **Kalkulation →
  „LA ausführen“** (immer mit „Fertig“ beenden); ggf. manuelle Anpassung.
- **Änderungen** nur im Typ **„Angebot“** → „Ändern“ (z. B. Stapler `Stap ABH = Y`) → „Fertig“.
  **Angebotsbindefrist 14 Tage** (fix, künftig evtl. dynamisch).
- **Annahme:** Sendung „ändern“ → Typ **„Einzelsendung“** → an Disposition. **Nicht umkehrbar**
  (Einzelsendung = Kundenbestätigung). Spätere Änderungen → **stornieren** + neu anlegen.
- **Absage:** Status **„negativ erledigt“**.
- **Angebotstexte** auf **MD-DEV** anpassen → zieht nach **15 min** automatisch auf PROD.
- **Archivierung:** Bestelleingang (Mail/PDF) **immer per Drag & Drop** auf die Sendung als
  **„Kundenbestellung“** (→ [08]).

## Disposition (Proz. F26–28)
Neue Fahrt → **Wochenfahrt** befüllen, korrektes **VKM** + **KST 4000** → Sendung(en) per
**Drag & Drop** disponieren → Rechtsklick **Statuswechsel „in Vorbereitung“** (Box-Abfrage) →
**„auf Fahrt“** (nicht umkehrbar) → nach Abschluss **„in Kontrolle“** (Abrechnung kontrolliert,
an Finance) → **Hinweis an Logistik**.

## Track & Trace (Proz. F29–34)
- **Auf Adresse hinterlegen:** Kunde unter „Adressen“ → Modulaufruf → **„TrackTrace“** → „Neu“
  + E-Mail. E-Mail-Versand: Modulaufruf → **„Zusatzattribute“** → über [F5] `Y/N`.
- **Auf Sendung:** jede Einzelsendung erhält automatisch einen **Track & Trace-Link**; Kunde
  bekommt Mail mit Verfolgungslink (Status via Driver App / manuell).
- **Sendungs-Stati** (über Statuswechsel der Fahrt, „zugewiesen“ + speichern):
  „In Vorbereitung“ (immer) · **„Abgeholt“ = AB & 290** · **„Unterwegs“ = ZU & 001** ·
  **„Im Auslieferdepot“ = ZU & 053** · **„In Zustellung“ = ZU & 54** ·
  **„Erfolgreich zugestellt“ = ZU & 012**. Datum = Zeitstempel.

## Abrechnung (Proz. F35–45)

1. **Belege** auf Fahrten/Sendungen zuordnen; nicht zuordenbare Kosten → **Monatsfahrt**.
2. Fahrt im Status „auf Fahrt“, Woche vorbei → Info Dispo → Auftragsmanagement.
3. Auftragsmanagement **prüft Abrechnungs-LA**, korrigiert (mit „Fertig“), lädt relevante
   **Anlagen als Archivdokument** auf die Sendung, setzt Fahrt **„in Kontrolle“** → Mail an FiBu.
4. **FiBu** druckt **Testrechnung** (autom. Filter); wenn i. O. → Druck **„Ausgangsrechnung
   ohne Anhang“**. Alle Rechnungen einer Fahrt abgerechnet → Fahrtstatus **„abgeschlossen“**.
5. **Generieren:** **„LA-nachbearbeiten“** → Filter **„Rechnung Testdruck“** prüfen → erneut
   „LA-nachbearbeiten“ → Filter **„Rechnung PDF“** → finale Abrechnung → automatisch an **SAP**.
- **Korrekte Bestellnummer** essenziell (muss in die Rechnung).
- **Offene Abrechnungen:** LA-Abfrage zeigt noch nicht gedruckte/verbuchte Abrechnungs-LAs.
- **Abrechnung anpassen inkl. Status:** bisherige Abrechnungs-LA → Anzeigen → **Datensatz
  kopieren** → anpassen.

### Sammelfaktura (Proz. F38–41)
Abrechnungs-LA ausführen → **„Setzen Feld“**: `S` = Sammelfaktura, Kundenkennzeichen,
`M` = monatlich, `L` = lang; **Trennkennzeichen** je Standort (z. B. `361` = Oberderdingen) –
alle Sendungen einer Sammelrechnung brauchen **dasselbe Kennzeichen** → „ok“. Druck starten →
Infos hinterlegen → „ok“. Sammelfaktura-Stammdaten in **MD PROD**.

## Buchungsperiode / Monatsabschluss (Proz. F48–50)
In App **„MD Prod“**: **Organisation → Organisationseinheiten** → Filter → „Ok“ → **Buchungs-
& Kostenrechnungsperiode** anpassen. ⚠️ Außerhalb der Periode können **keine Abrechnungen
(EB/AB)** erstellt werden.

## Eingangsrechnung / Eingangsvorschau (Proz. F51–54)
Jede Fahrt erhält eine **Eingangsvorschau (EV)** mit erwarteten Kosten. Erfassen: Fahrt →
**„LA-Erfassung“** → EV-LA. **Auflösen:** Eingangsbeleg erfassen → **„Zugeordnete
Eingangsvorschau“** → korrekter Fahrt zuordnen, Betrag mit EV abgleichen (sonst EV anpassen).
Werte über LA-Übersicht → EV → „LA ausführen“ → „Spesencode einfügen“ → erwartete Kosten → Fertig.

## Produktion / Fahrer (Proz. F57–59)
- **Hallenscannung:** Benutzerkennung → Barcode **Fahrt** scannen → Barcode **Box** → **[F6]**
  am Handscanner → Tabelle „Soll–Ist“ → **„J“** + Enter → Fortfahren „Ja“.
- **Touren-App (Fahrer):** Sendung → Be-/Entladehinweise → „Abholung erledigt“ → Name +
  Unterschrift → Fahrt durchführen → Zustellung: Sendung wählen → Barcode-Scan → „Packstück
  ok?“ → „Zugestellt“ → Name Empfänger + Unterschrift.

## Adressen-Konvention (Proz. F56, Einf. F27)
**Kunden „81…“** · **Lieferanten „87…“** · Rechnungsempfänger Kunde immer an **„810…“**.

## Gruppenauswertung / Abrechnungsgruppen (Proz. F64–68)
Bei **manuellen** Angebots-/Abrechnungsänderungen werden Spesencodes teils **nicht automatisch**
der Abrechnungsgruppe zugeordnet (Textfeld ≠ 1:1). Korrektur: **LA-Auswahl** → Filter →
Abrechnungszeilen (mit/ohne Gruppe) → Cursor in editierbare Spalte → **[F5]** passenden Eintrag
→ Speichern (mehrere Zeilen möglich; nach „leerer“ Gruppe filterbar). *(Vgl. [07] Schritt XIX.)*

## Ticketsystem (Proz. F61–62)
Mail an **support@lagermax.com**, Betreff beginnt mit **„LB_Lbase21st“** → Rückmail mit
**Ticketnummer** (bei Folge-Mails Ticketnummer **an erster Stelle** im Betreff). Alternativ
**Ticketportal** (`support.lagermax.com/otobo/...`) → Service **„LogBAtt“**.

## GoLive-Hinweise & offene To-Dos (Einf. F22–23, F27)
„Shit in – shit out“ (Stammdaten/Angebote sauber halten) · Leistungsdatum = **Ende der Fahrt** ·
RV extern über Axians (1 BT = 1.200 €) · neue Mail **lbase@logbatt.de** · Bestellnummer muss
auf Sendung liegen und automatisch in die Rechnung. Projektphase 2: **Driver App & Scannung,
Batteriedatenbank, Fahrt-/Sendungs-Referenz auf Abrechnungen**.

## Verhältnis zu [07]
[07] = Prozessdoku L3 (Stand 04.12.2024), Schritte I–XIX. **[16] ist neuer (12.11.2025)** und
konkretisiert/aktualisiert v. a. Angebot, Track & Trace, Sammelfaktura, Eingangsvorschau und
Gruppenauswertung. Bei Widersprüchen gilt **[16] (aktueller)**.
