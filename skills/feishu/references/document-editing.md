# Document editing and known CLI pitfalls

Read this before modifying document content. These observations came from `lark-cli` 1.0.96–1.0.97. They are not guarantees about later versions; current command help and a read-back take precedence.

Read the installed official guides first:

```bash
lark-cli skills read lark-doc
lark-cli skills read lark-doc references/lark-doc-update.md
```

Read the XML or Markdown format reference selected by that guide before writing content in that format.

## Preserve content and confirm the result

Fetch the affected content with `--detail with-ids`, or `--detail full` when styles/resources matter. Keep the fetched content, comments, and complete `reference_map` in a permitted local task directory. Pass the map as documented when reusing references. Do not reconstruct an editable document from a filtered summary.

Prefer `str_replace`, a block operation, or a section-sized patch appropriate to the structure. After each write batch, check exit status, the top-level `ok`, the operation's own result, and `warnings`, then fetch the affected content again. IDs and ranges can change after a write; use the latest response and fetch rather than stale IDs.

Avoid whole-document `overwrite` unless the user authorized a complete rewrite and the consequences for comments and resources have been accounted for. A failed narrow edit is not permission to replace the whole document. Preserve the title with the documented title syntax when a full rewrite is appropriate.

## Observed pitfalls

| Behavior | Practical response |
| --- | --- |
| `result: success` can still include warnings or degraded content (1.0.96). | Inspect warnings and fetch the result. Envelope success alone is insufficient. |
| A newly created document may contain a default empty `<p></p>`; appending leaves a leading blank paragraph (1.0.97). | For a new otherwise-empty document, inspect its block IDs and replace that empty paragraph with `block_replace`. |
| `<callout emoji="⚠️">` may silently become the default light-bulb emoji, with or without the variation selector (1.0.97). | Fetch with `--detail full` and check the emoji. Light-bulb and siren emoji worked in that version; do not silently substitute if the exact symbol matters. |
| `str_replace` matching multiple locations rejects the operation with `matched multiple locations` (1.0.97). | Use a longer unique pattern or replace the intended blocks individually by ID. |
| Inline `<whiteboard type="svg">` can lose SVG elements and leak labels into document text. | For a new board, insert `<whiteboard type="blank"></whiteboard>`, take its token from `new_blocks`, and update it through the whiteboard command. For an existing board, reuse its token. Check current whiteboard help/reference before writing. |
| Some list-containing `block_replace` / `block_delete` ranges fail with `Invalid block range` (1.0.97). | Inspect the real list/container structure and current range restrictions. Prefer supported list-item/block operations; do not switch to `overwrite` merely to bypass this error. |

The board-update pattern observed in those versions was:

```bash
lark-cli whiteboard +update --as user --whiteboard-token <token> --input_format svg --source @./diagram.svg
```

Check the installed command's help before using it. These notes do not replace the current business-domain guides or grant authorization to modify any resource.
