# RFC 0005: Layout Marks and Page Instructions

## Scope and evidence

This RFC records LineMark, PageMark, PaperMark, page styles, and Header structures. Stored units, anchors, and display instructions belong to interpretation. Pixel conversion, font baselines, and output placement belong to implementations.

Separate the initial source 0007 inventory, rjtd's controlled 2026 page/header cases, and Tika source 0013 §2.5 direction cases. Attach input IDs, hashes, authoring versions, and settings to each profile.

## PageMark

One family has a 12-byte header and 84-byte entries. Three BE-u32 header values fit `(N, 16, N-1)`. N, stored entry count, and visible page count are distinct.

| Initial input | Header N | Stored 84-byte entries |
| --- | ---: | ---: |
| `46.jtd` | 96 | 97 |
| `a5.jtd` | 74 | 75 |
| `b6.jtd` | 98 | 98 |

Variable-length families and trailing data are also reported. Do not always divide the stream as `12 + N * 84`. Some values correlate with source-line ranges; mixed payloads and sentinel candidates prevent treating every entry as an active body page. Confirm whether line origins include margins or headers.

## PaperMark

One observed header fits `(N, 12, N-1)` as BE-u32 values, followed by index/flag pairs. N agrees with PageMark in some inspected inputs. This alone does not establish paper/page counts. All flag meanings remain unresolved.

### Different direction interpretations

Tika source 0013 §2.5 reports six authored/confirmed inputs with this BE-u16 form:

```text
header: [0, N, 0, 0x000c, 0, N-1]
group:  [0, idx, 1, orient]
```

The header corresponds to the u32 form above. A group can also be read as u32 index and `0x00010000 | orient` flags. Equivalent word grouping does not establish flag semantics.

| Tika contrast group | Reported values |
| --- | --- |
| Three horizontal inputs, including narrow wrapping that looks vertical | idx 0 has `0x10`; other orient values are zero |
| Three vertical inputs, including crossing rules and text | idx 0–2 have `0x10`; others are zero |

The reported discriminator is: an idx ≥ 1 group with `orient=0x10` indicates vertical writing. The role of idx 0 remains unknown. Retain the result within those six cases.

rjtd's earlier archival investigation records a different bit-0 vertical candidate in `0x00010011`. Its later DocumentViewStyles direction contrast is another evidence source. These are not the same predicate as Tika's bit-4/index condition.

Do not combine them into a global bit, OR predicate, or priority rule. Compare full PaperMark groups, view profiles, and known writing direction on identical inputs. Visual appearance alone does not determine writing direction.

## LineMark

Starting words include `0x0914`, `0x090b`, and `0x0912`. Internal tag candidates include `0x1000`, `0x1001`, and `0x1002`.

Controlled complete profiles associate body source-unit intervals with physical lines or vertical columns. A starting word alone does not determine direction or document kind. Coincident numbers do not establish cross-stream addresses.

## Page-style changes and dimensions

In the three-page contrast, paired class-`0x0020` records apply style ID 1 and reset ID 0. The landscape middle page has PageMark flags `0x00050100`; neighbors have `0x00010000`. Style boundaries, stock dimensions, and margins corroborate the relationship. Arbitrary IDs and general inheritance remain open.

Sequential DocumentViewStyles `0x1001` profiles have:

| Payload bytes | Observed dimensions |
| ---: | --- |
| 258 | Repeated LE-u32 dimensions at 126/130 and 154/158 |
| 267 | Added nine-byte orientation prefix, BE-u32 dimensions at 1/5 |

Confirm repeated sizes and margins. Mixed endianness in one profile does not make every field little-endian.

## Explicit margins and view defaults

rjtd's detailed research distinguishes explicit PageLayoutStyle `0x4444` / `0x4002` records from view defaults. One 39-byte payload starts `fe 80 00`, followed at byte 3 by four BE-u16 values: top, bottom, left, right in 1/100 mm.

View `0x1002` has the same order at byte 2 in a 32-byte `00 d8` form, or byte 3 in a 33-byte `00 d9 01` form. Controlled view-only changes leave explicit settings and displayed positions unchanged. rjtd uses explicit-setting precedence in that profile. This does not establish all style inheritance or the PaperMark direction discriminator.

## Page boundaries and ruled continuation

Tika source 0013 §3.8 explains strokes as page-local and redrawn on the next page, then proposes page-separated output tables. It still leaves the authoritative source boundary trigger unresolved.

rjtd preserves multi-page source ranges and style changes. This does not decide whether aligned strokes form one logical table. Distinguish physical stroke extent, flow continuation, and output splitting.

Do not combine header word-9 changes, coordinate restart, and PageMark into a confirmed page-break rule. Preserve the `0x000c` differences in [RFC 0002](0002-document-text.md).

## Header slots

The observed plain-text Header profile has:

| Stream offset | Form |
| --- | --- |
| 0–7 | ASCII `SsmgV.01` |
| 8–11 | BE-u32 slot count |
| 12–15 | BE-u32 `0x100` |

Slot i starts at `16 + 512 * i`, with these relative positions:

| Slot offset | Observation |
| --- | --- |
| +4 | `TextV.01` |
| +12 | BE-u32 UTF-16-unit count |
| +16 | Text, followed by zero and a repeated length |
| +260 | Empty TCntV.01 part |

The trailing table has zero prefix/suffix and one 24-byte six-BE-u32 entry per slot:

```text
[slotID, 0, 17 + 2 * units, 1, 1, 2 * i]
```

Controlled slot IDs 0/1/2/5/6 associate with page number and primary/secondary headers/footers. Odd/even and cover behavior applies only to known settings profiles.

Slot presence and printing enable are distinct. In the observed 2,026-byte settings section, a BE-u32 at payload byte 8 changes 0→2 with page-number printing. This trailer has code/u32-length `0x1001`/`0x1002` sections, not the sequential styles' u16-length grammar.

## Differences between source records

| Topic | rjtd | Tika JTD+ | Treatment |
| --- | --- | --- | --- |
| Initial marks | Initial inventory retained | Imported source 0007 | One source |
| Direction | Archival bit-0 candidate and later view contrasts | Six PaperMark contrasts in 0013 | Keep input-specific predicates |
| Stock/margins/Header | Later authored contrasts | Those additions are absent from imported 0007 | Attribute additions to rjtd |
| Page-separated tables | Source ranges and boundaries | Projection proposal with unresolved trigger | Separate source meaning and output policy |

Distinguish different word-width notation from different interpretation. Compare authoring conditions and versions, not just counts of successful cases.

## Validation and open questions

Hold text constant and change direction, margins, page style, headers/footers, and numbering individually. Record changed stream extents, style application/reset, active page ranges, wrapping, and page counts. Screens/PDFs can corroborate source meaning; fitted output corrections do not define source units.

Open areas include other mark families, counters/sentinels, coordinate origins, flags, inheritance, multiline/styled headers, arbitrary numbering, sections, and version coverage.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd source RFC 0007](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0007-layout-mark-streams.md)
- [KHiyowa/Tika-JTD source RFC 0007](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0007-layout-mark-streams.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L94)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L94)
- [KHiyowa/Tika-JTD source RFC 0013 (L188)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L188)
- [OpenJTD/rjtd source RFC 0008 (L285)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0008-object-stream-candidates.md#L285)
