# Feishu Output Guidance

Use this file when the user asks to create or update a Feishu document.

## Writing Rules

- Prefer creating a new document for a new evaluation report.
- When updating an existing report, use section-level insert/replace operations rather than full overwrite, so comments are preserved.
- Do not mention internal source document names unless the user asks. Apply the methodology, not its bibliography, in the main body.
- Use quote blocks at the start of major sections to orient non-eval readers.
- Use tables for metrics, samples, steps, and optimization items.
- If a table is meant for later human review, put prompts in headers and leave cells blank.

## Feishu Editing Pattern

1. Fetch the current doc before editing.
2. Save a local copy.
3. Identify headings and image/table tokens.
4. Prepare small Markdown fragments.
5. Apply local section edits.
6. Fetch again and inspect headings, duplicated sections, and key new terms.

Avoid whole-document replacement unless the document has no important comments.

## Visuals

Use visuals only when they explain structure:

- architecture overview
- evaluation flow
- dataset preparation and split
- judge selection
- failure feedback loop

For generated diagrams with Chinese labels, prefer deterministic text overlay or a native whiteboard when available. Verify final placement after upload.
