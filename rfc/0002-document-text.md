# RFC 0002: DocumentText Content Extents and Text Interpretation

## Scope and evidence

This RFC records body extents, text encoding, inline content, and associated properties. Stored data and relationships belong to interpretation. HTML/SVG/PDF projection, substitute fonts, wrapping approximations, and table reconstruction belong to implementations.

The recorded profiles include early local documents, controlled Ichitaro 2026 cases, and later ruled-flow observations. Their authoring generations and evidence scopes differ.

Offsets below are bytes from the owning logical stream unless stated otherwise. [RFC 0007](0007-records-and-ruled-flow.md) describes record framing and trailing style events.

This RFC carries forward the cited research. The reported observations were not independently reproduced during this consolidation. The text, inline, and paragraph cases have source-record links below. Their input-level reproduction materials and authoring-version coverage are not fully linked here.

## Named content extent

| Offset | Observed form |
| --- | --- |
| 0–7 | ASCII `SsmgV.01` |
| 20–27 | ASCII `TextV.01` |
| 28–31 | BE-u32 content length N |
| 32 onward | N UTF-16 code units of body content |
| `32 + 2 * N` onward | Trailing style region in the observed profile |

The complete 32-bit value counts UTF-16 code units, not characters. Visible text can begin at byte 32 without `0x001f`; the absence of that marker does not make the prologue non-text.

Trailing styles lie outside the body extent. An extent beyond the available bytes indicates incomplete input. The named-stream layout does not identify the ownership or continuity of arbitrary physical-file fragments.

## Encoding and source ranges

Observed body and inline strings use UTF-16BE. A surrogate pair represents one supplementary character and two code units. Character counts, code-unit counts, and byte lengths are distinct.

Source ranges are relative to their owning streams. Surrogate pairs and inline regions are bounded by the declared body extent.

## Inline regions

Observed caches start with `0x001d` and end with `0x001e`. Their surrounding records and selectors determine the context.

```text
001c 0001 0007 0000 0000 0003 001d <base text>      001e
001c 0001 0007 0000 0001 0082 001d <reading>        001e
001c 0001 0007 0000 0000 0001 001d <display value>  001e
001c 0001 0007 0000 0001 0000 001d <instruction>    001e
```

The controlled grouped-ruby profile links selector 3 base text to `0x0082` annotations. Selectors alone do not define every inline role; surrounding records, lengths, terminators, and strings provide the context.

Base text, phonetic annotations, cached field values, and instructions have distinct roles, including when plain-text output omits them. Parser search limits are resource policy, not format maximum lengths.

## Controls in context

| Value | Observation and scope |
| --- | --- |
| `0x001f` | Text start/resumption and record footer; validate surrounding structure |
| `0x001c` | Record and inline opener contexts, not universally a cell boundary |
| `0x001d`, `0x001e` | Inline start/end in the observed forms |
| `0x000e` | One-word separator in ruled flow; counts alone do not determine logical rows or ownership |
| `0x000a` | Occurs inside cell/paragraph flow; alone does not define cell or paragraph boundaries |
| `0x000c` | Explicit-boundary handling exists in implementations; a standalone break remains unconfirmed in the inspected ruled contexts |

[RFC 0007](0007-records-and-ruled-flow.md) describes complete record framing. COM text export's box-drawing and page-break characters are comparison evidence, not sufficient proof of JTD control meanings.

## Prologue and header differences

Early parser descriptions use the first `0x001f` as a starting point. Later records include markerless content and mixed prologues; a marker-first parser does not cover all these forms.

A recorded form lacks `TextV.01` after `SsmgV.01` and has title text at word index 10 before the first structural marker. This differs from the named-stream byte-32 layout. Its authoritative content boundary remains unresolved.

| Topic | rjtd record/code | Tika record/code | Unresolved difference |
| --- | --- | --- | --- |
| Named length | Complete BE-u32 at byte 28 | General limit helper reads u32; some raw/prologue paths use the low u16 and a zero-high-word condition | Implementation restrictions are not format limits |
| Header word 9 | No universal selector inferred | Value 1 gates a raw path; source 0013 also reports 13→14 as a segment-count candidate | No common selector or count meaning established |
| Leading text | Read from the declared named extent | Separate raw, mixed P1, and unnamed-prologue paths | Preserve each form and condition |
| Supplementary characters | Decode pairs and retain source-unit ranges | Some scanners reject surrogate units as invalid scalars | Do not assume equal preservation coverage |

The table describes the cited code revisions. Independent results for long bodies and supplementary characters are not included.

## Different control interpretations

The inherited COM sections in rjtd's detailed source 0003 and Tika source 0003 name `0x001c` as a cell delimiter, `0x000e` as a row delimiter, and `0x000c` as a page break. Other sections in those records leave the meanings unresolved. These are inherited claims from the same research, not independent corroborations.

Later source 0013 distinguishes record classes and treats `0x000e` as flow-row advance. Its inspected `0x000c` values are explained as lengths or enumeration-table fields; a standalone page break remains unconfirmed there.

The lack of a confirmed standalone break in these ruled contexts does not establish the meaning of explicit-boundary handling elsewhere. Bare value scans, framed fields, and actual body controls provide different evidence. Synthetic boundary tests alone do not establish native page-break grammar.

Flow-row advance and a projected table row are different concepts. Multiple flow rows can be merged into one output row. The controlled case with a preceding paragraph in the first ruled interval limits separator-only table ownership.

## Character properties

These associations apply to the controlled combinations and text ranges, not every occurrence of the property ID.

| Property | Observed association |
| --- | --- |
| 2 | BE-u16 size in 0.01 mm; 370 ≈ 10.5 pt, 494 ≈ 14 pt; zero restores the observed default |
| 3 | `/Font` entry ID; `0xffff` restores the observed default; zero is valid |
| 15 | Text-range `0x00BBGGRR` color; `0xffffffff` is automatic/default |
| `20 = 0x84000000` | Bold selection |
| `20 = 0x90000000` | Italic selection |
| `20 = 0x80000010`, `13 = 1` | Single underline |
| `20 = 0x80000c00` / `0x80000400`, `4 = 5 = 50` | Upper/lower quarter menu profiles with half-size dimensions |

In the supported sequential `/DocumentViewStyles` `0x1006` profile, payload bytes 3–4 hold default size and 5–6 default font ID. These offsets are not established for other payload forms.

Property 15 also occurs on non-text table-state ranges. Unknown combinations, duplicate font IDs, and unsupported widths remain unresolved. Font selection does not establish glyph metrics or placement.

### Text properties versus wall styles

Controlled ruled-line junctions also have directional preset, transparency, and color properties. Property 2 denotes text size on the controlled text ranges but a downward-stroke preset at those junctions.

The Lite 2 trailing `FE 02 02` and `FE 03 02` families are associated with wall direction/weight. [RFC 0007](0007-records-and-ruled-flow.md) keeps the junction and Lite 2 profiles separate. Their grammar, cursor, and target correspondence remain untested; matching bytes do not establish matching semantics.

## Saved fields, notes, and TOC

### Fields

| Feature | Class-zero length/tag | Observed argument |
| --- | --- | --- |
| Printing date | 28 words / `0x0033` | `DATE` |
| Page number | 15 words / `0x0035` | `PAGENUMBER` |
| External link | 12 words / `0x0048` | HTTP(S) target |

These field observations depend on complete contiguous argument/value wrappers. Cached display text and expressions are distinct. General evaluation and renumbering remain outside the observed scope.

### Notes and ruby

The single-link profile associates `/Footnote`, `/FootnoteLink`, note text/markers, and a body marker. Property-1 references 1/2 and matching TextLayoutStyle slots corroborate marker typography for that case. General inheritance, note linking, and placement remain unresolved.

Grouped ruby links base text and annotations. Ruby units, exact placement, and output font sizes need separate evidence.

### Table of contents

Saved TOC regions have paired class-`0x0020` setting records. The 17-word title and 18-word leader contexts have different following empty-cache variants: 5 after a title and 3 after a leader. Raw leader values 1/100 correspond to solid/dotted selection; another variant lacks the leader record.

Saved titles/page labels do not define heading levels, navigation targets, generation, or regeneration.

## Direction, spacing, and paragraphs

The controlled sequential view `0x1002` direction profiles differ as follows:

- Horizontal: 32-byte payload, byte 10 = `0x40`.
- Vertical: 33-byte payload, bytes 10–11 = `0x50, 0x01`.

These byte values identify direction only within the matching complete profiles. Changing caret/edit state is not a direction discriminator.

The observed `0x1006` tail `0x0266` with its known `0x100b` form corresponds to the UI's 60% character spacing. A general conversion formula is unknown.

### Two-digit tatechuyoko (縦中横)

The controlled no-fit, two-digit case has a 12-word class-zero record with tag `0x0047`, followed contiguously by these complete inline caches:

| Cache | Observed content |
| --- | --- |
| Index 0, selector `0x0001` | Empty |
| Index 1, selector `0x0101` | Two digits |

This form includes the full context record, both wrappers, and their terminators. The ordinary `123` comparison retains vertical orientation; digit content alone does not identify this form. That comparison does not rule out other three-digit tatechuyoko forms.

Other lengths, nonnumeric caches, fit modes, ruby, and incomplete records are outside this observation. The saved-data candidate does not determine horizontal placement. Geometry, font choice, and spacing remain unresolved.

### Selected-page spacing

The controlled portrait/landscape/portrait case associates the middle page's
60% spacing selection with a 26-byte PageLayoutStyle `0x4006` subrecord and
the `0x400a` form `c300000d000050400100`. The `0x4006` record has prefix
`0000c10000`, the default font size at bytes 5–6, `8000003f000266` at bytes
7–13, 100/100 scales at bytes 14–17, and suffix `8000800002800000`.

This association requires the known style apply/reset, stock dimensions,
margins, and horizontal-writing profile. It does not define a general spacing
formula or style inheritance.

### Paragraph pitch, indentation, and spacing

One 16-word paragraph-header profile associates pitch 1000 with 10 mm. This is distinct from the repeated-pitch table/body forms.

Controlled first-line and continuing-line indent cases use a 17-word
class-`0x0010` record. It has subtype `0x0026` at w4, unit selector 1,
and separate continuing-line and first-line values. Value
1000 corresponds to the selected 10 mm indent in those cases. Right-side
fields are zero; other units and right-side values remain unresolved.

The before-0%/after-100% case uses a 25-word record with `0x0022`, `0x0023`,
and `0x0026` payload sections. The observed modes are zero and the
before/after values are 0/1000. These cases do not establish a general
percentage conversion or paragraph inheritance rule. The source records
the complete forms and their adjacent paragraph ranges.

Leading cell spaces and visible labels can have different property ranges. A label's font size therefore does not necessarily apply to those spaces.

## Auxiliary text and positions

`/LayoutBoxText` also contains bounded TextV.01 content. Printable values in control data do not establish visible text. Recovery alone does not determine object placement or ownership.

[RFC 0004](0004-position-tables-and-bookmarks.md) records MarkTag names and unresolved position tables.

## Source text and derived text

Some output contracts include numbering-prefix restoration, serializer whitespace, and table-text reordering. The recorded audits include cases with preserved character multisets but changed reading order.

Source strings, cached values, restored numbers, and output spaces have different origins. Character-multiset equality, order preservation, and complete source-range preservation establish different properties. Plain text or SAX output from one implementation does not by itself define the expected source interpretation.

## Open questions

Open areas include general paragraph/cell ownership, property inheritance, Japanese tracking and line distribution, numeric units, other tatechuyoko forms and fit modes, field evaluation, multi-note linking/placement, ruby units, TOC regeneration, bookmark positions, and older profiles.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd detailed research 0003 (L530)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0003-document-text.md#L530)
- [OpenJTD/rjtd detailed research 0003 (L635)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0003-document-text.md#L635)
- [OpenJTD/rjtd detailed research 0009 (L637)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0009-document-text-paragraph-record.md#L637)

- [OpenJTD/rjtd source RFC 0003](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0003-document-text.md)
- [KHiyowa/Tika-JTD source RFC 0003](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0003-document-text.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L53)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L53)
- [KHiyowa/Tika-JTD source RFC 0013 (L308)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L308)
- [KHiyowa/Tika-JTD DocumentTextParser.kt (L495)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/DocumentTextParser.kt#L495)
- [KHiyowa/Tika-JTD RuleFlow.kt (L205)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/RuleFlow.kt#L205)
- [OpenJTD/rjtd document_text.rs (L552)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/crates/rjtd-core/src/document_text.rs#L552)
