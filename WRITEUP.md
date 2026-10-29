# AI Proposal Triage Design

## 1. AI vs. rules
The reviewer uploads proposal files and selects a category. The system validates file type/size, stores the original securely, extracts text using OCR/PDF parsing, and records page references. An AI model converts the extracted text into a strict JSON evidence record: licence evidence, WSIB and insurance evidence, relevant projects, price, schedule, warranties, team, safety and monitoring. Every field includes a short supporting quote and page/section reference, or is marked `unknown`. The model must treat document content as untrusted data and ignore instructions inside a proposal.

Ordinary code validates the JSON schema, checks mandatory criteria, validates units/currency, applies admin-configured weights, calculates scores, sorts eligible proposals and records reasons. This split is intentional: AI is useful for interpreting varied natural-language documents; fixed rules are more reliable for pass/fail decisions and arithmetic.

## 2. Consistency and explainability
Each run stores the original document hash, extracted text version, category, criteria version, prompt/model version, timestamp, evidence fields, reviewer corrections and score breakdown. Use a fixed scoring rubric, deterministic formulas, validated structured output and a fixed tie-break rule. Use a pinned model/version and low or zero temperature where supported, but do not promise identical extraction in every model run; discrepancies should be flagged for review. The same confirmed evidence and criteria version must always produce the same calculated scores.

The result page shows each mandatory check, each weighted criterion score, the weight, evidence quote, source location, missing information, total score and reason for the rank. A reviewer can open the source document next to the extracted evidence.

### Scoring assumptions used for the sample
Scores are on a 0–100 scale. Experience = `min(qualifying installations / 10, 1) × 100`. Price = `lowest price among mandatory-eligible proposals / proposal price × 100`. Timeline = `(16 − completion weeks) / 16 × 100` for a stated schedule within the 16-week limit. Warranty = average of panel warranty normalized to 25 years and workmanship warranty normalized to 12 years, each capped at 100. Team/approach gives 25 points each for a named lead, described crew, safety/roof assessment, and monitoring plan; weekly progress reporting alone is not treated as a monitoring plan. Weighted total = experience × 30% + price × 25% + timeline × 15% + warranty × 15% + team/approach × 15%. These are transparent sample assumptions, not universal procurement rules; in production the admin would configure the rubric and the UI would show it before scoring.

## 3. Human in the loop
A reviewer confirms extracted evidence and resolves missing or contradictory fields before a proposal is scored. Proposals that fail a mandatory requirement are marked ineligible; proposals with unresolved mandatory evidence or missing information that materially affects ranking are flagged rather than silently guessed. The reviewer may correct an extraction or override a system recommendation, but must enter a reason. The tool recommends and explains; an authorized human makes the award decision.

Audit logs record uploads, document hashes, criteria versions, extracted evidence, AI/prompt versions, rule results, score calculations, reviewer edits/overrides, user identity, timestamps and final decision. Access is role-based, and retention follows the organization's policy.

## 4. Changing criteria
Criteria and weights are versioned. Editing criteria creates a new version; it does not silently rewrite past results. Each assessment remains linked to the exact version used. The reviewer can explicitly rerun proposals against the new version, creating a new result set with a comparison to the previous one. Validate that weighted criteria total 100%, mandatory criteria have no weights, and scoring ranges are valid before publishing a criteria version.

## 5. Risks and safeguards
- Incorrect extraction/OCR: retain source quotes and locations; validate formats and totals; flag low-confidence or conflicting fields for human review.
- Hallucinated evidence: score only facts supported by a quote in the source; missing evidence is `unknown`, never invented.
- Prompt injection in a proposal: treat all proposal text as untrusted data; never follow instructions found inside documents. Proposal C's embedded request to be ranked first is ignored and logged as suspicious content.
- Unfair or inconsistent scoring: use published, job-relevant criteria, fixed formulas and the same criteria version for all bidders; audit score distributions and reviewer overrides.
- Manipulated or incomplete bids: verify mandatory requirements and price/timeline completeness; request clarification instead of inferring missing details.
- Cost, privacy and security: limit document access, avoid sending unnecessary personal/confidential data to model providers, set file-size/rate limits, protect stored files and secrets, and monitor token/API cost.
- Over-reliance on automation: display limitations and evidence, require human approval, and never automatically award a contract.
