# 04 – Formate-Referenz (Befehle im Moduleditor)

← [Index](00_INDEX.md) · verwandt: [03 Moduleditor](03_module-startbedingung-version.md) · [05 Variablen](05_variablen-referenz.md) · [09 Rezepte](09_uebungen-rezepte.md)

**Formate** sind die „Befehle“ der LI-Sprache. Sie erhalten Daten als **Parameter
(Variablen)** oder direkt. Viele Parameterfelder unterstützen die Suchfunktion **[F5]**.
Hier die wichtigsten Formate (vollständige Liste: Online-Hilfe **[F1]**).

> Grundregel: **„Alles was programmiert wird, muss auch kommentiert werden.“**

## Dokumentation

| Format | Zweck |
|---|---|
| **Kommentar** | Einzeiliger Kommentar im Modul (nicht im Ergebnis sichtbar) |
| **Kommentarblock** | Mehrere Kommentarzeilen in die Modulübersicht einfügen |
| **Kommentarblock-Text** | Einzelne Zeilen innerhalb eines Kommentarblocks |

## Vorgabe-Formate (nur Bibliothek „Sendungs-Vorgabe“)

Befüllen Felder während der Sendungserfassung. Felder können **fix** belegt oder per
**Dialog** ermittelt werden.

| Format | Zweck / Parameter |
|---|---|
| **Vorgabe Feld** | Gibt Feldinhalt vor (z. B. Frankatur). Dialog über Spalte **Dlg-Art**: `A` = alphanumerisch, `N` = numerisch |
| **Vorgabe SDG-Adresse** | Adresse zur Sendung vorgeben – definiert durch **Typ** + **Adress-ID** (z. B. Absender) |
| **Vorgabe SDG-Beschreibungszeile** | Alle bekannten Daten der Sendungsbeschreibungszeile hinterlegen |
| **Vorgabe Attribut** | Attribut vorgeben. `zu SDG/SDB` (Sendung oder Beschreibungszeile), **Attributname** [F5], **Wert** [F5], **Fix**=`Y` → Wert kann später nicht mehr mit demselben Format überschrieben werden. Hinweis: Attribut muss per Maskendesign auf der Hauptmaske sein; Zusatzattribute der Checkbox NICHT über dieses Format setzbar |

## Zusatzattribute lesen/setzen

**Bereiche (Träger)** für beide Formate:
`ADR` Adresse · `SDG` Sendung · `SDB` Sendungsbeschreibung · `FAR` Fahrt ·
`BUC` Lagerbuchung · `ART` Artikel · `LVE` Lagervereinbarung · `VKM` Verkehrsmittel ·
`ZRL` Zusatzattr. auf Kostenstellen-Relationszuordnung · `LAF` Leistungsanforderung ·
`ADU` Partner-Kundenadresse · `STO` Stopps.

| Format | Zweck / Parameter |
|---|---|
| **Lesen Zusatzattribut** | Liest Zusatzattribut in eine Variable. Parameter: Bereich, **Attributname**, **In Feld** (Zielvariable), **Für (Key)** (z. B. Adress-ID oder `#ADRID`) |
| **Setzen Zusatzattribut** | Setzt Zusatzattribut. Parameter: Bereich, **Attributname** [F5], **Mit** (Quellfeld/Variable) [F5], **Für (Key)** [F5] (nur nötig, wenn Träger ≠ aktueller; z. B. Sendungsgruppenvater → `#SDG_GRP_NEU`) |

## Entscheidungen (Verzweigungen)

| Format | Zweck |
|---|---|
| **Entscheidung** | Wahrheitsprüfung → **JA-Zweig** (trifft zu) / **NEIN-Zweig** (trifft nicht zu). Parameter: **Feld** (Variable) [F5], **Oper** (log. Operator) [F5], **Text** (Vergleichswert) |
| **Entscheidung / Entsch. und Folgezeile** | Nur zusammen mit *Entscheidung*. Fügt **Und-Folgezeilen** hinzu: **alle** Kriterien müssen zutreffen, damit JA gilt |
| **Entscheidungsblock** | Mehrere Entscheidungen; trifft eine zu, wird in ihren Auswahlteil verzweigt, dann Block verlassen. Beim Übernehmen werden automatisch `Entscheidungsblock (Auswahlteil)`, `(Auswahl 999)` und `(Ende)` angehängt. Bezug Entscheidung↔Auswahlteil über **Nummern**. Variable `ZZAUSAUT` = zuletzt automatisch ausgewählte Info |

**Dialog-Varianten (Anwender antwortet):**

| Format | Zweck |
|---|---|
| **Dialogentscheidung** | Ja/Nein-Frage an den Anwender; je nach Antwort verzweigen |
| **Dialogentscheidungsblock** | Dialog mit **mehreren Auswahlmöglichkeiten**. Automatisch angehängt: `(Auswahlteil)` + `(Ende)`. Variablen: `ZZAUSWTXT` = letzte Textauswahl, `ZZAUSWNR` = letzte ausgewählte Nummer |

## Felder setzen

| Format | Zweck |
|---|---|
| **Setzen Feld** | Weist einer Variablen einen Wert zu. Ohne **Dlg-Art** → Wert aus Spalte **Text**. Mit Dlg-Art `A`/`N` → Dialog mit dem Anwender (+ **Vorschlag**). Auch zum **Rücksetzen** (Initialisieren) von Hilfsvariablen genutzt |
| **Setzen Feld original** | Schreibt Wert **direkt in die DB-Tabelle** (z. B. Sendung nachträglich verändern) |
| **Setzen Feld mit Textersatz** | Fasst Text + Variablen oder mehrere Variablen in **einer** Variablen zusammen |
| **Rücksetzen Feld** | Setzt Feld/Variable auf **ursprünglichen Wert** (aus DB) zurück / neu initialisieren – nützlich, wenn ein Wert nur temporär überschrieben wurde |
| **Feldinhalte sichern** | Sichert Variableninhalte (Gegenstück zum Rücksichern) |

## Dialoge

| Format | Zweck |
|---|---|
| **Mehrfelddialog (MFD)** | Dialog, in dem der Anwender **mehrere Werte** einträgt. Besteht **immer aus 4 Formaten in Kombination** (siehe unten) |

### MFD – die 4 Pflicht-Formate
1. **MFD Anlegen** – leitet den MFD ein, **Name** (max. 10 Zeichen, eindeutig).
   > ⚠️ Groß-/Kleinschreibung beachten – abweichende Schreibweise → Zeile wird nicht verwendet.
2. **MFD Setzen Feld** – fügt Zeilen hinzu (mehrfach). Parameter: **Dialogname**, **Feld**
   (zu befüllende Variable) [F5], **Fragetext**, **Vorschlag**, **Eigenschaft** (`M`=Muss,
   `O`=optional), **Var. Indirekt ermitteln** (`Y` nur in Schleifen für dynamische
   ZUFELD-Variablen; `N` = Inhalt der Variablen nehmen).
3. **MFD Start** – startet den Dialog. Parameter: **Name**, **Hinweistext**, **Fragetext**,
   **Fensterhöhe** (1–5), **Breite Fragetext/Wert/Bezeichnung** (je 1–5), **Focus**
   (Variable mit Fokus).
4. **MFD Löschen** – **muss** nach jedem MFD aufgerufen werden → Speicherfreigabe.

## Schleifen

Wiederholen einen Modulteil (Schleifenrumpf) bis zur Abbruchbedingung. Viele Schleifen
brechen automatisch ab (z. B. wenn alle Beschreibungszeilen abgearbeitet sind).

| Schleife | Läuft über … |
|---|---|
| **ALLGEMEINE_SCHLEIFE** | max. **2048** Wiederholungen; Zähler in Variable **ANZAHL** |
| **SDG_BESCH_G** | alle Sendungsbeschreibungszeilen |
| **SDG_BESCH_C** | Beschreibungszeilen der **Colliebene** (1. Ebene) |
| **SDG_BESCH_V** | Beschreibungszeilen der **Verpackungsebene** (2. Ebene) |
| **SDG_BESCH_A** | Beschreibungszeilen der **Artikelebene** (3. Ebene) |
| **LDG_FAHRT** | alle Ladungen einer Fahrt (siehe [09] Bsp. 13) |
| **SDG_LDG_U** | alle Sendungen einer Ladung (oberste Ebene) |

Weitere Parameter: **Schleifen-Bedingungen** (Performance – z. B. nur Ein-/Ausbuchungen),
**Schleifen-Sortierung** (Reihenfolge, z. B. nach Name statt Adress-ID), **Parameter 1–6**.

| Format | Zweck |
|---|---|
| **Schleifenabbruch** | Bricht Schleife ab (verhindert Endlosschleifen, wichtig bei ALLGEMEINE_SCHLEIFE) |

## Zeit & Rechnen

| Format | Zweck |
|---|---|
| **Zeitberechnung Wochentag** | Ermittelt Wochentag eines Datums. **Codierung: 1=So, 2=Mo, 3=Di, 4=Mi, 5=Do, 6=Fr, 7=Sa** |
| **Zeitberechnung addieren** | Addiert **Minuten** zu Datum. Parameter: *Datum/Zeit von*, *Minuten* (1 Tag = **1440** Min.), *Ergebnis Datum/Zeit* |
| **Zeitberechnung Dauer** | Dauer zwischen zwei Terminen. Parameter: *Von Datum/Zeit*, *Bis Datum/Zeit*, *Ergebnis in Minuten* |
| **Rechnen** | Grundrechenarten. Parameter: **Von** (Basis) [F5], **Operator** [F5], **Operand** (Wert), **Ergebnis** (Zielvariable) |
| **Teil** | Liest **Teil einer Variablen** aus. Parameter: **Von** [F5], **Ab Stelle** (1=Anfang), **In Länge**, **Blanks abschneiden**. (z. B. Stunde aus einem Datum lesen) |

## Meldungen & Abbruch

| Format | Zweck |
|---|---|
| **Meldung** | Zeigt Anwenderhinweis in Dialogbox. Mehrzeilig via **[F2]** (Editierbox) |
| **Abbruch** | Bricht das **laufende Modul** (und weitere folgende Module) ab – vorher wird eine Meldung angezeigt |

## Adressen & Distanz

| Format | Zweck |
|---|---|
| **Lesen Adresse** | Liest Adresse (per Adress-ID oder Sendungs-Adresstyp) → Variablen: **#ADRID, #NAME1, #NAME2, #STRASSE, #PLZ, #ORT, #LAND, #REFERENZ, #TXTADR, #TEXT**. Parameter: *Sendungs-Adress-Typ* [F5], *Adress-ID/Partner-ID* [F5], *Externe ID* (für Partner-Kundenadresse) |
| **Distanzermittlung** | Entfernung zwischen zwei PLZ unter Beachtung eines **Entfernungswerks**. Parameter: Entfernungswerk, Abgangs-/Empfangsland, PLZ, Ort, **Ergebnis Distanz (km)**, **Mautkilometer** |

## Abrechnung (Berechnung & Spesencodes)

> Die **gesamte Abrechnung basiert auf dem LI**. Ergebnis ist das **Resultatset**.
> **Ohne Spesencodemodul kann nicht abgerechnet werden.** Basis der Verrechnung sind
> **Spesencodes (Leistungsarten)**.

### Format Berechnungselement
Berechnet & rundet Werte. Zwei Teile: **Berechnungsteil** + **Modifikationsteil**.

Wichtige Parameter:
- **Von = Rechenbasis** [F5] – wovon gerechnet wird (Gewicht, Wert …)
- **RDG = Rundung** [F5] – z. B. `-10` auf volle 10 **ab**runden, `+100` auf volle 100 **auf**runden; leer = keine Rundung
- **Per = Werteinheit** [F5] – Art der Rechenoperation (z. B. pro 100 kg)
- **Wert** – womit die Basis behandelt wird (z. B. 2 € je 100 kg)
- **Wrg** [F5] – Ergebniswährung
- **RDG (Ergebnis)** – rundet das Berechnungsteil-Ergebnis
- **Ab/Zu** – Ab-/Zuschläge (z. B. +10 %, −20)
- **Min / Max** – Ergebnis wird bei Unter-/Überschreitung durch diesen Wert ersetzt
- **Zurücksetzen Berechnung** – ob Ergebnis zum nächsten Berechnungselement addiert wird
- **Ziel-Variablenname** [F5] – Variable für das Ergebnis (v. a. bei zurückgesetzter Berechnung)

### Format Berechnungselement Matrix
Ein-/zweidimensionale Anordnung von Berechnungselementen (Spalten × Zeilen); ein Element
liegt im Schnittpunkt. Über Matrizen lassen sich **Tarife aller Art** abbilden.
- Aufruf **nur aus dem Berechnungselement** heraus (Funktion **[Matrix]**).
- **Matrix Definition**: **ID** (autom., eindeutig), **Bezeichnung/Text**, je Achse
  **Variable** [F5] + **Operator** (`=`, `<=` …), **Num.Dialog/Alph.Dialog** (Wertabfrage),
  **Kein X-Wert** (Platzhalter `*`), **Erhöhung** (Schrittfolge der Suche), **Tabelle Wert**.
- **Bearbeitung Matrix** (zusätzlich zu Berechnungselement-Feldern): **Addb.X / Addb.Y**
  (mit zu berücksichtigende Werte), **Kennz.** (`/`, `-`, `=` – wie mit Ergebnis umgehen).
- **Nicht definierter Schnittpunkt:** `F` im Feld **PER** + `?` im Feld **Wert** → im
  Resultatset erscheint eine **rote „0“**.
- **Alternativberechnung** (Matrix-Feature): berechnet automatisch zusätzlich das nächste
  Element in X- oder Y-Achse; **Minimum** der beiden Berechnungen kommt in die Rechnung.

### Format Spesencode
Legt die **Leistungsart** fest.
- **Code** [F5] – Spesencode/Leistungsart
- **Text** – Text der Leistungsart (nur änderbar, wenn Spesencode in Stammdaten so definiert)
- **STD** [F5] – `J` = Berechnung nach dem im **Spesencodemodul** hinterlegten Standard;
  `N` = es muss ein **Berechnungselement** im Modul die Ermittlung liefern.
  → Zu jedem in Stammdaten definierten Spesencode **muss ein Spesencodemodul** existieren.
- **AKKU** – Akkumulationszeichen (Buchstabe A–Z/a–z oder Ziffer 0–9) fasst Spesencodes
  auf dem Rechnungsausdruck zusammen.
- **Spesencode Ende** – schließt die Spesencodeberechnung ab; nachfolgende
  Berechnungselemente werden dem Spesencode **nicht mehr** zugeordnet.

### Format Textzeile
Fügt Texte ein, die im **Resultatset** erscheinen und auf die **Rechnung** gedruckt werden.
**TEXT-KZ** [F5]: `G` = gebundener Text (an vorherigen Spesencode; wird mit ihm gelöscht),
`U` = ungebundener Text, `A` = Anlagentext, `K` = Kopftext, `F` = Fußtext.
> Variablen in Textzeilen: mit `#` und **großgeschrieben** → Werte/Zwischenergebnisse auf der Rechnung.

## Leistungsanforderungen (LA)

| Format | Zweck |
|---|---|
| **LA für Abrechnung** | Generiert LA vom Typ **Abrechnung** |
| **LA für Transport** | Generiert LA vom Typ **Transport** |
| **LA für Ausdruck** | Generiert LA vom Typ **Ausdruck** (z. B. Subtyp Auftragsbestätigung) |
| **LA Statuswechsel** | Führt für eine LA einen **Statuswechsel** durch |

## Modularisierung

| Format | Zweck |
|---|---|
| **Allgemeines Modul** | Ruft ein Modul aus Bibliothek **Allgemeine Module** auf → Wiederverwendung. **Parameter 1–6** werden im gerufenen Modul über **#P1 … #P6** gelesen. Änderungen nur einmal nötig → einfachere Wartung |
| **#deklarierte Variable** | Deklariert lokale/globale Variablen als Nummer/Datum/String (siehe [05]) |

→ Konkrete Anwendungsbeispiele für (fast) alle Formate: [09 Übungs-Rezepte](09_uebungen-rezepte.md).
