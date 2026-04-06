# Group Report: Lab 3 - Chatbot vs ReAct Agent (Repository-Aligned)

- **Team Name**: C401-C2
- **Team Members**: Nguyễn Thùy Linh, Bùi Minh Ngọc, Phạm Việt Anh, Phạm Việt Hoàng, Phan Tuấn Minh, Lê Đức Thanh
- **Report Date**: 2026-04-06

---

## 1. Executive Summary

This repository contains a travel-planning ReAct agent prototype with:
- ReAct loop implementation (`Thought -> Action -> Observation -> Final Answer`).
- Mock travel tools for deterministic development/testing.
- Provider abstraction for OpenAI-compatible APIs, Gemini, and local GGUF model execution.
- Structured telemetry (JSON log events + metric tracker utilities).

Based on the current repository state, this report only states claims that are verifiable from checked-in code and files.

---

## 2. System Architecture & Tooling

### 2.1 ReAct Loop Implementation
Implemented in `src/agent/agent.py`:
- Iterative loop with step budget (`max_steps`, default 6).
- Parsing blocks: `Thought`, `Action`, `Final Answer`.
- Tool call execution and observation feedback into the next prompt.
- Session-level metric reporting through `tracker.get_session_summary()`.

### 2.2 Tool Inventory (Current Mock Tools)
Defined in `src/tools/mock_tools.py`:

| Tool Name | Purpose |
| :--- | :--- |
| `suggest_destinations` | Suggest destinations by travel constraints |
| `check_weather` | Return weather conditions for a destination |
| `search_flights` | Provide candidate flight options |
| `search_hotels` | Provide candidate hotel options |
| `get_attractions` | Return notable attractions |
| `calculate_total_cost` | Estimate total trip cost and remaining budget |

### 2.3 LLM Provider Layer
Implemented in `src/core/`:
- `llm_provider.py`: abstract provider interface (`generate`, `stream`).
- `openai_provider.py`: OpenAI-compatible chat completion provider.
- `gemini_provider.py`: Google Gemini provider.
- `local_provider.py`: local GGUF provider via `llama-cpp-python`.

In test scripts (`tests/test_agent_run.py`, `tests/test_chat.py`), runtime defaults currently use OpenRouter credentials through `OpenAIProvider`.

---

## 3. Telemetry & Evaluation Status

Telemetry components are implemented:
- `src/telemetry/logger.py`: structured JSON logging to `logs/YYYY-MM-DD.log`.
- `src/telemetry/metrics.py`: token, cost, latency, efficiency, percentile/stat summary.
- `tests/analyze_metrics.py`: parser/summary script for generated logs.

Current artifact status in this repository:
- No `logs/` directory is checked in.
- No checked-in benchmark result files for chatbot-vs-agent comparison are present under `report/group_report/`.

Therefore, numeric benchmark claims (success rate, p95 latency, per-case token tables) are not included in this corrected report.

---

## 4. Observed Risks and Gaps (From Current Code)

1. The agent enforces `max_steps`, but there is no dedicated early-stop policy module beyond prompt instructions and `Final Answer` parsing.
2. Tool invocation currently uses mock responses; there is no real external API integration in `src/tools/`.
3. The group-level comparison artifact expected by scoring guidance is missing (no checked-in chatbot-vs-agent evaluation table generated from logs).

---

## 5. Improvement Plan (Aligned with Scoring Criteria)

1. Add reproducible benchmark artifacts:
  - Commit logs generated from fixed test scenarios.
  - Export chatbot-vs-agent comparison tables to markdown in `report/group_report/`.
2. Strengthen guardrails:
  - Add robust action parsing and explicit error categories (parse error, unknown tool, timeout).
  - Add retry/repair behavior when action format is invalid.
3. Increase production realism:
  - Replace or complement mock tools with real data connectors.
  - Add schema validation for tool arguments and output contracts.

---

## 6. Conclusion

The repository already demonstrates the core architecture expected in Lab 3: ReAct loop, provider abstraction, and telemetry scaffolding.  
To fully satisfy evaluation/reporting requirements, the team should add reproducible run artifacts and checked-in comparison evidence derived from actual logs.

---

> [!NOTE]
> This file was corrected to match the currently available repository artifacts and implementation details.
