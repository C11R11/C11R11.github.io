# Spec driven development

This study starts with a youtube video from a man working at google using ssd

- [Cómo mis agentes escribieron 200k líneas de código en producción - T3chFest 2026](https://www.youtube.com/watch?v=SUG-cEMFKFM)
- [Presentation](https://docs.google.com/presentation/d/1nMuN1xpDj5DUCTqOcewRSQuux6tEbnGs0h2B6m-K0co/edit?slide=id.p14#slide=id.p14)

## Papers

- **The Guide** [Spec-Driven Development: From Code to Contract in the Age of AI Coding Assistants](https://arxiv.org/pdf/2602.00180)

```text
This is a comprehensive guide specifically designed for practitioners. It defines the core principles of SDD, explains the workflow patterns, and discusses modern tools (like GitHub Spec Kit). It focuses on the "Spec-First" vs. "Spec-Anchored" philosophy, making it the ideal foundational text.
```
> References and articles
> - [Spec-driven development with AI: Get started with a new open source toolkit](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
> - [Spec-driven development](https://www.thoughtworks.com/radar/techniques/spec-driven-development)
> - [Exploring Generative AI](https://martinfowler.com/articles/exploring-gen-ai.html)

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


