---
name: documents
description: "Makes or changes a text document in any format: Word (.docx), LibreOffice or OpenDocument (.odt), Google Docs, or PDF. Use whenever the person asks for a document, a report, notes, a summary, a letter or a certificate as a Word, Google Doc or PDF file, wants a Word file turned into PDF or ODT, or wants text added to a Google Doc that already exists. A quote, proposal, report or letter that a client or management will read starts with the `business-document` skill, which calls this one for Word and Google Docs. Holds the rules every tool shares (real heading and list styles, tables with a header row, descriptive links, a stated language, nothing typed to imitate formatting) and says which reference to read for the tool that makes the file: the office converter, the Google Workspace connector, or the PDF routes."
---
# Documents

A document is made by a tool, never by you: your container runs no Python and no office software. This file holds the rules that apply to every tool; the reference for the tool you use holds its exact calls.

## Choose the tool

| The person wants | Read first |
|---|---|
| a Word file (`.docx`), a LibreOffice file (`.odt`) | `office-converter.md` |
| a Google Doc, new or an existing one to change | `google-docs.md` |
| a PDF | `pdf.md` |

When the person names no format, make a `.docx`. Make a Google Doc only when they ask for a Google file or give you a Doc to change: it puts the content in their Drive.

Other kinds of file are other skills: a table with figures is `spreadsheets`, slides are `presentations` (or `deck` for an HTML or PDF deck). A quote, an offer, a proposal, a report or a letter for a client or for management is the `business-document` skill, which writes it and then calls this skill for Word or a Google Doc and renders the PDF itself. Read the reference before the first call, and again in each new turn that makes or changes a document.

## Inputs

From the caller (`business-document`, `deck`, `source-to-artifact`) or from the person:
- **the content**: a markdown file in the workspace, or the text from the conversation;
- **the format**: `docx`, `odt`, Google Doc or PDF;
- **the file name**, such as `report-q3.docx`;
- **a template**, optional: a `.docx` in the workspace whose fonts, colours and heading styles the document takes.

## Rules every tool shares

1. **Structure comes from styles, not from looks.** The title is the title, and sections are real heading styles in order: Heading 1, then Heading 2 under it, then Heading 3 at most. A level is never skipped, and a bold line is never used in place of a heading.
2. **A list is a real list.** Never type `•`, `-` or `1.` as characters at the start of a paragraph. At most 7 items in a group; a longer one is split under sub-headings.
3. **A table holds data and has a header row.** Numeric columns are right-aligned. No merged or split cells, no blank row or column, and no table used for layout.
4. **Spacing comes from paragraph spacing.** Never an empty paragraph or a stack of line breaks to make room.
5. **A link has descriptive text** ("the Q3 price list"), never "click here" or a bare address.
6. **Colour is never the only way a meaning is shown**, and text keeps a contrast of at least 4.5:1 against its background.
7. **An image that carries information needs a description for a screen reader.** The tools below cannot write one, so such an image is not added without telling the person to set its description in the editor. The converter cannot carry images at all.
8. **The language** is stated in the document (`lang` in the Word file's header block) and is the source's, or the person's chat when the source has none.
9. **Nothing is invented and nothing is dropped.** A figure, a name or a date comes from the source you were given. When the source is too large for one document, ask in the person's language whether to split it, for example one document per chapter; ask before a PDF of more than 50 pages.
10. **Formatting symbols never show as text.** A `#`, `**`, `|` or `- ` in the finished document means the style was not applied.

## Check before you hand it over

Read the result back with the way the reference gives, and check:

- the headings, in order, are the source's, at the right levels, and the title is the title;
- every list is a list and every table has its header row;
- no formatting symbol is left in the text, and no slot left to fill such as `<...>`; a `⟦…⟧` placeholder you read back stands for a value the document holds in full, so it stays as it is;
- the figures, names and dates are the source's.

Fix what fails and read back again. Then tell the person in a few lines what the document contains, and what is missing and what would settle it.

## The operations

These are all the operations this skill uses; do not invent others. Their exact forms are in the references.

- Office converter: `convert_md_to_docx`, `convert_docx_to_odt`, `convert_odt_to_docx`, `convert_docx_to_pdf`, `convert_md_to_pdf`, `convert_html_to_pdf`.
- Google Workspace connector: `uploadFile`, `downloadFile`, `createDocFromHTML`, `readGoogleDoc`, `getGoogleDocStyleSummary`, `copyFile` and, for text in a Doc that exists, `getDocumentInfo`, `describeGoogleDocRange`, `insertText`, `applyParagraphStyle`, `applyTextStyle`, `createParagraphBullets`, `findAndReplaceInDoc`.
- Reading: `cerase-docreader.read_document`.

## Deliver

Attach a file with `[[attach: outputs/<name>.<ext>]]`. For a Google Doc, give the person the link the tool answered with. Never paste a file's content or any base64 in the chat.

## Don't

- Don't write Python, or call `libreoffice`, `soffice`, `pandoc` or a browser from bash: none of them is in your container.
- Don't password-protect a PDF: these tools cannot.
- Don't replace the whole content of a Google Doc that already has a layout (`updateGoogleDoc`): it loses the styles, header, footer and letterhead.
