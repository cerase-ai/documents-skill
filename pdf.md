# PDF

A PDF is made from content you hold, for internal use: notes, a summary, an export, a certificate on a designed page. A quote, a proposal, a report or a letter that a client or management will read is the `business-document` skill: it renders the PDF with `render_document`, whose layout reads as a business document. These routes give a layout that reads as an academic paper, which is wrong for those. A presentation as a PDF is the `deck` skill. A table as a PDF is `spreadsheets`. Every call belongs to the `cerase-office-converter` connector, and each answers `{path, filename, size_bytes}`: the PDF is at `outputs/<file name>`.

## Choose the route

### Route 1: markdown to PDF (the default)

For text with headings, lists and tables. Write `<name>.md` in the workspace as `office-converter.md` says (the YAML block with `title`, `author`, `date` and `lang`, `#` sections, `- ` bullets, pipe tables). Then:

```
call_recipe("cerase-office-converter.convert_md_to_pdf", {"path": "<name>.md", "output_filename": "<name>.pdf"})
```

### Route 2: a Word document to PDF

When the document already exists as a `.docx` (made as `office-converter.md` says, or sent by the person):

```
call_recipe("cerase-office-converter.convert_docx_to_pdf", {"path": "<file>.docx", "output_filename": "<name>.pdf"})
```

### Route 3: a designed page to PDF

When the layout itself matters: a certificate, a letter on letterhead, a one-page flyer, columns placed where you choose. Write `<name>.html` in the workspace, one file with its CSS in a `<style>` block, then:

```
call_recipe("cerase-office-converter.convert_html_to_pdf", {"path": "<name>.html", "output_filename": "<name>.pdf", "paper": "A4", "orientation": "portrait"})
```

It prints the page the way a browser does, so CSS grid, flexbox and background colours are kept. `paper` is A4, A3, A5, letter or legal; `orientation` is portrait or landscape. Put images inline as `data:` URIs: the converter receives the HTML file alone, so an image named by a relative path is not found. Write the HTML with real structure: `<h1>` to `<h3>` in order, `<ul>`/`<ol>` for lists, `<table>` with `<th>` for a data table, `<html lang="…">` set, and text with a contrast of at least 4.5:1.

## A PDF of a Google file

A PDF of a Google Doc, Sheet or Slides is the one Google makes, never one rebuilt from its text, which loses the layout: `google-docs.md`, "A file from a Google Doc".

## Style

- **A4 portrait** unless the person asks for landscape.
- **Fonts**: in Route 3 use Liberation Serif, Liberation Sans, DejaVu or Noto; other fonts are not on the converter and print as a substitute.
- **One title**, then sections; a report longer than a few pages starts with a short summary.
- More than 50 pages: ask first. It usually means the source was too long to turn into prose.

## Read it back and check it

```
call_recipe("cerase-docreader.read_document", {"path": "outputs/<name>.pdf"})
```

Check the title, every section in order, every table and every figure against the source, and that no `#`, `**` or `|` shows up as text. A PDF made from your own markdown that carries text as symbols is converted again from corrected markdown.

## Deliver

`[[attach: outputs/<name>.pdf]]`; never the content or any base64 in the chat, and no file paths the person does not need. These calls are the complete set; do not invent others. Filling or signing a PDF the person sent is not part of this skill.
