# RFC 0001: JTD Container Structure and Stream Inventory

## Scope

This RFC records CFB storages and streams observed in JTD/JTT documents. An inventory does not establish payload semantics, mandatory streams, or a common tree for every version.

## Evidence and coverage

The initial five-document inventory dates from 2026-06-18. Later observations extend that inventory. Record input IDs, SHA-256 values, authoring versions, and reproduction results before extending a profile to other inputs.

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

Tika JTD+ sheet, embedding, and equation research includes `/ObjectSheets/DocSheet/DOCS_XXXX/` and nested `EmbedItems/Embedding N/`. These paths extend the observed inventory; they are not mandatory for every document.

Body, header, box text, and object data can occupy separate streams. Identify a stream by its owning container and sheet, as well as its name.

## Differences between source records

| Topic | rjtd record | Tika JTD+ record | Interpretation |
| --- | --- | --- | --- |
| Initial CFB inventory | Five-input inventory | Imported initial inventory | One research source, not independent reproductions |
| Container reading | Standard reading and bounded recovery history | Imported RFC retains Rust descriptions; current reader uses POIFS | Library and recovery choices are implementation policy |
| Later storages | Object paths and source-range research | Source RFCs 0010–0012 add sheet and embedding records | Keep profile and owning paths explicit |
| Save comparison | Physical bytes and logical streams are distinct | Source RFC 0013 §§20.10–20.12 correct an extractor defect | Use corrected logical-stream measurements |

Tika JTD+ reports that the P2 input and its unedited resave have byte-identical content streams, including `DocumentText`, when read again through POIFS. The earlier ASCII ledger and content-fingerprint changes were withdrawn after a FAT-chain ordering defect was found. This applies to that save pair, not every save operation.

## Reading failures and recovery

FAT and sector-chain inconsistencies were reported in some inputs. Successful recovery does not make a malformed chain a normative JTD rule. Distinguish missing streams, unreadable streams, and streams obtained through recovery.

Libraries, recovery algorithms, and resource budgets belong to implementations.

## Reproduction and validation

For each input, record:

- Input ID, SHA-256, and authoring version.
- Basis for analysis and sharing.
- Complete storage/stream paths, entry kinds, and byte lengths.
- Content hashes for readable logical streams.
- Reading failures and recovery use.
- Tools and implementation versions.

Other maintainers can compare paths, kinds, lengths, and logical stream contents on the same input. Keep physical sector placement separate from stream contents. Maintain version coverage in a separate compatibility table.

## Open questions

- Stream inventories across versions and features.
- Conditions that save or omit layout marks.
- Ownership of fragments inaccessible through named streams.
- Roles of unresolved metadata and auxiliary streams.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd source RFC 0001](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0001-container.md)
- [KHiyowa/Tika-JTD source RFC 0001](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0001-container.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L622)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L622)
- [KHiyowa/Tika-JTD source RFC 0010](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0010-document-sheets.md)
- [KHiyowa/Tika-JTD source RFC 0012 (L19)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0012-jseq-equation-object.md#L19)
