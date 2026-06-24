
1. Zep is able to maintain multiple versions of temporally aware events

The paper's main claim was that Zep outperformed MemGPT in the DMR benchmark but it later claimed that the DMR benchmark wasn't even a good enough comparison because of the small token size per session, such that everything could be stored in the context window of the model and achieve the same results.


[[Zep]]

Communities is what is missing from the codebase-memory-mcp as communities could greatly help in answering long encompassing queries

The novel invention in this memory system is the introduction of timestamps in the edges. 

The edges store facts and the facts can be retrieved between two nodes in variety of ways, thus that facts needs to be deduplicated. We store the t-valid and t-invalid along with the fact, so that when something contradicting is seen later in time then the older fact is invalidated.
This might not be so useful in the code, because the current fact (the code) is available for us to parse and understand, but this might be very useful in **creating an up to date documentation because a code change can invalidate a fact in the documentation which can be handled gracefully **


The worst thing they do is inflate the delta in their evaluations on Locomo benchmark. The full conversation gave a accuracy of 20% and zep says 56.7% then the delta they show is relative to the baseline which becomes 184%. This is slightly misleading. This show delta in reference to the full conversation results thus if a model was getting correct results 20 times out of 100 now it is doing 57 which is around 1.84 times better.

Although their results on reducing token usage and latency is real and very impressive.

