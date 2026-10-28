# xml-modlog

A single-file, dependency-free viewer for the modification history stored in a FileMaker **Save a Copy as XML** export. Drop in the XML and see, for every schema object, how many times it has been modified, when it was last changed, and by whom.

Everything runs locally in the browser (or a FileMaker web viewer). Nothing is uploaded.

## What it shows

Each object that carries a modification stamp in the XML becomes one row:

| Column | Source |
| --- | --- |
| Catalog | Object type — Table, Field, Script, Layout, Value List, Custom Function, Custom Menu, Table Occurrence, Relationship, Account, Privilege Set, Theme, etc. |
| ID | The object's internal id |
| Name | Object name (fields shown as `Table::Field`, relationships as `TO A → TO B`) |
| Mod Count | Number of modifications |
| Mod Time | Last modification timestamp |
| User | User name that made the last change |
| Account | Account name that made the last change |

CSV export also includes each object's UUID.

## Features

- Drag-and-drop or click-to-choose loading; large exports (hundreds of MB) are parsed in a Web Worker
- Search across name, user, account, catalog, and id
- Filter by catalog, sort by any column, paginate (50 – 1000 rows or all)
- Hide individual rows, then restore them with **Show hidden**
- Export the current view to CSV
- Light / dark theme toggle (remembered per browser)

## Usage

### In a browser

Open `index.html` directly, or serve the folder:

```bash
python3 -m http.server 8765
```

Then browse to `http://localhost:8765` and drop an XML file onto the page.

### In a FileMaker web viewer

Point a web viewer at the contents of `index.html` (for example, store the HTML in a field and use a `data:text/html,` URL, or load it from disk).

Web viewer setup:

- Turn **off** *Display progress bar* and *Display status messages*. Both reserve a strip at the bottom of the web viewer that the page can't paint over.
- Enable *Allow JavaScript to perform FileMaker scripts* so exports work.

Browser-style downloads don't work inside FileMaker's web viewer, so exports are handed to a FileMaker script named **Export File** through `FileMaker.PerformScript`. The parameter is JSON:

```json
{ "name": "MyFile_modlog.csv", "data": "<base64>" }
```

A minimal **Export File** script:

```
Set Variable [ $p ; Value: Get ( ScriptParameter ) ]
Set Variable [ $name ; Value: JSONGetElement ( $p ; "name" ) ]
Set Variable [ $bin ; Value: Base64Decode ( JSONGetElement ( $p ; "data" ) ; $name ) ]
Set Field [ Globals::gExport ; $bin ]
Export Field Contents [ Globals::gExport ; "$name" ; Automatically open ]
```

`Globals::gExport` is any global container field.

## Generating the XML

In FileMaker Pro: **Tools › Save a Copy as XML…**. The mod counts and timestamps come from the `UUID` elements FileMaker writes for each object.

## Version

The current build number is shown next to the title on the start screen and at the bottom left of the list view (`APP_VERSION` in `index.html`).

## License

GPL-3.0 — see [LICENSE](LICENSE).
