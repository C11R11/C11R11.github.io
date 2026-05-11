# Papers

- **Guide** [Spec-Driven Development: From Code to Contract in the Age of AI Coding Assistants](https://arxiv.org/pdf/2602.00180)

```text
This is a comprehensive guide specifically designed for practitioners. It defines the core principles of SDD, explains the workflow patterns, and discusses modern tools (like GitHub Spec Kit). It focuses on the "Spec-First" vs. "Spec-Anchored" philosophy, making it the ideal foundational text.
```
> References
> - [Spec-driven development with AI: Get started with a new open source toolkit](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
> - [Spec-driven development](https://www.thoughtworks.com/radar/techniques/spec-driven-development)
> - [Exploring Generative AI](https://martinfowler.com/articles/exploring-gen-ai.html)
> - [5-Minute DevOps: Spec-Driven Development Isn’t New](https://bdfinst.medium.com/5-minute-devops-spec-driven-development-isnt-new-3a5c552efc95)
> - [Spec Driven Development: When Architecture Becomes Executable](https://www.infoq.com/articles/spec-driven-development/)

- [The Kitchen Loop: User-Spec-Driven Development for a Self-Evolving Codebase](https://arxiv.org/pdf/2603.25697)

```text
This paper is about "self-evolving" software and using AI agents as "synthetic power users." It’s more about the future of autonomous engineering than the day-to-day SDD workflow you would use as a developer.
```

- [Constitutional Spec-Driven Development: Enforcing Security by Construction in AI-Assisted Code Generation](https://arxiv.org/pdf/2602.02584)

```text
It’s nearly twice as long and focuses specifically on embedding security "constitutions" into specs to prevent AI from generating vulnerable code. Read this only if your primary interest is secure coding and DevSecOps.
```

- [Intelligent AI Delegation](https://arxiv.org/pdf/2602.11865)

```text
The Concept: This paper proposes a formal framework for delegation. It argues that for complex systems, we shouldn't just "prompt" an AI; we should treat it as a "delegatee" with specific boundaries.

Why it's useful for you: It discusses how to evaluate the "matching capability" of an agent. It suggests a workflow where a human delegator reviews a specification and decides which sub-tasks are high-risk (human-only) vs. low-risk/repetitive (agent-assigned).
```

- [Tokenomics: Quantifying Where Tokens Are Used in Agentic Software Engineering] (https://arxiv.org/pdf/2601.14470)

```text
The rise of AI coding assistants has reignited interest in an old idea: what if specifications-not code-were the primary artifact of software development? Spec-driven development (SDD) inverts the traditional workflow by treating specifications as the source of truth and code as a generated or verified secondary artifact. This paper provides practitioners with a comprehensive guide to SDD, covering its principles, workflow patterns, and supporting tools. We present three levels of specification rigor-spec-first, spec-anchored, and spec-as-source-with clear guidance on when each applies. Through analysis of tools ranging from Behavior-Driven Development frameworks to modern AI-assisted toolkits like GitHub Spec Kit, we demonstrate how the spec-first philosophy maps to real implementations. We present case studies from API development, enterprise systems, and embedded software, illustrating how different domains apply SDD. We conclude with a decision framework helping practitioners determine when SDD provides value and when simpler approaches suffice.
```

- [Interpretable Context Methodology: Folder Structure as Agent Architecture](https://arxiv.org/pdf/2603.16021v1)  

```text
Current approaches to AI agent orchestration typically involve building multi-agent frameworks that manage context passing,
memory, error handling, and step coordination through code. These frameworks work well for complex, concurrent systems.
But for sequential workflows where a human reviews output at each step, they introduce engineering overhead that the
problem does not require. This paper presents Model Workspace Protocol (MWP), a method that replaces framework-level
orchestration with filesystem structure. Numbered folders represent stages. Plain markdown files carry the prompts and
context that tell a single AI agent what role to play at each step. Local scripts handle the mechanical work that does not
need AI at all. The result is a system where one agent, reading the right files at the right moment, does the work that would
otherwise require a multi-agent framework. This approach applies ideas from Unix pipeline design, modular decomposition,
multi-pass compilation, and literate programming to the specific problem of structuring context for AI agents. The protocol is
open source under the MIT license.1
CCS Concepts: • Human-centered computing → Interactive systems and tools; HCI design and evaluation methods; •
Computing methodologies → Artificial intelligence; • Software and its engineering → Software design engineering.
Additional Key Words and Phrases: context engineering, human-AI interaction, AI agent orchestration, filesystem architecture,
human-in-the-loop, mixed-initiative systems, workflow automation
```

-[The Illusion of Thinking: Understanding the Strengths and Limitations of Reasoning Models via the Lens of Problem Complexity](https://arxiv.org/pdf/2506.06941)

```text
Recent generations of frontier language models have introduced Large Reasoning Models
(LRMs) that generate detailed thinking processes before providing answers. While these models
demonstrate improved performance on reasoning benchmarks, their fundamental capabilities, scaling properties, and limitations remain insufficiently understood. Current evaluations primarily focus on established mathematical and coding benchmarks, emphasizing final answer accuracy. However, this evaluation paradigm often suffers from data contamination and does not provide insights
into the reasoning traces’ structure and quality. In this work, we systematically investigate these
gaps with the help of controllable puzzle environments that allow precise manipulation of compositional complexity while maintaining consistent logical structures. This setup enables the analysis
of not only final answers but also the internal reasoning traces, offering insights into how LRMs
“think”. Through extensive experimentation across diverse puzzles, we show that frontier LRMs
face a complete accuracy collapse beyond certain complexities. Moreover, they exhibit a counterintuitive scaling limit: their reasoning effort increases with problem complexity up to a point, then
declines despite having an adequate token budget. By comparing LRMs with their standard LLM
counterparts under equivalent inference compute, we identify three performance regimes: (1) lowcomplexity tasks where standard models surprisingly outperform LRMs, (2) medium-complexity
tasks where additional thinking in LRMs demonstrates advantage, and (3) high-complexity tasks
where both models experience complete collapse. We found that LRMs have limitations in exact
computation: they fail to use explicit algorithms and reason inconsistently across scales and problems. We also investigate the reasoning traces in more depth, studying the patterns of explored
solutions and analyzing the models’ computational behavior, shedding light on their strengths,
limitations, and ultimately raising questions about the nature for their reasoning capabilities.
```