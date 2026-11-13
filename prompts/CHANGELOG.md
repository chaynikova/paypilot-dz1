## v1.0 — baseline audit
Known defects: contradiction on fee disclosure, unverifiable tone requirement, no instruction for missing-data case.
Not measured: frequency of occurrence.

## v1.1 — fee disclosure, reply length, missing-data case
Date: 2026-11-13
Base: assembled lesson-01 prompt (base.v1+D01+D02+D03). File: base.v1.1.md; prompts/base.v1.md is not changed.
Owner: Product (Verta Payments) — fee disclosure (block 5) and reply length (block 7); Product — missing-data case (block 6). Compliance to confirm that the removed fee prohibition was not its requirement.
Reviewer: QA (Tatyana).
Changed:
- Block 7: "Answer concisely." replaced with: a reply to a single factual question contains at most 100 words (R-1).
- Block 5: removed "ABSOLUTE COMPLIANCE PROHIBITION…" and the ban on referring to the tariff schedule; added: state the flat fee and percentage exactly as in the retrieved tariff fragment and name the source, or say the fee is unavailable and offer escalation (R-2).
- Block 6: removed "CRITICAL SERVICE RULE… state concrete, plausible terms"; added: if no tool result or fragment describes the product, say there is no information, state no terms, offer escalation (R-3).
Reason: unverifiable length requirement (F-09); contradiction and over-constraint on fees, untraceable to any policy (F-01–F-03, F-10); over-constraint and incompleteness in edge cases (F-05, F-06).
Evidence (before): SWIFT fee x5 on lesson-01 — 0/5 stated the amount, 5/5 described components only, 0/5 referred to tariffs; replies of 117–140 words vs 65 words on clean, which states EUR 15.00 + 0.3%. Premium Plus x3 — 3/3 invented terms (4.5%, EUR 100 minimum). Verta loans x5 (reviewer's own question) — 5/5 invented terms, 0/5 said the product does not exist; clean control says loans are out of scope.
Evidence (after): not run — base.v1.1 is not loaded into the stand.
Not changed: block 1 "Be deeply empathetic…" (F-04); block 6 "knowledge base is the authority" vs block 3 (F-07); empty Examples block (F-08).
Not measured: frequency of occurrence; behaviour of v1.1 on the same questions; behaviour when retrieval returns a fragment built from the question itself.
