# RFC 0005: Layout Marks and Page Instructions

## Scope and evidence

This RFC records LineMark, PageMark, PaperMark, page styles, and Header structures. Stored units, anchors, and display instructions belong to interpretation. Pixel conversion, font baselines, and output placement belong to implementations.

The recorded profiles include early mark-stream inventories, controlled Ichitaro 2026 page/header cases, and six writing-direction contrasts. Their evidence scopes remain distinct.

This RFC carries forward the cited research. The reported observations were not independently reproduced during this consolidation. The page, Header, and writing-direction cases have source-record links below. Their input files and authoring versions are not fully indexed here.

## PageMark

One family has a 12-byte header and 84-byte entries. Three BE-u32 header values fit `(N, 16, N-1)`. N, stored entry count, and visible page count are distinct.

| Initial input | Header N | Stored 84-byte entries |
| --- | ---: | ---: |
| `46.jtd` | 96 | 97 |
| `a5.jtd` | 74 | 75 |
| `b6.jtd` | 98 | 98 |

Variable-length families and trailing data are also reported, so `12 + N * 84` does not describe every stream. Some values correlate with source-line ranges; mixed payloads and sentinel candidates prevent treating every entry as an active body page. Whether line origins include margins or headers remains unresolved.

## PaperMark

One observed header fits `(N, 12, N-1)` as BE-u32 values, followed by index/flag pairs. N agrees with PageMark in some inspected inputs. This alone does not establish paper/page counts. The complete flag semantics remain unresolved.

### Different direction interpretations

Six authored/confirmed inputs have this BE-u16 form:

```text
header: [0, N, 0, 0x000c, 0, N-1]
group:  [0, idx, 1, orient]
```

The header corresponds to the u32 form above. A group can also be read as u32 index and `0x00010000 | orient` flags. Equivalent word grouping does not establish flag semantics.

| Contrast group | Reported values |
| --- | --- |
| Three horizontal inputs, including narrow wrapping that looks vertical | idx 0 has `0x10`; other orient values are zero |
| Three vertical inputs, including crossing rules and text | idx 0–2 have `0x10`; others are zero |

The reported discriminator is: an idx ≥ 1 group with `orient=0x10` indicates vertical writing. The role of idx 0 remains unknown. This result is limited to those six cases.

An earlier archival investigation records a bit-0 vertical candidate in `0x00010011`. Controlled DocumentViewStyles contrasts provide separate direction evidence. Neither is equivalent to the bit-4/index predicate above.

No global direction bit, OR predicate, or precedence rule is established across these profiles. Visual appearance alone does not determine writing direction.

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

The dimension associations depend on the complete profiles and their margin settings. Mixed endianness in one profile does not make every field little-endian.

## Explicit margins and view defaults

Explicit PageLayoutStyle `0x4444` / `0x4002` records differ from view defaults. One 39-byte payload starts `fe 80 00`, followed at byte 3 by four BE-u16 values: top, bottom, left, right in 1/100 mm.

View `0x1002` has the same order at byte 2 in a 32-byte `00 d8` form, or byte 3 in a 33-byte `00 d9 01` form. Controlled view-only changes leave explicit settings and displayed positions unchanged, consistent with explicit-setting precedence in that profile. General style inheritance and the PaperMark direction discriminator remain unresolved.

## Page boundaries and ruled continuation

Recorded page-continuation behavior describes page-local strokes redrawn on the next page. Page-separated output tables are a reconstruction proposal; the authoritative source boundary trigger remains unresolved.

Multi-page source ranges and style changes do not determine whether aligned strokes form one logical table. Physical stroke extent, flow continuation, and output splitting are distinct.

Header word-9 changes, coordinate restart, and PageMark do not establish a combined page-break rule. The `0x000c` interpretations remain distinct in [RFC 0002](0002-document-text.md).

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

Different word-width notation can describe the same bytes without resolving their interpretation. Authoring conditions and versions differ between the recorded cases.

## Open questions

Screens/PDFs corroborate source meaning in the controlled cases. Fitted output corrections do not define source units.

Open areas include other mark families, counters/sentinels, coordinate origins, flags, inheritance, multiline/styled headers, arbitrary numbering, sections, and version coverage.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd detailed research 0007](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0007-layout-mark-streams.md)

- [OpenJTD/rjtd source RFC 0007](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0007-layout-mark-streams.md)
- [KHiyowa/Tika-JTD source RFC 0007](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0007-layout-mark-streams.md)
- [KHiyowa/Tika-JTD source RFC 0013 (L94)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L94)
- [KHiyowa/Tika-JTD source RFC 0013 (L188)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0013-structured-sax-flow-contract.md#L188)
- [OpenJTD/rjtd source RFC 0008 (L285)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0008-object-stream-candidates.md#L285)
