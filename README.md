# Interactive Dashboard with Power BI: Evaluation of Tourist Accommodations

An interactive Power BI report that analyses the official classification of tourist accommodations (hotels, tourist residences, campsites) in Paris and the Île-de-France region. The project covers the full BI workflow: data extraction, transformation in Power Query, data modelling, and interactive visualisation.

## Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Data Sources](#data-sources)
- [Data Preparation (Power Query)](#data-preparation-power-query)
- [Report Pages](#report-pages)
- [Key Power BI Features Used](#key-power-bi-features-used)
- [Getting Started](#getting-started)
- [Tools](#tools)

## Overview

The goal is to explore how classified accommodations (rated 1 to 5 stars) are distributed across Paris and the surrounding area, and to answer questions such as:

- How many establishments were classified each year, and in which category (Paris / outside Paris)?
- What is the split by star rating and by type of establishment?
- Is there a relationship between the number of rooms and the number of stars?
- Where are establishments located, and how large are they?

The report also includes a search tool to look up establishments by name, type, and star rating, with clickable website links.

## Repository Structure

```
.
├── Evaluation des établissements.pbix   # Final Power BI report
├── Hebergement-paris.csv                # Main dataset (classified accommodations)
├── fr_postal_codes.csv                  # French postal codes with coordinates
├── Etoiles_Couleurs.xlsx                # Star image reference (Excel source)
├── 1_etoile.png                         # Star rating images used in the report
├── 2_etoiles.png
├── 4_etoiles.png
├── 5_etoiles.png
├── TP Power BI - Partie 1.pdf           # Guide: data extraction & transformation
└── TP Power BI - Partie2.pdf            # Guide: report building & visualisation
```

## Data Sources

| File | Description | Size |
|------|-------------|------|
| `Hebergement-paris.csv` | Export from the official French register of classified tourist accommodations (classement.atout-france.fr). Semicolon-separated, Latin-1 encoded. | 2,261 rows, 19 columns |
| `fr_postal_codes.csv` | French postal codes with region, department, latitude and longitude. Used to enrich each establishment with coordinates. | ~51,000 rows, 7 columns |
| `Etoiles_Couleurs.xlsx` | Excel file referencing the star image used in the report. | n/a |

**About the main dataset**

- Classification dates range from **2011 to 2016**.
- Star ratings: 3 stars (1,074), 4 stars (536), 2 stars (467), 1 star (103), 5 stars (81).
- Types: hotels (2,076), tourist residences (118), campsites (66), holiday parks (1).
- Columns include classification date, type, star rating, commercial name, address, postal code, municipality, website, capacity, and number of rooms, pitches, housing units, and dwellings.

## Data Preparation (Power Query)

All transformations are done in the Power Query editor.

**Accommodation table (`Hebergement-paris.csv`)**

1. Remove Power BI's automatic transformation steps and re-check them.
2. Remove unused columns: category, mention, phone, email, stay type.
3. Replace `-` with empty values in the numeric columns, then convert them to whole numbers.
4. Split the `CLASSEMENT` column on the space delimiter, drop the "étoile(s)" part, and rename the result to the number of stars.
5. Add a `LOCALISATION` column by concatenating address, postal code, commune and "France", for geocoding on the map.
6. Add a `Année` column extracted from the classification date.

**Postal code table (`fr_postal_codes.csv`)**

1. Promote the first row to headers and keep only postal code, latitude and longitude.
2. Split the postal code column to keep only the numeric code, then rename it `Code Postal`.
3. Replace decimal points with commas and convert to decimal numbers.

**Merge and enrichment**

- Merge both tables on the postal code (as a new query) to retrieve latitude and longitude.
- Add a conditional column to group communes into **PARIS** and **HORS PARIS** (`Regroupement`).
- Add conditional columns for **room-count intervals** and a sort-order column (`Ordre intervalles`).
- Clean the `SITE INTERNET` column by adding a URL prefix (`Début URL`) and building a full `URL Site Internet`.
- Organise intermediate queries in a **Tables temporaires** group with loading disabled, so only the final `Hébergement` table is loaded into the model.

## Report Pages

**Page 1: Evaluation des établissements**
- Slicers: year, grouping (Paris / outside Paris), establishment type, room-count interval.
- KPI cards: number of evaluations (distinct addresses), number of stars, number of rooms.
- Map: rooms and stars by location (bubble size = rooms, colour saturation = stars).
- Star images and a chart of establishments by star rating.
- Line chart of establishments by star category over time (year slicer interaction disabled so the trend stays visible).

**Page 2: Etude des évaluations**
- Column chart of evaluations per year with **drill-down** (year → Paris / outside Paris → establishment type).
- Treemap by grouping.
- Bar charts by room-count interval and by establishment type, sorted in descending order.
- Scatter plot of rooms vs. stars, with a calculated average measure.

**Page 3: Information search**
- Search-engine style page to look up establishments, with website links displayed as URLs.

**Page 4: Recherche d'établissements**
- Search page with a page-level filter excluding the single "Parc résidentiel de loisirs" entry, and clickable website links (data category set to Web URL).

## Key Power BI Features Used

- Power Query: column splitting, replacement, type conversion, custom and conditional columns, date extraction, query merging, query groups
- DAX measures (for example, averages)
- Slicers and edited visual interactions
- Drill-down hierarchies
- Map visual with geocoded addresses
- Scatter plot, treemap, bar, column and line charts
- Page-level filters and data categories (address, URL)

## Getting Started

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Windows).
2. Clone or download this repository.
3. Open `Evaluation des établissements.pbix`.
4. If Power BI cannot find the data, go to **Home → Transform data → Data source settings** and update the paths of `Hebergement-paris.csv` and `fr_postal_codes.csv` to your local folder.
5. Click **Refresh** to reload the data.

To rebuild the report from scratch, follow the two guides in order: `TP Power BI - Partie 1.pdf` (data preparation) then `TP Power BI - Partie2.pdf` (report building). Both are in French.

## Tools

- Power BI Desktop
- Power Query (M language)
- DAX
- Excel / CSV data sources
