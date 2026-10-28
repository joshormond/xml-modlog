# xml-modlog

A single-file, dependency-free viewer for the modification history in a FileMaker **Save a Copy as XML** export. Load the XML and see, for every schema object, how many times it has been modified, when it was last changed, and by whom.

Parsing happens locally. Nothing is uploaded.

## What it shows

| Column | Contents |
| --- | --- |
| Catalog | Object type: Table, Field, Script, Layout, Value List, Custom Function, Custom Menu, Table Occurrence, Relationship, Account, Privilege Set, Theme, etc. |
| ID | The object's internal id |
| Name | Object name (fields as `Table::Field`, relationships as `TO A → TO B`) |
| Mod Count | Number of modifications |
| Mod Time | Last modification timestamp |
| User | User name of the last change |
| Account | Account name of the last change |

## Features

- Drag and drop or click to choose an XML file; large exports are parsed in a background worker
- Search by name, user, account, catalog, or id
- Filter by catalog, sort by any column, paginate (50 to 1000 rows, or all)
- Hide rows and restore them with **Show hidden**
- Export the current view to CSV (includes each object's UUID)
- Light / dark theme toggle

## Usage

Open `index.html` in a browser, or load it in a FileMaker web viewer.

### FileMaker web viewer notes

- Turn off **Display progress bar** and **Display status messages** in the web viewer setup. Both reserve a strip at the bottom of the web viewer.
- Browser downloads don't work inside the web viewer, so exports call a FileMaker script named **Export File** via `FileMaker.PerformScript`, passing JSON: `{ "name": "<file name>", "data": "<base64>" }`.

## License

GPL-3.0. See [LICENSE](LICENSE).
