### LangGraph

1. Why LangGraph?
>> LangGraph lets us represent the execution explicitly:
>> So instead of hiding orchestration inside a large loop, we define the system as a graph.

2. StateGraph
>> The central concept is the StateGraph.

3. State
>> State is the shared data passed through the graph 

4. Nodes
>> A node performs work.
>> So a node is essentially a unit of computation within the graph.

5. Edges
>> An edge determines where execution goes next.

6. Conditional edges
>> The decision is represented by a conditional edge.

7. Validator 
Validator checks things like: 
>> ✓ Did we answer the user's question?
>> ✓ Did required data exist?
>> ✓ Are required fields present?
>> ✓ Did the tool calls succeed?
>> ✓ Is the output structurally valid?

8. Checkpoints
>>This is where LangGraph starts becoming particularly valuable.
>>A persisted snapshot of the graph's state at a particular execution point.

9. Persistence
>>Persistence means state survives beyond the immediate execution.

10. Interrupts
>>This is human-in-the-loop orchestration.

#### Questions

1. What is LangGraph?
>> LangGraph is a graph-based orchestration framework for building stateful, multi-step applications and agents where execution can branch, loop, persist state, and be interrupted.
2. Why use a graph instead of a simple chain?
>> A chain is primarily linear. Agents often require conditional execution, cycles, retries, human intervention, and stateful execution, which are naturally represented as a graph.

3. What is StateGraph?
>> StateGraph is the graph abstraction where nodes operate on shared application state and edges determine how execution moves between nodes.

4. What is a node?
>> A node is a unit of computation that reads state and produces updates to state.

5. What is a conditional edge?
>> A conditional edge determines the next node dynamically based on the current state or result.

6. Why do agents need cycles?
>> Because the agent may need to iteratively reason, call a tool, observe the result, and decide on another action before reaching the final answer.

7. Why checkpoints?
>> Checkpoints persist graph state so execution can be recovered, resumed, inspected, or interrupted rather than losing all progress.

8. Why interrupts?
>>Interrupts allow execution to pause for external input, commonly human approval, before continuing a potentially sensitive or irreversible action.

9. 