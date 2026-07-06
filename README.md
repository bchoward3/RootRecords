**Brandon Howard**
University of Wisconsin–Madison — GEOG 576 (mid-term project)
bchoward@wisc.edu
# RootRecords - Genealogy Field Mapping Application

## Overview
RootRecords is a full-stack web mapping application built for genealogy research. It allows users to spatially record and visualize sites of family historical significance — graves, birthplaces, and homes — and trace ancestral and descendant relationships across generations through an interactive family web visualization.

The application was designed with a specific real-world use case in mind: conducting a family mapping project in eastern Kentucky in September 2026. Most genealogical data exists in tabular form with no spatial component. RootRecords addresses this gap by providing a geographic record of family history that can reveal migration patterns, family clustering, and generational dispersal that tabular data alone cannot show.

**Live App:** [https://bchoward.github.io/RootRecords](https://bchoward.github.io/RootRecords)

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | ArcGIS Maps SDK for JavaScript 5.1 (ES Modules) |
| Backend | ArcGIS Online — Hosted Feature Service |
| Database | Three point feature classes + one non-spatial table |
| Hosting | GitHub Pages |
| Field Collection | Esri Field Maps (companion mobile app) |

---

## Features

### Map & Navigation
- Centered on Kentucky with World Topographic basemap
- Home, Locate (GPS), and Address Search widgets
- Basemap Gallery for switching between World Topographic and Light Gray Canvas
- Layer visibility toggles for Graves, Birthplaces, and Houses & Sites
- Tight-radius clustering (~500ft) groups nearby graves in the same cemetery

### Site Management
- **Custom Add Site workflow** — a three-step panel that simultaneously writes a new site record (Grave, Birthplace, or House) and a linked Persons table entry in a single submission, avoiding duplicate data entry
- **New or existing person** — when adding a site, users can either create a new Person record or search existing records and link to one already in the database
- **Edit and Delete** — accessible from both the popup (Edit/Delete action buttons) and the built-in Editor widget
- **Photo and document attachments** — all three site layers support file attachments for photos, scanned documents (e.g. death certificates), and general files

### Filter by Person
- Search for any individual by name to filter all three site layers simultaneously
- Browse all recorded persons alphabetically with birth/death year when available
- Selecting a person from browse automatically applies the filter

### Family Web Visualization
- **Show All Family Web** — draws lines connecting all graves with known parent–child relationships across the entire dataset, revealing migration patterns at a glance
- **Trace Family Web** — activated from the Filter by Person panel after selecting an individual; traces bidirectionally up to 4 generations in both directions:
  - **Red lines** (ancestors) — gets progressively lighter with each generation back
  - **Blue lines** (descendants) — gets progressively lighter with each generation forward
- Name labels appear on all graves involved in the active family web
- Map automatically zooms to fit the full extent of the traced lineage
- Generation key legend displayed when trace is active

---

## Backend Architecture

The backend is a single ArcGIS Online hosted feature service (`RootRecords/FeatureServer`) containing:

| Layer Index | Name | Type | Purpose |
|---|---|---|---|
| 0 | Birthplace | Point feature class | Records where individuals were born |
| 1 | House | Point feature class | Records homes and other sites of interest |
| 2 | Graves | Point feature class | Records burial sites |
| 3 | Persons | Non-spatial table | Stores biographical data (DOB, DOD, Father, Mother) |

Relationship classes are defined between each site layer and the Persons table via `GlobalID → person_globalid`, allowing a single Person record to be linked to multiple sites across different feature classes.

Attachments are enabled on all three point feature layers to support photo capture via Esri Field Maps.

---

## Family Web — How It Works

The family web feature matches names between the `Father` and `Mother` text fields in the Persons table and the `person_id` field on the Graves layer. Name matching is case-insensitive. Consistent spelling is required for links to resolve correctly.

**Ancestor trace (upward):** Recursively follows Father/Mother fields from the selected person backward up to 4 generations, drawing a line between each child's grave and the matching parent's grave.

**Descendant trace (downward):** Builds a reverse lookup of who has the selected person listed as their Father or Mother, then follows those links forward up to 4 generations.

---

## Field Maps Integration

RootRecords is designed to work alongside Esri Field Maps for in-the-field data collection:

- The RootRecords web map is available in Field Maps after signing in with ArcGIS Online credentials
- All three feature layers are configured with Field Maps forms: Person Name is the only required field; all other fields are optional and can be backfilled later
- Attachments (photos, documents) can be captured directly from the device camera
- Offline sync is enabled for use in areas with limited cell coverage

## Repository Structure

```
RootRecords/
├── RootRecords.html      # Main application — all HTML, CSS, and JS in one file
└── README.md             # This file
```

## Known limitations and issues

- Family web name matching requires exact spelling consistency between the `Father`/`Mother` fields in the Persons table and the `person_id` field on Graves. Spelling variations will break the link.
- The application is currently set to public sharing on ArcGIS Online to allow unauthenticated access.
- Several ArcGIS SDK widgets (Expand, Legend, BasemapGallery) are marked deprecated in SDK 5.1 in favor of web components. These continue to function correctly but will need to be addressed if updated in the future.



