# RFC Consolidation and Source Comparison

This ledger maps source-project numbering to shared RFCs 0001–0009. It preserves different interpretations and their limits; inclusion does not make an unresolved hypothesis a confirmed rule.

## Source revisions

- rjtd: `3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1`.
- Tika JTD+: `045ef005f7e45202650d5601b46ef6f4915d8190`.
- KHiyowa's original Rust sheet addition: `c67c9493cf25be573edddbd868ff8068a886772f`.
- Fork reapplication: `40e288b1bfcd9d4f26a1fd1f3f9b68e2f3e2c06a`.
- Kotlin sheet port: `e78385bedb27179f9ffa6790a1447097b0fa9328`.

rjtd's compact `openjtd-spec/rfc/` and detailed `rjtd/docs/research/` records were both compared. Absence from the compact record was not treated as proof of a Tika-only discovery. Seven detailed rjtd files matched the cited revision by Git blob hash. The four Tika source RFCs 0010–0013 also matched the cited revision.

The principal later sources are [Tika 0012](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0012-jseq-equation-object.md) and [Tika 0013](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md).

## rjtd mapping

Each original number below refers to both its compact RFC and its detailed same-name research record.

| Shared RFC | rjtd source | Consolidated content |
| --- | --- | --- |
| 0001 | Original 0001 | Streams, macro tree, reading exceptions |
| 0002 | Original 0003 and 0009 styles | u32 extent, UTF-16, ruby, properties, fields, notes, TOC |
| 0003 | Original 0005 | Wrapper, LHA member, inner CFB, position spaces |
| 0004 | Original 0006 and 0003 bookmarks | Position tables, first-item alternative, MarkTag, bias counterexample |
| 0005 | Original 0007; detailed 0008 direction/margins | Marks, pages, Header, view and explicit margins |
| 0006 | Original 0008 | PNG Frame, figures, fill, GCI equation contrast |
| 0007 | Original 0009 and related 0003/0007 | Framing, ownership counterexamples, styles |
| 0008 | No dedicated mainline source | KHiyowa's Rust-fork 0010; not independent mainline evidence |
| 0009 | Detailed original 0008 | Image extents, envelopes, source-path candidates versus ownership |

## Tika source mapping

| Source RFC | Shared RFC | Treatment |
| --- | --- | --- |
| 0001 | 0001 | Initial common inventory; Rust/rhwp/recovery policy separated |
| 0003 | 0002 | Inline observations; COM assertions versus later parser |
| 0005 | 0003 | Common JTTC observations; historical dependency rules excluded |
| 0006 | 0004 | Competing coordinates/unknown fields; imported diagnostics not counted twice |
| 0007 | 0005 | Initial marks, supplemented by later 0013 direction contrasts |
| 0008 | 0006/0009 | Object/payload candidates; renderer gates not storage rules |
| 0009 | 0002/0007 | Styles, framing, ruled flow; early assertions versus revisions |
| 0010 | 0001/0008 | Sheets, Rust→Kotlin lineage, document/code path discrepancy |
| 0011 | 0009 | Workbook/METAFILE; source versus derived CFB; current image/EMF code |
| 0012 | Mainly 0006; related 0008/0009 | MATH.VAF, length variants, fonts/tree candidates, snapshots |
| 0013 | Section mapping below | Native observations, reconstruction, reader reference, and retractions separated |

The original filter-research 0002 and rjtd model/export 0004 are excluded from the shared RFC sequence. The existence and limits of 0013 reader research are recorded without transferring implementation procedures.

## Source 0012 sections

| Source section | Shared location and treatment |
| --- | --- |
| §§1–2 storage/identity/streams | 0006: root/subsheet paths and bounded sizes; ProgID/string/CLSID distinction |
| §3 header/length/fonts | 0006: 32-byte header, 27/29 difference of 1,162, exceptions 1,334/990 |
| §4.1 AST candidate | 0006: child pattern retained; operators/order unresolved |
| §4.2 mapping experiment | 0006: proposed Word/RTF→WMF experiment; code-page/symbol conversion checks |
| §4.3 cache-free variants | 0006: no universal cache-presence assumption |
| §5 pipeline | 0006: implementation proposal, not completed AST/cmap evidence |

## Source 0013 sections

| Source section | Shared location and treatment |
| --- | --- |
| §1 flow model | 0007: source flow/ranges versus table projection |
| §2.1 controls | 0002/0007: Row Advance, WRAP, class 0020, 000c differences |
| §2.2 text invariants | 0002: prologues, source order, restored numbers |
| §§2.3–2.4 direction/axis/crossing | 0005/0007: 36-header contrast; axes/gaps not universal units |
| §2.5 PaperMark | 0005: six-input index/orient result versus bit-0/view evidence |
| §3.1 span count | 0007: w5 arithmetic and physical declarations |
| §§3.2–3.7 reconstruction | 0007: repeated boundaries retained; merging/padding/span/state machine implementation-specific |
| §3.8 pages | 0005/0007: strokes, unresolved trigger, output splitting separated |
| §4 styles | 0007: BOX/crossing codes, initial tail mappings and later non-reproduction |
| §5 SAX/Markdown/sheets | 0002/0007/0008: source scope versus representation contracts |
| §6 LayoutBox | 0002/0006: separate text/object scope; recursive tables proposed |
| §7 implementation status | 0007/0008: source-project completion is not shared format acceptance |
| §13.1 unnamed prologue | 0002: word-index-10 form separate from byte-32 named form |
| §§13.2–17, 19 continuation/bands | 0007: w6/w7/WRAP/parent observations; thresholds and joins remain projection rules |
| §§20.1–20.7 reader internals | 0007: distinct evidence and unresolved native binding; RAM sizes not file strides |
| §§20.8–20.9 initial dumps | 0001/0007: correction applies; withdrawn offsets/ledgers/churn excluded |
| §§20.10–20.12 remeasurement/P2 | 0001/0007: bounded stable streams and FE direction/weight observations |
| §20.13 P3 sides/colors | 0007: left/right IDs, axis/color subentries, different groups, unresolved alignment |
| §§21–22 seams | 0002/0007: 179/175 and inspected-feature limits; handmade HTML not storage grammar |

The English source lacks section bodies 8–12 and 18 and retains some old references. Missing sections were not reconstructed by guesswork; surviving sections and code provide the cited evidence.

## Alternatives kept separate

| Topic | Differences | Limit |
| --- | --- | --- |
| `0x000c` | Early COM naming, later Tika unconfirmed break, rjtd explicit-boundary handling | Compare native body controls and framed fields on identical inputs |
| Header word 9 | Raw gate, count candidate, general named extent | No universal selector/count |
| Direction | bit-0/view versus bit-4/index predicates | No invented priority/OR test |
| Rows/ownership | Flow advance, logical merging, preceding paragraph | Separate source advance and projected ownership |
| Class 0020 | Termination versus page/TOC settings | No one meaning for all subprofiles |
| FE styles | Text size/font versus wall direction/weight | Grammar, target, and cursor correspondence unresolved |
| Rule appearance | Tail codes, Lite 2 P2/P3, Sun internals | Separate lineages; top/bottom/alignment unresolved |
| Equation 0x94 | GCI font packet versus JSEQ3 child candidate | Different streams/framing |
| MATH.VAF size | Common relationship plus two exceptions | 1,162 is not universal |
| Sheet paths | Root RFC versus nested Rust/Kotlin paths | Native inventory needed |
| Extraction/placement | Signature-to-end slices versus complete extents/Frame links | Validity, end, ownership, and placement checked separately |

## Verification boundary

The consolidation compared documents, relevant code, and test conditions. It did not author new native inputs, analyze DLLs, execute parsers, or perform independent cross-parser validation.

Reported experiments retain attribution and scope. Imported records, synthetic tests, character-multiset equality, and candidate searches are not independent format validation. Each RFC retains pinned source links and open questions.
