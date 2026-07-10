# 09 – Übungs-Rezepte (Praxis-Muster)

← [Index](00_INDEX.md) · verwandt: [04 Formate](04_formate-referenz.md) · [05 Variablen](05_variablen-referenz.md) · [02 Bibliotheken](02_bibliotheken-kriteriumssaetze.md)

Quelle: **T102 Logikinterpreter – Übungen**. Sammlung typischer Aufgabenstellungen mit den
jeweils genutzten Formaten – als **Muster/Rezepte** für „Wie baue ich so etwas?“.

## A) Sendungsvorgaben (Bibliothek *Sendungs-Vorgabe*, [F4])

Auslöser: **[Vorgabemodul]** bzw. **[F4]** in der Sendungserfassung. Kriteriumssatz-Suche
nach Priorität (spezifisch → allgemein), dann Startbedingung, dann Version (→ [02]).
LIM-Aufruf = Aufruf über das Feld **LIM** (Kriteriumssatz „LI-Modul und Abteilung“).

| Bsp | Ziel | Kriteriumssatz | Genutzte Formate |
|---|---|---|---|
| 1 | Felder fix vorgeben (Relation 200, Frankatur 900, Zustelltext), Empfänger + SDB-Zeile (Inhalt „Autozubehör“, VP „PAL“) | Absender+Abteilung | *Vorgabe Feld, Vorgabe SDG-Adresse, Vorgabe SDG-Beschreibungszeile* |
| 2 | Bei bestimmtem Empfänger Auftragsnummer per Dialog in „Zeichen/Nummer“; Selbstabholer via Weiterleitungscode | Auftraggeber+Abteilung | *Entscheidung, Vorgabe SDB-Zeile (Dialog), Vorgabe Feld* |
| 3 | Stammkunde: Empfänger per Dialog wählen; je Empfänger unterschiedliche Vorgaben (Referenz `MM-…`, IV-Rechnungsempfänger, Währung, Relation, Empfängertext); „sonstige“ → Adresssuche | Auftraggeber+Abteilung | *Dialogentscheidungsblock, Vorgabe Feld/Adresse/SDB, Setzen Feld mit Textersatz* |
| 4 | Über LIM: fixer Absender, 3 Empfänger-Auswahl (+ Käufer-Adresse), Verkehr „EU ausgehend“, SDB-Felder per Dialog, Cursor zuletzt auf Frankatur | LI-Modul+Abteilung | *Dialogentscheidungsblock, Vorgabe SDG-Adresse, Setzen Feld (Dialog)* |
| 5 | Über LIM: Absender/Empfänger + mehrere SDB-Zeilen (Pflichtfelder) per **MFD**; Termine automatisch (heute+1 / heute+2) | LI-Modul+Abteilung | *MFD (4 Formate), Zeitberechnung addieren* |
| 6 | Über LIM: Absender/Empfänger per Dialog; Stückzahl abfragen; Gewicht/Kubik aus Stückzahl berechnen (20 kg/0,216 m³ je Karton, 18 Karton/Pal, +25 kg Leergewicht) | LI-Modul+Abteilung | *MFD, Dialog, Rechnen / Berechnungselement* |

## B) Sendungsprüfung (Bibliothek *Sendungs-Prüfmodule*)

Werden **beim Speichern** einer Sendung **automatisch** ausgeführt. Kriteriumssätze:
LI-Modul · Auftraggeber · Empfänger · Abteilung · Firma · Absender · Allgemein.

| Bsp | Ziel | Kriteriumssatz | Formate |
|---|---|---|---|
| 1 | Pflichtfelder prüfen (Auftraggeber, Zustelltermin, Sendungstyp Einzelsendung); je Fehler Meldung + **Abbruch** | Absender | *Entscheidung, Meldung, Abbruch* |
| 2 | Bei best. Empfänger: Abholtermin = heute (`#SYS_DATUM`), Zustellung in max. 3 Tagen; fehlende/falsche Daten automatisch korrigieren + melden | Auftraggeber | *Entscheidung, Teil, Zeitberechnung addieren, Setzen Feld, Meldung* |

## C) Allgemeine Module (Bibliothek *Allgemeine Module*)

An keine Maske gekoppelt; wiederverwendbar, Aufruf über *Erweiterte Funktionen → Allg.
Modulaufruf* oder per Format *Allgemeines Modul* (Parameter → `#P1..#P6`).

| Bsp | Ziel | Formate |
|---|---|---|
| 1 | **Frachtpflichtiges Gewicht** ermitteln: Minimalgewicht 500 kg für PAL/EUP/PRP/EWP; bei Unterschreitung Verpackungsgewicht heranziehen | *Schleife (SDG_BESCH…), Entscheidung, Berechnung*; **AKKU/ZUFELD vor Nutzung mit 0 initialisieren!** |
| 2 | **Dialogschleife Distanzermittlung** (DE, PLZ-Eingabe), 1,20 €/km, wiederholbar bis Abbruch, Ergebnis als Meldung | *Entscheidung, Distanzermittlung, Rechnen, Meldung, (Schleife)* |

## D) Abrechnung (Bibliothek *Abrechnung*)

Basiert vollständig auf dem LI → Ergebnis **Resultatset**. Kernformate: *Spesencode* +
*Berechnungselement* (ggf. *Matrix*). **Ohne Spesencodemodul keine Abrechnung.**

| Bsp | Thema | Wichtige Punkte / Formate |
|---|---|---|
| 1 | **Berechnungselemente** (Spesencode 401): Fixbetrag, pro 100 kg (Rundung, Min/Max), pro Colli, pro 70 € Wert (Max), % vom Wert | *Berechnungselement* (Von/RDG/Per/Wert/Min/Max); Var. `#CLLANZ, #GEW_BTO, #WERT` |
| 2 | **Fracht** (absenderseitig): Abholung pauschal 50 €, Fracht Sp.110 (auf 10 kg auf, 6 €/100 kg), Versicherung 2 % vom Wert (min 1 €, max 15 €) | Nicht-Standard-Spesencode braucht **mind. 1 Berechnungszeile** |
| 3 | **Matrix**: Fracht (10 €/100 kg), Versicherung 3 %, Zustellung nach Gewicht×Colli | Nicht def. Schnittpunkt = `F`/`?` → rote 0 im Resultatset |
| 4 | **Entscheidung**: PLZ 6800–69.. Unterlandzuschlag 22 €, sonst Oberland 36 €; Musikinstrumente +150 € (Dialog) | Platzhalter `?`, Bereich `:`, Aufzählung `;` |
| 5 | **Zwischenberechnung**: Warenwert aus Gewicht ableiten (1000 kg = 58 €), Versicherung nach Tarif AT-SVS, +1 % Zuschlag | *Berechnungselement* als Vorberechnung |
| 6 | **Zeit auslesen**: Sonderabholung 11:00–11:59 → 90 € (sonst 50 €); Fracht je nach Frankatur 900→12 €/sonst 10 €; Versicherung 1 % (1–15 €) | Format **Teil** (Stunde aus Zeit), *Entscheidung* |
| 7 | **Entscheidungsblock nach Zonen + Alternativberechnung**: Zonen per PLZ; Hebebühne (`#HEBEBUEHNE`) 150 €; Matrix mit Alternativberechnung (Min); Wochenendzuschlag Samstag 40 € | *Entscheidungsblock, Matrix (Alternativ), Zeitberechnung Wochentag*; `ZZAUSAUT` |
| 8 | **Frachtvereinbarung nach Zonen**: 4 Zonen per PLZ-Muster; Zuschlag je Instrument; Rundung ≤10000 kg auf 50, darüber auf 100; Zone/Gewicht/Art im Rechnungstext | `ZZAUSWTXT/ZZAUSWNR`, *Textzeile* mit Variablen |
| 9 | **Alternativberechnung**: mehrere Zweige (Gewicht vs. Colli), niedrigere Summe (Min) abrechnen | Matrix-Alternativberechnung |
| 10 | **Add-Basis mit Koordinaten**: Frachtstaffel Colli×Gewicht + „je weiteres Colli“; Zuschlag mit Rundung/Min/Max | *Berechnungselement Matrix* mit Addb.X/Addb.Y |
| 11 | **Zusammenzug 2 Spesencodes**: Frachtzuschlag 2 €/Packstück (max 5) mit Fracht in **einer** Zeile (Sp.110) zusammenführen; graue Codes nicht andrucken | **Akkumulationszeile / AKKU-Zeichen** |
| 12 | **Frachtpfl. Gewicht als Modulvorlage**: allg. Modul aus C1 integrieren; Fracht auf Basis frachtpfl. Gewicht (Staffel, Rundung 100 kg) | *Allgemeines Modul* (`#P…`); AKKU/ZUFELD initialisieren |
| 13 | **Gewicht zur Satzfindung**: Zusammenzug aller Kundensendungen einer Ladung (nur ausgehend); Staffel je 100 kg | Schleifen **LDG_FAHRT** → **SDG_LDG_U** |

## E) Fahrten (Bibliothek *Fahrten* / *Allgemeine Module*)

Automatisieren wiederkehrende Fahrten & Sendungen. Aufruf in der **Disposition** über
**[Allg. Fahrtmodul]** (oder über Auftragsbearbeitung → Allgemeine Module).

| Bsp | Ziel | Formate / Details |
|---|---|---|
| 1 | **Fahrt anlegen**: Status geplant, Relation fix 200, Arbeitsgruppe+Disponent automatisch; Bezeichnung/Typ/Datum/Frachtführer/Verkehrsmittel (DOK-187) + Verkehrsmittelstrecke per **MFD**; Info-Dialog „Fahrt wurde angelegt“ | *MFD, Dialog* |
| 2 | **Fahrt mit 3 Einzelsendungen**: Fahrt (Status geplant, Typ Eigenfuhrpark, Frachtführer 999990, Datum heute, Relation 200, VKM DOK-187); je Sendung fixe Werte (Termine, Frankatur 990, Verkehr Inland ausgehend, VP Paletten, Inhalt Elektrogeräte); Empfänger/Collianzahl/Bruttogewicht per **MFD** (mit Vorschlag) | *MFD*; Schleifenlogik über Sendungen |

## Wiederkehrende Merkregeln aus den Übungen

- **AKKU-/ZUFELD-Variablen vor Verwendung mit 0 initialisieren** (global!). → [05]
- **Nicht-Standard-Spesencode** benötigt **mindestens eine Berechnungszeile**. → [04]
- **Platzhalter/Bereiche in Parametern:** `?` = 1 Zeichen, `:` = von–bis, `;` = Aufzählung. → [06]
- **Nicht definierte Matrix-Schnittpunkte** → `F`/`?` → **rote 0** im Resultatset. → [04]
- **Alternativberechnung** vergleicht Zweige und nimmt je nach Min/Max den niedrigsten/höchsten Betrag. → [04]
