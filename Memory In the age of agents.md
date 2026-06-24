LLMs are going to become the decision making cores of interactive systems
Formally defining agents
1. We define State Space (S), action (A) and Observation (B)
2. New state space is generated based on the action on the previous state space
3. And observation is created by looking at the
	1. History of previous conversations
	2. Instructions provided by User
	3. State space
4. An action is created by a combination of
	1. Memory
	2. Observation
	3. User Instructions

To create an effective action only observations are not sufficient, sometimes we might need observations from previous interactions, thus we keep an evolving memory state. This would consist of a memory system


#### Memory System
**Formation**: In one cycle when the agent produces informational artifacts, these are converted to memory candidates in the memory system. 
**Updation**: We then update the memory system based on the a heuristic by resolving, consolidating, invalidating existing entries.
**Retrieval**: At the time of determining an action the LLM uses some sort of heuristic for fetching artifacts from this memory store.





### Questions
1. Is there an existing schema definition of what a memory candidate might look like?