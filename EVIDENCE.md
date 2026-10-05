# Evidence labels

Status: draft for joint review

Label individual claims, not only an entire RFC. More than one label may apply.

| Label | Meaning | Minimum record |
| --- | --- | --- |
| `observed` | Directly observed in JTD input | Input ID/hash, source location, observation, acquisition basis |
| `reproduced` | Independently reproduced with controlled inputs | Generator/authoring procedure, changed variable, tool/version, results, reproducer |
| `hypothesis` | Interpretation not established by the available evidence | Supporting observations, alternatives, falsification procedure |
| `historical` | Corroborated by a historical implementation | Implementation/version, permissible source, exact behavior, independent comparison |

Synthetic bytes verify a parser contract; they do not establish that a native
authoring application emits those bytes. Record implementation regressions as
such until an independent reproduction supports a stronger claim. Imported
historical RFCs keep their original status and must be annotated claim by claim
before acceptance under this format.
