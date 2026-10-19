# Initial import ledger

Imported: 2026-10-05

Source: [OpenJTD/rjtd at d5c7f33](https://github.com/OpenJTD/rjtd/tree/d5c7f337e55ce4411bf4130a2352d0d3942bab87/openjtd-spec/rfc).

At the initial import, these four files were copied byte for byte under their existing Apache-2.0
license. Their source history remains in rjtd. The initial copies retained
`Status: draft`; that import did not establish joint acceptance or independent reproduction.

| File | SHA-256 |
| --- | --- |
| `0001-container.md` | `def4f15d0330ece019a2887cec646308908e48cb9ca0a404ab2ae95d2f5b1ca2` |
| `0001-container.ja.md` | `81f337ae6c5db5462bcbe02d5af9da1806ce4ecab168e9b2f060220517ba0df3` |
| `0003-document-text.md` | `4f95566b9ae28f19d06f0c54771435a79fa732f83a083bc64dcd75a17413ccef` |
| `0003-document-text.ja.md` | `bb72eaf8151509e7e5d9c6429cc85e0ea411855e994294a95fe555f18484000e` |

RFC 0001 records local container inventories and implementation fallbacks.
RFC 0003 combines observed text structures, local experiments, and rjtd parser
behavior. The original local inputs are not included in this import, so local
sweep counts are historical reports rather than executions of the public trial.
References to rhwp are design/reference context, not JTD evidence by themselves.
Apply the draft evidence labels to individual claims before accepting either
RFC. The corpus trial demonstrates only two synthetic text-stream contracts.

## Consolidated RFC sequence

The initial imported versions are preserved at [spec f07e191](https://github.com/OpenJTD/spec/tree/f07e191cb9d8ac99c341d624ed80aa6e8d3df4b8/rfc).
The hashes above identify those original copies, not the revised files.

The shared sequence consolidates source RFCs into 0001–0009. Initial 0001 is
revised in place; initial DocumentText 0003 is covered by shared 0002. Shared
0003 now describes compression. The original Japanese 0001/0003 translations
remain available at the history link; they are removed from the current tree
because their wording and numbers no longer match the revised English texts.
This change does not alter the original rjtd or Tika repositories.

[The consolidation ledger](research/CONSOLIDATION.md) records pinned sources,
new/original numbering, evidence limits, and different interpretations. It
includes later Tika 0010–0013 research without treating imported records or
ports as independent verification.
