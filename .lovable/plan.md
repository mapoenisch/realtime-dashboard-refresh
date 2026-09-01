# LeadPilot Dashboard — Frontend-Redesign

## Ausgangslage

Das Dashboard aus deinen Screenshots (LeadPilot Enterprise: Executive Dashboard, Live-Simulation Ebene B, Sidebar, Simulations-Topbar) liegt nicht in diesem Lovable-Projekt. Der Klon von `github.com/mapoenisch/realtime-dashboard-refresh` funktioniert inzwischen, enthält aber nur den leeren Template-Stand (ein Commit, kein Dashboard-Code) — das Repo ist offenbar mit einem leeren Projekt verbunden, nicht mit dem LeadPilot-Dashboard.

**Schritt 0 (nötig, bevor ich Code anfasse) — eine dieser drei Optionen:**

1. **Code als ZIP hier hochladen** (schnellster Weg): Im LeadPilot-Projekt den Code-Editor öffnen → „Download codebase" → ZIP hier in den Chat ziehen.
2. **Das LeadPilot-Lovable-Projekt mit diesem Repo verbinden** und synchronisieren lassen, dann klone ich es erneut.
3. **Das richtige Lovable-Projekt öffnen** und mich dort um das Redesign bitten.

Sobald der Code erreichbar ist, setze ich das Redesign unten um. Alle Design-Entscheidungen sind bereits aus den Screenshots abgeleitet und bleiben gültig.

Die Design-Analyse und alle Entscheidungen sind bereits aus den Screenshots abgeleitet und gelten unverändert.

## Diagnose aus den Screenshots

Was heute schwach wirkt:

- **Charts wirken leer.** ARR-Entwicklung ist eine einsame Linie ohne Gitternetz, ohne Y-Achse, ohne Werte-Labels, ohne Ziel-/Baseline-Referenz. Die MRR-Verteilung sind drei freistehende Balken ohne Achse und ohne Prozentkontext.
- **Kein Vergleich, keine Bewertung.** Nirgends steht, ob 411.840 € gut oder schlecht ist: keine Deltas, keine Vormonats-/Vorjahresbezüge, keine Zielerreichung, keine Trendrichtung.
- **KPI-Kacheln sind flach.** 8 gleich große Kacheln mit gleichem Gewicht — kein visueller Vorrang für ARR/EBITDA, keine Sparklines, keine Farbcodierung für negative Werte (EBITDA −309.000 € sieht aus wie jede andere Zahl).
- **Layout ohne Rhythmus.** Alles ist ein gleichmäßiges 4er- und 2er-Grid; das Auge findet keinen Einstiegspunkt.
- **Farbe wird kaum genutzt.** Cyan und Orange stehen nebeneinander ohne feste Bedeutung — mal Akzent, mal Datenserie.

## Design-Prinzipien für das Redesign

- **Analytics-Cockpit-Stil:** Combo-Charts (Bar + Line), Sparkline in jeder KPI-Kachel, Delta-Badges, Zoom/Brush im Hauptchart.
- **Farbe bekommt Bedeutung:** Cyan = Ist-/Primärserie, Orange = Vergleich/Ziel/Warnung, gedämpftes Grau-Grün = Baseline/Kontext, Rot nur für echte Alarme.
- **Dunkelgrüne Flächen behalten**, aber mit drei sauber getrennten Ebenen (Seiten-Hintergrund / Card / erhöhte Card) statt einem Grünton für alles.
- **Zahlen tabellarisch:** Tabular-Figures-Font für alle Kennzahlen, damit Ziffern in Spalten stehen.

## Umsetzung

### 1. Design-Tokens (globales CSS)

- Grün-Skala als Flächen: `surface-0` (Seite), `surface-1` (Card), `surface-2` (erhöht), plus Border-Töne mit echtem Kontrast statt kaum sichtbarer Kanten.
- Semantische Datenfarben: `--data-primary` (Cyan), `--data-accent` (Orange), `--data-muted`, `--positive`, `--negative`, `--warning`.
- Chart-Tokens: Gitternetz-Farbe, Achsen-Farbe, Tooltip-Hintergrund, Gradient-Stops für Flächen.
- Typografie: eigene Skala für Kennzahlen (tabular-nums), engeres Tracking bei großen Zahlen.

### 2. Chart-System (`recharts`)

Ein gemeinsamer Chart-Rahmen für alle Diagramme, damit nichts mehr nackt aussieht:

- Horizontale Gitternetzlinien, formatierte Y-Achse (`411,8k €` statt Rohzahl), Achsen-Titel.
- Einheitlicher Tooltip: dunkle Karte, alle Serien, Delta zum Vorpunkt, formatierte Werte.
- Legende mit klickbarem Serien-Toggle.
- Referenzlinien: Ziel (500.000 € ARR), Baseline, Break-Even bei EBITDA.
- Sanfte Einblend-Animation beim Mount, kein Dauer-Wackeln.

Konkrete Chart-Überarbeitungen:

- **ARR-Entwicklung:** Area+Line mit Cyan-Gradient, orange gestrichelte Ziel-Referenzlinie, Y-Achse, Quartals-Gitter, Brush zum Zoomen, Punktmarkierungen nur bei Hover.
- **MRR-Verteilung nach Paketen:** von drei einsamen Balken zu einem gestapelten Horizontal-Bar mit Anteil in % und absolutem MRR pro Paket, plus Kunden-Anzahl je Paket.
- **Quartalsentwicklung 2025:** Combo — Neukunden als Balken, Vertriebskosten als Linie auf zweiter Y-Achse, statt zwei zusammenhanglosen Balken.
- **Kundensektoren DACH:** Donut behalten, aber mit Center-Label (Gesamtkunden), Hover-Highlight und Legende, die die Segmente spiegelt.

### 3. KPI-Kacheln

- Neue `KpiCard`-Komponente: Label, Großwert, Delta-Badge (Pfeil + %, grün/orange/rot), Sparkline der letzten 12 Perioden, Kontextzeile.
- Hierarchie: ARR als doppelt breite Hero-Kachel mit größerem Chart, danach das 4er-Raster.
- Negative Werte (EBITDA, Cash Flow) automatisch in der Negativ-Farbe mit Vorzeichen.
- Optionaler Zielfortschritt als dünner Balken am Kachelfuß (z. B. ARR 82,4 % vom Ziel).

### 4. Sidebar & Topbar

- Sidebar: klarere Gruppentrennung, aktiver Eintrag mit Cyan-Kante statt Vollfläche, ruhigere Icon-Behandlung, kompaktere Zeilenhöhe für mehr sichtbare Navigation, Scroll-Fade oben/unten.
- Topbar: Simulationsstatus als deutlich lesbarer Status-Chip (pausiert/läuft, pulsierender Punkt nur wenn wirklich live), Tempo-Buttons als echte Segmented Control, Live-Kennzahlen rechts mit Label über Wert statt in einer Zeile.
- Sticky Page-Header mit Breadcrumb + Titel + Zeitraum-/Aktions-Slot.

### 5. Live-Simulation (Ebene B)

- Aktionsleiste: die 6 gleichwertigen Buttons hierarchisieren — „Simulation starten" als einziger Primär-Button, Rest als sekundär bzw. in ein Overflow-Menü.
- P50-Kacheln bekommen den Unsicherheitskorridor als visuelles Element (kleiner P10–P90-Balken mit Median-Marker) statt als Fließtext.
- Neues Fan-Chart für die Prognose: P10/P90 als Band, P50 als Linie, historische Baseline als gestrichelte Serie.
- Zielstatus (`AT_RISK`) als eigene Statusleiste mit Fortschrittsbalken zum 500.000-€-Ziel.
- Finanzwarnung als strukturierter Alert mit Icon, Kurzursache und Handlungsempfehlung statt Fließtextblock.

### 6. Feinschliff

- Konsistente Abstände und Card-Radien über alle Seiten.
- Skeleton-States für Charts und Kacheln beim Laden.
- Empty-States, wenn noch kein Simulationslauf existiert (statt Charts mit Nullwerten).
- Responsives Verhalten: Grid bricht sauber auf 2 und 1 Spalte, Sidebar wird zum Drawer.

## Abgrenzung

- Reines Frontend-/Präsentations-Redesign — Simulationslogik, Datenmodell und Berechnungen bleiben unverändert.
- Keine neuen Datenquellen; die bestehenden Werte werden nur besser dargestellt.
