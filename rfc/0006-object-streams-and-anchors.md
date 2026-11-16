# RFC 0006: Object and Equation Streams and Source Associations

## Scope and evidence

This RFC records candidate paths/payloads, body references, Frame/Figure associations, and saved display data. Ownership, geometry, and paint order require separate evidence. A signature or matching dimensions alone is insufficient.

The recorded profiles include the initial object inventory, controlled Ichitaro 2026 PNG/figure/equation cases, and a multi-equation JSEQ3 study. Numeric associations are profile-specific.

This RFC carries forward the cited research. The reported observations were not independently reproduced during this consolidation. The sources link the object contrasts and JSEQ3 study. Input-level reproduction links and authoring-version coverage are incomplete.

## Candidate paths

Observed names include Frame, Figure, LayoutBox, EmbedItems, EmbeddingInfo, Contents, EmbeddedPress, FDMIndex, and FDMVector. Paths, image signatures, and `SO\0\0` identify investigation candidates, not complete payload validity, ownership, or placement.

Absence of table-named objects does not exclude ruled structures in body flow.

## Single PNG and Frame

A 76-byte Frame with one 60-byte row associates with a complete PNG in `/EmbedItems/Embedding 1/Contents`. ID 0/type 1, complete body object/cache framing, frame dimensions, and aspect ratio corroborate the association.

Offsets below are relative to the 60-byte row:

| Offset | Controlled association |
| --- | --- |
| 16, BE-u16 | 0 inline, 1 floating |
| 28/32/36/40 | Integer x/y/width/height candidates; adjacent low words zero |
| 44 | 200 corresponds to 2 mm text clearance |
| 46 | 1 wrapping, 4 in front |

The class-zero/tag-`0x0030` body record has `0x0107,0x0010` inline and `0x0507,0x0012` floating contexts. The association depends on the complete contiguous cache group and its source ranges. The 0.01-mm interpretation is bounded to this profile; arbitrary units, baselines, cropping, and alignment remain open.

## Frame-linked text boxes

The 2026-09-26 inventory associates 60-byte Frame rows whose word at row byte 8 is 2 with `/LayoutBox` and `/LayoutBoxText` records in Frame stream order. Counts agree in 25 of 27 inspected files. The other two have incomplete or malformed stream evidence and are outside that profile.

Frame IDs can be sparse. They are not text-block indexes. The word at row
byte 12 is not a universal object type or text-block selector: in the reported
`tmogi2_2` case, text block 6 belongs to Frame object 9, while sorting by the
byte-12 values gives a different association.

The source records this 14-word class-zero context in body and text-box text:

```text
001c 0000 000e 0000 0030 [w5] 0507 0012 [frame-id] 0000 000e 0000 0000 001f
```

In one controlled document, the body references Frame 0 and its text box
references Frames 6 through 1. Their relative rectangles form two columns
and three rows. This is a frame hierarchy observation, not evidence that
every rectangular arrangement uses the ruled-flow records in RFC 0007.
General trees, mixed objects, page assignment, and paint order remain open.

## Figure identity, order, and fill

In the three-figure contrast, Frame kind 4 at row offset 8 and the one-based slot at 12 correspond to separate Figure ID ordering. Swapping rectangle/ellipse order corroborates this independently of dimensions.

FDMIndex uses axis pairs `x1,x2,y1,y2` in this profile.

| Raw marker | Observed outline |
| --- | --- |
| `01000660` | Rectangle |
| `01000460` | Ellipse |
| `01000160` | Line |

An observed `01000a60` fill parent has a 46-byte prefix and nested outline child. The color association depends on complete parent/child lengths and boundaries: BE-u32 at parent offset 36 corresponds to BGR fill color. Empty and opaque AlphaBlend profiles accompany nontransparent cases. Arbitrary paths, world units, alpha modes, composites, and paint-order generalization remain open.

## GCI equation-display profile

The first `JSEQ.Document.3` embedding permits a frame-reference-zero association when primary/trailing dimensions agree. This is not a universal zero-reference rule.

The controlled GCI snapshot has these packet relationships:

| Value | Observed role |
| --- | --- |
| `0x94` | Font |
| `0x60` | Select/restore |
| `0x40` | Background state |
| `0xc8` | One-character TextOut |
| `0x65` | Release |

Size values 370/240 correspond to normal/smaller characters. Ordered text/color agrees with editable JSEQ3 packets in the same input. TextOut supplies a top reference, not a decoded font baseline. This does not define a general equation AST, character map, or editing grammar.

## JSEQ3 editable-data profiles

### Storage and identity

Equation objects occur under sheet-local `EmbedItems/Embedding N/` storages. A controlled case also has this family below the root; the owning sheet/storage distinguishes these locations.

Reported identity strings include `一太郎数式オブジェクト`, `JSEQ3`, `JS.EqtnCtrl.1`, and `JSEQ.Document.3`. Strings, ProgIDs, and binary CLSIDs are distinct. The source groups some under the same heading; exact CompObj fields and the CLSID value remain unresolved.

| Stream | Reported form |
| --- | --- |
| `\x01CompObj` | JSEQ3 identity; 99-byte case |
| `JSEQ3Contents` | Editable data with UTF-16LE MATH.VAF marker |
| `\x03EmbeddedPress` | JSSnapShot32/GCI display snapshot |
| `\x03Contents` | 34-byte FORM stub case |
| `\x01Ole` | 20-byte case |
| `\x04JSRV_SegmentInformation` | 720-byte case |

These are observed sizes, not mandatory fixed sizes for every object.

### MATH.VAF header and lengths

The recorded MATH.VAF profile has a 32-byte header:

| Offset | Source interpretation |
| --- | --- |
| `0x00–0x19` | UTF-16LE MATH.VAF identification area and padding |
| `0x1a–0x1b` | u16 zero, meaning unknown |
| `0x1c–0x1d` | LE-u16 `0x0103`, version/flag candidate |
| `0x1e–0x1f` | LE-u16 body-length candidate L_body |

The reported relationship is `total bytes - L_body = 1,162` for 27 of 29 equations, described as 32 header bytes plus 1,130 trailing bytes. The other differences are 1,334 and 990 bytes.

The 1,162-byte difference is not universal. Auxiliary style sections are a proposed explanation for the two variants, not an established structure. The separate GCI display contrast does not independently verify this header or length relationship.

### Fonts and tree candidates

The source records Times New Roman, JustUnitMark, JustOubunMark, and recurring slots `0x16e6`/`0x177a`. Recurrence does not establish selection/inheritance semantics.

A reported child-record candidate is `[x,0x94,1,2,0,childID,1,x,0]`, with coordinate-like x values. Operator grammar and child order remain unknown. Fractions, radicals, and scripts are not a fully decoded AST.

Symbol-font references can be glyph indices rather than Unicode. The source also reports variants without inline caches; cache presence is not guaranteed.

## Differences in equation research

| Topic | rjtd | Tika source 0012 | Treatment |
| --- | --- | --- | --- |
| Main evidence | One Frame/GCI/editable-data contrast | Multi-equation storage/header/length/font study | Complementary experiments |
| Display text | Saved TextOut text/color agrees with editable data | Proposes AST and character-map extraction | Separate saved display text from equation semantics |
| WMF absence | Specific GCI snapshot | Contents stubs support an equation-specific absence statement | Do not extend to other JTD embeddings |
| `0x94` | GCI font packet | Value in a JSEQ3 child candidate | Different streams/framing, not one opcode |
| Sizes/fonts | GCI 370/240 and specific styling | Names/slots/internal glyph references | Do not transfer units between structures |

The Word/RTF→WMF full-palette mapping and AST→text→SAX pipeline remain proposals. They do not establish a completed AST or character map, and WMF values are not a Unicode oracle without resolved code-page, symbol-font, and conversion mappings.

## Open questions

Open areas include rich/multi-object/vertical anchors, coordinate units, cropping, composites/transparency, paint order, MATH.VAF variants, CompObj/CLSID fields, font slots/maps, cache-free forms, ASTs, and editing semantics.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd detailed research 0008 (L299)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0008-object-stream-candidates.md#L299)

- [OpenJTD/rjtd source RFC 0008](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0008-object-stream-candidates.md)
- [KHiyowa/Tika-JTD source RFC 0008](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0008-object-stream-candidates.md)
- [KHiyowa/Tika-JTD source RFC 0012](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0012-jseq-equation-object.md)
- [OpenJTD/rjtd source RFC 0008 (L435)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0008-object-stream-candidates.md#L435)
