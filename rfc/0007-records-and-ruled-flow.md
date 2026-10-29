# RFC 0007: DocumentText Records and Ruled-Line Flow

## Scope

This RFC records body framing, span declarations, ruled-line (罫線, keisen) fields, and trailing styles.

Stored display instructions belong to interpretation. Row/column/cell reconstruction and HTML/Markdown contracts belong to implementations.

## Evidence and coverage

| Evidence group | Scope |
| --- | --- |
| Initial record inventory | 948 footers in two inputs and later records |
| Controlled Ichitaro 2026 cases | Paragraphs, short tables, empty spans, wrapping, surrounding text, and directional junction styles |
| Additional ruled-flow records | Span, direction, field-combination, and reconstruction experiments; input/version coverage is incomplete |
| Lite 2 P2/P3 | Wall direction, weight, side, and color contrasts |
| Sun-reader internals | Reference observations, separate from native data |

These groups have different input profiles, authoring generations, and representations. No common dialect is established across them.

This RFC carries forward the cited research. The reported observations were not independently reproduced during this consolidation. The sources identify the separate evidence groups. Input-level reproduction links and authoring-version coverage are incomplete.

## Non-inline record framing

Observed non-inline records use BE-u16 words:

```text
w0               0x001c
w1               class
w2               total word length L, including opener/footer
w3 .. w[L-5]     class-specific payload
w[L-4]           length echo L
w[L-3]           0x0000
w[L-2]           class echo
w[L-1]           0x001f
```

This framing requires consistent length, class, footer, and owning body extent. Not every `0x001c` is such a record: `001c 0001 0007 ... 001d` inline openers and sequential control-value runs are separate forms.

A bare payload `0x001d` can be a coordinate value; its presence alone does not identify inline content. Conversely, a parser's rejection of a `0x001d 0x001e` pair is not established as universal format grammar.

## Classes and subprofiles

| Class/field | Observation | Scope |
| --- | --- | --- |
| `0x0010`, `w4=0x008f` | Parent ruled declaration; initial records fit `L = w5 + 12` | That header family |
| `0x0030`, length 12 | Span declaration before text; `w4=b0`, `w5=b1` | Complete framing and parent association |
| `0x0020` | Used as flow termination in one parser; page-style/TOC settings also occur | Class alone does not terminate every table |
| `0x0000` | Field, object, and inline contexts | Check length, tag, and contiguous wrappers |

Initial records also include `w4=0x002e`, `0x0026`, `0x002a`, and `0xffff`. The `0x002a` family contains large-number pairs and inner-008f candidates; 0026 has style/indent candidates. Small values in one family and coordinate-like values in another do not define a global unit.

The controlled fixed-pitch case also has a composite `0x0010` record with this prefix at w3–w9:

```text
0000 0020 0004 0008 03e8 0008 03e8
```

The two pitch attributes agree; `0x03e8` (1000) corresponds to the selected 10 mm line pitch. The inner `0x008f` starts at w10, shifting the ordinary ruled fields and style-unit positions by six words. These offsets belong to the original composite frame. The direct `w4=0x008f` layout does not cover this form.

## Declared span counts and coordinates

The recorded `w4=0x008f` family has this reported relationship:

```text
w5 = 4 * (n - 1) + 3
w5 = 7 -> n = 2; w5 = 11 -> n = 3; w5 = 15 -> n = 4
```

n is the physical declared span count in that family, not necessarily the logical output-cell count. Compatibility with other parent/span records does not establish independent reproduction of the formula.

Initial records have four-word subentries. One reported case has wall tag 78, `b1=78`, and the next `b0=80`. A parent w6 equal to the following maximum b1 is another bounded observation, not a universal total-width rule.

### Advance axis and crossing rules

One interpretation places b0/b1 on the orientation-relative text advance axis. A vertical crossing-rule case has 36 parent headers, repeated `[0,40]` / `[42,160]` spans, and wall tag 40. A text-free case also emits repeated declarations.

These cases support text-independent declarations and relative axes, not direct paper x coordinates or fixed column numbers. Their relation to page-space units and transforms remains unresolved.

The early description associates 2/4-unit gaps with stroke footprint. Later crossing-rule observations associate varying gaps with text avoidance/partitioning and distinguish them from physical rule length. These interpretations do not establish a universal stroke-width or cell-padding unit.

## Separators and ownership

`0x000e` is a payload-free one-word separator in the inspected ruled contexts. Its Row Advance interpretation concerns source flow. Reconstructions can merge several flow rows into one output row, so separators and logical `<tr>` elements are not in one-to-one correspondence.

A controlled Ichitaro 2026 case places a preceding body paragraph in the same `0x000e` interval as the first ruled span. This does not disprove flow advance; it limits deriving table start and ownership from the separator alone.

`0x000a` occurs in cell/paragraph flow and is interpreted as WRAP or blank-span advance in one parser. The value alone does not equate hard breaks, displayed wraps, and output `<br>`.

Empty projected output does not establish the absence of source declarations.

### Controlled empty spans and partial boundaries

In the controlled two-column family, a truly empty span has consecutive `0x0030` declarations without a text event. A space-only span retains actual space characters and its own source range.

The vertical-merge comparison changes the left middle junction from `0x17`
to `0x13` and its following run from `0x14` to zero. The right boundary and
text order remain. An LF after the first left text and an LF-only boundary
span also remain. These observations do not establish logical rowspan or
editing ownership.

The explicit-break case adds a vertical-only physical band. A separate
row-growth case has 11 physical bands for four logical rows. Physical band
count therefore need not equal logical row count or follow an alternating
text/border pattern.

## Additional span-header fields

The additional flow records associate w6/w7 with continuation and closure:

| Field/condition | Reported use | Limit |
| --- | --- | --- |
| `w6=0x00ff` | Standard/continuation, excluded from closed-family test | Repeated boundaries alone do not establish merging |
| `w6=0x0001` / `0x0003` | Closed family; bit 0 and new text detect merge conflicts | Not every bit-0 value is decoded |
| `w7=0x0002` | Same-span downward-continuation anchor | Keep the specific contrast evidence |
| WRAP termination, identical span signature, each row's own parent | Tall-cell merge conditions | Separate raw combinations from output decisions |

These associations are not independently verified by the initial framing records. Whitespace, character-class, and width thresholds used for reconstruction are not shared storage grammar.

## Trailing text-style grammar

The named profile has byte-oriented events after `32 + 2 * content_unit_count`. Its observed source-unit cursor starts at 16.

```text
00 <u32 BE length>                         source-unit run
fe (<property ID> <value length> <bytes>)* ff 00
                                          one-unit property change
ff                                        terminal
```

Changes persist until replaced. The initial shared widths are:

| Property IDs | Value bytes |
| --- | ---: |
| 4–7, 9–12 | 1 |
| 1–3, 8, 13, 14, 18, 19 | 2 |
| 15–17, 20 | 4 |

[RFC 0002](0002-document-text.md) records text size/font/color associations. The same IDs occur over non-text table-state ranges, so those meanings are not universal.

## Separate wall-style observations

### Controlled Ichitaro 2026 junction profile

The black-base, transparent, thick, red, dashed, and one-transparent-edge comparisons retain identical content records, LineMark, and PageMark. Only trailing style events change. Junction source units have these directional associations:

| Direction | Preset property ID | Transparency property ID | BGR24 color property ID |
| --- | ---: | ---: | ---: |
| Up | 1 | 4 | 15 |
| Down | 2 | 5 | 16 |
| Right | 3 | 6 | 17 |

The numbers in this table are property IDs, not property values. Their
meanings depend on the junction context. Property 2 still denotes size on
the controlled text ranges.

The profile has at least three consecutive physical parent records with
`w4=0x008f` and length `w5 + 12`. A complete strip is
`[junction, 0, right-run-state, run-length]`. The final junction can instead
end as `[junction, 0]` before `ffff, 0`, or have an explicit blank padding run.
Junctions occupy two source grid units; their centers are one unit after
their strip starts. Interior widths and any final padding agree across
bands and exactly cover the parent extent. The explicit-tail form includes
one additional terminal unit in that extent.

Top/bottom bands use `0x16`/`0x15` before the last junction and `0x12`/`0x11`
at the last junction. Internal bands use `0x13` or `0x17` before the last
junction and `0x13` at the last. Masks `0x01`, `0x02`, and `0x04` select
up, down, and right strokes in this profile. A right stroke has run state
`0x14`; otherwise that state is zero.

Preset value 3 associates with thick solid lines and value 4 with dashed
lines. Transparency value 1 hides a stroke; value 0 leaves it visible.
Value `0x80` occurs at inactive horizontal ends; its meaning on an active
stroke remains open. Color uses the controlled BGR24 mapping.

Reset/unset paint and unsupported values have no established universal defaults. The source reports native print widths of 0.6/1.92 pt and 2.4 pt dashes, but the UI offers presets. These measurements do not decode a source length unit. Joins, dash phase, corner clipping, other presets, logical merge ownership, and general multipage paint remain unresolved.

Malformed strips, inconsistent widths or extents, source gaps, and incomplete style events are outside this profile. Its grammar and field correspondence with the Lite 2 profiles below remain unresolved.

### Early inline-style description

An early record associates source notation `payload[4]` values 0x14/0x08 with flow partition/inter-line rules. It reports BOX sequence `0x16→0x13→0x15` and crossing sequence `0x12→0x13→0x11`.

That payload notation differs from absolute parent `w4=0x008f`. Its coordinate origin is unresolved, so the offsets are not directly interchangeable.

The early tail mapping is: 0x22 thin black, 0x1c thick black, 0x06 cyan dashed, 0x4f thin vertical black, and 0x00 standalone rule. Later 2026/Lite 2 records report that vocabulary as unreproduced. Negative observations affected by the extractor defect remain unverified. No correspondence with the FE family below is established.

### Lite 2 P2/P3 trailing styles

Corrected §20.11 measurements report `[id] FE ...` entries:

| Pattern | Reported association |
| --- | --- |
| `FE 02 02` then `0x0003` | Bold vertical wall |
| `FE 03 02` then `0x0003` | Bold horizontal wall |
| `FE 02 02` then `0x0000` | Thin vertical wall in that family |

The all-thin baseline lacks these FE 02/03 entries. This does not establish that absence means thin walls in every profile.

§20.13 adds Lite 2 P3 contrasts with Ichitaro 2026 conversion PDFs:

| Item | Association and limit |
| --- | --- |
| New-group ID `0x09` | Leading/left edge, linked to the unique bold red edge |
| New-group ID `0x01` | Trailing/right edge, linked to the unique bold blue edge |
| Axis `0x02` / `0x03` | Vertical / horizontal |
| `(axis, 0x04, ...)` subentry | Color declaration; RGB24 windows observed, byte alignment unresolved |
| Multiple subentries under one marker | Compound declaration contrast |
| Individual top/bottom IDs | Unresolved |

The earlier single-edge ID set is `{0e,12,0c,96}`, while P3 reports `{09,01,03}`. Different authoring operations are a possible explanation, but this remains a hypothesis. The IDs are not established across all documents or versions.

PDF widths of 0.6 pt thin / 1.92 pt bold describe that conversion output. They do not establish JTD units or equivalence with Sun-reader quanta or renderer defaults.

### Unresolved relation to text properties

In the text profile, property 2 denotes size and 3 denotes font references. The controlled junction profile associates those IDs with down/right presets. The Lite 2 FE 02/03 profile associates them with wall direction/weight. Matching bytes do not establish the same field semantics.

Owning ranges, cursor origins, preceding IDs, and complete framing have not been reconciled across these profiles. Target-specific meanings in one grammar and different substructures remain alternatives.

The inspected FE scanner searches all bytes for candidates and filters registered marker IDs into WallRule events. This does not prove grammar boundaries. Its fallback classification of unregistered style values as thin is not a shared storage rule.

## Reader-internal observations and corrections

The Sun-reader observations concern loaders, RAM models, and property behavior. Per-edge codes/colors, chosen widths, and decoration names are reference observations, not direct native-field assignments.

Whether `0x5555` and `0x5001–0x500b` vocabulary is native or intermediate remains open. RAM sizes 0x584/0x51c are not file strides. This RFC does not transfer reconstructed implementation logic or field-reading procedures from the reader.

Early §§20.8–20.9 dumps, ASCII ledgers, and per-save content changes are affected by the extractor correction. Withdrawn offsets/lengths/ledgers are excluded as evidence. The observations above use explicitly remeasured §20.11+ results.

## Reconstruction remains implementation-specific

Span coalescing, column unions, colspan/rowspan, blank-strip removal, numeric-header adoption, and character-class joins are reconstruction rules. Their supporting cases do not make these algorithms required shared behavior.

The later output contract permits merge-boundary LineBreaks, revising an earlier no-in-cell-br rule. The recorded audit has 179 oracle versus 175 parser in-cell breaks and finds that the inspected native features do not distinguish the author's seam intent.

That is a limit of that input and feature census, not proof of permanent impossibility across the format. Handmade HTML, character-multiset preservation, and source-order preservation are different checks.

## Interpretation differences

| Topic | rjtd | Tika JTD+ | Shared treatment |
| --- | --- | --- | --- |
| Flow and tables | Preserve ranges/boundaries | Flow events and table state machine | Share source interpretation; separate output algorithms |
| Span count | Parent/span framing | Reported w5 arithmetic | Retain formula/profile without claiming independent reproduction |
| Rows/ownership | Surrounding-paragraph counterexample | Row Advance then logical merging | Distinguish source advance and output rows |
| Class 0020 | Style/TOC apply/reset | Termination event | No single meaning for the entire class |
| Wall styles | Text-property contrasts and controlled junction preset/transparency/color profile | Tail codes, Lite 2 FE, reader internals | Keep contextual meanings and source profiles separate |
| Page continuation | Multi-page source ranges | Per-page output-table proposal | Separate source relation and output splitting |

## Open questions

Unresolved areas are count-formula coverage and ownership; b0/b1 axes/units; w6/w7 continuation versus settings; tail/FE/text-property relationships; P3 top/bottom IDs, color alignment, and ID variation; page boundaries; and edit/save grammar.

## Sources

Source RFC numbers retain their original project numbering. The cited English source RFC 0013 has no section bodies for 8–12 or 18; no claims are reconstructed from those missing sections.

- [OpenJTD/rjtd detailed research 0009 (L467)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0009-document-text-paragraph-record.md#L467)
- [OpenJTD/rjtd detailed research 0009 (L536)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0009-document-text-paragraph-record.md#L536)
- [OpenJTD/rjtd detailed research 0009 (L602)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0009-document-text-paragraph-record.md#L602)

- [OpenJTD/rjtd source RFC 0009](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0009-document-text-paragraph-record.md)
- [OpenJTD/rjtd detailed research 0009](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0009-document-text-paragraph-record.md)
- [KHiyowa/Tika-JTD source RFC 0009](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0009-document-text-paragraph-record.md)
- [KHiyowa/Tika-JTD source RFC 0013](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L117)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L117)
- [KHiyowa/Tika-JTD source RFC 0013 (L633)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L633)
- [KHiyowa/Tika-JTD source RFC 0013 (L664)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L664)
- [KHiyowa/Tika-JTD RuleFlow.kt](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/RuleFlow.kt)
