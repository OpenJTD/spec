# RFC 0007: DocumentText Records and Ruled-Line Flow

## Scope

This RFC records body framing, span declarations, ruled-line (罫線, keisen) fields, and trailing styles. It compares Tika source 0013 observations with rjtd source 0009.

Stored display instructions belong to interpretation. Row/column/cell reconstruction and HTML/Markdown contracts belong to implementations.

## Evidence and coverage

| Source | Scope |
| --- | --- |
| Shared initial 0009 | 948-footer two-input inventory and later records; imported material is one source |
| Later rjtd cases | Controlled Ichitaro 2026 paragraphs, short tables, empty spans, wrapping, and surrounding text |
| Tika 0013 §§2–4, 13–19 | Span, direction, field-combination, and reconstruction experiments; input/version links need completion |
| Tika 0013 §§20.11, 20.13 | Lite 2 P2/P3 wall direction, weight, side, and color contrasts |
| Tika 0013 §§20.1–20.7 | Sun-reader internal observations, separate from native data |

Do not join different inputs, authoring generations, and reader-internal representations into one dialect. This consolidation did not execute new native experiments.

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

Validate length, class, footer, and owning body extent together. Not every `0x001c` is such a record: `001c 0001 0007 ... 001d` inline openers and sequential control-value runs are separate forms.

Tika §14.1 records a bare payload `0x001d` that can be a coordinate value. Its presence alone must not reject a valid record as inline content. Conversely, the parser's rejection of a `0x001d 0x001e` pair is not established as universal format grammar.

## Classes and subprofiles

| Class/field | Observation | Scope |
| --- | --- | --- |
| `0x0010`, `w4=0x008f` | Parent ruled declaration; initial records fit `L = w5 + 12` | That header family |
| `0x0030`, length 12 | Span declaration before text; `w4=b0`, `w5=b1` | Complete framing and parent association |
| `0x0020` | Tika uses flow termination; rjtd also observes page-style/TOC settings | Class alone does not terminate every table |
| `0x0000` | Field, object, and inline contexts | Check length, tag, and contiguous wrappers |

Initial records also include `w4=0x002e`, `0x0026`, `0x002a`, and `0xffff`. Preserve the `0x002a` large-number pairs/inner-008f candidates and the 0026 style/indent candidates. Small values in one family and coordinate-like values in another do not define a global unit.

## Declared span counts and coordinates

Tika §3.1 reports this relationship in the `w4=0x008f` family:

```text
w5 = 4 * (n - 1) + 3
w5 = 7 -> n = 2; w5 = 11 -> n = 3; w5 = 15 -> n = 4
```

n is the physical declared span count in that family, not necessarily the logical output-cell count. The result is compatible with rjtd's parent/span records but was not independently reproduced during this consolidation.

Initial records have four-word subentries. Tika reports wall tag 78 with `b1=78` and next `b0=80`. A parent w6 equal to the following maximum b1 is another bounded observation, not a universal total-width rule.

### Advance axis and crossing rules

Tika §§2.3–2.4 interprets b0/b1 on the orientation-relative text advance axis. A vertical crossing-rule case has 36 parent headers, repeated `[0,40]` / `[42,160]` spans, and wall tag 40. A text-free case also emits repeated declarations.

Preserve this evidence for text-independent declarations and relative axes. Do not substitute paper x coordinates or fixed column numbers. Equivalence with rjtd's page-space units/transforms requires further comparison.

The early description associates 2/4-unit gaps with stroke footprint. Later crossing-rule observations associate varying gaps with text avoidance/partitioning and distinguish them from physical rule length. Do not merge these into a universal stroke-width or cell-padding unit.

## Separators and ownership

`0x000e` is a payload-free one-word separator in the inspected ruled contexts. Tika interprets it as Row Advance. Later sections merge several flow rows into one output row, so a separator is not in one-to-one correspondence with logical `<tr>`.

rjtd's controlled 2026 case places a preceding body paragraph in the same `0x000e` interval as the first ruled span. This does not disprove flow advance; it limits deriving table start and ownership from the separator alone.

Tika handles `0x000a` as WRAP or blank-span advance. rjtd preserves it in cell/paragraph flow. That value alone does not equate hard breaks, displayed wraps, and output `<br>`.

Compare single rows/columns, trailing empty rows, erased boundaries, wrapping, and ordinary text between ruled regions. Empty projected output is not a reason to discard source declarations.

## Additional span-header fields

Tika §§13–19 reports w6/w7 continuation and closure associations:

| Field/condition | Reported use | Limit |
| --- | --- | --- |
| `w6=0x00ff` | Standard/continuation, excluded from closed-family test | Repeated boundaries alone do not establish merging |
| `w6=0x0001` / `0x0003` | Closed family; bit 0 and new text detect merge conflicts | Not every bit-0 value is decoded |
| `w7=0x0002` | Same-span downward-continuation anchor | Keep the specific contrast evidence |
| WRAP termination, identical span signature, each row's own parent | Tall-cell merge conditions | Separate raw combinations from output decisions |

rjtd framing records do not independently verify all these conditions. Retain field observations without adopting whitespace, character-class, and width thresholds as shared storage grammar.

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

### Early inline-style description

Tika §4 associates source notation `payload[4]` values 0x14/0x08 with flow partition/inter-line rules. It reports BOX sequence `0x16→0x13→0x15` and crossing sequence `0x12→0x13→0x11`.

That payload notation differs from absolute parent `w4=0x008f`. Confirm its origin before normalizing offsets.

Retain §4.2's initial tail mapping separately: 0x22 thin black, 0x1c thick black, 0x06 cyan dashed, 0x4f thin vertical black, and 0x00 standalone rule. Later 2026/Lite 2 records report that vocabulary as unreproduced. Negative observations affected by the extractor correction also require remeasurement. Do not join this mapping with the FE family below.

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

Preserve the earlier single-edge ID set `{0e,12,0c,96}` versus P3 `{09,01,03}`. Authoring-operation differences are a hypothesis. Do not generalize IDs to every document/version.

PDF widths 0.6 pt thin / 1.92 pt bold describe that conversion output. Do not combine them with Sun-reader quanta or rjtd defaults to define JTD units.

### Unresolved relation to text properties

rjtd's text ranges associate property 2 with size and 3 with font references. Tika associates FE 02/03 with wall direction/weight. Matching bytes do not establish the same field semantics.

Compare owning ranges, cursor, preceding ID, and complete framing on identical inputs. Target-specific meanings in one grammar versus different substructures remain alternatives.

Current Tika code scans all bytes for FE candidates and filters registered marker IDs into WallRule events. That search does not prove boundaries between these grammars. Its fallback classification of unregistered style values as thin is not a shared storage rule.

## Reader-internal observations and corrections

Tika §§20.1–20.7 concerns Sun-reader loaders, RAM models, and property behavior. Per-edge codes/colors, chosen widths, and decoration names are reference observations, not direct native-field assignments.

Whether `0x5555` and `0x5001–0x500b` vocabulary is native or intermediate remains open. RAM sizes 0x584/0x51c are not file strides. This RFC does not transfer reconstructed implementation logic or field-reading procedures from the reader.

Early §§20.8–20.9 dumps, ASCII ledgers, and per-save content changes are affected by the extractor correction. Withdrawn offsets/lengths/ledgers are excluded as evidence. The observations above use explicitly remeasured §20.11+ results.

## Reconstruction remains implementation-specific

Span coalescing, column unions, colspan/rowspan, blank-strip removal, numeric-header adoption, and character-class joins are reconstruction rules. Preserve supporting cases without making these algorithms required shared behavior.

Later Tika sections revise the early no-in-cell-br contract to permit merge-boundary LineBreaks. §§21–22 retain 179 oracle versus 175 parser in-cell breaks and conclude that the inspected native features do not distinguish the author's seam intent.

That is a limit of that input and feature census, not proof of permanent impossibility across the format. Handmade HTML, character-multiset preservation, and source-order preservation are different checks.

## Interpretation differences

| Topic | rjtd | Tika JTD+ | Shared treatment |
| --- | --- | --- | --- |
| Flow and tables | Preserve ranges/boundaries | Flow events and table state machine | Share source interpretation; separate output algorithms |
| Span count | Parent/span framing | Reported w5 arithmetic | Retain formula/profile without claiming independent reproduction |
| Rows/ownership | Surrounding-paragraph counterexample | Row Advance then logical merging | Distinguish source advance and output rows |
| Class 0020 | Style/TOC apply/reset | Termination event | No single meaning for the entire class |
| Wall styles | Modern text-property/range contrasts | Tail codes, Lite 2 FE, reader internals | Keep lineages and text-property relation separate |
| Page continuation | Multi-page source ranges | Per-page output-table proposal | Separate source relation and output splitting |

## Validation and open questions

Other maintainers should check logical streams, declared extents, and framed fields on permitted identical inputs. Change span counts, single rows/columns, wrapping, edge/weight/color, and direction independently.

Unresolved areas are count-formula coverage and ownership; b0/b1 axes/units; w6/w7 continuation versus settings; tail/FE/text-property relationships; P3 top/bottom IDs, color alignment, and ID variation; page boundaries; and edit/save grammar.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd source RFC 0009](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0009-document-text-paragraph-record.md)
- [OpenJTD/rjtd source RFC 0009](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0009-document-text-paragraph-record.md)
- [KHiyowa/Tika-JTD source RFC 0009](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0009-document-text-paragraph-record.md)
- [KHiyowa/Tika-JTD source RFC 0013](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L117)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L117)
- [KHiyowa/Tika-JTD source RFC 0013 (L633)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L633)
- [KHiyowa/Tika-JTD source RFC 0013 (L664)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L664)
- [KHiyowa/Tika-JTD RuleFlow.kt](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/RuleFlow.kt)
