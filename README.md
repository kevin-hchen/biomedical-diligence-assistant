# Biomedical Diligence Assistant

An evidence-retrieval tool for first-pass diligence on biotech and drug-development companies. I built it at Pebble Accelerator, a Tigermed-backed biomedical venture accelerator in Hong Kong. It now runs on all of Pebble's live deals, and I'm still maintaining it.

> **The source code is private** because the tool is used internally on live deals. This repo is a write-up of what it does, how it works, and how I checked that it's right.

---

## The problem

First-pass diligence on a drug asset meant an analyst spending **10–15 hours** in trial registries, FDA databases and the literature. They were answering the same questions every time: What trials exist? What phase are they in? Which ones stopped, and why? What has the FDA already said?

An LLM chatbot doesn't solve this. An investor can't act on a confident answer they can't trace.

## What it does

- Answers a fixed set of diligence questions about a drug, target or company in **under 10 minutes**.
- Searches **241,298 clinical trials**, **six FDA databases** and **PubMed**.
- **Every claim links back to a source record**, so an analyst can check each point in one click.
- Looks for negative evidence too (terminated trials, safety signals), not only the good news.
- Produces a memo-ready summary.

## How it works

```
question ──► router ──┬──► structured store (trials, FDA records) ──┐
                      │      filter on phase, status, sponsor, dates │
                      │                                              ├──► answer + source links
                      └──► semantic search (PubMed, free text) ──────┘
```

**Key design decision:** trial records live in a **structured database, not an embedding index**. Phase, status and sponsor are things you filter on, not fuzzy text to match, so they don't belong in an embedding index as prose. Questions are routed to structured queries or semantic search depending on what they need.

**Precomputed results:** eligibility results are precomputed into versioned data, which took queries from **12.8s to 0.03s (400×+ faster)**.

**Stack:** Python, SQL, FAISS, OpenAI API, openFDA, ClinicalTrials.gov, PubMed.

## How I know it's right

- **150 studies hand-read** and compared against the tool's answers.
- **865 automated tests**.
- **2.15M parity checks with zero divergences** between the precomputed data and the live calculation.

## What I'd do differently

I had about three weeks before my placement ended, so I skipped the UI and put the time into getting the answers right. The next step is a lightweight interface so analysts don't need me to run it.

---

**Kevin Chen** · Math/CS + Business @ Waterloo · [LinkedIn](https://linkedin.com/in/kevinhchen) · [kevinhchen.ca](https://kevinhchen.ca)
