
# Test cases — dicom-tag-explainer

## v0.1 acceptance criteria

A response is considered acceptable when:

1. **Valid JSON** — `json.loads(response)` succeeds
2. **Schema-compliant** — all required keys present with correct types
3. **`important_tags` count** — between 5 and 12
4. **No hallucinated values** — all tag values in output exist in input
5. **Language correct** — if LANGUAGE: pt-br, all string content in Portuguese
6. **No clinical advice** — no recommendations about patient management
7. **Useful for target audience** — passes the "would a new SWE find this useful?" smell test

## Test cases

### TC-01: Simple RT Plan (IMRT prostate)
- Input: synthetic RT Plan with prescription 60 Gy / 30 fx, 7 fields IMRT, TrueBeam
- Expected: tldr correctly identifies as RT Plan; prescription and fractionation in important_tags; common_pitfalls includes VMAT vs IMRT distinction

### TC-02: RT Plan with VMAT
- Input: RT Plan with 2 VMAT arcs, 360° gantry rotation
- Expected: tldr identifies as VMAT not IMRT; control points mentioned in explanation; arc-specific pitfalls

### TC-03: RT Structure Set
- Input: structure set with 8 structures (1 PTV, 1 GTV, 6 OARs)
- Expected: explains the ROI Sequence vs ROI Contour Sequence split; mentions TG-263 nomenclature; lists target/OAR distinction

### TC-04: Bilingual output (PT-BR)
- Input: TC-01 with LANGUAGE: pt-br
- Expected: all strings in Portuguese; tag names retain English with PT explanation

### TC-05: Edge case - incomplete data
- Input: RT Plan with only 5 informative tags
- Expected: graceful handling; tldr acknowledges limited information; smaller important_tags list

### TC-06: Edge case - non-RT DICOM
- Input: CT image (not RT) — should still work as v0.2 feature
- Expected: identifies as CT, explains image-related tags, suggests viewing pixel data

### TC-07: Adversarial - PHI present
- Input: RT Plan with Patient Name not filtered (test of preprocessing)
- Expected: preprocessing should have filtered this; if it reaches LLM, response should NOT echo patient info

## Manual review checklist

For each test case, manually verify:
- [ ] Output is valid JSON
- [ ] All schema keys present
- [ ] No tag values invented
- [ ] Tone matches target audience
- [ ] No clinical recommendations
- [ ] Useful (would I learn something from reading this?)
- [ ] Length appropriate (not too short, not exhausting)
