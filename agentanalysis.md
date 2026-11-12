# Agent Analysis

Agent Analysis inspects the architecture, language model call sites, tool integrations, execution graphs, and migration risks of your AI workloads. Explore the eight analysis tabs to evaluate portability, diagnose execution loops, and verify configuration hygiene.

**Analysis Scope:** Agent Analysis combines static Abstract Syntax Tree (AST) scanning with orchestration flow analysis across repository source files. Results reflect detected signatures, parameters, and structural dependencies in the scanned codebase.

## Overview

The Overview tab delivers an executive summary of the analyzed agentic AI workload. It aggregates detection signals across scanned repositories to quantify workload composition, scanner confidence, migration portability, and critical risk hotspots. Use this view to evaluate architectural complexity before planning migrations, focusing first on modules with elevated termination risk or low portability scores to establish the scope of required engineering effort.

![Agent Analysis Overview tab displaying workload summary counts, migration portability indicators, and risk distribution](/assets/docs/agentanalysis/agent-analysis-overview.png)

### Overview Indicators & Workload Characteristics

- **Workload & Scan Metadata:** Identifies the scanned repository, execution mode (Static AST + Orchestration Analysis), scan timestamp, and overall scanner Confidence Level. Note: A high confidence score reflects strong pattern matching across known SDK signatures and does not guarantee the accuracy of every individual finding.

- **Summary Counts:** High-level totals quantifying detected Models, autonomous Agents, integrated Tools, and active Findings across the analyzed codebase.

- **Migration Portability Indicators:** Contrasts Portable Capabilities (such as standard chat completions and tool-calling interfaces) against Capabilities Needing Work (proprietary provider hooks, custom memory backends, or unmanaged loop boundaries).

- **Finding Hotspots & Distribution:** Visualizes concentration of findings across repository modules and severity tiers to highlight architectural components requiring code updates.

- **Termination-Risk Distribution:** Categorizes agent loops and workflow transitions into High, Medium, Low, or None risk levels based on exit condition guarantees and timeout enforcement.

- **Model-Call Distribution:** Breaks down invocation frequency and volume across primary production models and auxiliary fallback or embedding engines.

- **Detected Frameworks & SDKs** AST scanning identifies signatures for primary client libraries and agent orchestration frameworks (such as OpenAI SDK, LangGraph, or LlamaIndex) in repository imports and call sites.

- **Supported Interaction Modes** Catalogs active model interaction modalities, including text completions, function and tool calling, streaming generation, and multimodal processing.

- **Orchestration Characteristics** Characterizes workflow execution topology, distinguishing sequential agent chains, autonomous tool loops, and directed multi-agent graph flows.

- **Observed Evidence vs. Inferred Classifications** Maintains a clear distinction between directly observed syntactic AST evidence (verified imports, explicit configuration parameters) and inferred architectural classifications derived from flow analysis.

## Models & Calls

The Models & Calls section helps you understand how language models are used across your application. It brings together information about detected models and their invocation patterns, making it easier to understand model usage across different parts of your workload.

![Models and Calls tab showing detected model cards and the comprehensive LLM Call Sites table](/assets/docs/agentanalysis/agent-analysis-models-calls.png)

### Model Cards & Call Site Parameters

- **Detected Model Cards:** Summary cards for each detected model displaying provider name, model identifier, total call site occurrences, and capability tags such as Text, Vision, Audio, Tool Use, and Embedding. Use this summary to evaluate model family capabilities and plan tier upgrades across environments.

- **Expandable Model Details:** Collapsible inspectable panels revealing context-window boundaries, supported input/output formats, and detected default parameters. Parameter support varies across provider APIs and should be verified individually when planning upgrades.

- **LLM Call Sites Table:** Exhaustive audit listing of every line of code where an LLM is invoked, detailing file path, line number, client SDK, model name, temperature, and generation parameters. Use this table to locate hardcoded model references that require dynamic environment abstraction and audit temperature consistency across call sites.

- **Streaming & Thinking Flags:** Status indicators highlighting whether token streaming (stream=True) or extended reasoning/thinking modes are configured at each call site.

- **Search, Filtering & Pagination:** Interactive search controls allowing engineers to filter call sites by file path, model family, or SDK, alongside column sorting and Load More pagination.

Developers can use this tab to identify hardcoded model references that need dynamic environment abstraction, verify temperature consistency, and plan model tier upgrades. Parameter support varies by provider and should be verified individually for each model family.

## Agents & Tools

The Agents & Tools section provides a high-level view of the agent-based architecture identified in your application. It helps you understand how agents, language models, memory, and tools contribute to the overall workflow.

![Agents and Tools tab presenting agent architecture cards, memory backends, and tool inventory](/assets/docs/agentanalysis/agent-analysis-agents-tools.png)

### Agent Architecture & Tool Inventory Elements

- **Orchestration Pattern & Framework:** Identifies the high-level pattern (e.g., Plan-and-Execute, StateGraph, Supervisor-Worker), framework version, memory storage backend (e.g., Redis, in-memory, Postgres), and Human-in-the-Loop checkpoints.

- **Individual Agent Cards:** Structural cards for each agent displaying agent name, defining source file, associated primary and fallback models, memory retention strategy, and assigned tools. Tracing these dependencies helps enforce the principle of least privilege across agent roles.

- **Agent Model Distribution:** Visual breakdown showing model allocation across specialized agent roles, contrasting heavy reasoning agents with lightweight router agents.

- **Tool Registry & Search Controls:** Comprehensive tool catalog equipped with search and category filters (e.g., Database, Web Search, Code Execution, File System).

- **Tool Details & Schema Definitions:** Expandable items detailing tool names, function signatures, docstring descriptions, schema parameters, and source file locations. Note that a configured tool definition confirms registration in the codebase, but successful execution depends on valid runtime credentials and network connectivity.

Trace the dependencies between agents and tools to enforce the principle of least privilege. The presence of a configured tool definition in the codebase confirms its registration, but does not prove successful runtime execution or API key authorization.

## Prompts

The Prompts section helps you understand how prompts and instruction templates are used throughout your application. It provides a high-level view of the prompt resources identified in the codebase and their role in guiding model and agent behavior.

![Prompts inventory tab showing template names, source locations, prompt types, and sizes](/assets/docs/agentanalysis/agent-analysis-prompts.png)

### Prompt Inventory Columns & Attributes

- **Prompt Identifier & Name:** Descriptive name or constant identifier (e.g., SYSTEM_TRIAGE_PROMPT, CODE_GENERATION_INSTRUCTION) assigned to the prompt template. Reviewing identifiers across agents helps eliminate redundant prompt declarations.

- **Source File Location:** Exact repository file path and line number where the prompt string or template is defined.

- **Prompt Size:** Character count and text length of the raw prompt template in source code. Monitoring template size helps pinpoint sprawling system instructions that would benefit from modularization or prompt-caching optimization.

- **Prompt Type:** Categorization badges distinguishing Agent System Instructions, User Templates, Few-Shot Exemplars, and Tool Descriptions.

- **Template Variables:** Detected placeholders and variable substitutions injected dynamically at runtime.

Use the prompt inventory to locate sprawling system instructions that would benefit from modularization, eliminate redundant prompt definitions across agents, and assess readiness for prompt caching on target platforms.

## Graphs

The Graphs tab visualizes agent execution flows, state-machine transitions, and cyclic routing logic. It renders the full workflow graph so developers can inspect node connections, conditional branches, and termination boundaries.

![Graphs tab visualizing agent workflow execution paths, nodes, conditional edges, and state transitions](/assets/docs/agentanalysis/agent-analysis-graphs.png)

### Graph Visualization & Canvas Controls

- **Workflow Selector:** Interactive dropdown allowing users to select and visualize different multi-agent workflows, state machines, or sub-graphs in the application.

- **Workflow Metadata:** Displays the active workflow name, defining source file, orchestration framework (e.g., LangGraph StateGraph), total node count, and edge count.

- **Entry & Terminal Nodes:** Clearly marked workflow entry points (e.g., __start__) and deterministic termination states (e.g., __end__). Graph topologies should be inspected to confirm cyclic loops have bounded recursion limits and router branches terminate predictably.

- **Processing, Tool & Router Nodes:** Visual distinction between standard LLM execution steps, tool-calling actions, human-in-the-loop gates, and conditional branch routers. Review human-in-the-loop gates to ensure safety-critical operations intercept tool actions before execution.

- **Canvas Controls:** Interactive zoom, pan, fit-to-view, and fullscreen controls allowing inspection of large graph structures in a proportional frame.

Inspect graph topologies to verify that cycles have bounded iteration limits, conditional router branches terminate predictably, and human review steps intercept safety-critical operations before tool execution.

## Findings

The Findings section helps you understand the important observations identified during Agent Analysis across your workload. It brings together detected migration risks, configuration weaknesses, and structural anti-patterns, giving you clear visibility into areas that may need attention.

![Findings tab displaying detected issues grouped by severity with category breakdown and finding rows](/assets/docs/agentanalysis/agent-analysis-findings.png)

### Findings Groupings & Audit Elements

- **Severity Groupings:** Findings prioritized into High, Medium, and Low risk tiers based on their potential to cause runtime failures, migration blockers, or security risks. Remediation should be prioritized using both severity tier and occurrence frequency, evaluating findings alongside workload business context.

- **Top Kinds Chart:** Distribution breakdown highlighting the most frequent categories of findings across the scanned repositories (e.g., Missing Loop Timeouts, Hardcoded API Keys, Non-portable Tool Schemas).

- **Search & Severity Filters:** Filtering controls allowing engineers to narrow down findings by severity tier, finding category, or specific file path.

- **Individual Finding Rows:** Detailed rows showing finding title, assigned severity badge, total occurrence count across files, and direct codebase references.

- **Expandable Remediation Details:** Expandable row sections containing detailed code snippets, scanner rationale, and suggested remediation code diffs.

Prioritize remediation based on both severity classification and occurrence frequency. Scanner severity indicates technical risk and should be considered alongside workload business context rather than serving as the sole factor in prioritization.

## Migration Risk

The Migration Risk tab evaluates workload readiness for migration between cloud environments, model hosts, or inference gateways. It audits capability compatibility, timeout safety, and gateway alignment to flag potential migration blockers.

![Migration Risk assessment showing termination risks, prompt caching compatibility, and provider capability alignment](/assets/docs/agentanalysis/agent-analysis-migration-risk.png)

### Migration Risk Dimensions & Capabilities

- **Termination & Timeout Risks:** Audits agent loops and recursive transitions for explicit termination guarantees and execution timeout parameters, grouping risks into High, Medium, Low, and None tiers. High termination risks and unmanaged timeouts should be mitigated prior to cloud cutovers to prevent runaway execution costs.

- **Prompt Caching Compatibility:** Evaluates prefix stability and dynamic variable placement in system prompts to determine compatibility with provider prompt-caching mechanisms.

- **Context-Window Boundaries:** Identifies call sites prone to context exhaustion during long-running agent loops or heavy chat histories.

- **Structured Output & Tool Choice:** Examines schema compliance and checks whether target gateways support required tool_choice constraints and strict JSON schema enforcement.

- **Streaming & Observability Alignment:** Assesses streaming response handling and confirms OpenTelemetry/tracing span propagation across distributed agent calls.

- **Source References & Status Badges:** Displays relevant file locations alongside scanner status badges. Status badges reflect automated static classifications and indicate areas requiring verification before cutover.

Review migration risks before initiating cloud cutovers. Mitigate high termination risks and unmanaged timeouts first to prevent runaway execution costs or orphaned processes in new deployment environments.

## Infra & Env

The Infra & Env section helps you understand the infrastructure and execution environment dependencies identified in your application. It provides an overview of external service connections, environment configuration requirements, and background processing components that support your agent workload.

![Infrastructure and Environment tab showing vector stores, background workers, and configuration status labels](/assets/docs/agentanalysis/agent-analysis-infra-env.png)

### Infrastructure Dependencies & Configuration Labels

- **Vector Stores & Collections:** Inventories detected vector databases (e.g., Pinecone, Chroma, Qdrant, Azure AI Search), collection names, vector dimensions, file counts, and associated embedding models. Ensure all referenced vector stores and collections are provisioned in target deployment environments prior to migration.

- **Environment Variables:** Lists detected environment variable names required by the codebase. Note: Variable names define required configuration keys and do not expose or store secret values.

- **Background Workers & Async Jobs:** Documents asynchronous queues, Celery tasks, Temporal workflows, or background cron workers driving decoupled agent execution.

- **Resilience Patterns:** Details configured retry algorithms, exponential backoff policies, circuit breakers, and fallback model chains across provider integrations.

- **Model Configuration Hygiene:** Labels model configuration status across files. Eliminating hardcoded configuration instances in favor of environment-backed parameters facilitates seamless multi-environment staging.

Ensure all referenced vector stores, background workers, and environment variables are provisioned in target deployment environments. Eliminate hardcoded configuration instances to facilitate multi-environment staging.
