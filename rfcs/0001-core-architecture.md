# RFC-0001: Core Architecture

**Status:** Draft

**Author:** Himanshu Pathak

**Created:** 2026-07-12

---

## Scope

This RFC defines the core architectural principles, system boundaries, and high-level component model of AEGIS.

Detailed designs for individual subsystems (such as the plugin system, adapter interfaces, evaluation engines, scoring model, and reporting formats) are intentionally deferred to separate RFCs.

---

## Summary

<Ye wahi final summary hogi jo hum review karke finalize karenge.>

---

## Motivation

Traditional software engineering relies on well-established quality gates before deployment, including linting, automated testing, security scanning, and continuous integration. These practices provide engineering teams with confidence that software behaves as expected under known conditions.

AI systems introduce fundamentally different failure modes that cannot be sufficiently validated through traditional software testing alone. Their behavior depends not only on code, but also on prompts, external tools, retrieval systems, memory, model behavior, and other dynamic components. As a result, evaluating AI systems requires new forms of testing that extend beyond conventional engineering practices.

Unlike traditional software, AI systems currently lack a reproducible engineering workflow that enables teams to answer a simple question before deployment:

> **Can we trust the behavior of this AI system under realistic and adversarial conditions?**

Existing evaluation approaches are often fragmented, manual, provider-specific, or difficult to integrate into engineering workflows. This makes AI evaluation inconsistent, difficult to reproduce, and challenging to automate within modern software delivery pipelines.

This gap motivates the development of AEGIS: a framework that aims to establish a reproducible engineering workflow for evaluating AI systems within modern software delivery processes.

---

## Goals

### Functional Goals

- Establish a repeatable engineering workflow for evaluating AI systems before deployment.
- Enable software engineering teams to evaluate AI systems without requiring specialized expertise in AI safety research.
- Provide a reproducible evaluation methodology supported by evidence, traceability, and replayable execution records.
- Integrate AI evaluation into existing software engineering and CI/CD workflows.
- Generate actionable, evidence-based findings that help engineering teams evaluate and improve the overall quality and operational behavior of AI systems.

### Architectural Goals

- Maintain a provider-agnostic architecture through stable adapter interfaces.
- Support extensibility through independently developed plugins and evaluation engines.
- Build a modular platform where discovery, planning, execution, evaluation, scoring, and reporting remain independently evolvable.

---

## Non-Goals

- AEGIS is not an AI agent framework or orchestration platform.
- AEGIS is not an LLM provider abstraction layer or model serving framework.
- AEGIS uses adversarial techniques strictly to evaluate and report on system behavior—it is not designed or intended to exploit, compromise, or gain unauthorized access to production systems.
- AEGIS is not an academic benchmarking or leaderboard framework for comparing foundation models.
- AEGIS does not replace human engineering judgment, security reviews, or organizational governance processes.
- AEGIS does not automatically modify, patch, or deploy production systems.
- AEGIS does not guarantee that an AI system is secure, reliable, correct, or free from vulnerabilities; instead, it provides reproducible evidence to support informed engineering decisions.
---

## Proposed Architecture

### Architectural Principles

The architecture of AEGIS is guided by a small set of principles that define how the system evolves over time. These principles are intended to constrain architectural decisions throughout the project rather than describe implementation details.

#### 1. Provider Independence

The core architecture MUST remain independent of any specific LLM provider, orchestration framework, or AI SDK.

All communication with target AI systems MUST occur through stable adapter interfaces. Provider-specific behavior is isolated within adapters and MUST NOT leak into the core architecture.

#### 2. Evidence Before Verdict

Every evaluation result MUST be supported by reproducible evidence.

Architectural components are expected to produce traceable execution records that enable findings to be inspected, replayed, and independently verified. Reports and scores are derived from evidence rather than opaque evaluation outcomes.

#### 3. Separation of Responsibilities

Each architectural component is responsible for a single stage of the evaluation workflow.

Discovery, planning, execution, evaluation, scoring, and reporting are independent responsibilities that communicate only through well-defined interfaces. Components SHOULD remain loosely coupled and independently evolvable.

#### 4. Extensibility by Default

The core architecture SHOULD remain intentionally small.

New evaluation capabilities, attack techniques, reporting formats, adapters, and integrations SHOULD be introduced through extensions instead of modifications to the core system whenever practical.

#### 5. Defensive by Design

AEGIS is designed exclusively for defensive evaluation.

Potentially destructive evaluation techniques MUST execute only within explicitly authorized environments. The architecture assumes safe evaluation, controlled execution, and responsible disclosure as fundamental design requirements.

#### 6. Reproducibility over Determinism

AI systems are inherently non-deterministic.

Rather than guaranteeing identical outputs across executions, AEGIS guarantees reproducible evaluation methodology, execution evidence, and traceability. Engineering decisions are based on reproducible process rather than deterministic model behavior.


### Core Components

The AEGIS architecture is composed of a set of independent components, each responsible for a single stage of the evaluation workflow. Every component exposes a well-defined contract, communicates through explicit interfaces, and owns a clearly defined responsibility.

---

#### Configuration Resolver

**Responsibility**

Resolve and normalize all runtime configuration required for a scan.

**Consumes**

- CLI arguments
- Configuration files
- Environment variables
- Default configuration

**Produces**

- Normalized scan configuration

**Must Not**

- Discover target capabilities.
- Execute evaluation logic.
- Interact with target AI systems.

---

#### Extension Registry

**Responsibility**

Discover, validate, and expose available architectural extensions to the core system.

**Consumes**

- Installed extensions
- Runtime configuration

**Produces**

- Registered extensions
- Extension metadata

**Must Not**

- Execute evaluations.
- Select attack plans.
- Modify evaluation results.

---

#### Discovery Engine

**Responsibility**

Discover the capabilities of the target AI system and construct a capability model for planning.

**Consumes**

- Normalized scan configuration
- Agent Adapter
- Extension Registry

**Produces**

- Capability graph

**Must Not**

- Execute attacks.
- Produce evaluation scores.
- Generate reports.

---

#### Attack Planner

**Responsibility**

Generate an evaluation plan based on the discovered capabilities of the target system.

**Consumes**

- Capability graph
- Extension Registry
- Scan configuration

**Produces**

- Attack plan

**Must Not**

- Execute attacks.
- Evaluate results.
- Modify execution evidence.

---

#### Orchestrator

**Responsibility**

Coordinate the execution of the evaluation plan and manage communication between architectural components.

**Consumes**

- Attack plan
- Agent Adapter

**Produces**

- Execution traces

**Must Not**

- Score findings.
- Generate reports.
- Make evaluation decisions.

---

#### Agent Adapter

**Responsibility**

Provide a stable abstraction for communication between AEGIS and target AI systems.

**Consumes**

- Requests from architectural components

**Produces**

- Normalized responses
- Tool interaction events
- Model execution events

**Must Not**

- Perform evaluation logic.
- Score findings.
- Contain provider-specific business logic outside the adapter boundary.

---

#### Evidence Store

**Responsibility**

Persist execution evidence required for traceability, replayability, and reporting.

**Consumes**

- Execution traces

**Produces**

- Evidence references
- Replayable execution records

**Must Not**

- Evaluate findings.
- Generate reports.
- Modify execution history.

---

#### Evaluation Engine

**Responsibility**

Analyze execution evidence and determine evaluation findings.

**Consumes**

- Execution evidence
- Capability context

**Produces**

- Evaluation findings

**Must Not**

- Modify execution evidence.
- Generate reports.
- Calculate final scores.

---

#### Scoring Engine

**Responsibility**

Aggregate evaluation findings into structured scoring outputs.

**Consumes**

- Evaluation findings

**Produces**

- Evaluation scores

**Must Not**

- Execute evaluations.
- Interpret execution evidence directly.
- Generate reports.

---

#### Reporter

**Responsibility**

Generate human-readable and machine-readable reports from evaluation outputs.

**Consumes**

- Evaluation findings
- Evaluation scores
- Evidence references

**Produces**

- Terminal output
- JSON reports
- HTML reports

**Must Not**

- Execute evaluations.
- Modify findings.
- Recalculate scores.
---

## Package Responsibilities

> TODO

...
