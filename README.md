# Customer Support Ticket Intelligence System

**No-Code Track | n8n + GPT-4 + LLM-as-a-Judge | 2026**

---

## Problem

ShopNest Global receives 5,000+ customer support tickets daily across 30+ countries. The manual process had serious limitations:

- **2–3 minutes** to handle each ticket
- **Inconsistent quality** — agent-dependent responses
- **Policy compliance gaps** — wrong timelines, incorrect resolutions
- **Unscalable** — current capacity capped at ~1,000 tickets/day
- **High agent burnout** from repetitive, unstructured ticket reading

---

## Solution

A 10-node no-code n8n pipeline that automatically reads raw support tickets, generates clean summaries and policy-compliant customer responses using GPT-4, evaluates both outputs using a dual LLM-as-a-Judge framework, and saves everything to a consolidated CSV for agent review.

---

## Architecture

```
CSV Input → Extract Tickets → Summarize (GPT-4) → Evaluate Summary (GPT-4)
         → Generate Response (GPT-4) → Evaluate Response (GPT-4)
         → Compile All Fields → Export to CSV
```

### Node Breakdown

| Node | Purpose |
|---|---|
| Manual Trigger | Initiate workflow |
| Read File | Load `support_ticket_data.csv` (30 rows) |
| Extract from File | Parse CSV → JSON array of tickets |
| Summarize Ticket | GPT-4 generates 3-sentence summary |
| Evaluate Summary | LLM-as-a-Judge scores: Accuracy / Conciseness / Clarity |
| Generate Response | GPT-4 writes policy-grounded customer reply |
| Evaluate Response | LLM-as-a-Judge scores: Empathy / Policy Accuracy / Clarity / Professionalism |
| Edit Fields | Consolidate all outputs into single record |
| Convert to File | Format as CSV |
| Write to Disk | Save `customer_analysis_results.csv` |

---

## Tech Stack

| Component | Tool |
|---|---|
| Workflow Automation | n8n |
| LLM (all tasks) | GPT-4 |
| Prompting Technique | Zero-Shot + Policy Grounding |
| Evaluation | LLM-as-a-Judge (GPT-4) |
| Output | CSV file |

---

## Policies Embedded in Prompt

All 4 ShopNest business policies were embedded directly in the response generation system prompt:

1. **Refund & Return Policy** — full refund/replacement within 10 days, processed in 5–7 business days
2. **Delivery Delay Compensation** — Rs 100 voucher if delayed 3+ days; full refund if delayed 7+ days
3. **Wrong / Damaged Item Policy** — free replacement or refund, pickup within 2 business days, no return shipping cost
4. **Payment Failure Policy** — automatic reversal within 3–5 business days; transaction reference required if not reversed

---

## Prompting Design Decisions

| Task | Technique | Reason |
|---|---|---|
| Summarization | Zero-Shot | Straightforward extraction — GPT-4 needs no examples |
| Summary Evaluation | Zero-Shot, Temp 0.3 | Subjective criteria allow slight scoring variation |
| Response Generation | Zero-Shot + Policy Grounding | Full policy text in prompt ensures correct timelines and resolutions |
| Response Evaluation | Zero-Shot, Temp 0.1 | Policy compliance must be deterministic — same response should score consistently |

---

## Results

| Metric | Score |
|---|---|
| Summary Quality (avg) | 4.7 / 5 |
| Response Quality (avg) | 4.75 / 5 |
| Policy Compliance | 100% (all 4 policies correctly applied) |
| Processing Time | 5–10 seconds per ticket (vs. 2–3 minutes manual) |
| Throughput Increase | 1,000 → 5,000+ tickets/day |
| Estimated Annual Savings | $3.5M+ |

---

## Sample Output

**Raw Ticket:** *"I cannot believe the level of service I have received... Order SNX-4421 for a Bosch dishwasher. Delivered wrong model. Want replacement."*

**Generated Summary:**
> The customer, a long-time ShopNest patron, is frustrated with poor customer service and has received the wrong model of a Bosch dishwasher (Order SNX-4421). They have tried to reach the support team without success. The customer wants a replacement for the incorrect item.

**Summary Score:** Accuracy 5/5, Conciseness 5/5, Clarity 5/5, Overall 5/5 ✅

**Generated Response:**
> Dear Customer, I'm truly sorry for the inconvenience with your recent order SNX-4421. You are absolutely eligible for a free replacement. I've immediately initiated the process to pick up the incorrect dishwasher model from your location within the next 2 business days. Once we verify the return, we'll dispatch the correct Bosch dishwasher model to you. Please accept our sincere apologies — your satisfaction is our top priority.

**Response Score:** Empathy 5/5, Policy Accuracy 5/5, Clarity 5/5, Professionalism 5/5, Overall 5/5 ✅

---

## Files in This Repo

```
├── README.md
├── Support_Ticket_Analysis_Final.json          # n8n workflow — import to run
├── Customer_Support_Ticket_Intelligence.pdf    # Full project report
└── screenshots/                                # n8n workflow and output screenshots
```

---

## How to Run

1. Add OpenAI credentials (GPT-4) in n8n
2. Upload `support_ticket_data.csv` to `/data/shared/` via n8n Manage Files
3. Import `Support_Ticket_Analysis_Final.json` into n8n
4. Click **Execute Workflow**
5. Output saved to `/data/shared/customer_analysis_results.csv`

---

## Key Learnings

- Embedding full policy text directly in the system prompt is more reliable than expecting the model to recall policies from training data
- Temperature should match the task: 0.3 for subjective evaluation, 0.1 for compliance checking
- Sequential pipeline works well for batch processing but would need async rearchitecting for real-time ticket handling
- LLM-as-a-Judge enables scalable quality monitoring without human reviewers for every ticket
