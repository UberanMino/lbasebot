# LogBATT LinkedIn Content Cockpit

Lokal laufendes HTML-Tool (kein Backend, kein Build) zur Recherche und
Texterstellung für LogBATT-LinkedIn-Content. Einfach `linkedin-content-cockpit.html`
im Browser öffnen.

## Setup

1. Tavily API-Key besorgen: https://tavily.com
2. Anthropic API-Key besorgen: https://console.anthropic.com
3. Datei öffnen, unter "Einstellungen & API-Keys" beide Keys eintragen.
   Die Keys bleiben lokal im Browser (localStorage) und gehen ausschließlich
   direkt an `api.tavily.com` / `api.anthropic.com`.

## Architektur (3 Ebenen)

1. **Trend-Recherche (Tavily)** – sucht aktuelle News zu Batteriesicherheit,
   Havariefällen, Regulatorik (ADR/UN 38.3/EU-Batterieverordnung), Circular
   Economy und Marktumfeld. Themen sind editierbar.
2. **Format-Recherche (Tavily)** – sucht, welche Beitragsformate/Hooks bei
   Branchenvorreitern (Northvolt, Stena Recycling, Umicore …) und im B2B-Bereich
   auf LinkedIn aktuell funktionieren. Liefert vor allem Sekundärquellen, da
   LinkedIn-Feeds selbst kaum öffentlich indexiert sind.
3. **Content-Erstellung (Anthropic/Claude)** – schreibt daraus fertige,
   deutsche LinkedIn-Posts auf Basis des LogBATT-Unternehmenskontexts und sieben
   Content-Säulen (Gefahr/Havarie, Mythen-Check, Regulatorik, Behind-the-Scenes,
   Daten/Trends, Experten-Stimme, Circular Economy breit).

Ein Klick auf **"Vollautomatisch-Lauf starten"** durchläuft alle drei Ebenen
mit sinnvollen Defaults. Jede Ebene ist auch einzeln und manuell steuerbar
(Quellen an-/abwählen, eigene Recherchethemen ergänzen, Posts nachbearbeiten).

Generierte Posts werden automatisch in einer lokalen Historie gespeichert und
können als JSON exportiert werden.

## Veröffentlichung auf LinkedIn

Dieses Tool postet **nicht** direkt auf LinkedIn. LinkedIns API für
Veröffentlichungen (Community Management API) erfordert OAuth2, eine
freigegebene App und Partner-Review durch LinkedIn – keinen einfachen API-Key.
Empfohlener Weg: exportierte Posts (JSON-Export-Button) in n8n einlesen und dort
über den LinkedIn-Node/OAuth veröffentlichen oder einplanen.

## Hinweis

Die Branding-Farben sind eine Annäherung an LogBATTs Green-Logistics-/
Batteriesicherheit-Optik (automatischer Zugriff auf logbatt.de war beim Bau
blockiert) und über CSS-Variablen am Dateianfang leicht anpassbar. Generierte
Inhalte vor Veröffentlichung fachlich prüfen, insbesondere Gefahrgut- und
Regulatorik-Aussagen.
