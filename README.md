# HCL Claim Analysis Agent

A agent for reviewing employee travel reimbursement claims against a travel policy. It retrieves policy sections from a local Chroma vector store, checks expense limits and submission dates, and returns a structured decision with amounts, missing documents, policy references, and an explanation.

## What It Checks

- Eligible and ineligible expense categories
- Meal, lodging, and ground-transport daily limits
- Receipt requirements for individual expenses
- Approval thresholds and manual-review conditions
- Claim submission timing

The policy text, agent tools, evaluation workflow, five sample claims, and dashboard are in [`agent.ipynb`](agent.ipynb).

## Requirements

- Python 3.10 or later
- An OpenAI API key with access to `gpt-4o-mini` and `text-embedding-3-small`
- The dependencies listed in [`requirements.txt`](requirements.txt)
- VS Code with the Jupyter extension, or another Jupyter environment

## Setup

1. Create and activate a virtual environment

2. Install the project dependencies:

3. Create a local environment file and add your API key:

	Set `OPENAI_API_KEY` in `.env`.

4. Open `agent.ipynb` and run the cells from top to bottom. The notebook creates or loads the persistent Chroma data in `chroma_policy_db/`, evaluates the sample claims, and displays a results dashboard.

## Sample Claim

The notebook includes this synthetic example of a lodging claim that exceeds the policy cap:

```json
{
  "claim_id": "CLM-003",
  "employee": "C. Nakamura",
  "trip_dates": "2026-06-08 to 2026-06-10",
  "submitted_date": "2026-06-22",
  "purpose": "Client site visit (business)",
  "total_claimed": 940.00,
  "items": [
	 {"category": "airfare", "description": "Round-trip economy airfare", "amount": 300.00, "receipt": true},
	 {"category": "lodging", "description": "Hotel, 2 nights @ $250", "amount": 500.00, "receipt": true, "units": 2},
	 {"category": "meals", "description": "Meals, 2 days @ $70/day", "amount": 140.00, "receipt": true, "units": 2}
  ]
}
```


## Sample Outcomes

These illustrative outcomes correspond to the five claims included in the notebook. LLM-generated explanations and policy-reference lists may vary between runs.

| Claim | Scenario | Decision | Claimed | Approved | Deducted |
| --- | --- | --- | ---: | ---: | ---: |
| CLM-001 | Compliant conference expenses | APPROVE | $1,110 | $1,110 | $0 |
| CLM-002 | Spa and minibar expenses | REJECT | $380 | $0 | $380 |
| CLM-003 | Lodging exceeds nightly cap by $100 | PARTIAL_APPROVE | $940 | $840 | $100 |
| CLM-004 | Business-class airfare, missing receipt, and amount over $2,000 | MANUAL_REVIEW | $3,000 | $0 | $0 |
| CLM-005 | Meal claim missing a required receipt | MANUAL_REVIEW | $220 | $0 | $0 |

For manual-review cases, the notebook's decision instructions set the approved and deducted amounts to zero pending review; they are not final reimbursement amounts.

## Dashboard

The notebook generates a decision-count chart and a claimed-versus-approved-versus-deducted amount chart. The included screenshot is shown below.

![Travel reimbursement claim outcomes dashboard](UI_SS_1.png)
