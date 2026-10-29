# RFC 0003: JTTC Compressed Container

## Scope and evidence

The recorded outer CFB, `/JSCompDocument` wrapper, and decompressed inner CFB profile comes from `setsuden_05.jttc` and `setsuden_06.jttc`, initially examined on 2026-06-18.

The recorded profile has one LHA `-lh5-` member. Other methods, member counts, and versions require separate evidence.

This RFC carries forward the cited research. The reported observations were not independently reproduced during this consolidation. The sources identify the two JTTC inputs and their measurements. A complete redistributable reproduction set for those inputs is not attached to this RFC.

## Container relationship

```text
outer CFB -> /JSCompDocument -> JustCompressedDocument wrapper
          -> LHA -lh5- member -> decompression -> inner CFB
          -> /DocumentText and associated streams
```

The two outer containers expose `/JSCompDocument`, not a direct root `/DocumentText`. Outer absence does not imply absence from the inner document.

## Outer CFB

The recorded `setsuden_05.jttc` inventory includes:

| Stream | Bytes |
| --- | ---: |
| `/\x04JSRV_SegmentInformation` | 336 |
| `/\x04JSRV_SummaryInformation` | 2,294 |
| `/\x05SummaryInformation` | 416 |
| `/JSCompDocument` | 989,412 |

Control-character escapes denote actual name characters. This is an input-specific inventory, not a mandatory tree or set of fixed lengths.

## Wrapper and LHA member

The wrapper starts with:

```text
26 00 4a 75 73 74 43 6f 6d 70 72 65
73 73 65 64 44 6f 63 75 6d 65 6e 74
```

The first two bytes are `26 00`; the next 22 are ASCII `JustCompressedDocument`. The observed LHA member begins at logical-stream byte 38. Neither that offset nor the meaning of `26 00` is a general decoded rule. Intermediate wrapper fields remain unresolved.

| Input | JSCompDocument bytes | Method | Packed bytes | Declared original bytes |
| --- | ---: | --- | ---: | ---: |
| `setsuden_05.jttc` | 989,412 | `-lh5-` | 989,292 | 1,598,976 |
| `setsuden_06.jttc` | 1,182,497 | `-lh5-` | 1,182,377 | 1,913,856 |

Decompressed data starts with CFB signature `d0 cf 11 e0 a1 b1 1a e1`. The payload is an inner document container, not just text. Signature matches and successful decompression do not establish header, checksum, or CRC validation.

## Inner document

The recorded first input has 65 inner streams and a 564-byte `/DocumentText`. Fonts, styles, positions, layout marks, notes, and objects belong to the inner container. A blank/control-heavy body does not imply absent auxiliary data. Identical paths in outer and inner containers have different owners.

## Record and implementation differences

| Topic | Shared record or rjtd | Tika JTD+ | Interpretation |
| --- | --- | --- | --- |
| Two input measurements | Initial record | Imported record | One source, not independent experiments |
| Member start | Observed 38; code searches `-lh5-` and subtracts 2 | Port uses the same search | Separate observation from algorithm |
| Auxiliary owner | Current model reads auxiliary streams from decompressed CFB | Inspected JtdParser passes original input to header/box/sheet readers after inner body extraction | Do not assume equal JTTC feature coverage |
| Dependency policy | Current format record separates implementation policy | Source 0005 retains old Rust/rhwp dependency rationale | Not a shared compression rule |

Different owner selection is an implementation difference, not evidence of two JTTC dialects.

## Position spaces

The relevant position spaces are:

1. Physical outer-file bytes.
2. Logical JSCompDocument bytes.
3. Decompressed inner-CFB bytes.
4. Individual inner logical-stream bytes.

The observed 38 belongs to the second space. Inner body positions are not compressed or outer-file positions.

## Open questions

Synthetic LH5 tests cover decoder behavior, not native save rules. Byte-identical inner containers are not an established requirement for uncompressed/JTTC save pairs.

Open questions include wrapper fields/start selection, header variants and filenames, trailer/member termination, checksums/CRC, other methods, multiple members, version coverage, recompression, and save preservation.

## Sources

Source RFC numbers retain their original project numbering.

- [OpenJTD/rjtd detailed research 0005](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/docs/research/0005-jttc-just-compressed-document.md)

- [OpenJTD/rjtd source RFC 0005](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/openjtd-spec/rfc/0005-jttc-just-compressed-document.md)
- [KHiyowa/Tika-JTD source RFC 0005](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/docs/spec/rfc/0005-jttc-just-compressed-document.md)
- [KHiyowa/Tika-JTD JustCompressedDocument.kt (L51)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/JustCompressedDocument.kt#L51)
- [KHiyowa/Tika-JTD JtdParser.kt (L66)](https://github.com/KHiyowa/Tika-JTD/blob/045ef005f7e45202650d5601b46ef6f4915d8190/tika-parser-jtd/src/main/kotlin/com/hiyowa/tika/jtd/JtdParser.kt#L66)
- [OpenJTD/rjtd lib.rs (L486)](https://github.com/OpenJTD/rjtd/blob/3a3ddee66b21f4e3e088a13dbdfd196fdf511ad1/rjtd/crates/rjtd-model/src/lib.rs#L486)
