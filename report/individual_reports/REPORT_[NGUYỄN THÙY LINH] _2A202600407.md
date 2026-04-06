# Individual Report: Lab 3 - Chatbot vs ReAct Agent

- **Student Name**: [Nguyễn Thùy Linh]
- **Student ID**: [2A202600407]
- **Date**: [2026/04/06]

---

## I. Technical Contribution (15 Points)

*Describe your specific contribution to the codebase (e.g., implemented a specific tool, fixed the parser, etc.).*

- **Modules Implemented**: [src/agent/agent.py](../../src/agent/agent.py#L32)
- **Code Highlights**: I designed the `get_system_prompt()` method to define the agent identity, capabilities, constraints, and output format. The prompt explicitly forces the ReAct structure: `Thought -> Action -> Observation -> Final Answer`, and the agent passes this system prompt into both `llm.generate()` and `llm.stream()` so the behavior is consistent across streaming and non-streaming runs.
- **Documentation**: My contribution focused on the system prompt layer that controls the ReAct loop. The prompt makes the model reason step by step, use only one tool per step, wait for an observation before continuing, and stop once enough evidence is collected. This reduces free-form answers and helps the parser extract actions reliably.

---

## II. Debugging Case Study (10 Points)

*Analyze a specific failure event you encountered during the lab using the logging system.*

- **Problem Description**: In the comparison and planning cases, the agent sometimes kept generating extra `Thought` and `Action` steps instead of stopping with a final answer. In practice, this pushed the run close to the step budget and made the output less stable.
- **Log Source**: Latest evaluation run on 2026/04/06, recorded in the group benchmark logs and the session telemetry used for the final report.
- **Diagnosis**: The main issue was prompt pressure, not tool execution. The model understood the available tools, but the prompt did not always make the stopping condition explicit enough, so it continued collecting details even after enough evidence was already available.
- **Solution**: I refined the system prompt to be stricter about the ReAct format, especially the instructions to stop when enough information is collected and to produce a clear `Final Answer`. That made the agent more deterministic and reduced looping behavior in later runs.

---

## III. Personal Insights: Chatbot vs ReAct (10 Points)

*Reflect on the reasoning capability difference.*

1.  **Reasoning**: The `Thought` block made the model explain its next step before acting, so the process became inspectable instead of opaque. Compared with a direct Chatbot answer, the agent could break a travel-planning task into smaller decisions and ground those decisions in tool outputs.
2.  **Reliability**: The Agent performed worse on simple or open-ended questions where a direct answer was enough. In those cases, the ReAct loop added latency and token usage without much benefit, while the Chatbot was faster and simpler.
3.  **Observation**: The observation acted like feedback from the environment. After each tool call, the next `Thought` adjusted based on the returned information, which helped the agent refine destination choice, budget estimates, and comparisons instead of guessing.

---

## IV. Future Improvements (5 Points)

*How would you scale this for a production-level AI agent system?*

- **Scalability**: Use asynchronous tool execution and a queue-based orchestration layer so multiple requests can be handled without blocking the agent loop.
- **Safety**: Add a supervisor model or policy layer to validate tool calls, detect prompt injection, and block unknown actions before execution.
- **Performance**: Add retrieval-based tool selection and caching for repeated travel queries so the agent does not need to re-generate the same reasoning and observations every time.

---

> [!NOTE]
> Submit this report by renaming it to `REPORT_[YOUR_NAME].md` and placing it in this folder.
