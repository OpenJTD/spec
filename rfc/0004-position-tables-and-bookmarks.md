# RFC 0004: Text Position Tables and Bookmark Names

## Scope and evidence

This RFC records `/DocumentTextPositionTables`, position candidates, and the separate `/MarkTag` name directory. Recovering a bookmark name does not establish its navigation or editing coordinates.

Compare initial source RFC 0006 observations with rjtd's controlled Ichitaro 2026 bookmark cases. Tika's imported initial record is not an independent reproduction. Attach input IDs, hashes, authoring versions, and source extents. Positions below are relative to the owning logical stream.

## Position-table framing

Observed streams start with `SsmgV.01` and include `TCntV.01` and `MarkV.01`. Shared storage does not establish a common unit or origin. Marker occurrence alone does not define section boundaries.

## TCntV.01

One initial family has 29-byte entries from stream offset `0x24`:

| Entry offset | Form | Observation |
| --- | --- | --- |
| 0–3 | BE-u32 | First number |
| 4–7 | BE-u32 | Second number |
| 8–28 | Raw bytes | Tail fields and flag candidates |

The first numbers have ordered/range-like relationships. Body-byte, UTF-16-unit, and style-reference interpretations each fit only some cases. Shifted families also exist; do not impose this stride/layout on every input.

An overlap with body text does not establish paragraph ownership, style scope, or page coordinates. Small numbers matching style IDs are insufficient evidence.

## MarkV.01 in position tables

In the first five inspected streams, `MarkV.01` starts at byte 30. Its next six bytes have these forms:

```text
00 00 00 00 06 03
00 00 00 00 06 10
00 00 00 00 06 1c
```

Later six-byte items fit BE-u16 ID plus BE-u32 value; `0xffff` termination is observed.

The initial parser treated the leading six bytes as a header. Controlled bookmark cases also permit a first ID/value-item interpretation. Keep both alternatives; do not discard that potential item.

The values do not directly behave as extracted plain-text positions or LineMark word indexes. Their targets and origins remain unresolved.

## Offset-bias hypotheses

Adding 29 UTF-16 units reaches saved TOC-title neighborhoods in some initial inputs. Deltas 9/30 compete in others. rjtd's controlled first-line bookmark does not fit fixed `+29`.

No common fixed conversion is established. Associate proposed biases with their family and counterexamples before extending them.

## Separate MarkTag directory

The controlled 2026 profile is:

```text
8 bytes   ASCII MarkV.01
u16 BE    item count
repeat:
  u32 BE  directory value
  u16 BE  UTF-16-unit name length
  ...     UTF-16BE name
```

Three names have values 0, `0x00010000`, and `0x00020000`. These are neutral directory values, not decoded body addresses. Check full framing, Unicode, and exact consumption. Do not read this directory using position-table grammar merely because it shares `MarkV.01`.

The association between names and position-table items remains open.

## Differences between source records

| Topic | Tika source 0006 | Later rjtd record | Treatment |
| --- | --- | --- | --- |
| Leading six bytes | Unknown header | Possible first ID/value item | Retain both interpretations |
| `+29` | Competes with 9/30, not established | Additional first-line counterexample | Not a conflict over an accepted bias |
| MarkTag directory | This profile is absent | Controlled name/count/value observations | Attribute the addition to rjtd |
| TCnt tail roles | Cross-input style/coordinate diagnostics | Same initial research retained in detail | Do not count imported diagnostics twice |

Initial `f1` fields match style candidates in some inputs and text coordinates in others. Nearly constant `f7` also fails to resolve position/flag roles. Do not infer global references from numerical coincidence.

Descriptions of `documentTextOverlaps` and paragraph-boundary candidates in Tika's document describe imported rjtd diagnostics. They do not establish Kotlin implementation or independent reproduction of those diagnostics.

## Validation and open questions

Move one named bookmark across known plain, paragraph, control, supplementary-character, and ruby positions. Change names independently, then add/remove bookmarks. Record both streams and source ranges for each save.

Other maintainers should check item boundaries, strings, and raw values on the same input. A coordinate interpretation must explain units, origins, framing, and all contrasts.

Unresolved areas include complete TCnt layouts, the first six MarkV bytes, targets/units/origins, name-position correspondence, and version/profile differences.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd source RFC 0006](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0006-document-text-position-tables.md)
- [OpenJTD/rjtd source RFC 0003](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0003-document-text.md)
- [KHiyowa/Tika-JTD source RFC 0006](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0006-document-text-position-tables.md)
- [KHiyowa/Tika-JTD source RFC 0006 (L635)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0006-document-text-position-tables.md#L635)
