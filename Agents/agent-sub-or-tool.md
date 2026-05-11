- [Agent, Sub-Agent, Skill, or Tool? A Practitioner’s Guide to Extending Agentic AI Systems](https://www.techrxiv.org/doi/pdf/10.36227/techrxiv.177204917.78786098/v1?onload=true)

### 🏛️ Agentic AI Research Bibliography: Piskala (2026)

| # | Reference | Resource Type | FinOps / PhD Observation |
| :--- | :--- | :--- | :--- |
| **[1]** | **LangChain Documentation**, "Agents and Tools" (2024) | [Documentation](https://docs.langchain.com) | Foundation for the "Tool-Use" vs. "Agent-Logic" distinction. |
| **[2]** | **S. Yao et al.**, "ReAct: Synergizing Reasoning and Acting in Language Models" (2023) | [arXiv:2210.03629](https://arxiv.org/pdf/2210.03629) | **Critical:** The origin of the "Reasoning" tokens you are tracking in your telemetry. |
| **[3]** | **Y. Shen et al.**, "HuggingGPT: Solving AI Tasks with ChatGPT and its Friends" (2023) | [arXiv:2303.17580](https://arxiv.org/pdf/2303.17580) | Demonstrates how a master agent handles sub-tasks (similar to your "Factory"). |
| **[4]** | **DB Piskala**, "Where Should Intelligence Live? Agent-Centric vs. Environment-Centric Design" (2025) | [TechRxiv](https://doi.org/10.36227/techrxiv.176823084.40754167) | Justifies why your "Intelligence" is in the `ai-specs/` folder, not just the model. |
| **[5]** | **xLang AI**, "OpenAgents: An Open Platform for Language Agents in the Wild" (2024) | [arXiv:2310.10634](https://arxiv.org/pdf/2310.10634) | Benchmark for real-world agent behavior in the "wild" (like your GitHub repo). |
| **[6]** | **Significant Gravitas**, "Auto-GPT" (2023) | [GitHub](https://github.com/Significant-Gravitas/AutoGPT) | The "ancestor" of autonomous loops; shows how far SDD has improved since then. |
| **[7]** | **G. Wang et al.**, "Voyager: An Open-Ended Embodied Agent with LLMs" (2023) | [arXiv:2305.16291](https://arxiv.org/pdf/2305.16291) | Focuses on "Lifetime Learning"—your **Skills folder** acts as your agent's memory. |
| **[8]** | **S. Hong et al.**, "MetaGPT: Meta Programming for Multi-Agent Collaborative Framework" (2023) | [arXiv:2308.00352](https://arxiv.org/pdf/2308.00352) | Applies SOPs to agents—exactly what your `PRD.md` and specification workflow does. |
| **[9]** | **Anthropic**, "Building Effective Agents" (2024) | Technical Report | The "Bible" for architecture; supports moving to simplified prompts for efficiency. |
| **[10]** | **DB Piskala**, "From Everything-is-a-File to Files-Are-All-You-Need" (2026) | [arXiv:2601.11672](https://arxiv.org/pdf/2601.11672) | **PhD Core:** Connects your Unix-style folder structure to AI performance. |
| **[11]** | **S. Runkle**, "Choosing the Right Multi-Agent Architecture" (2024) | LangChain Blog | Practical guide to deciding when to use a single agent vs. a "Factory" of agents. |
| **[12]** | **Microsoft**, "Developing next-generation cancer care with multi-agent orchestration" (2025) | Industry Blog | Proves high-stakes industry use cases for the same tech you are using. |
| **[13]** | **OpenAI**, "Function Calling and Agents" (2024) | Documentation | Technical baseline for how Gemini interacts with your local shell and files. |
| **[14]** | **Gartner**, "Predicts 2025: AI Agents Will Transform Enterprise Operations" (2024) | Research | Market validation for your LinkedIn article: This is the "Gartner-approved" future. |
| **[15]** | **Z. Xi et al.**, "The Rise and Potential of LLM Based Agents: A Survey" (2023) | [arXiv:2309.07864](https://arxiv.org/pdf/2309.07864) | A comprehensive survey; use this to define terms in your article introduction. |
| **[16]** | **DB Piskala et al.**, "Dynamic LLM Routing and Selection based on User Preferences" (2025) | [arXiv:2502.16696](https://arxiv.org/pdf/2502.16696) | **FinOps Core:** Academic backing for switching between Flash and Pro for cost/perf. |
| **[17]** | **G. Mialon et al.**, "Augmented Language Models: a Survey" (2023) | [arXiv:2302.07842](https://arxiv.org/pdf/2302.07842) | Explains how tools (like your CLI) "augment" the model's limited training data. |
| **[18]** | **S. Russell & P. Norvig**, "Artificial Intelligence: A Modern Approach" (2020) | Textbook | Standard academic citation to prove your knowledge of classical AI theory. |
| **[19]** | **M. Wooldridge**, "An Introduction to MultiAgent Systems" (2009) | Textbook | Theory on agent communication—useful for your "Agentic Supply Chain" thesis. |
| **[20]** | **G. Li et al.**, "CAMEL: Communicative Agents for Mind Exploration" (2023) | NeurIPS 2023 | Role-playing autonomy; supports your use of `@architect.md` and `@developer.md`. |
| **[21]** | **Y. Kim et al.**, "Towards a Science of Scaling Agent Systems" (2025) | [arXiv:2512.08296](https://arxiv.org/pdf/2512.08296) | **FinOps Core:** Discusses the limits of context and scaling—perfect for your 2M analysis. |
| **[22]** | **Anthropic**, "Introducing Agent Skills" (2025) | [Claude Blog](https://claude.com/blog/skills) | The specific blog post that popularized the "Skills" folder strategy you are using. |
| **[23]** | **Anthropic**, "Agent Skills Specification v1.0" (2025) | [agentskills.io](https://agentskills.io/specification) | The "Standard" your repo follows. Use this to prove your work is "Standardized." |
| **[24]** | **S. Willison**, "Claude Skills are awesome, maybe a bigger deal than MCP" (2025) | [simonwillison.net](https://simonwillison.net) | Commentary on why this is better than MCP—use for a "hot take" in your article. |
| **[25]** | **M. Nunez**, "Anthropic launches enterprise Agent Skills" (2025) | VentureBeat | Evidence of enterprise adoption—shows your research is at the cutting edge. |
| **[26]** | **A. Laurent**, "Claude Skills vs. MCP: A Technical Comparison" (2026) | IntuitionLabs | Technical deep-dive for your dashboard's "Documentation" section. |

---

### 🏛️ Usage in your "Measurement Phase" Article
When you write your first series article, I recommend focusing on **References #10, #16, and #23**. 

* **The "Why":** Use **#10 (Unix Philosophy)** to explain that by organizing your code into a specific folder structure, you are making it "readable" for an AI. 
* **The "How":** Use **#23 (Skills Spec)** to explain that you aren't just making up folders; you are following an **Industry Standard** for Agentic AI.
* **The "ROI":** Use **#16 (Dynamic Routing)** to explain how you save money by choosing the right model (Flash) for the right task (Implementation), which is how you reached that **8.56x Shadow ROI**.