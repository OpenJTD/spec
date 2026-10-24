# OpenJTD spec

Shared research and specifications for Ichitaro JTD, JTT, and JTTC documents.
The RFCs describe source data and its interpretation. Subprojects choose their
internal models and presentation methods.

## Shared RFCs

| RFC | Subject |
| --- | --- |
| [0001](rfc/0001-container.md) | Container structure and stream inventory |
| [0002](rfc/0002-document-text.md) | Content extents and text interpretation |
| [0003](rfc/0003-compressed-container.md) | JTTC compressed container |
| [0004](rfc/0004-position-tables-and-bookmarks.md) | Position tables and bookmark names |
| [0005](rfc/0005-layout-marks-and-pages.md) | Layout marks and page instructions |
| [0006](rfc/0006-object-streams-and-anchors.md) | Objects, equations, and source associations |
| [0007](rfc/0007-records-and-ruled-flow.md) | Record framing and ruled-line flow |
| [0008](rfc/0008-document-sheets.md) | Document sheets |
| [0009](rfc/0009-embedded-payloads.md) | Embedded documents and graphic payloads |

Read each rule within its recorded profile and evidence scope. Explicit
hypotheses, alternative interpretations, and unknown fields remain unresolved
as stated; adopting a document does not turn those alternatives into facts.

[CONSOLIDATION.md](research/CONSOLIDATION.md) maps the source records, original
numbers, and later research to these RFCs. It identifies differences that have
not been joined. [IMPORTS.md](IMPORTS.md) preserves the initial import provenance
and links to the original texts in Git history.

## Research and validation

The [research policy](RESEARCH-POLICY.md), [evidence labels](EVIDENCE.md),
[RFC template](RFC-TEMPLATE.md), [observation format](OBSERVATIONS.md), and
[shared glossary](TERMINOLOGY.md) are maintained separately.
The labels, template, observation format, and glossary retain
their existing joint-review status; this RFC consolidation does not finalize
their formats.

The consolidation compares published records and code. It does not claim new
native authoring experiments or independent parser execution. Missing input
IDs, hashes, version coverage, and reproduction results remain explicit in the
RFCs for joint verification. Shared public fixtures are in
[OpenJTD/corpus](https://github.com/OpenJTD/corpus).

## Documentation language

Status: draft for joint review

Use English as the authoritative language for shared RFCs and specifications.
Japanese translations are optional.
Each translation must identify the English version as authoritative.
Preserve official Japanese UI terms where they matter for descriptions and reproduction.
Add English explanations where relevant.

Use the [shared glossary](TERMINOLOGY.md) for terms used across RFCs.
Keep terms specific to one RFC in that document.

Apache-2.0 covers our authored material. Referenced software, external inputs,
and quoted material retain their original rights and terms.
