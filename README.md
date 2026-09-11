# Interactive Data Visualization

Interactive analytical stories built with Observable Framework, Observable Plot, JavaScript, and geospatial data.

[Open the deployed project](https://jborri.github.io/Interactive-Data-Vis-Fall2025/)

## Overview

This repository documents a semester of interactive visualization work completed in CUNY Graduate Center's Data Analysis and Visualization program. The projects move from exploratory charts to multi-dataset dashboards, geospatial analysis, annotations, and narrative explanation.

The datasets and scenarios were supplied through course assignments unless a project page states otherwise. The analysis, visualization choices, interaction design, and written interpretations are my work.

## Featured projects

### Mayoral Mystery

A geospatial campaign-analysis dashboard combining election results, survey responses, campaign events, and NYC district geometry. The project uses maps and linked comparisons to identify demographic and policy patterns and develop recommendations for a hypothetical future campaign.

[View Mayoral Mystery](https://jborri.github.io/Interactive-Data-Vis-Fall2025/lab_3/)

### Clearwater Crisis

An investigative data story combining fish surveys, water-quality measurements, monitoring-station locations, and activity records. The analysis follows temporal and spatial evidence to evaluate competing explanations for a fictional ecological decline.

[View Clearwater Crisis](https://jborri.github.io/Interactive-Data-Vis-Fall2025/lab_4/)

### Subway Staffing

A multi-table operations dashboard relating ridership, local events, incident response, current staffing, and a future event calendar to recommend stations for additional staffing.

[View Subway Staffing](https://jborri.github.io/Interactive-Data-Vis-Fall2025/lab_2/)

### Prolific Pollinators

An exploratory dashboard examining pollinator morphology, weather conditions, visit frequency, and nectar production.

[View Prolific Pollinators](https://jborri.github.io/Interactive-Data-Vis-Fall2025/lab_1/)

## Skills demonstrated

- exploratory analysis across multiple CSV datasets;
- interactive charts and tooltips with Observable Plot;
- temporal and categorical comparison;
- choropleth and proportional-symbol mapping;
- GeoJSON and TopoJSON workflows;
- annotations and explanatory narrative;
- responsive dashboard composition; and
- automated deployment through GitHub Actions and GitHub Pages.

## Technology

- Observable Framework
- Observable Plot
- JavaScript
- Markdown and HTML
- CSS
- CSV, GeoJSON, and TopoJSON
- GitHub Actions and GitHub Pages

## Run locally

```bash
git clone https://github.com/jborri/Interactive-Data-Vis-Fall2025.git
cd Interactive-Data-Vis-Fall2025
npm ci
npm run dev
```

Then open the local address reported by Observable Framework.

To create a production build:

```bash
npm run build
```

## Repository guide

```text
src/
├── index.md            Project landing page
├── lab_1/              Pollinator exploration
├── lab_2/              Subway staffing analysis
├── lab_3/              Geospatial campaign analysis
├── lab_4/              Environmental investigation
└── lab_4.5/            Additional visualization experiments
```

Each project directory contains the dashboard, its local data files, and the original assignment brief for context.
