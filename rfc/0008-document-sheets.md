# RFC 0008: Document Sheets and Subdocument Storages

## Scope and provenance

This RFC records sheet metadata and body-storage associations. Keep names, storage IDs, and original-path candidates distinct. Selection APIs, text concatenation, tabs, and SAX containers belong to implementations.

The direct source is KHiyowa's Rust fork feature/source RFC 0010, followed by its Kotlin port. The original addition is `c67c9493cf25be573edddbd868ff8068a886772f` (2026-09-20), reapplied on dev-hiyo as `40e288b1bfcd9d4f26a1fd1f3f9b68e2f3e2c06a`. The Tika port is `e78385bedb27179f9ffa6790a1447097b0fa9328`.

The inspected rjtd mainline has no dedicated multi-sheet record. Related body/container observations do not independently validate sheets. Original Rust tests include synthetic cases and an expected seven-sheet native case that returns when its local input is absent. Test existence is not evidence that it ran.

## Terms

- Root body: root `/DocumentText`.
- Subsheet: a sheet candidate associated with an individual storage below `/ObjectSheets/DocSheet/`.
- Storage ID: a value used to associate a storage, distinct from its display name/order.

Equivalence with all Ichitaro UI sheet kinds remains open.

## Candidate paths and roles

Inspected code and synthetic inputs use:

```text
/DocumentText
/ObjectSheets/SheetInfo
/ObjectSheets/DocSheet/DocItemInfo
/ObjectSheets/DocSheet/DocItemInfo2
/ObjectSheets/DocSheet/DOCS_0000/DocumentText
/ObjectSheets/DocSheet/DOCS_0001/DocumentText
```

| Item | Candidate role | Further check |
| --- | --- | --- |
| Root DocumentText | Root body | Default/first UI sheet relationship |
| SheetInfo | Root-name metadata | Exact fields and complete layout |
| DocItemInfo | Subsheet names/IDs | Boundaries, order, and count semantics |
| DocItemInfo2 | Original-path strings | Correspondence and missing items |
| DOCS_0000 family | Subsheet bodies | ID-to-name conversion |

Original source 0010 describes root `/DocItemInfo`; both original Rust and Kotlin use `/ObjectSheets/DocSheet/DocItemInfo`. These are competing document/code descriptions. Compare native inventories before treating either path as universal or dismissing one as a typo.

## DocItemInfo candidate records

Original code and synthetic inputs use:

| Sequence | Form | Candidate role |
| --- | --- | --- |
| Stream start | LE-u32 | Following item count |
| Item start | 4 bytes | Unknown |
| Next | LE-u32 | UTF-16-unit name length |
| Name | length * 2 UTF-16LE bytes | Sheet name |
| Next | LE-u32 | Storage ID |
| Tail | 26 bytes | Unknown |

Verify native field boundaries/repetition before generalizing. Do not assign GUID semantics to skipped fields. Tolerating a short last trailer is parser behavior, not an established save rule.

## Names, paths, and body scope

The implementations search SheetInfo for length/string candidates and DocItemInfo2 for path-like strings. Their length caps and character filters are not format limits. Confirm list/path correspondence and missing-path behavior. Distinguish stored names from the fallback `タイトル`.

Identify each body by its owning storage. Metadata UTF-16LE and body UTF-16BE must remain distinct. Check subsheet notes, positions, marks, and styles individually; root streams are not necessarily global.

## Record and implementation differences

| Topic | Difference | Treatment |
| --- | --- | --- |
| Feature origin | Added in KHiyowa's Rust fork, then ported | Not a deleted mainline feature or two independent discoveries |
| DocItemInfo path | RFC says root; both implementations use nested path | Preserve discrepancy pending native verification |
| Storage suffix | Kotlin renders decimal ID padded to at least four digits | XXXX is not proven fixed-width or hexadecimal |
| Names/order | Code creates defaults and puts root before subsheets | Separate source values/order from generated output |
| Representation | Tika 0013 §5.4 uses sheet div/headings/notes | Output containers do not define CFB grammar |
| JTTC | Tika sheet enumeration uses original input | Porting alone does not establish compressed-inner-sheet coverage |

Synthetic tests support record handling/output contracts, not independent verification of every native metadata field. Source 0012's sheet-local equation paths are related evidence, not a complete sheet grammar.

## Validation and open questions

Create small Ichitaro 2026 inputs and change sheet count, order, name, and text individually. Record authoring steps/version, hashes, inventories, metadata changes, and body associations. Other maintainers should verify paths, boundaries, names, IDs, and bodies, without requiring identical UI or text concatenation.

Open areas include UI/list/ID order, root association, complete SheetInfo/DocItemInfo2 structure, unknown DocItemInfo fields/tails, shared versus local auxiliary streams, missing/damaged metadata, old versions, and JTTC.

## Sources

Source RFC numbers retain their original project numbering.

- [KHiyowa/OpenJTD source commit c67c949](https://github.com/KHiyowa/OpenJTD/commit/c67c9493cf25be573edddbd868ff8068a886772f)
- [KHiyowa/OpenJTD source RFC 0010](https://github.com/KHiyowa/OpenJTD/blob/40e288b1bfcd9d4f26a1fd1f3f9b68e2f3e2c06a/openjtd-spec/rfc/0010-document-sheets.md)
- [KHiyowa/OpenJTD sheet.rs](https://github.com/KHiyowa/OpenJTD/blob/40e288b1bfcd9d4f26a1fd1f3f9b68e2f3e2c06a/rjtd/crates/rjtd-core/src/sheet.rs)
- [KHiyowa/OpenJTD multi_sheet.rs](https://github.com/KHiyowa/OpenJTD/blob/40e288b1bfcd9d4f26a1fd1f3f9b68e2f3e2c06a/rjtd/crates/rjtd-model/tests/multi_sheet.rs)
- [KHiyowa/Tika-JTD source RFC 0010](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0010-document-sheets.md)
- [KHiyowa/Tika-JTD ObjectSheetsReader.kt](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/ObjectSheetsReader.kt)
- [KHiyowa/Tika-JTD source RFC 0013 (L249)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L249)
- [KHiyowa/Tika-JTD source RFC 0012 (L19)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0012-jseq-equation-object.md#L19)
- [KHiyowa/Tika-JTD ObjectSheetsTest.kt](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/test/kotlin/com/hiyowa/tika/jtd/ObjectSheetsTest.kt)
