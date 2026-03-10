# Obsidian Properties & DataView Reference

## Property Types

| Type | YAML Syntax | Use When | Example |
|------|-------------|----------|---------|
| text | `key: "value"` | Single text value | `gegner: "TSV Muster"` |
| list | `key: [a, b, c]` | Multiple queryable values | `tags: [sport, bericht]` |
| number | `key: 42` | Numeric, sortable, calculable | `hoehenmeter: 1200` |
| checkbox | `key: true` | Yes/No toggle | `heim: true` |
| date | `key: 2026-03-15` | Dates (NO quotes!) | `spieltag: 2026-03-15` |
| datetime | `key: 2026-03-15T14:30` | Date with time | `kickoff: 2026-03-15T14:30` |
| aliases | `aliases: [name1]` | Alternative note names | `aliases: [HSV Match]` |
| tags | `tags: [tag1, tag2]` | Broad categorization | `tags: [wasserball]` |
| cssclasses | `cssclasses: [wide]` | Visual styling of the note | `cssclasses: [wide-page]` |

## YAML Frontmatter Rules

- Properties go between `---` markers at the very top of the note
- Dates must NOT be in quotes: `spieltag: 2026-03-15` (correct) vs `spieltag: "2026-03-15"` (wrong — becomes text)
- Text with special characters needs quotes: `titel: "Spiel: HSV vs. FCB"`
- Lists can be inline `[a, b]` or multiline:
  ```yaml
  torschuetzen:
    - Spieler A
    - Spieler B
  ```
- Empty values: `""` for text, leave blank for dates/numbers
- Templater variables go in quotes: `created: "{{date:YYYY-MM-DD}}"`
- Booleans: `true` / `false` (lowercase, no quotes)

## Common Property Sets by Use Case

### Recurring Events (Matches, Meetings, Sessions)
| Property | Type | Purpose |
|----------|------|---------|
| doc-type | text | Note type identifier |
| created | date | Creation date |
| date / event-date | date | When the event happens |
| status | text | Draft, Done, Cancelled |
| teilnehmer | list | Who was there |
| ort | text | Location |
| tags | tags | Categorization |
| MOC | text | Link to parent MOC |

### Projects
| Property | Type | Purpose |
|----------|------|---------|
| doc-type | text | "projekt" |
| status | text | Active, Paused, Done |
| prioritaet | text or number | High/Medium/Low or 1-5 |
| deadline | date | Target date |
| verantwortlich | text | Owner |
| kunde | text | Client |
| tags | tags | Categorization |

### People / Contacts
| Property | Type | Purpose |
|----------|------|---------|
| doc-type | text | "kontakt" |
| firma | text | Company |
| rolle | text | Role / Position |
| email | text | Email address |
| telefon | text | Phone |
| letzter-kontakt | date | Last interaction |
| tags | tags | Relationship type |

### Knowledge / Learning Notes
| Property | Type | Purpose |
|----------|------|---------|
| doc-type | text | "wissen" |
| quelle | text | Source (book, article, URL) |
| autor | text | Author |
| thema | list | Topic areas |
| bewertung | number | Rating 1-5 |
| tags | tags | Subject tags |

### Content / Writing
| Property | Type | Purpose |
|----------|------|---------|
| doc-type | text | "artikel", "post", "skript" |
| status | text | Idee, Entwurf, Review, Fertig |
| plattform | text | Where it will be published |
| zielgruppe | text | Target audience |
| veroeffentlicht | date | Publication date |
| tags | tags | Topics |

### Hiking / Outdoor Activities
| Property | Type | Purpose |
|----------|------|---------|
| doc-type | text | "wanderung" |
| region | text | Area / Mountain range |
| distanz-km | number | Distance in km |
| hoehenmeter | number | Elevation gain |
| dauer | text | Duration (e.g., "4h 30min") |
| schwierigkeit | text | Difficulty level |
| rundweg | checkbox | Round trip yes/no |
| saison | text | Best season |
| tags | tags | [wandern, alpin] |

## DataView Query Patterns

### Basic TABLE
```dataview
TABLE property1 AS "Label 1", property2 AS "Label 2"
FROM ""
WHERE doc-type = "note-type"
SORT date-field DESC
```

### Filtered TABLE (e.g., only open items)
```dataview
TABLE status AS "Status", deadline AS "Frist"
FROM ""
WHERE doc-type = "projekt" AND status != "Erledigt"
SORT deadline ASC
```

### LIST (simpler, just titles with one detail)
```dataview
LIST gegner + " — " + ergebnis
FROM ""
WHERE doc-type = "spielbericht"
SORT spieltag DESC
```

### TASK (todos from notes)
```dataview
TASK
FROM ""
WHERE doc-type = "projekt" AND !completed
```

### CALENDAR (dates on a calendar view)
```dataview
CALENDAR spieltag
FROM ""
WHERE doc-type = "spielbericht"
```

### GROUP BY (organize by category)
```dataview
TABLE rows.file.link AS "Notizen", rows.status AS "Status"
FROM ""
WHERE doc-type = "projekt"
GROUP BY kunde AS "Kunde"
```

## Inline Queries (for MOC stats)

Use inline DataView for single values embedded in text:

- **Count all:** `` `= length(filter(pages(""), (p) => p.doc-type = "spielbericht"))` ``
- **Count filtered:** `` `= length(filter(pages(""), (p) => p.doc-type = "projekt" AND p.status = "Aktiv"))` ``
- **Latest date:** `` `= max(filter(pages(""), (p) => p.doc-type = "spielbericht").spieltag)` ``
- **Sum numbers:** `` `= sum(filter(pages(""), (p) => p.doc-type = "wanderung").hoehenmeter)` ``

## Templater Auto-Fill Syntax

Use inside YAML frontmatter (in quotes) for auto-population:

| Syntax | Result | Example |
|--------|--------|---------|
| `"{{date:YYYY-MM-DD}}"` | Today's date | `2026-03-15` |
| `"{{date:HH:mm}}"` | Current time | `14:30` |
| `"{{title}}"` | Note title | `Spiel gegen TSV` |
| `"{{date:YYYY-[W]ww}}"` | Calendar week | `2026-W11` |
| `"<% tp.system.prompt("Gegner?") %>"` | User prompt | Asks for input |
| `"<% tp.system.suggester(["Heim","Auswaerts"], ["Heim","Auswaerts"]) %>"` | Dropdown | Shows choices |

## Bases (No-Code Alternative to DataView)

For users who prefer visual configuration over code:

1. Create a new file with `.base` extension in Obsidian
2. Define columns matching your Properties
3. Set filters (e.g., `doc-type = "spielbericht"`)
4. Choose view type: Table, Board (Kanban), or Calendar
5. Embed in any note or MOC: `![[my-database.base]]`

Bases are fully interactive — you can edit property values directly in the table view, and changes propagate to the source notes.

## Naming Convention Reference

**Recommended: lowercase-with-hyphens**
- `doc-type`, `spiel-ort`, `letzter-kontakt`, `hoehen-meter`
- Consistent, easy to type, no ambiguity

**Alternative: camelCase**
- `docType`, `spielOrt`, `letzterKontakt`
- Common in programming contexts

**Avoid:**
- Spaces in property names (can cause issues in DataView)
- Mixed conventions in the same vault
- Special characters other than hyphens
- Starting with numbers

**Critical:** `Status` and `status` are DIFFERENT properties in Obsidian. This is the most common source of "my query shows nothing" errors.
