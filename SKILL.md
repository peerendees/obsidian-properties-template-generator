---
name: obsidian-property-template-generator
description: >
  Generates complete Obsidian note templates with YAML frontmatter Properties, DataView queries, and MOC (Map of Content) dashboard structures — tailored to a user's specific use case. Use this skill whenever someone wants to create or improve an Obsidian template, define Properties for a new note type, structure recurring activities (reports, meetings, projects, trips, recipes, client work, sports, hobbies, etc.) in Obsidian, set up DataView queries, or build a MOC dashboard. Also trigger when users describe any repeating workflow and ask how to organize it in Obsidian — even without explicitly saying "template" or "properties". If someone mentions Obsidian and any form of structuring, organizing, or tracking notes, this skill applies. Trigger also for German phrases like "Obsidian Vorlage", "Eigenschaften anlegen", "Notiz-Template", "Properties definieren", or "Dashboard in Obsidian bauen".
---

# Obsidian Property Template Generator

You help users create well-structured Obsidian templates by guiding them through a focused discovery process, then generating ready-to-paste templates with Properties, DataView queries, and a MOC dashboard.

## Why Properties matter

Properties (YAML frontmatter) are the backbone of a connected Obsidian vault. They determine how notes link to each other, how DataView queries find and display information, and how MOCs come alive as interactive dashboards. Getting Properties right from the start saves hours of restructuring later.

Many users are transitioning from folder-based thinking to property-based, networked thinking. This is a genuine mindset shift — like going from filing cabinets to a search engine. Be concrete, practical, and encouraging. Use the user's own examples to make abstract concepts tangible.

Adapt your language to the user. If they write German, respond in German. If English, respond in English.

## Phase 1: Discovery (2-3 questions, not more)

Start conversational. Don't dump all questions at once — pick the most relevant 2-3.

**Always ask:**
1. "Was genau willst du in Obsidian festhalten?" — Get the concrete use case. Push for specifics: not "Berichte" but "Spielberichte fuer meine Wasserball-Mannschaft" or "Wandertouren mit Hoehenmeter und Region".
2. "Welche Infos brauchst du auf einen Blick, wenn du eine Liste dieser Notizen siehst?" — This reveals which properties belong in a DataView table.

**Ask if unclear:**
- "Gibt es verschiedene Kategorien?" — Subtypes help decide between `text` and `list` properties.
- "Willst du einen Status tracken?" — Determines if workflow properties are needed.
- "Machst du das regelmaessig oder ist es einmalig?" — Templates make sense for recurring notes.

Once you have a clear picture, move to Phase 2. Don't over-interview — 2-3 answers are usually enough.

## Phase 2: Property Design

Suggest a property set as a clear table. For each property, include the name, type, an example value, and a brief explanation of why it's useful.

**Property type selection logic:**
- Single fixed value → `text`
- Multiple values that should be queryable individually → `list` (renders as pills)
- Yes/No → `checkbox`
- A date → `date` (no quotes in YAML!)
- A number you might sort or calculate with → `number`
- For broad categorization → `tags`

**Always include these core properties:**
- `doc-type` — Identifies the note type so DataView can filter (e.g., `spielbericht`, `wanderung`, `projekt`)
- `created` — Creation date (auto-filled via Templater: `"{{date:YYYY-MM-DD}}"`)
- `tags` — For discoverability across the vault
- `status` — If the use case has any workflow aspect
- `MOC` — Link back to the parent MOC: `"[[MOC Name]]"`

**Present as a table:**

| Property | Typ | Beispiel | Zweck |
|----------|-----|----------|-------|
| doc-type | text | "spielbericht" | Identifiziert den Notiz-Typ fuer Abfragen |
| created | date | 2026-03-15 | Erstelldatum (auto via Templater) |
| gegner | text | "TSV Blau-Weiss" | Gegner des Spiels |
| spieltag | date | 2026-03-15 | Datum des Spiels |
| tags | tags | [wasserball, bericht] | Verschlagwortung |

After presenting, ask: "Passt das so, oder fehlt dir noch was?" — Let the user adjust before generating.

**Start small principle:** If the user seems overwhelmed, suggest 5-6 core properties and mention that more can be added later. Adding properties is easy — cleaning up inconsistent ones is painful.

## Phase 3: Generate the Template

Output a complete, ready-to-paste Obsidian template inside a single code block.

**Template structure:**

```
---
<all properties as YAML frontmatter>
---

# {{title}}

<Sections with headers matching the use case>
<Placeholder text guiding the user what to write>
```

**Rules for the template:**
- `created` uses Templater syntax: `"{{date:YYYY-MM-DD}}"`
- Dates must NOT be in quotes in YAML (except Templater variables)
- Text values with special characters need quotes
- Empty text values: `""`
- Empty dates/numbers: leave blank (no quotes)
- Lists: use `[]` for empty, `[value1, value2]` for defaults
- Include meaningful section headers in the body
- Add short placeholder text in each section so the user knows what goes there
- Keep the body focused — don't add 20 sections, 4-6 is usually right

## Phase 4: DataView Query

Generate a DataView query that displays notes created with this template. Always provide:

**1. A TABLE query** (primary — shows notes as a sortable table):

````
```dataview
TABLE property1 AS "Label", property2 AS "Label"
FROM ""
WHERE doc-type = "note-type"
SORT date-property DESC
```
````

- Use `AS "Label"` to give columns readable names
- Filter by `doc-type` so only the right notes appear
- Sort by the most logical field (usually a date, descending)
- Pick 3-5 columns max for readability

**2. Optionally: a filtered view** (e.g., only drafts, only upcoming):

````
```dataview
TABLE property1 AS "Label", status AS "Status"
FROM ""
WHERE doc-type = "note-type" AND status = "Entwurf"
SORT date-property ASC
```
````

Briefly explain what the query does and how the user can modify it. Mention that the DataView plugin must be installed.

**Bases alternative:** For users intimidated by code, mention that Obsidian Bases offers a visual, no-code way to create the same table views. A `.base` file with the right filters achieves the same result without writing DataView queries.

## Phase 5: MOC Dashboard

Suggest a MOC (Map of Content) that serves as a command center for this note type.

**MOC structure:**

````
---
doc-type: moc
tags: [moc, <topic>]
---

# MOC <Topic Name>

## Offene / Aktuelle <Items>
```dataview
TABLE <key columns>
FROM ""
WHERE doc-type = "<type>" AND status != "Erledigt"
SORT <date> DESC
```

## Alle <Items>
```dataview
TABLE <all relevant columns>
FROM ""
WHERE doc-type = "<type>"
SORT <date> DESC
```

## Quick Stats
- Gesamt: `= length(filter(pages(""), (p) => p.doc-type = "<type>"))`
- <Relevant stat>: `= length(filter(pages(""), (p) => p.doc-type = "<type>" AND p.<property> = true))`
````

Explain that the MOC is a living dashboard — as notes are created and updated, the MOC updates automatically. This is the "magic" that makes Properties worthwhile.

## Phase 6: Quick Start

End with 3 concrete next steps. Keep them actionable and small:

1. **Template anlegen:** Kopiere das Template in deinen Obsidian-Vault unter `Templates/` und aktiviere es in den Templater-Einstellungen.
2. **Erste Notiz erstellen:** Erstelle eine Notiz mit dem Template und fuelle die Properties aus — so siehst du sofort, wie es funktioniert.
3. **MOC einrichten:** Kopiere das MOC-Template und lege es ab. Nach 2-3 Notizen fuellt sich das Dashboard von selbst.

## Output format summary

Present everything in this order:
1. Property overview (table)
2. Complete template (in code block, ready to copy)
3. DataView query (in code block, ready to paste)
4. MOC template (in code block, ready to copy)
5. Quick Start (3 next steps)

## Naming conventions — warn about this

This is critical and easily overlooked: Obsidian treats `Status`, `status`, and `STATUS` as three completely different properties. Pick ONE naming convention and use it everywhere.

Recommended: **lowercase with hyphens** (`doc-type`, `spiel-ort`, `letzter-kontakt`) — consistent, easy to type, works well in DataView.

Warn the user about this once during the property design phase. Inconsistent naming is the #1 source of "my DataView query shows nothing" frustration.

## Reference

For the complete list of Obsidian property types, DataView query patterns, and common property sets by use case, read `references/obsidian-properties-guide.md`.
