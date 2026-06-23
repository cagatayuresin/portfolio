---
title: "ITBench Explained: AI Agent Benchmark for IT Operations"
description: "A practical look at IBM Research's ITBench benchmark for evaluating AI agents across SRE, CISO, and FinOps automation tasks."
pubDate: 2026-06-23
updatedDate: 2026-06-23
tags: ["ai-agents", "aiops", "sre", "kubernetes", "benchmarking", "finops", "ibm-research"]
cover:
  src: "/blog/covers/itbench-ai-agent-benchmark-it-operations-cover.jpg"
  alt: "Abstract benchmark dashboard with three operational domains feeding an AI agent evaluation loop"
---

[ITBench](https://github.com/itbench-hub/ITBench) is IBM Research's open-source benchmark for evaluating AI agents on real IT operations work. Instead of asking a model to answer static questions, ITBench places an agent in operational scenarios across **SRE**, **CISO**, and **FinOps** domains, then measures whether it can diagnose and resolve the task with interpretable metrics.

That matters because the promise of AI agents in IT is much larger than generating a nice incident summary. The real question is whether an agent can inspect telemetry, reason about partial evidence, identify the root cause, and take or recommend the right action without making the system worse. ITBench is interesting because it tries to measure exactly that.

The benchmark is also a useful repository to study if you care about agentic infrastructure tooling. The [itbench-hub/ITBench repository](https://github.com/itbench-hub/ITBench) includes scenario definitions, deployment tooling, documentation, leaderboards, and links to reference agents. It is not just a paper artifact; it is a practical benchmark framework built around reproducible IT automation tasks.

## Why ITBench Exists

AI agents are often demonstrated in clean, narrow workflows. IT operations are not clean. A production incident usually starts with an indirect symptom: a high error rate alert, a failing pod, a compliance drift signal, or a cost anomaly. The operator has to decide which signals matter, which signals are downstream noise, and what action is safe.

That makes IT operations hard to benchmark. A multiple-choice dataset can test domain vocabulary, but it cannot show whether an agent can work through a live system state. A static log dataset can test retrieval, but it does not capture the feedback loop between investigation and action.

ITBench was built to close that evaluation gap. Its research paper, [ITBench: Evaluating AI Agents across Diverse Real-World IT Automation Tasks](https://arxiv.org/abs/2502.05352), frames the problem directly: if AI agents are going to automate critical IT tasks, we need a systematic way to measure whether they are correct, safe, and fast before they are trusted in production.

## What ITBench Measures

ITBench targets three operational personas:

| Domain | Operational focus | Example task shape |
| --- | --- | --- |
| **SRE** | Availability and resilience | Diagnose a Kubernetes service with a high error rate |
| **CISO** | Compliance and security operations | Assess posture after a new control rule is detected |
| **FinOps** | Cost efficiency and ROI | Investigate budget overruns or spend anomalies |

The repository describes ITBench as a framework for recreating realistic IT scenarios in Kubernetes environments. The public repository currently includes open-source setup tooling, a subset of scenarios, two reference agents, and a managed leaderboard path for evaluation.

That split is important. The paper describes an initial benchmark set of **94 real-world scenarios**, while the public repository exposes a subset that developers can run and extend. This helps the project remain usable as an open benchmark while reducing the risk that every evaluation becomes overfit to public test data.

## How the Benchmark Works

ITBench does not simply ask an agent, "What would you do?" It creates an environment where a problem occurs, exposes the agent to operational signals, and evaluates the final outcome against ground truth.

For SRE scenarios, the environment is Kubernetes-based. The benchmark can recreate incidents around services, deployments, telemetry, and fault propagation. The agent starts from symptoms and must work back to the actual contributing factor.

That distinction is the core difficulty. In Kubernetes, the thing that alerts is often not the thing that broke. A downstream service may show the error rate, while the real cause is an invalid image, a broken ConfigMap, a bad environment variable, a network policy, or a resource constraint somewhere else.

The ITBench architecture has four practical pieces:

- **Scenario specification and environment** define the task, metadata, deployment shape, injected problem, and ground truth.
- **Agent under test** investigates the environment and produces an answer or remediation.
- **Evaluator** compares the result with expected outcomes and awards interpretable scores.
- **Leaderboard** tracks submitted results across domains and scenarios.

Under the hood, the repository uses open-source infrastructure and automation around Kubernetes environments. The reference agents are linked from the main repository and are designed to interact with operational signals through natural-language-oriented tools and agent workflows.

## The Baseline Results Are the Point

The most useful benchmark results are often the uncomfortable ones. ITBench reports that state-of-the-art baseline agents resolve only a small fraction of scenarios in the original paper:

![Bar chart showing ITBench baseline results: SRE 13.8 percent, CISO 25.2 percent, and FinOps 0 percent.](/blog/itbench-baseline-results-chart.jpg)

| Domain | Baseline result reported in the paper |
| --- | ---: |
| SRE | 13.8% resolved |
| CISO | 25.2% resolved |
| FinOps | 0% resolved |

These numbers are not a reason to dismiss AI agents. They are a reason to take evaluation seriously. A coding benchmark can show that a model is good at producing patches. ITBench asks a different question: can an agent operate under partial information, inspect noisy system state, and find the operational root cause?

For now, the answer is mostly no. And that is exactly why the benchmark is useful.

## ITBench-AA and the 2026 Frontier-Model Signal

The benchmark has continued to matter beyond the initial paper. In May 2026, Artificial Analysis and IBM Research launched [ITBench-AA](https://huggingface.co/blog/ibm-research/itbench-aa), an implementation focused first on Kubernetes incident root-cause analysis.

ITBench-AA evaluates **59 SRE tasks**: 40 public tasks and 19 held-out tasks shared by the ITBench team. Each task provides an incident snapshot containing alerts, events, traces, metrics, logs, and application topology. The model must identify the minimal set of independent Kubernetes root-cause entities responsible for the incident.

The headline result is still sobering: frontier models remain below 50% on this evaluation. That is a useful reminder that agentic IT operations is not solved just because a model can use a shell, read logs, or explain Kubernetes concepts.

## Why This Is Different from Traditional AIOps

Traditional AIOps tools often focus on detection, correlation, or alert reduction. Those are valuable, but ITBench pushes closer to the operational endpoint: did the agent understand the incident well enough to localize the fault and resolve or report it correctly?

That makes the benchmark closer in spirit to [SWE-bench](https://www.swebench.com/) than to a dashboard analytics benchmark. SWE-bench helped make software engineering agents measurable against real GitHub issues. ITBench tries to create a similar pressure point for IT operations agents: realistic tasks, reproducible environments, and scoring that exposes where agents actually fail.

For platform teams, this is the interesting part. If an agent cannot reliably distinguish a symptomatic deployment from the real root cause in a controlled benchmark, it should not be given broad production permissions. Benchmarks like ITBench help define where the boundary should be.

## What to Look at in the Repository

The [ITBench GitHub repository](https://github.com/itbench-hub/ITBench) is worth browsing even if you do not plan to run it immediately.

Start with:

- `scenarios/` for the shape of benchmark tasks
- `documentation/` for setup and usage details
- `clusters/` for Kubernetes environment pieces
- the SRE and CISO leaderboard files for submitted results
- the linked reference agents for implementation patterns

The repository also shows the project's direction. Its README lists managed scenario environments, community contribution paths, related enterprise-agent benchmarks, and recent updates such as ITBench-AA. That makes it useful both as a research benchmark and as a map of where enterprise IT agent evaluation is heading.

## How I Would Use ITBench

For a platform or DevOps team, I would not treat ITBench as a pass/fail gate for production automation on day one. I would use it as a learning and evaluation harness.

A practical path could look like this:

1. Run a small public SRE scenario locally.
2. Capture the agent trajectory: tool calls, failed assumptions, and final diagnosis.
3. Compare the result with ground truth.
4. Improve only one layer at a time: retrieval, telemetry tools, Kubernetes action policy, or final answer validation.
5. Repeat with held-out scenarios before increasing trust.

The important lesson is that the model is only one part of the system. Tool design, context selection, action safety, environment snapshots, and scoring all matter. ITBench makes those layers visible.

## Practical Takeaways

ITBench is valuable because it sets a higher bar for AI agents in operations:

- It evaluates tasks that resemble real SRE, security, and cost-management work.
- It uses Kubernetes-based operational environments instead of only static Q&A.
- It measures outcomes with interpretable metrics rather than vague impressions.
- It shows that current agents still fail often, even with strong models.
- It gives researchers and engineers a shared target for improving agentic IT automation.

The strongest message is not "agents are ready to run everything." It is the opposite: real IT work is difficult, and we finally have a benchmark that makes that difficulty measurable.

## Conclusion

ITBench is one of the more important open benchmarks for anyone working near AI agents, Kubernetes, AIOps, or platform engineering. It turns the conversation from "can the model talk about incidents?" into "can the agent identify and resolve them under realistic constraints?"

That is the right question. If AI agents are going to become part of production operations, they need benchmarks that reflect production reality: partial information, noisy telemetry, hidden root causes, safety constraints, and measurable outcomes. ITBench is a serious step in that direction.

For the full project, start with the [itbench-hub/ITBench repository](https://github.com/itbench-hub/ITBench). For the research framing and baseline numbers, read the [ITBench paper on arXiv](https://arxiv.org/abs/2502.05352). For the newer frontier-model evaluation, see [ITBench-AA by Artificial Analysis and IBM Research](https://huggingface.co/blog/ibm-research/itbench-aa).
