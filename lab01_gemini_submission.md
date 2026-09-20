# Lab 01 - The Price of One Request

**Provider/model:** Google Gemini `gemini-3.5-flash`  
**Pricing used:** standard paid tier, USD per 1M tokens: **$1.50 input / $9.00 output (including thinking tokens)**.  
**Annual workload assumption:** **2,000 support requests/day**, because this represents a small but realistic support queue and makes the language-cost difference visible: 730,000 requests/year.

## 1. Prediction versus measurement

Part 1 predicted the token multiplier from UTF-8 byte length: Russian **1.92x** and Kazakh **2.13x** versus English.

| Language | Part 1 prediction vs EN | Measured request tokens | Measured vs EN | Billed input / output tokens |
|---|---:|---:|---:|---:|
| English | 1.00x | 100 | 1.00x | 101 / 535 |
| Russian | 1.92x | 130 | 1.30x | 131 / 698 |
| Kazakh | 2.13x | 236 | 2.36x | 237 / 1,160 |

The prediction was directionally correct for Kazakh but overestimated Russian; the measured Kazakh request was **2.36x** the English request, while Russian was only **1.30x**.

## 2. Annual cost at 2,000 requests/day

Cost per request = `(input tokens x $1.50 + output tokens x $9.00) / 1,000,000`; output includes Gemini thinking tokens.

| Language | Cost/request | Annual cost (730,000 requests) |
|---|---:|---:|
| English | $0.00497 | **$3,625.55** |
| Russian | $0.00648 | **$4,729.31** |
| Kazakh | $0.01080 | **$7,880.72** |

## 3. Production choice for a Kazakh support queue

I would start production with **Gemini 3.5 Flash**: the measured Kazakh response was fluent, in the requested language, and correctly refused to invent missing contract data. Its measured cost is about **$7,881/year** at the chosen volume, or **2.17x** English, so I would add a human-escalation/quality check for cases where the answer goes beyond the supplied documents. Before a final commitment, I would A/B-test a cheaper model such as Flash-Lite on a Kazakh evaluation set; Flash-Lite was not measured in this lab.

## 4. Cost-saving lever not used in this lab

I would enable **context caching for the repeated system prompt**, which this lab did not use, so the same instructions are not charged as fresh input on every request.

*Pricing source: [Google Gemini Developer API pricing](https://ai.google.dev/gemini-api/docs/pricing), Gemini 3.5 Flash Standard tier, accessed 2026-09-19.*

---

## AI-use declaration

I used Codex to inspect the laboratory repository and instructions, adapt the measurement script from the original provider to the Google Gemini API, troubleshoot SDK/API errors, calculate the annual-cost table, and format this submission. I used the Gemini API with `gemini-3.5-flash` to measure token counts and generate the English, Russian, and Kazakh test responses. I ran the scripts myself, checked the dry run and the successful measurements, and used the returned token-usage metadata for the numbers above; the measurements were not taken from an internet estimate. I did not include or disclose my API key. The final workload assumption, model recommendation, interpretation of quality, and wording of the declaration are my responsibility.
