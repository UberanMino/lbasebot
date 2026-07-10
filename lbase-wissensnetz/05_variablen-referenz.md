# 05 – Variablen-Referenz

← [Index](00_INDEX.md) · verwandt: [04 Formate](04_formate-referenz.md) · [09 Rezepte](09_uebungen-rezepte.md)

## Grundprinzip

Variablen speichern Werte – Eingänge/Ausgänge von der Trägerapplikation, Zwischen- und
Endergebnisse.

> **Konvention:** Jede Variable beginnt mit **`#`** und wird **GROSS** geschrieben
> (`#FRANK`, `#WERT`, `#ADRID_EM`). Ausnahme: die typisierten deklarierten Variablen
> (`ln_`, `ls_`, …) sind **case-sensitive**.

## Arten von Variablen

| Art | Beschreibung |
|---|---|
| **Vorgegebene Variablen** | Fest in lBase verankert, **nicht änderbar**; beziehen sich auf lBase-Felder. Ein-/Ausgabe möglich |
| **Vorgegebene Hilfsvariablen** | Globale Ablage für Zwischenergebnisse (`#ZUFELD…`, `#AKKU…`) |
| **Deklarierte Variablen** | Selbst deklariert per Format `#deklarierte Variable`, lokal oder global, typisiert |

### Hilfsvariablen (global!)
- `#ZUFELD11A … #ZUFELD99A` – **alphanumerisch**
- `#ZUFELD11N … #ZUFELD99N` – **numerisch**
- `#AKKU1 … #AKKU30` – **numerisch**

> ⚠️ **Wichtige Regel:** `#ZUFELD…` und `#AKKU…` sind **global** → vor der Verwendung
> **mit 0 (bzw. leer) initialisieren** (per Format *Setzen Feld*), sonst steckt evtl. ein
> ungewollter Wert drin. Zum Sichern/Rücksichern: Format *Feldinhalte sichern*.
>
> Die Vielzahl `ZUFELD 1–99` verschlechtert die Lesbarkeit → besser **deklarierte
> Variablen** verwenden (Format `#deklariere Variable`).

## Lokale vs. globale Variablen

| | Gültigkeit |
|---|---|
| **Globale Variable** | Während des **gesamten Modulaufrufs** gültig, auch in Untermodulen. Muss im Untermodul **ebenfalls deklariert** werden, um dort ansprechbar zu sein. Empfehlung: in **jedem** Modul deklarieren |
| **Lokale Variable** | Nur **innerhalb des Moduls** gültig, in dem sie definiert wurde |

## Format `#deklarierte Variable` – Typ-Präfixe

Der Variablenname **muss mit dem Typ-Präfix beginnen** (Vorschlag wird automatisch angezeigt,
mind. um einen Buchstaben ergänzen):

| Präfix | Typ |
|---|---|
| `ln_` | **lokal numerisch** |
| `ls_` | **lokal String** |
| `ld_` | **lokal Datum** |
| `gn_` | **global numerisch** |
| `gs_` | **global String** |
| `gd_` | **global Datum** |

Weitere Parameter: **Variable-Kommentar** (möglichst sprechend), **Initialwert** (optional;
Default: Leerstring bei String/Datum, `0` bei numerisch; **nur bei Erstanlage** berücksichtigt).

## Wichtige vorgegebene Variablen – Sendungserfassungsmaske

| Bereich | Feld | Variable |
|---|---|---|
| Kopf | Verkehr | `#VERKEHR` |
| | Datum | `#SDGDATUM` |
| | Typ | `#SDGTYP` |
| | Suchbegriff | `#SUCHBEGRIFF` |
| Adressen | Sendungsadresse | *Vorgabe SDG ADRESSEN* |
| Beschreibung | Sendungsbeschreibungszeile | *Vorgabe SDG BESCHREIBUNGSZEILE* |
| Konditionen | Frankatur | `#FRANK` |
| | Frankaturtext | `#FRANKTEXT` |
| | Übernahme | `#UEBCODE` |
| | Weiterleitung | `#WLCODE` |
| | Abholtermin von / bis | `#TERMABVON` / `#TERMABBIS` |
| | Zustelltermin von / bis | `#TERMZUVON` / `#TERMZUBIS` |
| | Relation | `#RELATION` |
| Zoll | Zollbehandlung | `#ZOLLCODE` |
| | Zollamtl. Eintragung | `#ZOLLEINT` |
| | TKZ | `#ZOLLTKZ` |
| | Wert (Währung) | `#WRGID` |
| | Wert (Betrag) | `#WERT` |
| | KM | `#SDG_KM` |
| Versicherung | Transportversicherung | `#VERSICH` |
| Texte | Zustelltext | `#SDG_TEXT_ZU` |
| | Empfängertext | `#SDG_TEXT_EM` |
| | Ladepersonal | `#SDG_TEXT_LD` |
| | Abrechnung | `#SDG_TEXT_AB` |
| | Interne Notiz | `#SDB_TEXT_IN` |
| | Zoll (intern) | `#SDG_TEXT_ZO` |

> ⚠️ **Achtung:** Variablen der **Sendungsbeschreibungszeile** beginnen mit **`ZEILE_`**
> (z. B. `#ZEILE_GEW_NTO` Nettogewicht, `#ZEILE_GEW_BTO` Bruttogewicht, `#ZEILE_ART`).

## Weitere häufig genutzte Variablen (aus Handbuch & Übungen)

| Variable | Bedeutung |
|---|---|
| `#SYS_DATUM` | Systemdatum (heute) |
| `#ADRID`, `#ADRID_EM` | Adress-ID (allgemein / Empfänger) |
| `#NAME1`, `#NAME2`, `#STRASSE`, `#PLZ`, `#ORT`, `#LAND`, `#REFERENZ`, `#TXTADR`, `#TEXT` | Felder aus *Lesen Adresse* |
| `#GEW_BTO` | Bruttogewicht (Sendung) |
| `#CLLANZ` | Collianzahl |
| `#WERT` | Warenwert |
| `#HEBEBUEHNE` | Kennzeichen Zustellung mit Hebebühne |
| `#ZUDATUM1` | Zustelldatum |
| `#SDG_GRP_NEU` | Key des Sendungsgruppenvaters (für *Setzen Zusatzattribut*) |
| `#P1 … #P6` | Parameter im aufgerufenen *Allgemeinen Modul* |
| `ANZAHL` | Zähler in `ALLGEMEINE_SCHLEIFE` |
| `ZZAUSAUT` | zuletzt automatisch ausgewählte Info (nur *Entscheidungsblock*) |
| `ZZAUSWTXT` | letzte Textauswahl (nur *Dialog-Entscheidungsblock*) |
| `ZZAUSWNR` | letzte ausgewählte Nummer (nur *Dialog-Entscheidungsblock*) |

> Variablennamen lassen sich in vielen Feldern per **[F5]** suchen.
