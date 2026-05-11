# 📓 Study Notes: AI Token Economics & FinOps

This document serves as a conceptual ledger for the **Agentic Code Supply Chain** research project. It defines the economic metrics used to calculate the efficiency of synthetic labor.

---

## 1. Shadow Cost (of AI Labor)
**Definition:** The "latent" or "market-equivalent" price of AI-generated output if it were purchased at standard industrial rates rather than marginal API costs. It represents the value a professional human or high-tier enterprise model would charge for the same logic.

* **Logic:** If the factory produces a complex audit for $0.05, but that same audit has a market value of $0.60, the "Shadow Value" is the higher figure.
* **Formula:** $$V_{shadow} = \sum (Tokens_{type} \times Rate_{industrial})$$
* **Learn More:** * [Investopedia: Shadow Price](https://www.investopedia.com/terms/s/shadowprice.asp)
    * [FinOps Foundation: Unit Economics](https://www.finops.org/introduction/what-is-finops/)

## 2. Shadow ROI (Efficiency Multiplier)
**Definition:** The ratio between the **Industrial Value** (Shadow Cost) and the **Real-World API Cost**. It measures the "leverage" or profit margin of the agentic workflow.

* **Context:** In the TST-001 benchmarks, we achieved an ROI of **8.56x**, meaning every $1.00 spent on API calls generated $8.56 of industrial-grade labor.
* **Formula:** $$ROI_{shadow} = \frac{Value_{industrial}}{Cost_{real}}$$
* **Learn More:** * [CloudZero: Cloud ROI Guide](https://www.cloudzero.com/blog/cloud-roi)
    * [A16Z: The Economics of Generative AI](https://a16z.com/generative-ai-platform-economics/)

## 3. Industrial Input Values
**Definition:** The fixed benchmark rates used to normalize the value of AI output across different models (Pro vs. Flash). These rates are derived from high-tier enterprise pricing tiers.

* **Established Benchmarks:**
    * **Input Tokens:** **$5.00 / 1M tokens**
    * **Reasoning/Output Tokens:** **$37.00 / 1M tokens**
* **Purpose:** These values prevent "efficiency bias" by providing a stable baseline for comparison regardless of which specific API tier is used for execution.
* **Learn More:**
    * [Google AI Studio Pricing](https://ai.google.dev/pricing)
    * [LLM Pricing Comparison (Updated)](https://anderegg.ca/2024/05/13/llm-pricing-comparison)

## 4. Context Tax
**Definition:** The exponential or linear increase in input costs as a conversation history or technical documentation grows.

* **The "Price Cliff":** As seen in the $0.89 vs. $0.07 comparison, "Context Tax" occurs when the model must re-read entire histories for every new turn.
* **Optimization:** The "Strict Ingestion Protocol" and "Isolated Tests" are used to minimize this tax.
* **Learn More:**
    * [The Problem with Long Context](https://www.restack.io/docs/gemini-ai-knowledge-context-window-optimization)
    * [DeepLearning.AI: Prompt Engineering for Developers](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/)

## 5. Token Settlement & Regional Taxes
**Definition:** The reconciliation of "Reasoning Tokens" and regional digital taxes (e.g., Chile's 19% IVA) that may not appear in real-time billing logs.

* **IVA (Chile):** A **19% tax** applied to digital services. For every $1.00 of API usage, the actual balance deduction is **$1.19**.
* **Settlement Window:** The 10–30 minute latency required for Google’s billing systems to finalize reasoning costs.
* **Learn More:**
    * [Google Cloud: Tax Information for Chile](https://support.google.com/cloud/answer/2373599?hl=en#chile)
    * [FinOps: Cloud Billing Latency](https://www.finops.org/projects/cloud-billing-data-latency/)

---
**Note:** These notes reflect the state of the factory as of **May 8, 2026**.