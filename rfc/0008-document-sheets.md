# RFC 0008: Document Sheets and Subdocument Storages

## Scope

This RFC records sheet metadata and body-storage associations. Names, storage IDs, and original-path candidates have distinct roles. Selection APIs, text concatenation, tabs, and SAX containers belong to implementations.

## Evidence and coverage

The candidate structures below come from code and synthetic tests. A native test expects seven sheets, but returns when its local input is absent. Its presence does not establish that the test ran or independently verified the native metadata fields.

This RFC carries forward the cited research. The reported observations were not independently reproduced during this consolidation. The sources link the code and tests, including the optional seven-sheet test. That test's native input is not attached to this RFC.

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

| Item | Candidate role | Unresolved detail |
| --- | --- | --- |
| Root DocumentText | Root body | Default/first UI sheet relationship |
| SheetInfo | Root-name metadata | Exact fields and complete layout |
| DocItemInfo | Subsheet names/IDs | Boundaries, order, and count semantics |
| DocItemInfo2 | Original-path strings | Correspondence and missing items |
| DOCS_0000 family | Subsheet bodies | ID-to-name conversion |

The source record describes root `/DocItemInfo`, while both inspected implementations use `/ObjectSheets/DocSheet/DocItemInfo`. The cited material does not resolve this discrepancy through native storage inventories or establish a universal path.

## DocItemInfo candidate records

The inspected code and synthetic inputs use:

| Sequence | Form | Candidate role |
| --- | --- | --- |
| Stream start | LE-u32 | Following item count |
| Item start | 4 bytes | Unknown |
| Next | LE-u32 | UTF-16-unit name length |
| Name | length * 2 UTF-16LE bytes | Sheet name |
| Next | LE-u32 | Storage ID |
| Tail | 26 bytes | Unknown |

The inspected code and synthetic tests do not independently verify native field boundaries or repetition. The skipped fields have no established GUID meaning. Tolerance of a short last trailer is parser behavior, not an established save rule.

## Names, paths, and body scope

The inspected implementations search SheetInfo for length/string candidates and DocItemInfo2 for path-like strings. Their length caps and character filters are not format limits. List/path correspondence and missing-path behavior remain unresolved. The fallback `タイトル` is generated output, not a confirmed stored name.

The inspected code associates each body with its storage and reads metadata as UTF-16LE and body text as UTF-16BE. The scope of subsheet notes, positions, marks, and styles remains unresolved; root auxiliary streams are not established as global.

## Record and implementation differences

| Topic | Difference | Treatment |
| --- | --- | --- |
| Feature origin | Added in KHiyowa's Rust fork, then ported | Not a deleted mainline feature or two independent discoveries |
| DocItemInfo path | RFC says root; both implementations use nested path | Preserve discrepancy pending native verification |
| Storage suffix | Kotlin renders decimal ID padded to at least four digits | XXXX is not proven fixed-width or hexadecimal |
| Names/order | Code creates defaults and puts root before subsheets | Separate source values/order from generated output |
| Representation | Tika 0013 §5.4 uses sheet div/headings/notes | Output containers do not define CFB grammar |
| JTTC | Tika sheet enumeration uses original input | Porting alone does not establish compressed-inner-sheet coverage |

The feature was added to KHiyowa's Rust fork on 2026-09-20 and then ported to Kotlin. The inspected rjtd mainline has no dedicated multi-sheet record. These implementations share a source; related body/container records do not independently verify sheet metadata. Sheet-local equation paths provide related evidence, but do not define a complete sheet grammar. Source commits are linked below.

## Open questions

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
- [KHiyowa/OpenJTD fork reapplication 40e288b](https://github.com/KHiyowa/OpenJTD/commit/40e288b1bfcd9d4f26a1fd1f3f9b68e2f3e2c06a)
- [KHiyowa/Tika-JTD Kotlin sheet port e78385b](https://github.com/KHiyowa/Tika-JTD/commit/e78385bedb27179f9ffa6790a1447097b0fa9328)
