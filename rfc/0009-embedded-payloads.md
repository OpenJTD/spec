# RFC 0009: Embedded Documents and Graphic Payloads

## Scope and evidence

This RFC records embedding paths, payload forms, and external-format associations. Separate original CFB bytes from bytes transformed for another parser. Tika delegation, MIME metadata, and generated names belong to implementations.

Compare Tika source 0011/current ObjectBoxExtractor with rjtd source 0008. Native input IDs/hashes, versions, and payload ranges still need association. Names/search results alone do not establish ownership.

## Storage paths

Recorded examples include:

```text
/Embedding 1/Workbook
/Embedding 1/\x03EmbeddedPress
/Embedding 1/\x03EmbeddedPress2
/OleItem 1/Workbook
/OleItem 1/\x03EmbeddedPress
/EmbedItems/Embedding 1/Contents
```

`\x03` denotes a control character. Identify nested/sheet-local objects with their owning container and storage. Prefix-based discovery is not a complete object grammar. Lexicographic traversal is not source placement/paint order.

## Workbook payloads

| Form | Recorded observation |
| --- | --- |
| Raw BIFF candidate | BOF-like `09 08 10 00`, no outer CFB header |
| Self-contained CFB | Separate compound document with CFB signature |

Current code uses a shorter prefix in some paths. A match does not validate a complete workbook; check record lengths and full extent.

Wrapping raw BIFF in a new CFB with a Workbook entry is a parser adapter, not evidence that JTD stored that new container. Record original/derived hashes, sizes, and transformation relationships separately.

## Raw images in Contents

Current Tika code searches the beginning of Contents for JPEG/PNG/GIF/BMP and delegates from the found position to stream end:

| Candidate | Current code condition |
| --- | --- |
| JPEG | `ff d8 ff` |
| PNG | Checks eight available bytes, but compares four-byte `89 50 4e 47` |
| GIF | `GIF8` |
| BMP | BM plus DIB header size 40/108/124 |

These conditions do not validate whole images or exact ends. Minimal-signature synthetic tests do not establish complete native payload validity.

rjtd's detailed records distinguish complete payload candidate extents from surrounding headers/trailers. Early JPEG/GIF cases have source-path candidates and `09 00 01 00` / `04 00 01 00` envelope families. Some last-four-header-byte values match payload size as LE-u32. These are unresolved envelope fields, not a general object header.

Signature detection, complete payload recovery, and the controlled single-PNG Frame association are different levels of evidence. Extracting an image does not establish its body anchor, page position, or paint order.

## METAFILE EmbeddedPress

One family begins with eight-byte ASCII METAFILE:

```text
4d 45 54 41 46 49 4c 45
```

A proprietary prefix precedes a candidate metafile. The early source describes WMF; current code handles WMF and EMF:

| Candidate | Current detection |
| --- | --- |
| WMF | `01 00 09 00` |
| EMF | `01 00 00 00`, with `20 45 4d 46` at candidate +40 |

Matches do not validate the entire metafile or its terminal boundary. Record original start/end, wrapper prefix, and trailing bytes. Slicing from a match to stream end is not a general save rule.

The old approximate `0x08–0x60` window and current 256-byte search window are implementation choices, not maximum format header lengths. Keep other EmbeddedPress/equation snapshot forms separate.

## Text in graphics

The source records Shift_JIS WMF TextOut/ExtTextOut cases. Check record types, code pages, fonts, and glyph mappings. Graphics do not universally expose the same encoding or direct Unicode.

Compare graphic text with body text for duplication. Finding text does not require appending it to the body.

## Equation snapshots versus METAFILE

Tika source 0012 reports JSSnapShot32/GCI snapshots and 34-byte Contents stubs for equations. This differs from source 0011's METAFILE family.

[RFC 0006](0006-object-streams-and-anchors.md) compares GCI TextOut and JSEQ3Contents/MATH.VAF. Do not extend equation-specific WMF absence to all embeddings or apply METAFILE slicing to GCI.

## Documentation and implementation differences

| Topic | Tika source document | Code/rjtd evidence | Treatment |
| --- | --- | --- | --- |
| BIFF | Four-byte BOF description | Kotlin tests two bytes | Prefix detection differs from complete validation |
| Metafiles | Early WMF/window description | WMF/EMF and 256-byte window | Documentation/version difference |
| Images | No detailed detection table in 0011 | Four-format code; rjtd extents/envelopes/Frame contrasts | Distinguish candidates, complete payloads, and anchors |
| Depth | 4, with update to 8 planned | Current 8; object cap 64 | Not format constants |
| Returned extent | Describes payload extraction | Code slices to stream end | Check trailing data |
| Order | Case-normalized lexicographic walk | Separate rjtd Frame/Figure order contrasts | Extraction order is not placement order |

Tika delegation/recursion and rjtd ownership/geometry research are complementary. Neither is assumed to include the other's entire scope.

## Adapter contracts

Implementations choose delegation and order, derived names/MIME/output containers, depth/count/resource budgets, and failure handling. Record source relationships by storage paths and payload ranges, not generated file indexes.

## Validation and open questions

Insert one workbook/graphic in a small document, then vary type/content, nesting, object count, and sheet independently. Record original and transformed bytes separately. Other maintainers should check boundaries and inner formats.

An external parser accepting wrapped/sliced bytes does not establish all JTD header meanings or ownership. Open areas include all object types/paths, body references, proprietary prefixes/end structures, BIFF variants, encodings/maps, duplication, nesting, and version coverage.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd source RFC 0008](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0008-object-stream-candidates.md)
- [KHiyowa/Tika-JTD source RFC 0011](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0011-object-box-embedded-documents.md)
- [KHiyowa/Tika-JTD ObjectBoxExtractor.kt](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/ObjectBoxExtractor.kt)
- [KHiyowa/Tika-JTD source RFC 0012 (L49)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0012-jseq-equation-object.md#L49)
- [KHiyowa/Tika-JTD ObjectBoxExtractor.kt (L293)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/ObjectBoxExtractor.kt#L293)
- [KHiyowa/Tika-JTD ObjectBoxRecursionTest.kt](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/test/kotlin/com/hiyowa/tika/jtd/ObjectBoxRecursionTest.kt)
- [OpenJTD/rjtd source RFC 0008 (L143)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0008-object-stream-candidates.md#L143)
