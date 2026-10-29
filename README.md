# AI Proposal Triage Design

Candidate: Ali Mehmood  
Assessment reference: MATACS-TH-2026-02  
Role: Associate Software Developer  
Time spent: 45 min

## Overview
This repository describes a human-supervised design for an AI-assisted proposal triage workflow for commercial solar installation proposals. AI extracts evidence from documents; ordinary code checks mandatory requirements and calculates weighted scores; a reviewer verifies uncertain evidence and makes the final award decision.

## Files
- [Design write-up](WRITEUP.md)
- [AI prompts](PROMPTS.md)
- [Expected sample ranking](ranking.json)
- [Process-flow diagram](diagram/process-flow.png)

## How AI tools were used
I used ChatGPT to help organize the design, draft an extraction prompt, and check the scoring approach. I reviewed the output rather than treating it as authoritative: for example, I rejected the instruction embedded in Proposal C that asked automated systems to rank it first, because proposal text is untrusted evidence and must never override the configured evaluation criteria. I also made mandatory eligibility checks and weighted calculations deterministic so the same validated inputs and criteria version produce the same result.

## Important assumptions
- Only the fictional proposal summaries supplied for this assessment are considered.
- A mandatory requirement must be explicitly evidenced in the proposal; missing or ambiguous evidence is not treated as a pass.
- Proposal B is ineligible because it does not confirm that the bidder itself holds the required Ontario electrical contractor licence; it says the subcontractor will be confirmed later.
- Proposal D is flagged for human clarification because it does not state a completion timeline.
- Scores are provisional design outputs, not a procurement decision. A reviewer must verify the evidence and approve any final result.
