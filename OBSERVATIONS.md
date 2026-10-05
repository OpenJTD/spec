# Comparable low-level observations

Status: draft for joint review; no shared engine adapter is established yet.

Use JSON with `schema_version`, `input_id`, `input_sha256`, `surface`,
`producer` (implementation/version), `evidence`, and `observations`.
Each observation records `kind`, `byte_start`, `byte_end`, and the relevant
raw value or text. Byte ranges are zero-based, start-inclusive/end-exclusive,
relative to the stated surface (for example the logical `/DocumentText`
stream), not automatically the physical file. Name any other unit explicitly.

The corpus trial records expected text runs for two synthetic logical streams.
These expectations capture rjtd regression behavior, not native authoring or
independent agreement with Tika JTD+. An adapter may emit the same shape and
compare overlapping supported observations; missing support must be reported,
not counted as agreement. Unknown data remains available in the input bytes.

Agree on additional event kinds and normalization only after this small trial.
