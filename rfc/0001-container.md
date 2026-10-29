# RFC 0001: JTD Container Structure and Stream Inventory

## Scope

This RFC records CFB storages and streams observed in JTD/JTT documents. An inventory does not establish payload semantics, mandatory streams, or a common tree for every version.

## Evidence and coverage

The initial inventory comprises five documents examined on 2026-06-18. Later records add sheet and embedded-object storages.

This RFC carries forward the cited research. The reported observations were not independently reproduced during this consolidation. A complete redistributable reproduction set for the initial 2026-06-18 inventory is not attached to this RFC. This describes the material provided here, not the existence of the underlying research material.

## Observed top-level streams

The initial five inputs share these streams:

```text
/\x04JSRV_SegmentInformation
/\x04JSRV_SummaryInformation
/\x05SummaryInformation
/AutoTextInfo
/DocumentEditStyles
/DocumentPeripheralThree
/DocumentPeripheralTwo
/DocumentText
/DocumentTextPositionTables
/DocumentViewStyles
/Font
/Footnote
/Header
/MarkTag
/PageLayoutStyle
/ReferenceInfo
/RelatedDocuments
/TextLayoutStyle
/ThinkingTemplate
```

`\x04` and `\x05` denote control characters in names, not literal backslash sequences.

Some inputs also contain `/LineMark`, `/PageMark`, `/PaperMark`, and `/PageLayoutStyleHeader`. Other inputs lack those streams. Presence alone does not determine layout capability or page structure.

## Observed macro storage

The initial inventory includes:

```text
/DocumentMacro
/DocumentMacro/\x04JSRV_SegmentInformation
/DocumentMacro/Macros
/DocumentMacro/Macros/\x04JSRV_SegmentInformation
/DocumentMacro/Macros/BaseStorage0
/DocumentMacro/Macros/BaseStorage0/\x04JSRV_SegmentInformation
/DocumentMacro/Macros/BaseStorage0/InfoStream
/DocumentMacro/Macros/BaseStorage0/MacrosStream
/DocumentMacro/Macros/BaseStorage0/MacrosStreamStyle3
```

This records storage presence. Macro contents and execution semantics are outside this RFC.

## Roles and exceptions

`/DocumentText` is associated with body data in the inspected profiles. [RFC 0002](0002-document-text.md) describes its bounded content. Style, font, position, and layout streams require separate interpretation. Names and sizes alone do not establish their roles.

Some inputs lack a directly readable named `/DocumentText` but contain physical-file `SsmgV.01` or `TextV.01` fragments. A marker does not establish stream ownership or logical continuity.

JTTC has an outer and a decompressed inner container. Absence from the outer directory does not establish absence from the inner document. [RFC 0003](0003-compressed-container.md) records that profile.

## Later sheet and object observations

Observed storage configurations include sheet-local `/ObjectSheets/DocSheet/DOCS_XXXX/` storages and nested `EmbedItems/Embedding N/` object storages. These are examples of observed paths, not mandatory entries in every document.

Body, header, box text, and object data can occupy separate streams. A stream's identity includes its owning container and sheet as well as its name.

## Differences between source records

| Topic | rjtd record | Tika JTD+ record | Interpretation |
| --- | --- | --- | --- |
| Initial CFB inventory | Five-input inventory | Imported initial inventory | One research source, not independent reproductions |
| Container reading | Standard reading and bounded recovery history | Imported RFC retains Rust descriptions; current reader uses POIFS | Library and recovery choices are implementation policy |
| Later storages | Object paths and source-range research | Source RFCs 0010–0012 add sheet and embedding records | Keep profile and owning paths explicit |
| Save comparison | Physical bytes and logical streams are distinct | Source RFC 0013 §§20.10–20.12 correct an extractor defect | Use corrected logical-stream measurements |

In the recorded P2 input and its unedited resave, logical content streams including `DocumentText` are byte-identical when read through POIFS. Earlier ASCII-ledger and content-fingerprint changes were withdrawn after a FAT-chain ordering defect was found in the extractor. This result applies to that save pair.

## Reading failures and recovery

FAT and sector-chain inconsistencies were reported in some inputs. Successful recovery does not make a malformed chain a normative JTD rule. A missing stream, an unreadable stream, and a recovered stream are different outcomes.

Libraries, recovery algorithms, and resource budgets belong to implementations.

## Open questions

- Stream inventories across versions and features.
- Conditions that save or omit layout marks.
- Ownership of fragments inaccessible through named streams.
- Roles of unresolved metadata and auxiliary streams.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd detailed research 0001](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0001-container.md)

- [OpenJTD/rjtd source RFC 0001](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0001-container.md)
- [KHiyowa/Tika-JTD source RFC 0001](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0001-container.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L622)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L622)
- [KHiyowa/Tika-JTD source RFC 0010](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0010-document-sheets.md)
- [KHiyowa/Tika-JTD source RFC 0012 (L19)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0012-jseq-equation-object.md#L19)
