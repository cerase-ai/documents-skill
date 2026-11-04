# Word and LibreOffice: the office converter

You write the document as markdown in your workspace, and the converter turns it into Word. Every call below belongs to the `cerase-office-converter` connector, and each answers `{path, filename, size_bytes}`: the file is in your workspace at `path`, which is `outputs/<file name>`.

## Step 1: write the markdown

Write `<name>.md` with your write tool. The converter reads this shape:

- A YAML block on the first lines makes the title block, and says the language:
  ```
  ---
  title: Quarterly report for Northwind Traders
  subtitle: Third quarter 2026
  author: Ada Rossi
  date: 2026-10-05
  lang: en
  ---
  ```
- `#` for a section (Heading 1), `##` for a sub-section (Heading 2), `###` at most (Heading 3). The title is in the YAML block, not a `#` line. Never go from `#` to `###`.
- `- ` for bullets and `1. ` for numbered lists, each item on its own line, at most 7 items in a group.
- Pipe tables with a header row. A numeric column is right-aligned with `|---:|` under its header. No merged cells.
- `**bold**` and `*italic*`; a link is `[the Q3 price list](https://...)`.
- A blank line between paragraphs; no empty paragraphs to make space.
- No images: the converter receives this one file, so an image path inside it is not found.

## Step 2: convert

```
call_recipe("cerase-office-converter.convert_md_to_docx", {"path": "<name>.md", "output_filename": "<name>.docx"})
```

When the person gives you a Word template (a `.docx` in the workspace), add `"reference_doc_path": "<template>.docx"`: the document then takes the template's fonts, colours and heading styles. A letterhead that is a Word file is passed the same way.

## Read it back and check it

```
call_recipe("cerase-docreader.read_document", {"path": "outputs/<name>.docx"})
```

The text comes back as markdown: a Word heading appears as a `#` line, a list item as `- `, and a table with its header row as a pipe table. That is how the check sees that the styles were applied. What `SKILL.md` lists applies: the headings in order and at the right levels, every list a list, every table with its header, the figures the source's. A `#` or `**` that shows up in the middle of a paragraph is text left unconverted: correct the markdown and convert again.

## Other formats

- **OpenDocument (.odt)**: convert the `.docx` you just made: `call_recipe("cerase-office-converter.convert_docx_to_odt", {"path": "outputs/<name>.docx", "output_filename": "<name>.odt"})`
- **An .odt the person sent**, to read or to change: `call_recipe("cerase-office-converter.convert_odt_to_docx", {"path": "<path>", "output_filename": "<name>.docx"})`
- **PDF**: `pdf.md`
- **A Google Doc**: `google-docs.md`, route 1.

## A Word file the person already has

The converter cannot open a Word file and change it. Read it with `call_recipe("cerase-docreader.read_document", {"path": "<path>"})` and offer to write a new document from what it says, with their file as `reference_doc_path` so the styles carry over. Tell them the new file is rebuilt, not edited; tracked changes and comments in the original are not carried over.

## Deliver

`[[attach: outputs/<name>.docx]]`. These calls are the complete set; do not invent others.
