# 17 – Sendungsstatus manuell setzen & Compliance-Prüfung

← [Index](00_INDEX.md) · verwandt: [16 Aktueller Prozess](16_aktueller-prozess-handbuch.md) (Track & Trace Stati) · [12 LBase 21st](12_lbase-21st-bedienung.md)

Zwei operative Zusatzabläufe aus eigenen Kurzanleitungen.

---

## A) Sendungsstatus manuell setzen (Statusbehandlung)

Quellen: **20250129_Sendungsstatus_IOD** (docx) + **20250327_Sendungsstatus_POD** (pdf, 4 S.,
Autor „guggse“). Ergänzt die Track-&-Trace-Stati aus [16] um das **konkrete Vorgehen**.

> Kontext: Der Sendungsstatus steuert die Sendungsverfolgung (Track & Trace, → [16]). Er wird
> normalerweise über die **Driver App** gesetzt, kann aber **manuell** über die
> **Statusbehandlung** aus der Dispomaske gesetzt werden.

### Methode 1 – aus der Dispomaske (S.1–3)

1. **Fahrt auswählen** in der Dispomaske.
2. **Status**-Knopf drücken. *(Markiert man in der unteren Maske eine einzelne Sendung, wird
   der Status **nur für diese Sendung** gesetzt.)*
3. Ggf. erscheint ein Auswahlfenster (Firma/Niederlassung/Abteilung/Arbeitsgruppe, Filter/
   Anzeigeart/Sortierung, Optionen „nur Sendungen mit Status“ / „alle Status-LA's“ /
   „Beladetätigkeit“) → mit **OK** bestätigen.
4. Es öffnet sich die **Statusbehandlungs-Maske** mit einer Sendungsliste. Gewünschte Sendung
   **markieren** und **oben links** befüllen.

### Werte für Zustellung (S.2)
- **LA-Untertyp = `ZU`** (Zustellung)
- **Status = `012`** (erfolgreich zugestellt; mit **[F5]** anderen Code suchen)
- **Empfängername/Übernehmer** (falls vorhanden)
- **Datum/Uhrzeit** im Format **`DD.MM.YYYY HH:MM`**
- dann **[Zuweisen]** → **[Speichern]**

### Werte für Abholung (S.4)
- **LA-Untertyp = `AB`** (Abholung)
- **Status = `290`** (erfolgreich abgeholt; sonst [F5])
- **Absendername/Abgeber** (falls vorhanden)
- **Datum/Uhrzeit** `DD.MM.YYYY HH:MM`
- dann **[Zuweisen]** → **[Speichern]**

> ⚠️ **Wichtig:** Der Status wird **erst beim [Speichern] tatsächlich gesendet** – nur
> „Zuweisen“ reicht nicht!

### Status-Codes im Überblick (Abgleich mit [16])
| Ereignis | LA-Untertyp | Code |
|---|---|---|
| In Vorbereitung | — | immer hinterlegt |
| **Abgeholt** | AB | **290** |
| **Unterwegs** | ZU | 001 |
| **Im Auslieferdepot** | ZU | 053 |
| **In Zustellung** | ZU | 54 |
| **Erfolgreich zugestellt** | ZU | **012** |

---

## B) Compliance-/Sanktionslisten-Prüfung

Quelle: **Anleitung Compliance Prüfung LBase** (Felix Bauer, 04.10.2024, 3 S.).

> Zweck: Sicherstellen, dass keine Sendung an sanktionierte Personen/Firmen/Länder abgewickelt
> wird (Sanktionslisten-Screening). Betrifft v. a. internationale Sendungen (Beispiel:
> Projektlogistik CTI).

### Ablauf

1. **Auslöser:** E-Mail mit einer Sendungsnummer. Die Sendung bleibt bei der Einspielung im
   **Status „ungeprüft“** (gelbe Maske) stecken.
2. In der **LA-Übersicht** legen sich **zwei LAs** an (S.1) 🖼:
   - LA **`Sonstiges (Evidenz)`**, Subtyp **`Complianceprüfung`**, Status *angefordert*,
     Betreff „Complianceprüfung Treffer …“.
   - LA **`Ausdruck`**, Subtyp **`E-Mail`**, Status *positiv erledigt*, Betreff
     „Compliance-Treffer in …“.
3. Für die Freigabe ist die **Complianceprüfungs-LA** relevant: im **Textfeld** ist
   dokumentiert, **welche Liste den Treffer verursacht** hat (im Beispiel: US-Liste – *„The
   Office of Foreign Assets Control of the US Department | ATLANTIS“*).
4. **Firmen prüfen** über die offiziellen Listen:
   - **EU-Liste:** `webgate.ec.europa.eu/fsd/fsf` · `sanctionsmap.eu`
   - **US-Liste:** **BIS – Denied Persons List** (`bis.doc.gov`, Bureau of Industry and
     Security) · **OFAC – Office of Foreign Assets Control** (`ofac.treasury.gov`)
5. **Bewertung & Freigabe:**
   - Ist der Treffer **unkritisch/kein echter Bezug** (im Beispiel betraf der Treffer ein
     iranisches **Schiff**, nicht die Firma) → **Sendung freigeben** durch **Ausführen der LA**.
     Alternativ bei einer angelegten Adresse das **Attribut `Whitelist`** setzen.
   - Nach der Freigabe kann die Sendung **bearbeitet und neu gespeichert** werden – **erst dann
     legen sich die Transport-LAs an**.
6. **Echter Treffer:** Bei einem **tatsächlichen** Sanktionstreffer ist **umgehend der
   Auftraggeber zu informieren** und die **Sendung zu stoppen**.

### Merksätze
- „Ungeprüft“ = **Sperre**; ohne Compliance-Freigabe entstehen **keine Transport-LAs**.
- Freigabe = **LA ausführen** *oder* **Whitelist-Attribut** auf der Adresse.
- Whitelist wirkt dauerhaft für die Adresse; LA-Ausführen wirkt für die einzelne Sendung.
