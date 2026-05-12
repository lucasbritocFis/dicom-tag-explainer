# dicom-tag-explainer

> Upload a DICOM file. Get a plain-language explanation of what's inside it, organized by clinical importance. Powered by LLMs, written for developers and medical physicists entering the field of radiation oncology.

**Status:** 🚧 v0.1 in development

---

## The problem this solves

DICOM-RT files contain 200-400 tags, identified by hexadecimal codes like `(300A,0010)` or `(3006,0080)`. The official DICOM standard documentation runs thousands of pages. For someone entering radiation oncology — a software engineer at a healthtech company, a medical physics resident, a researcher building auto-segmentation models — that's an intimidating wall to scale.

Most explanations either assume deep DICOM knowledge (useless for beginners) or stop at trivial examples (useless for real files). What's missing is a tool that takes *your actual file* and tells you what's actually in it, with the noise filtered out.

This tool does exactly that, using a large language model with a carefully engineered prompt grounded in DICOM-RT expertise.

## What it does

- Accepts a DICOM file upload (`.dcm`)
- Detects object type (RT Plan, RT Structure Set, RT Dose, RT Image, CT)
- Pre-processes tags server-side: filters PHI, groups by category, decodes code values
- Sends structured data to an LLM with a domain-specific system prompt
- Returns a categorized, plain-language explanation:
  - **TL;DR** — one paragraph summary of what this file is
  - **What matters** — the clinically/technically important tags, explained
  - **Skip these** — administrative and metadata tags you can ignore at first
  - **Where to learn more** — links to relevant chapters of `dicom-rt-explained`

## Demo

[Live demo on Streamlit Community Cloud — coming soon]

[Screenshot — coming soon]

## Privacy and safety

This tool is for **anonymized DICOM files only**. Tags containing personally identifiable information (Patient Name, Patient ID, Birth Date, etc.) are filtered out before reaching the LLM, but you are still responsible for ensuring your input is appropriately anonymized for your use case.

The DICOM data is processed by a third-party LLM provider (Anthropic or Google) — see your provider's data handling policies. The author recommends only using this tool with:
- Synthetic / test DICOMs
- Properly anonymized clinical files
- Files where you have appropriate consent and legal authority

This tool is **not a medical device** and provides **no clinical decision support**. The explanations are educational only.

## Installation

```bash
git clone https://github.com/lucasbritocFis/dicom-tag-explainer.git
cd dicom-tag-explainer
pip install -r requirements.txt
cp .env.example .env  # add your LLM API key
streamlit run app.py
```

## How it works

```
DICOM file (.dcm)
    ↓
pydicom parses + extracts tags
    ↓
Server-side preprocessor:
  - Detects object type
  - Filters PHI
  - Groups by category (Patient / Study / Plan / Beam / etc.)
  - Decodes code values to readable strings
    ↓
LLM (Claude / Gemini) with structured prompt
    ↓
JSON output with categorized explanations
    ↓
Streamlit renders interactive UI
```

## Supported object types

| Object | Status |
|---|---|
| RT Plan | ✅ v0.1 |
| RT Structure Set | ✅ v0.1 |
| RT Dose | 🚧 v0.2 |
| RT Image | 🚧 v0.2 |
| CT image series | 🚧 v0.2 |
| RT Ion Plan | 📋 planned |
| RT Treatment Record | 📋 planned |

## Roadmap

- [x] v0.1 scaffold + system prompt
- [ ] v0.1 RT Plan + RT Struct support
- [ ] v0.2 RT Dose + RT Image + CT support
- [ ] v0.3 interactive Q&A mode ("ask follow-up questions")
- [ ] v0.4 batch mode (entire folder at once)
- [ ] v0.5 export explanation as PDF / Markdown

## Bilingual (EN / PT-BR)

The tool supports output in English and Brazilian Portuguese. Most DICOM-RT educational material exists only in English; this project intentionally adds Portuguese as a first-class language.

## See also

- [dicom-rt-explained](https://github.com/lucasbritocFis/dicom-rt-explained) — a deeper tutorial-style reference for DICOM-RT
- [eclipse-dicom-fix](https://github.com/lucasbritocFis/eclipse-dicom-fix) — converts compressed DICOMs to Eclipse-compatible format

## License

MIT — see [LICENSE](./LICENSE).

## Author

**Lucas Brito, PhD** — Clinical Medical Physicist and Software Developer.
[GitHub](https://github.com/lucasbritocFis) · Rio de Janeiro, Brazil
