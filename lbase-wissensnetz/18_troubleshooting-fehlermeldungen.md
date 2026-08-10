# 18 – Troubleshooting: lBase-Fehlermeldungen (Ursache & Abhilfe)

← [Index](00_INDEX.md) · verwandt: [13 LIM & GenTabs](13_lim-und-gentabs.md) · [12 LBase 21st](12_lbase-21st-bedienung.md) · [03 Module/Version/Gültigkeit](03_module-startbedingung-version.md)

Sammelknoten für **konkrete Fehlermeldungen** aus dem Echtbetrieb, mit Ursache und Abhilfe.
Erfüllt den To-Do aus [00_INDEX](00_INDEX.md) („Konkrete lBase-Fehlermeldungen + Ursachen/
Abhilfe sammeln“).

> **Grundregel:** Steht in der Dialogbox **„SQL-Fehler“** bzw. **„Programmfehler – Sqlkommando
> konnte nicht durchgeführt werden! Bitte um Mitteilung an die IT-Abteilung!“** mit einem
> **ORA-xxxx**-Code, ist das **kein Erfassungs-/Bedienfehler**, sondern ein Problem in der
> Datenbank-/Programmschicht. Der Sachbearbeiter kann es **nicht** durch Ändern der
> Eingabedaten lösen → an IT/Lagermax bzw. Axians melden. Immer **[Details]** öffnen und das
> vollständige SQL + den ORA-Code weitergeben.

---

## F-001 · ORA-904 beim Anlegen einer Sendung (View `gnvw_lmx_ab_int`)

**Meldung:** `SQL-Fehler`, `Nr ORA-904`, Text *„Programmfehler – Sqlkommando konnte nicht
durchgeführt werden! Bitte um Mitteilung an die IT-Abteilung!“*
**Wann:** beim **Erfassen/Speichern einer Sendung** (im Beispiel: Einzelsendung, TPV **LTL**,
Übernahme **Direktabholung**, Weiterleitung **Direktzustellung**, Verkehr national).

### Was ORA-904 bedeutet
Oracle-Fehler **ORA-00904 = „invalid identifier“**: In der SQL-Anweisung wird ein **Spaltenname
verwendet, den es (in dieser Tabelle/diesem View) nicht gibt**. Das ist ein **fixer
Struktur-/Definitionsfehler** – er tritt **datenunabhängig** bei **jeder** Sendung auf, die
diesen Code-Pfad durchläuft. Reihenfolge/Werte der Eingabe ändern nichts.

### Das auslösende SQL (aus [Details])
Es ist eine **Konfigurations-/Ermittlungsabfrage** von lBase gegen den generischen View
`gnvw_lmx_ab_int` (interne Ablauf-/Automatik-Steuerung, `ab_int`). Sie ermittelt anhand von
**Sendungstyp, TPV, Verkehr, Übernahme, Weiterleitung** und den **LAF-Codes** (hier
`ABHOLUNG`/`ABHOLUNG_PARTNER`/`ABHOL_ZUSTELL`) sowie der **ORGID-Hierarchie**, welche
Automatik-/Ablaufregel greift:

```sql
select lmx_ab_int_orgid, lmx_ab_int_sdg_typ, lmx_ab_int_tpv, lmx_ab_int_verkehr,
       lmx_ab_int_uebernahme, lmx_ab_int_weiterleitung, lmx_ab_int_umschlag,
       lmx_ab_int_laf_code1, lmx_ab_int_laf_code2, lmx_ab_int_dialog,
       lmx_ab_int_bedingung, lmx_ab_int_recid, lmx_ab_int_relev,
       lmx_ab_int_prio, lmx_ab_int_durch
from   gnvw_lmx_ab_int
where  1=1
and    lmx_ab_int_sdg_typ      = :p0
and    lmx_ab_int_tpv          = :p1
and    lmx_ab_int_laf_code2 in ('ABHOLUNG','ABHOLUNG_PARTNER','ABHOL_ZUSTELL')
and    lmx_ab_int_verkehr      = :p2
and    lmx_ab_int_uebernahme   = :p3
and    lmx_ab_int_weiterleitung= :p4
and    :p5 IN (SELECT org_orgid FROM sorg_t_einheit
               START WITH org_orgid = lmx_ab_int_orgid
               CONNECT BY PRIOR org_orgid = org_orgidh)
and    trunc(sysdate) between rec_von and rec_bis      -- ⚠ unqualifiziert!
order  by lmx_ab_int_prio
```

### Wahrscheinliche Ursache
**Alle** Spalten des Views tragen das Präfix `lmx_ab_int_` – **nur** `rec_von` und `rec_bis`
in der Gültigkeitsprüfung (`trunc(sysdate) between rec_von and rec_bis`) sind **unpräfixiert**.
Genau diese beiden sind die heißen Kandidaten für den „invalid identifier“:

- Der View `gnvw_lmx_ab_int` **enthält die Gültigkeitsspalten `rec_von`/`rec_bis` nicht (mehr)**
  bzw. sie heißen dort anders (z. B. `lmx_ab_int_rec_von`/`…_rec_bis`). Der Code, der das SQL
  zusammenbaut, erwartet aber genau `rec_von`/`rec_bis`.
- Typische Auslöser: **View-Definition nach Update/Patch nicht mitgezogen**, **Versions-Mismatch
  Client ↔ DB-Schema**, oder der View ist in Oracle **INVALID** (referenziert eine gelöschte/
  umbenannte Basistabellen-Spalte).

`rec_von`/`rec_bis` sind die **Gültigkeit von/bis** der Konfigurationszeile – dasselbe Prinzip
wie **Version/Gültigkeitszeitraum** bei Modulen (→ [03]). Die Zeile soll nur gezogen werden,
solange `heute` in ihrem Gültigkeitsfenster liegt.

### Warum nur bei dieser Sendung – und nicht bei anderen?
ORA-904 ist zwar datenunabhängig (eine nicht existierende Spalte scheitert *immer*), aber lBase
führt **nicht eine feste Abfrage für alle Sendungen** aus, sondern **baut das SQL je Konstellation
dynamisch zusammen** (Sendungstyp, TPV, Übernahme, Weiterleitung → daraus die LAF-Codes). Sichtbar
an `lmx_ab_int_laf_code2 in ('ABHOLUNG','ABHOLUNG_PARTNER','ABHOL_ZUSTELL')`: diese Liste ist der
**Abhol-Zweig** und steht nur wegen **Direktabholung + Direktzustellung** im Statement. Nur diese
Kombination erzeugt die Variante, die den fehlerhaften View mit `rec_von`/`rec_bis` anfasst; andere
Konstellationen bauen ein anderes (funktionierendes) SQL → speichern fehlerfrei.

„Warum jetzt?" – drei typische Auslöser:
1. **Selten genutzte Konstellation** – der latente Bug im View wird erst jetzt erstmals durchlaufen.
2. **Regression durch Update/Patch** – der View verlor die Gültigkeitsspalten; betroffen ist nur der
   Code-Pfad, der diesen View nutzt.
3. **Neue/geänderte Konfigzeile** (GenTab), die lBase erst jetzt in diese Ermittlung führt.

**Für die IT:** reproduzierbar **nur mit dieser Feld-Kombination** (Einzelsendung, LTL, national,
Direktabholung, Direktzustellung) – gezielt prüfen, ob der View seit dem letzten Patch
`REC_VON`/`REC_BIS` verloren hat.

### Abhilfe – für IT / Lagermax / Axians
Diagnose in der DB (Schema von lBase):

```sql
-- 1) Welche Spalten hat der View wirklich?  Gibt es rec_von/rec_bis?
SELECT column_name FROM all_tab_columns
WHERE  table_name = 'GNVW_LMX_AB_INT' ORDER BY column_name;

-- 2) Ist der View gültig?
SELECT object_name, status FROM all_objects
WHERE  object_name = 'GNVW_LMX_AB_INT' AND object_type = 'VIEW';

-- 3) View-Quelltext ansehen (wird rec_von/rec_bis überhaupt selektiert?)
SELECT text FROM all_views WHERE view_name = 'GNVW_LMX_AB_INT';

-- 4) Übersetzungsfehler des Views?
SELECT * FROM all_errors WHERE name = 'GNVW_LMX_AB_INT';
```

- Fehlen `REC_VON`/`REC_BIS` in Schritt 1 → **View-Definition korrigieren** (die
  Gültigkeitsspalten der Basistabelle mit exakt diesen Aliassen wieder aufnehmen) **oder** den
  SQL-erzeugenden Code auf die korrekten Spaltennamen anheben. Anschließend
  `ALTER VIEW gnvw_lmx_ab_int COMPILE;`
- Status **INVALID** in Schritt 2 → Basistabelle/abhängige Objekte prüfen, dann rekompilieren.
- Als Ursache i. d. R. ein **Patch-/Release-Stand**, der Programm- und DB-Schema
  auseinanderlaufen ließ → passenden lBase-Patch nachziehen.

### Abhilfe – für den Sachbearbeiter
- **Kein** Daten-Workaround möglich (Struktur-, kein Datenfehler). Nicht durch andere
  Feldwerte „reparierbar“.
- Meldung mit **Screenshot + vollständigem SQL aus [Details] + ORA-904** an die IT geben
  (Dialog nennt genau das: *„Bitte um Mitteilung an die IT-Abteilung“*).
- In der Meldung mit angeben: **Session-ID** aus dem Dialog (im Beispiel `1728, 62476` /
  `DEPLO1-2TVYUPZ`) und die genutzte **Kombination** (Sendungstyp, TPV, Verkehr, Übernahme,
  Weiterleitung), damit IT den Fall reproduzieren kann.

---

## Vorlage für neue Einträge

```
## F-00n · <ORA-Code / Kurztitel> (<Kontext>)
**Meldung:** …
**Wann:** …
### Ursache
### Abhilfe – IT
### Abhilfe – Sachbearbeiter
```
