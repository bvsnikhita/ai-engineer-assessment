# ai-engineer-assessment
# AI Engineer Assessment — Submission

## Business Vertical
Personal loan qualification (HDFC Bank).

## Source Data
- 3 real HDFC Bank PDFs (`raw_pdfs/`): `MITC HDFC.pdf` (terms, fees, rate
  tables), `personal-loans hdfc.pdf` (full loan agreement), and
  `hdfc brouchre.pdf` (marketing brochure — image-based, text extraction
  failed, see below)
- 8 manually written documents (`raw_content.md`): 6 FAQ entries on loan
  eligibility, 1 product summary (transcribed from the brochure since it
  couldn't be extracted automatically), and 1 deliberately planted PII
  example (mock customer record with a fake phone number and email) to
  test PII detection

## Cleaning Pipeline (`clean.py`)
Extracts text from PDFs (`pdfplumber`), parses hand-written documents,
cleans formatting artifacts, detects and redacts PII via regex (phone
numbers, emails), detects and drops near-duplicate documents, and outputs
a structured dataset with `record_id`, `title`, `content`, `category`,
`source`, `version`, and `pii` fields.

## Results
- **10 clean documents** produced (2 from PDFs, 8 manual)
- **1 extraction failure caught and logged**: the brochure PDF is
  image-based with no extractable text; this was detected and logged
  rather than silently skipped, and its content was separately
  hand-transcribed so no data was lost
- **1 PII case correctly detected and redacted**: the mock customer record's
  phone number and email were flagged and replaced with
  `[REDACTED_PHONE]` / `[REDACTED_EMAIL]`
- No duplicates found in this dataset (dedup logic tested separately and
  confirmed working during development)

**Known limitation:** PII detection currently covers phone numbers and
emails via regex only; it does not detect names or ID numbers, which would
need a dedicated PII-detection library in production.

## Repository Structure
```
knowledge-base/
  raw_pdfs/
    MITC HDFC.pdf
    personal-loans hdfc.pdf
    hdfc brouchre.pdf
  raw_content.md
  clean.py
  clean_docs.json
  cleaning_log.md
```

## How to Run
```
pip install pdfplumber
python clean.py
```
