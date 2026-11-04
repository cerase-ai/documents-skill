# Google Docs

Every call here belongs to the `google-workspace` connector, which the organisation's administrator assigns. When it is not among your connectors, say so in the person's language and offer a `.docx` (`office-converter.md`) or a PDF (`pdf.md`). Make or change a Google Doc only when the person asked for one: the content goes to their Drive.

## Which route

| The person wants | Route |
|---|---|
| a new Google Doc | **Route 1**: write the markdown, build a `.docx` with the converter and upload it converted. Headings, lists and tables arrive as Google styles. |
| a new Google Doc and the converter is not among your connectors | **Route 2**, `createDocFromHTML` |
| text added to or changed in a Doc that exists | **Route 3** |

`createGoogleDoc` is not used for a document with structure: it writes the text as plain Normal text, so a `#` or `**` shows up as symbols and no heading exists.

## Route 1: a new Doc from a .docx

Write the markdown and build the `.docx` as `office-converter.md` says, check it, then upload it converted:

```
call_recipe("google-workspace.uploadFile", {"localPath": "outputs/<name>.docx", "name": "<title>", "mimeType": "application/vnd.openxmlformats-officedocument.wordprocessingml.document", "convertToGoogleFormat": true})
```

The answer carries the new file's `Link:`; give the person that link. Then read it back as "Read it back" says, with the new file's id.

## Route 2: create the Doc from HTML

```
call_recipe("google-workspace.createDocFromHTML", {"name": "Quarterly report", "html": "<h1>Quarterly report for Northwind Traders</h1><h2>Summary</h2><p>Revenue grew 18% on the quarter.</p><ul><li>Three new accounts</li><li>Two renewals</li></ul><h2>Figures</h2><table><tr><th>Line</th><th style=\"text-align:right\">Q3</th></tr><tr><td>Revenue</td><td style=\"text-align:right\">2,300,000</td></tr></table>"})
```

Tags become Google styles: `<h1>` to `<h3>` Heading 1 to 3 (in order, none skipped), `<p>` Normal text, `<b>` and `<i>` bold and italic, `<ul>` and `<ol>` lists, `<table>` a table, `<a href="…">descriptive text</a>` a link. Put `<th>` cells in a table's first row, and look at the first row again in the read-back. A document with the same name in that folder is refused. The answer carries `ID:` and `Link:`.

## Route 3: text in a Doc that exists

The Doc's layout, styles, header and footer belong to it, and your text has to land inside them as if its author had typed it.

1. **Read it.** `call_recipe("google-workspace.getDocumentInfo", {"documentId": "<id>"})` for what it is; `call_recipe("google-workspace.readGoogleDoc", {"documentId": "<id>", "format": "markdown"})` for its headings and paragraphs in order; for the paragraphs on each side of the place your text goes, `call_recipe("google-workspace.describeGoogleDocRange", {"documentId": "<id>", "textToFind": "<a few words of that paragraph>"})`, which gives their indices and `namedStyleType`.
2. **Say where the text goes**, before any write, in the person's language: after which paragraph, before which heading, in which style. When two places fit, ask. When the person has not said whether to change the original or work on a copy, ask: `call_recipe("google-workspace.copyFile", {"fileId": "<id>", "newName": "<title> (draft)"})` makes the copy.
3. **Insert at a paragraph boundary**, with the index of the paragraph your text goes before, and your text ending with `\n`: `call_recipe("google-workspace.insertText", {"documentId": "<id>", "text": "<your paragraphs, each ending with \n>", "index": <start index of the following paragraph>})`. Every write moves the indices after it: read again before the next write, or write from the end of the document towards its start. `insertText` writes plain characters, so no markdown syntax goes in.
4. **Set the style of what you inserted**, whatever it looks like, because inserted text takes the style of the paragraph it lands in, a heading included: `call_recipe("google-workspace.applyParagraphStyle", {"documentId": "<id>", "startIndex": <start>, "endIndex": <end>, "namedStyleType": "NORMAL_TEXT"})` for body text, `"HEADING_2"` and so on for a heading of yours. Font, size and colour like the neighbours': `call_recipe("google-workspace.applyTextStyle", {"documentId": "<id>", "startIndex": <start>, "endIndex": <end>, "fontFamily": "<font>", "fontSize": <size>, "foregroundColor": "<#hex>"})`. A list: `call_recipe("google-workspace.createParagraphBullets", {"documentId": "<id>", "startIndex": <start>, "endIndex": <end>, "bulletPreset": "BULLET_DISC_CIRCLE_SQUARE"})`, or `"NUMBERED_DECIMAL_ALPHA_ROMAN"` for numbers.
5. **A placeholder in a template** is replaced in place, and keeps its style. Count first: `call_recipe("google-workspace.findAndReplaceInDoc", {"documentId": "<id>", "findText": "<placeholder>", "replaceText": "<value>", "matchCase": true, "dryRun": true})`; only when it counts the matches you mean, the same call without `dryRun`.

A letterhead in Google Docs is copied for each letter with `copyFile`, and the copy's body is filled by insertion or placeholders. The `business-document` skill's `existing-document.md` says the same with more detail, when that skill is among yours.

## Read it back

The outline and the styles of the whole document:

```
call_recipe("google-workspace.getGoogleDocStyleSummary", {"documentId": "<id>"})
```

It gives the heading outline, the named-style counts, the fonts and the tables. Check that the headings are the source's, in order, with no skipped level; that there are Heading styles at all (a document of Normal text only has none); and that the tables are there. For the text, `call_recipe("google-workspace.readGoogleDoc", {"documentId": "<id>", "format": "markdown"})`: no `#`, `**` or `|` inside a paragraph, which would be symbols typed as text. After Route 3, `describeGoogleDocRange` on your text names each paragraph's `namedStyleType`: it must match its neighbours', and no word of theirs may be split.

A write that fails may have done nothing or part of the change: read the document back before saying anything about it.

## A file from a Google Doc

A PDF or a Word file of a Google Doc is the one Google exports:

```
call_recipe("google-workspace.downloadFile", {"fileId": "<id>", "localPath": "/home/agent/cerase/workspace/downloads/<name>.pdf", "exportMimeType": "application/pdf"})
```

With `"exportMimeType": "application/vnd.openxmlformats-officedocument.wordprocessingml.document"` and a `.docx` name it exports Word. Deliver the path the answer names with `[[attach: <path>]]`.

## The calls

These are the complete set: `uploadFile`, `createDocFromHTML`, `getDocumentInfo`, `readGoogleDoc`, `getGoogleDocStyleSummary`, `describeGoogleDocRange`, `copyFile`, `insertText`, `applyParagraphStyle`, `applyTextStyle`, `createParagraphBullets`, `findAndReplaceInDoc` and `downloadFile`, all of the `google-workspace` connector. Do not invent others.
