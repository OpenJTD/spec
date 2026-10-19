# RFC 0002: DocumentText Content Extents and Text Interpretation

## Scope and evidence

This RFC records body extents, text encoding, inline content, and associated properties. Stored data and relationships belong to interpretation. HTML/SVG/PDF projection, substitute fonts, wrapping approximations, and table reconstruction belong to implementations.

Distinguish the initial shared observations, rjtd's controlled Ichitaro 2026 cases, and later Tika JTD+ source RFC 0013 observations. Older-version coverage requires a separate compatibility table. Attach input IDs, authoring steps, hashes, and source locations to observations.

Offsets below are bytes from the owning logical stream unless stated otherwise. [RFC 0007](0007-records-and-ruled-flow.md) describes record framing and trailing style events.

## Named content extent

| Offset | Observed form |
| --- | --- |
| 0–7 | ASCII `SsmgV.01` |
| 20–27 | ASCII `TextV.01` |
| 28–31 | BE-u32 content length N |
| 32 onward | N UTF-16 code units of body content |
| `32 + 2 * N` onward | Trailing style region in the observed profile |

Use the complete 32-bit count. This is a code-unit count, not a character count. Visible text can begin at byte 32 without `0x001f`. Do not discard a prologue solely because a text marker is absent.

Keep trailing styles outside the body. Record a declared extent beyond available bytes as incomplete input. Do not apply a named-stream layout to arbitrary physical-file fragments without establishing ownership and continuity.

## Encoding and source ranges

Observed body and inline strings use UTF-16BE. A surrogate pair represents one supplementary character and two code units. Distinguish character counts, code units, and bytes.

Record the owning stream with each range. Decoding a surrogate pair or inline region must not cross its declared body boundary.

## Inline regions

Observed caches start with `0x001d` and end with `0x001e`. Their surrounding records and selectors determine the context.

```text
001c 0001 0007 0000 0000 0003 001d <base text>      001e
001c 0001 0007 0000 0001 0082 001d <reading>        001e
001c 0001 0007 0000 0000 0001 001d <display value>  001e
001c 0001 0007 0000 0001 0000 001d <instruction>    001e
```

The controlled grouped-ruby profile links selector 3 base text to `0x0082` annotations. Selectors alone do not define every inline role. Check surrounding records, lengths, terminators, and strings.

Keep base text, phonetic annotations, cached field values, and instructions distinct. Preserve observations omitted from plain-text output. Parser search limits are resource policy, not format maximum lengths.

## Controls in context

| Value | Observation and scope |
| --- | --- |
| `0x001f` | Text start/resumption and record footer; validate surrounding structure |
| `0x001c` | Record and inline opener contexts, not universally a cell boundary |
| `0x001d`, `0x001e` | Inline start/end in the observed forms |
| `0x000e` | One-word separator in ruled flow; counts alone do not determine logical rows or ownership |
| `0x000a` | Occurs inside cell/paragraph flow; alone does not define cell or paragraph boundaries |
| `0x000c` | rjtd handles text after explicit page boundaries; Tika source 0013 reports no confirmed standalone break in its inspected ruled contexts |

Use [RFC 0007](0007-records-and-ruled-flow.md) to validate complete records. COM text export's box-drawing and page-break characters are comparison evidence, not sufficient proof of JTD control meanings.

## Prologue and header differences

Tika source 0003 describes an early marker-first parser that ignores bytes before the first `0x001f`. Later source 0013 §2.2 and current code preserve markerless content and mixed prologues. The old description is not a statement of current Tika capability.

Source 0013 §13.1 also records a form without `TextV.01` after `SsmgV.01`, with title text starting at word index 10 before the first structural marker. Keep that form separate from the named-stream byte-32 rule. Its authoritative content boundary still needs investigation.

| Topic | rjtd record/code | Tika record/code | Unresolved difference |
| --- | --- | --- | --- |
| Named length | Complete BE-u32 at byte 28 | General limit helper reads u32; some raw/prologue paths use the low u16 and a zero-high-word condition | Implementation restrictions are not format limits |
| Header word 9 | No universal selector inferred | Value 1 gates a raw path; source 0013 also reports 13→14 as a segment-count candidate | No common selector or count meaning established |
| Leading text | Read from the declared named extent | Separate raw, mixed P1, and unnamed-prologue paths | Preserve each form and condition |
| Supplementary characters | Decode pairs and retain source-unit ranges | Some scanners reject surrogate units as invalid scalars | Do not assume equal preservation coverage |

These are code comparisons, not newly executed cross-parser results. Reproduce long bodies and supplementary-character cases on identical inputs.

## Different control interpretations

Source 0003's COM section strongly names `0x001c` as a cell delimiter, `0x000e` as a row delimiter, and `0x000c` as a page break. Earlier sections in that same document leave meanings unresolved.

Later source 0013 distinguishes record classes and treats `0x000e` as flow-row advance. Its inspected `0x000c` values are explained as lengths or enumeration-table fields; a standalone page break remains unconfirmed there.

Keep this negative observation separate from rjtd's explicit-boundary handling. Compare bare scans, framed fields, and actual body controls on the same native input. Passing synthetic boundary tests alone does not establish native page-break grammar.

Flow-row advance and a projected table row are different concepts. Tika's later sections merge multiple flow rows into one output row. rjtd's preceding-paragraph counterexample limits using the separator alone to establish table ownership.

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

In the supported sequential `/DocumentViewStyles` `0x1006` profile, payload bytes 3–4 hold default size and 5–6 default font ID. Do not apply these positions to other payload forms.

Property 15 also occurs on non-text table-state ranges. Unknown combinations, duplicate font IDs, and unsupported widths remain unresolved. Font selection does not establish glyph metrics or placement.

### Text properties versus wall styles

Tika source 0013 §§20.11–20.13 associates trailing `FE 02 02` and `FE 03 02` families with wall direction/weight. Do not merge these with text size/font semantics because their byte shapes match. Whether they are target-specific uses of one grammar or different substructures remains untested. [RFC 0007](0007-records-and-ruled-flow.md) retains both records.

## Saved fields, notes, and TOC

### Fields

| Feature | Class-zero length/tag | Observed argument |
| --- | --- | --- |
| Printing date | 28 words / `0x0033` | `DATE` |
| Page number | 15 words / `0x0035` | `PAGENUMBER` |
| External link | 12 words / `0x0048` | HTTP(S) target |

Check complete contiguous argument/value wrappers. Cached display text and expressions are distinct. These observations do not define general evaluation or renumbering.

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

Use only with the matching complete profile. Changing caret/edit state is not a direction discriminator.

The observed `0x1006` tail `0x0266` with its known `0x100b` form corresponds to the UI's 60% character spacing. A general conversion formula is unknown.

One 16-word paragraph-header profile associates pitch 1000 with 10 mm. Keep it separate from repeated-pitch table/body forms. Leading cell spaces and visible labels can have different property ranges; do not assign label font size to all spaces.

## Auxiliary text and positions

`/LayoutBoxText` also contains bounded TextV.01 content. Printable values in control data do not establish visible text. Recovery alone does not determine object placement or ownership.

[RFC 0004](0004-position-tables-and-bookmarks.md) records MarkTag names and unresolved position tables.

## Source text and derived text

Tika source 0013 includes numbering-prefix restoration, serializer whitespace, and table-text reordering in output contracts. Later audits record preserved character multisets with changed reading order.

Distinguish source strings, cached values, restored numbers, and output spaces. Character-multiset equality, order preservation, and complete source-range preservation are different checks. Neither implementation's plain text or SAX string is the common oracle by definition.

## Validation and open questions

Use small controlled documents, changing one condition at a time: body text, supplementary characters, ruby, page breaks, size/font/color, and saved fields. Compare declared extents, prologues, style boundaries, source units, inline roles, property transitions, and unknown ranges. Other maintainers should verify source ranges and interpretations, without requiring the same output format or internal model.

Open areas include general paragraph/cell ownership, property inheritance, Japanese tracking and line distribution, numeric units, field evaluation, multi-note linking/placement, ruby units, TOC regeneration, bookmark positions, and older profiles.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd source RFC 0003](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0003-document-text.md)
- [KHiyowa/Tika-JTD source RFC 0003](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0003-document-text.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L53)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L53)
- [KHiyowa/Tika-JTD source RFC 0013 (L308)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L308)
- [KHiyowa/Tika-JTD DocumentTextParser.kt (L495)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/DocumentTextParser.kt#L495)
- [KHiyowa/Tika-JTD RuleFlow.kt (L205)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/RuleFlow.kt#L205)
- [OpenJTD/rjtd document_text.rs (L552)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/crates/rjtd-core/src/document_text.rs#L552)
