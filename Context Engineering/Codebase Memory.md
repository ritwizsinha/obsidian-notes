LLM agents operate on unstructured text, yet the questions developers ask are inherently structural—call graphs, dependency chains, module boundaries, and impact analysis. Text-based search cannot capture transitive relationships without iteratively following references, each iteration consuming additional tokens and increasing the risk of lost context

Recent empirical analyses confirm that input tokens dominate the cost of agentic coding tasks, even with caching

LLMs underutilize information in the middle of a long context







#### Observations
1. Using it as an mcp multiple tool calls, increasing context bloat in case of larger questions about the nature of the system or explaining something.
2. Git needs to be integrated with this.