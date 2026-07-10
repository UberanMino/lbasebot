# 02 – Bibliotheken & Kriteriumssätze

← [Index](00_INDEX.md) · verwandt: [01 Grundlagen](01_grundlagen-lis.md) · [03 Module](03_module-startbedingung-version.md) · [09 Rezepte](09_uebungen-rezepte.md)

## Bibliotheken

Programme/Module/Funktionen werden nach Kriterien in **Bibliotheken** zusammengefasst,
um die Verwaltung überschaubar zu machen. **Ein Modul muss in einer Bibliothek angelegt
werden.** Die Bibliotheken werden **vom System vorgegeben** und sind eng mit der
Trägerapplikation gekoppelt.

**Aufruf:** `Stammdaten → ADMINISTRATION → Verwaltungstools → Module` (bzw. `… → Bibliotheken`).

lBase ruft **je nach Tätigkeit** die passende Bibliothek auf und sucht darin nach Modulen,
deren Kriterien zu den in der Auftragsbearbeitung vorhandenen Daten passen.

### Tätigkeit → Bibliothek (Mapping)

| Tätigkeit in lBase | Bibliothek(en) |
|---|---|
| **Sendung speichern** | Sendungs-Prüfmodule, Sendungs-LA-Modul |
| **Sendungsvorgabe** (Funktion *Vorgabemodul* / Taste **[F4]**) | Sendungs-Vorgabe |
| **LA vom Typ Abrechnung ausführen** | Abrechnungs-Einstellungen, Abrechnung |
| **Disponieren** (LA TR wird auf die Fahrt gezogen) | Disposition |
| **Fahrtstatuswechsel** | Fahrten – Statuswechsel |
| **Fahrtmodul** (Aufruf aus Dispositionsmaske) | Fahrten |
| **Modulaufruf aus Sendungsübersicht** | Sendungen Allgemein |
| **Modulaufruf aus jeder anderen Übersicht** | Übersichten Allgemein |
| **Allgemeiner Modulaufruf** (aus Auftragsbearbeitung bzw. per Format im Modul) | Allgemeine Module |

## Kriteriumssätze

Ein **Kriteriumssatz** ist eine **Zusammenfassung mehrerer Kriterien** (z. B.
Kundennummer, Relation, LA-Untertyp), über die die Modulsuche zusätzlich eingegrenzt wird.

- Bei der **Erstellung eines Moduls** muss festgelegt werden, **welcher Kriteriumssatz**
  für die Suche herangezogen wird (abhängig von der zugeordneten Bibliothek).
- Dabei werden die Kriterien mit **konkreten Werten** belegt.
  - *Beispiel* (Kriteriumssatz „Adresse / Abteilung / LA-Untertyp“ in Bibliothek Abrechnung):
    Kundennummer = 123456, Abteilung = 759, LA-Untertyp = AB.
- Beim **Aufruf** übergibt das aufrufende Programm diese **Eckdaten** an den LI. Der LI
  vergleicht sie mit den Kriteriumssätzen. **Nur Module, deren Kriterien mit den Eckdaten
  übereinstimmen, werden ausgeführt.**

**Anzeige:** `Ribbon Stammdaten → Administration → Verwaltungstools → Bibliotheken` →
gewünschte Bibliothek markieren → Button **[Kriterium-Sätze]**. Dort sieht man auch, ob
ein Satz als **F** (fix) oder **A** (alternativ) läuft und mit welcher **Priorität**.

## Zwei Suchvarianten: Fixe vs. Alternative Suche

### Fixe Suche (Deklaration „F“)
Jeder Kriteriumssatz wird mit den übergebenen Eckdaten geprüft. Stimmen sie überein,
gelangt **jedes zutreffende Modul** entsprechend der **Priorität** zur Ausführung.

> ⚠️ **Nebeneffekt:** Ein **nachfolgendes Modul kann Werte eines zuvor durchlaufenen
> Moduls überschreiben.** Das ist häufige Fehlerquelle bei „warum steht da plötzlich ein
> anderer Wert?“.

**Bibliotheken mit fixen Kriteriumssätzen:**
Statusbehandlung · Sendungs-Vorgabe · Sendungs-Prüfmodule · Sendungs-LA-Module ·
Sendung-Scannen · Generische Dokumente · Air Waybill.

### Alternative Suche (Deklaration „A“)
Auf einen „A“-Kriteriumssatz muss ein **weiterer alternativer** folgen. Die Reihenfolge
bestimmt die **Priorität**. Trifft der erste nicht zu, wird der nächste geprüft.
**Trifft kein Kriteriumssatz zu, wird das Modul NICHT ausgeführt** (erster Treffer gewinnt).

**Bibliotheken mit alternativen Kriteriumssätzen (Auswahl):**
Zoll-Spesenblock · Spesenblock · Übersichten Allgemein · Tarif · Spesencode ·
Sendungssplit (+ Rechtesteuerung) · Sendungs-DFUE-Eingang · Sendung Allgemein ·
Nachschub · Lagerplatz · Lagerbuchung · Lagerartikel · Lademittel · Kalkulation ·
Inventur · Fahrten-Statuswechsel · Fahrten · Disposition · Belegimport aus
Belegerfassung · Beleg speichern oder freigeben · Allgemeine Module ·
Abrechnungs-Einstellung · Abrechnung.

## Priorität der Kriteriumssätze bei Sendungsvorgaben (konkret)

Bei **Sendungsvorgaben** sucht das LIS die Kriteriumssätze in dieser **Reihenfolge
(Priorität von spezifisch → allgemein)**:

1. LI-Modul und Abteilung
2. Auftraggeber und Abteilung
3. Absender und Abteilung
4. Empfänger und Abteilung
5. Abteilung
6. Absender und Firma
7. Empfänger und Firma
8. Firma
9. Allgemein

> Praxis: Ein spezifischeres Match (z. B. „Auftraggeber und Abteilung“) sticht das
> allgemeinere („Firma“, „Allgemein“). Für **Sendungsprüfmodule** gelten die
> Kriteriumssätze: LI-Modul · Auftraggeber · Empfänger · Abteilung · Firma · Absender · Allgemein (siehe [09]).

## Zusammenspiel (Merksatz)

**Bibliothek** legt fest *welcher Topf* durchsucht wird → **Kriteriumssatz** grenzt
*welche Module* in Frage kommen → **Suchart (F/A)** + **Priorität** bestimmen *welche und
wie viele* Module tatsächlich laufen. Erst danach greifen **Startbedingung & Version** ([03]).
