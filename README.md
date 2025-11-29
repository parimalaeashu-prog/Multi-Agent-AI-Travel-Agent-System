# Multi-Agent AI Travel Agent System — Design Document

## 1) Problem Statement
Travel planning requires combining many data sources (flights, hotels, visas, weather), respecting user constraints (budget, dates, preferences), and maintaining conversational context across multi-turn interactions. Monolithic assistants struggle with modularity, observability, and safe execution (e.g., accidental auto-booking). This project separates concerns into specialized agents to produce explainable, testable, and extensible travel planning.

## 2) Solution Overview
A modular multi-agent architecture (Planner, Worker, Evaluator) coordinated by an orchestrator offers:
- Planner: decomposes user goals into tasks.
- Worker: executes tasks via tools and APIs.
- Evaluator: scores and validates results against constraints.
Session memory provides continuity; observability ensures auditing and debugging. Agents communicate through a typed A2A protocol and use standardized JSON schemas.

## 3) Multi-Agent Architecture (Planner, Worker, Evaluator)
- **Planner**: High-level planning, task decomposition, fallback strategies.
- **Worker**: Executes tasks (search, query, booking stubs) using tools, returns structured results with provenance.
- **Evaluator**: Checks constraints, ranks options, triggers replanning when needed.
- **Orchestrator**: Coordinates agents, manages session lifecycle, handles retries and human escalation.

## 4) Sessions & Memory
- SessionMemory stores session_id, user_profile, conversation_history, planner_state, tasks, constraints, and bookings.
- Supports short-term (conversation) and long-term (preferences) memory, with snapshot/rollback APIs.
- Namespaced storage per agent to avoid collisions.

## 5) Context Engineering
- Layered contexts: system, session, task, agent, tool.
- Agents receive filtered, token-efficient contexts (e.g., last N messages + relevant profile fields).
- Use canonical JSON schemas for tasks/results and include provenance in every output.

## 6) Observability (logging)
- Centralized structured logs (JSON) and traces (trace_id).
- Metrics: latencies, success/failure rates, replans, acceptance rate.
- Audit trails: planner reasoning, evaluator scores, worker tool outputs (PII masked).
- Alerting for repeated failures or safety flags.

## 7) Tools
- Standardized tool registry (flight_search, hotel_search, visa_checker, currency_convert, summarizer).
- Tools return normalized data and provenance metadata.
- Caching and rate-limit handling within tools.

## 8) A2A Protocol
- Typed message envelope: message_id, session_id, trace_id, from, to, timestamp, type (TASK/RESULT/FEEDBACK), task, result, meta.
- TASK includes expected_schema, constraints, priority.
- RESULT includes status (success/partial/failed), payload, errors, provenance.
- Error handling: bounded retries, exponential backoff, partial results with remediation advice.

## 9) Full File Structure (Design)
project/
  agents/
    planner.py            # Planner agent (task decomposition)
    worker.py             # Worker agent (executes tasks via tools)
    evaluator.py          # Evaluator agent (scores & validates)
  tools/
    tools.py              # Tool registry & adapters
  memory/
    session_memory.py     # SessionMemory abstraction
  core/
    context_engineering.py # Context shaping helpers
    observability.py      # Logging and metrics helpers
    a2a_protocol.py       # A2A message types & constructors
  main_agent.py           # Orchestrator & high-level flow
  app.py                  # Minimal HTTP API / CLI
  requirements.txt        # Dependencies

## Notes
- Code files intentionally left as placeholders per user request ("Do NOT generate code yet").
- This archive provides the full design as a README and an empty skeleton project structure so you can implement code when ready.

