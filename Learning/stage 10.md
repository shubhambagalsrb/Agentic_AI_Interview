### Production Engineering


- Now we ask

>>What happens when any of (agent mcp, tool , rag) components fails?

1. Failure injection

| Failure                   | What we're testing          |
| ------------------------- | --------------------------- |
| LLM timeout               | Timeout + retry             |
| API timeout               | Retry + backoff             |
| Bad SQL                   | Validation + error handling |
| Invalid tool arguments    | Schema validation           |
| Hallucinated tool         | Tool validation             |
| Infinite agent loop       | Max iterations              |
| Rate limit                | Backoff + rate limiting     |
| Vector DB unavailable     | Fallback                    |
| Cloud service unavailable | Fallback / degradation      |
| Duplicate tool execution  | Idempotency                 |
| Slow repeated query       | Caching                     |


2. Retry 
- For example

>> API timeout       → retry ✓
>> 503 Service       → retry ✓
>> 429 Rate limit    → retry ✓
>> 
>> Invalid SQL       → retry ✗
>> Invalid argument  → retry ✗
>> Unauthorized      → retry ✗


- Interview answer
>> "I retry transient failures, not deterministic failures. For example, timeouts, temporary network failures and 5xx responses may be retried, while invalid input, authentication failures and malformed SQL should generally be handled without blind retries."

3. Exponential backoff
- Instead retry 5 times
>> Attempt 1 → wait 1 sec
>> Attempt 2 → wait 2 sec
>> Attempt 3 → wait 4 sec
>> Attempt 4 → wait 8 sec
- usually with jitter

4. Timeout
- And you should have different timeout budgets for different operations.
- Example
>> Database query → 3 sec
>> External API   → 5 sec
>> LLM call       → 30 sec

5. Circuit breaker
- If you continuously hammering an already failing service.
- We use circuite breaker instead
- This protects both your application and the downstream service.


6. Fallback

- We can use fallback service like primary llm we provide fallback llm
- But fallbacks must preserve correctness.
- Graceful failure is better than hallucination.

7. Idempotency
- This becomes important when agents can execute actions.
- Imagine
>> Agent  
>>  ↓  
>> create_order()  
- The API succeeds.
- But the network response gets lost.

- Agent thinks:
>> "Maybe it failed."

and retries

- Now

>> Order no#123 created
>> Order no#124 created
- You've accidentally created two orders.

- Idempotency allows:
>> request_id = abc123
- The server remembers:
>> abc123 → already processed
- and doesn't execute the same operation twice.

- This is particularly important for:
  - payments  
  - orders  
  - refunds  
  - emails  
  - database writes  
  - ticket creation  

8. Caching 
- Suppose 500 users ask:
>> "What is the product catalog?"
- Repeatedly calling:
>> Agent
>>  ↓
>> MCP
>>  ↓
>> Database

is wasteful

- Cache appropriate results:

>> Request  
>>  ↓  
>> Cache?  
>>  ├── HIT → return  
>>  └── MISS  
>>        ↓  
>>      Database  
>>         ↓  
>>       Cache  
>>        ↓  
>>     Return  

- But we need to think about cache invalidation and freshness.
- Don't blindly cache rapidly changing information such as live inventory or stock prices.

9. Rate limiting

- Imagine one user or one agent suddenly generates:
>> 10,000 requests/minute  
- You need protection.

>> Request  
>>  ↓  
>> Rate limiter  
>>  ↓  
>> Within limit?  
>>  ├── YES → process  
>>  └── NO  → 429 / queue / reject  

- You can apply limits at multiple levels:
>> User
>> Tenant
>> API key
>> Agent
>> MCP server
>> IP

10. Structured logging
- This is one of the most important things for debugging agents.
- Don't log:
>> "Something went wrong"
- Log structured information:
```json 
    {
        "trace_id": "abc123",
        "request_id": "req456",
        "user_id": "...",
        "agent_version": "v3",
        "tool": "get_sales",
        "arguments": {
                        "product_id": "P1001"
                        },
        "latency_ms": 1240,
        "status": "timeout",
        "retry_count": 2,
        "error_type": "TIMEOUT"
    }
```

- Now you can ask:
>> "Show me all get_sales timeouts for agent v3 during the last hour."
- That's production observability.


11. Agent-specific failures

This is where traditional backend engineering isn't enough.
- Infinite loop

>> Agent  
>>  ↓  
>> Tool  
>>  ↓  
>> Agent  
>>  ↓  
>> Tool  
>>  ↓  
>> Agent  
>>  ↓  
>> Tool  
>>  ↓  
>> ...  

  - Solution:
    > max_iterations = 10

  - Then:

>> 10 iterations reached  
>>        ↓  
>>       Stop  
>>        ↓  
>> Return controlled failure  

- Hallucinated tool

  - The LLM says:
  >> call get_revenue_forecast()
  - but that tool doesn't exist.
  - The execution layer should never blindly execute arbitrary model-generated code/tool names.
  - Instead: 
  >> LLM requested tool  
  >>   ↓  
  >>  Is tool registered?  
  >>   ├── YES → validate → execute  
  >>   └── NO  → reject  

  - Invalid tool arguments
  - LLM generates:
  ```json 
    {
        "product_id": 123,
        "month": "banana"
    }
    ```
  - Schema validation catches it:
  >> LLM  
  >> ↓  
  >> Pydantic/schema  
  >>  ↓  
  >> INVALID  
  >>  ↓  
  >> Return structured error  
  >>  ↓  
  >> Agent can correct/retry  



12. Stage 10 architecture

                         USER
                           │
                           ▼
                    LANGGRAPH AGENT
                           │
                    ┌──────┴──────┐
                    │             │
                 Timeout       Max steps
                    │             │
                    ▼             ▼
                MCP Client    Loop protection
                    │
              Retry / Backoff
                    │
              Circuit Breaker
                    │
                    ▼
                MCP Server
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
        APIs       DB       Vector DB
          │         │         │
       timeout    cache    fallback
          │
          ▼
    Structured logging
          │
          ▼
      Observability


13. The production question


- How would you productionize an agentic AI system?  
>> "I would first make the agent bounded and observable, with strict tool schemas, timeouts, retries with exponential backoff for transient failures, circuit breakers for unhealthy dependencies, fallbacks where safe, idempotency for side-effecting operations, caching for appropriate reads, rate limiting, structured logging and distributed tracing. I'd also enforce maximum agent iterations and validate tool selection and arguments. Finally, I'd monitor latency, errors, token usage, cost and agent quality in production."

