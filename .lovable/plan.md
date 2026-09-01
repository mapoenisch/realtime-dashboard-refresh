# Dashboard-Frontend-Redesign

## Ziel
Das Echtzeit-Dashboard (MRR, ARR, KPIs) bekommt ein professionelles, dichtes UI mit spannenderen Diagrammen, stärkerer Hierarchie und einem eigenen Farbsystem: dunkelgrüne Flächen/Hintergründe, Cyan als Primärfarbe, Orange als Akzent.

## Ist-Zustand
`src/routes/index.tsx` zeigt aktuell nur den Lovable-Placeholder. Ein bestehendes Dashboard ist im Repository nicht sichtbar. Der Plan geht daher davon aus, dass das Dashboard neu aufgebaut oder der bestehende Code ersetzt wird.

## Umsetzung

### 1. Design-System in `src/styles.css`
- Neues Farbschema im `:root`/`.dark`-Block und `@theme inline`:
  - Hintergrund/Card/Surface: dunkles Grün (z. B. oklch-Werte um #0f1f1a)
  - Primär: Cyan
  - Akzent/Positive Trends: Orange
  - Destruktiv/Negative Trends: Rot
  - Text: helle Graustufen für guten Kontrast
- Neue semantische Tokens ergänzen (z. B. `--success`, `--warning`, `--kpi-up`, `--kpi-down`).

### 2. Dashboard-Route `src/routes/index.tsx`
- Platziert den neuen Dashboard-Screen auf `/`.
- Header mit Titel, Zeitraum-Selector (7 Tage / 30 Tage / 90 Tage / 1 Jahr) und Aktualisierungs-Status.
- Hauptgrid:
  - Oben: 4 KPI-Cards (MRR, ARR, Kunden, Churn Rate) mit Betrag, Veränderung in % und Mini-Sparkline.
  - Mitte: Hauptchart MRR/ARR über Zeit (Area/Line-Combo-Chart mit Farbverlauf).
  - Unten links: Kreisdiagramm oder Horizontal-Bar für Revenue-Mix / Plan-Anteile.
  - Unten rechts: Tabelle "Neueste Kunden/Transaktionen" oder Top-Mover-KPIs.

### 3. Komponenten
- `KPICard`: Einheitliche Karte mit Icon, Wert, Delta-Pill, Sparkline.
- `MRRChart`: `recharts` AreaChart mit Gradient-Fill, Tooltip, Achsen im Dashboard-Stil.
- `RevenueMixChart`: `recharts` Pie/Donut oder BarChart.
- `RecentTransactions`: Tabelle mit shadcn/ui `Table`.
- `DashboardShell`: Layout-Wrapper mit Header und Grid.

### 4. Daten
- Zunächst realistische Mock-Daten für MRR/ARR/KPIs (12 Monate + letzte 30 Tage), damit das Dashboard sofort visuell überzeugt.
- Datenstruktur so anlegen, dass später einfach echte API-Daten eingespeist werden können (Server-Fn oder Supabase).

### 5. Interaktion & Motion
- Zeitraum-Selector filtert die Mock-Daten clientseitig.
- Dezentre Hover-States auf Karten und Charts.
- Kein übermäßiges Animations-Feuerwerk – professionell und schnell.

### 6. SEO/Head
- `src/routes/index.tsx` bekommt `head()` mit app-spezifischem Titel, Description und Open-Graph-Tags (kein Placeholder-Text mehr).

## Abgrenzung
- Kein Backend/Auth/DB in diesem Schritt.
- Keine echten Echtzeit-Streams; die Daten sind statische Mocks mit einer "Live"-Anzeige (z. B. "zuletzt aktualisiert vor 2 Minuten").
- Keine komplexen Drill-Downs oder Settings-Seiten.

## Ergebnis
Ein sofort visuell professionelles SaaS-Finanz-Dashboard mit eigenständigem Look, das auf echten Daten aufgesetzt werden kann.
