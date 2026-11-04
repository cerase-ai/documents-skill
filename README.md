# documents-skill

A Cerase skill that has the assistant produce or change a text document in any
format: Word (`.docx`), an OpenDocument text (`.odt`), a Google Doc, or a PDF.
It replaces the `docx` and `pdf-writer` skills. `business-document` calls it for
Word and Google Docs output, `source-to-artifact` and `deck` for documents, and
it also serves a direct request for a document in one of these formats.

## What the assistant does

`SKILL.md` holds the rules every tool shares, and a table of which reference to
read for which tool:

- **Rules:** real heading styles in order and no skipped level; real lists, at
  most 7 items a group; tables with a header row, right-aligned numbers and no
  merged cells; paragraph spacing instead of empty paragraphs; descriptive link
  text; the language stated; no image that carries information, because the
  tools write no alternative text; nothing invented and nothing dropped; no
  formatting symbol left as text.
- **Check:** the assistant reads the document back for the heading outline,
  lists, table headers and leftover symbols, and says what is missing.
- `office-converter.md`: the markdown shape, `convert_md_to_docx`
  with an optional Word template, read-back, `.odt`.
- `google-docs.md`: a new Doc by uploading the `.docx` converted, or
  `createDocFromHTML`; text into a Doc that exists; read-back with
  `getGoogleDocStyleSummary`; export to PDF or Word.
- `pdf.md`: markdown, Word or a designed HTML page to PDF, for
  internal documents. A document a client will read is `business-document`.

The assistant's container has no Python or office software, so it never builds a
file itself and never pastes file content or base64 in the chat.

## Requirements

- The `cerase-office-converter` connector for `.docx`, `.odt` and PDF, and
  `cerase-docreader` to read results.
- The Google Workspace connector for Google Docs.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | The instructions the assistant loads: `name` and `description` frontmatter, the shared rules, the check and the closed set of operations. |
| `` | One file per tool, read on demand: the office converter, Google Docs, PDF. |
| `cerase.json` | Marketplace manifest: namespace `studio.guidance`, name `documents`, display name, description, licence. |
| `i18n.yaml` | Italian display name and description for the Marketplace; not sent to the assistant. |
| `LICENSE` | MIT licence text. |

## License

MIT. See [LICENSE](LICENSE).
