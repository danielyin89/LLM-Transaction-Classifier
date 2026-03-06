# LLM-Assisted Transaction Reason-Code Classifier

A lightweight Python workflow that uses an LLM (GPT-4o-mini) to automatically classify transaction discrepancy descriptions into standardized reason codes, supporting downstream reconciliation triage.

---

## Business Problem

In a high-velocity transaction environment (400–600 orders/day across Douyin Local Life and Meituan), exception triage requires manually labeling each discrepancy with a reason code before routing it to the correct resolution workflow. This manual step is time-consuming and inconsistent across reviewers.

This project automates the classification step using an LLM, allowing analysts to focus manual review capacity on edge cases rather than routine labeling.

---

## Reason Code Taxonomy

| Reason Code | Description |
|---|---|
| `Weekend Batch Delay` | Settlement timing gap due to weekend or holiday batch processing |
| `Gateway Timeout` | Payment gateway failure, connection error, or timeout |
| `Duplicate Entry` | Same transaction appears more than once in settlement files |
| `Refund Mismatch` | Refund processed but not correctly reconciled in the ledger |
| `Fee Deduction Gap` | Platform fee discrepancy not captured in revenue report |
| `Unclassified` | LLM cannot confidently assign a reason code — routed to manual review |

---

## Project Structure

```
├── sample_transactions.csv     # Simulated transaction discrepancy data (20 records)
├── classifier.ipynb            # Main Colab notebook — runs the full pipeline
├── output_classified.csv       # Output: labeled transactions + review status
└── README.md
```

---

## How to Run

1. Open `classifier.ipynb` in Google Colab
2. Upload `sample_transactions.csv` to the Colab session
3. Add your OpenAI API key in Step 2
4. Run all cells in order

**Cost estimate**: ~$0.01 for 20 transactions using `gpt-4o-mini`

---

## Pipeline Steps

```
Raw Descriptions → LLM Classifier → Reason Code Labels
                                  → Accuracy Validation vs Manual Labels
                                  → QA Exception Report (Auto-Approved / Flagged)
```

1. **Load** transaction data from CSV
2. **Classify** each description via OpenAI API (temperature=0 for consistency)
3. **Validate** LLM output against manual labels — compute accuracy
4. **Generate** QA exception report: auto-approved vs. flagged for manual review
5. **Summarize** reason code distribution

---

## Design Decisions

- **temperature=0**: Ensures deterministic, consistent classification output
- **Output validation**: LLM response checked against known taxonomy before saving — unknown labels default to `Unclassified`
- **Rate limit buffer**: 0.5s delay between API calls to avoid throttling
- **Separation of auto-approved vs. flagged**: Mirrors real-world triage workflow where high-confidence matches bypass manual review

---

## Sample Output

| transaction_id | llm_label | match | review_status |
|---|---|---|---|
| TXN001 | Weekend Batch Delay | True | Auto-Approved |
| TXN002 | Gateway Timeout | True | Auto-Approved |
| TXN003 | Duplicate Entry | True | Auto-Approved |

---

## Connection to IEEE Fraud Monitoring Project

This project extends the [End-to-End Fraud Monitoring & Risk Strategy Pipeline](https://github.com/danielyin89/End-to-End-Fraud-Monitoring-Risk-Strategy-Pipeline) by automating the reason-code labeling step that previously required manual triage. Where the fraud pipeline focuses on detection and threshold policy, this classifier focuses on exception routing and audit-ready documentation.

---

## Tech Stack

- Python 3
- OpenAI API (`gpt-4o-mini`)
- Pandas
- Google Colab

## Author

Wangyang Yin | [GitHub](https://github.com/danielyin89)
