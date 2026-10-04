### Tool Calling

Our target architecture:

>>                         ┌─────────────────┐
>>                         │      User       │
>>                         └────────┬────────┘
>>                                  ↓
>>                        ┌─────────────────┐
>>                        │       LLM       │
>>                        └────────┬────────┘     
>>                                  ↓
>>                         Select tool + args
>>                                  ↓
>>              ┌───────────────────┼───────────────────┐
>>              ↓                   ↓                   ↓
>>       calculate()   query_database()       web_search()
>>              │                   │                   │
>>              └───────────────────┼───────────────────┘
>>                                  ↓
>>                            Tool result
>>                                  ↓
>>                         ┌─────────────────┐
>>                         │       LLM       │
>>                         └────────┬────────┘
>>                                  ↓
>>                               Answer


2. Structured tool schemas
3. Why structured schemas matter
4. Tool validation : This is one of the first places where agent engineering becomes different from simply prompting an LLM.
5. Tool errors: If tool gives error our entire agent shouldn't crash
6. Retries :  Some failures are temporary use retry. But don't blindly retry everything.

This distinction is an excellent interview topic:
>> Transient errors should generally be retryable; deterministic/business errors usually shouldn't be.

7. Timeouts: Production agents need explicit timeout policies.

8. Authentication:
- Agent should not receive credential.
- The tool execution layer should manage credentials.
- The model gets the capability, not the secret.
- 

12. Interview questions you'll be able to answer:

- what is tool calling?
>> Tool calling allows an LLM to request execution of an external function using structured arguments, with the application executing the function and returning its result to the model

- Why use structured schemas?
>> They constrain and validate model-generated arguments, making tool invocation safer, more predictable, and easier to integrate with downstream systems.

- How do you handle tool failures?
>> Classify failures into transient and deterministic errors, retry transient failures with bounded exponential backoff, return structured errors to the agent when appropriate, and avoid retrying errors where retrying cannot change the outcome.

- How do you secure agent tools?
>> Keep credentials outside the model context, authenticate at the tool/service layer, enforce authorization and least privilege, validate inputs, and audit tool invocations.

- What happens when a tool times out?
>> The tool layer should enforce a bounded timeout and return a structured failure so the agent can decide whether to retry, use an alternative tool, or communicate the limitation.

