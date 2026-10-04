### Agent Fundamentals
> Our goal is to understand the difference between a normal LLM application, a workflow, and an agent by building the smallest useful agent.

**1. Start with the mental model :**
- Level 1 - LLM application
>>User  
>>  ↓  
>>Prompt  
>>  ↓  
>>LLM  
>>  ↓  
>>Answer  

**Example:**

>> User: "Explain why inflation increased in India."

The LLM simply generates an answer.

There is **no external action.**


- Level 2 - Workflow
A workflow has predefined steps.
>>User  
>> ↓  
>>Retrieve data  
>> ↓  
>>LLM summarizes data  
>> ↓  
>>Generate report  

The developer decides the sequence.

The system isn't deciding what to do next.

**You decided the workflow.**

- Level 3 - Agent
Now we let the LLM decide which tool/action is appropriate.

>>                 ┌──────────────┐
>>                 │     User     │
>>                 └──────┬───────┘
>>                        ↓
>>                 ┌──────────────┐
>>                 │     LLM      │
>>                 └──────┬───────┘
>>                        ↓
>>                 "I need stock data"
>>                        ↓
>>                 ┌──────────────┐
>>                 │    Tool      │
>>                 │ Stock API    │
>>                 └──────┬───────┘
>>                        ↓
>>                 Observation
>>                        ↓
>>                 ┌──────────────┐
>>                 │     LLM      │
>>                 └──────┬───────┘
>>                        ↓
>>                     Answer

The important difference:

>> **The LLM is not merely generating text. It is participating in a decision/action loop.**



**2. What exactly is an agent?**
For interview purposes, use this definition:
>>**An agent is an LLM-based system that can reason about a goal, decide what action to take, invoke tools or interact with an environment, observe the result, and continue until it reaches an appropriate outcome.**

Don't get too attached to the word reason.

In production systems, you don't necessarily expose or depend on a chain-of-thought process. What matters architecturally is:

**decision → action → observation → next decision**


**3. Learn the 8 concepts:**
3.1 LLM application
An application that uses an LLM to perform some task.

Examples:

- summarization
- classification
- question answering
- text generation
- extraction

Architecture:

>> Input -> LLM -> Output  
It doesn't necessarily qualify as an agent

3.2 Workflow
** A predefined sequence of operations**

Example: 
>> User question  
>>      ↓  
>> Search database  
>>      ↓  
>> Retrieve documents  
>>      ↓  
>> LLM  
>>      ↓  
>> Generate answer  

The developer determines the sequence.

Key interview distinction
>> Workflow = developer controls the path

**4. Agent**
An agent has some degree of **runtime decision-making.**

Suppose we give the system these tools:
>> search_web()
>> get_stock_price()
>> calculate()
>> get_company_financials()

User:
>> "Should I buy Reliance?"

The LLM might decide:

>> I need financial data  
>>         ↓  
>> get_company_financials()  
>>         ↓  
>> Observation  
>>         ↓  
>> I need current price  
>>         ↓  
>> get_stock_price()  
>>         ↓  
>> Observation  
>>         ↓  
>> I need to calculate valuation  
>>         ↓  
>> calculate()  
>>         ↓  
>> Observation  
>>         ↓  
>> Final answer  

The important thing is that **the next step wasn't necessarily hard-coded.**

** 5. Autonomous agent**
An autonomous agent has greater freedom to determine and execute multiple steps toward a goal.

For example:
>> "Research Nvidia and prepare an investment report."

>> **Autonomy is a spectrum, not a binary property.**

A tightly constrained tool-calling agent is still an agent.


6. Tool

**A tool is an external capability available to the agent**

Examples:
>> search_web()
>> calculator()
>> database_query()
>> python()
>> send_email()
>> get_weather()
>> stock_price()
>> CRM_lookup()

Think:
>>LLM = decision maker
>>Tool = capability

An LLM by itself cannot actually query your database.
You give it a tool:
The agent can then decide to invoke it.

7. State 
State is the information the system needs to remember across steps.

**State becomes extremely important once we move into LangGraph and production agent systems.**

8. Observation
An observation is the information the agent receives after taking an action.
Example:
Action/Tool:
get_stock_price("RELIANCE")

Tool returns:
1425

That returned information is the observation.

So:

>> Action  
>>   ↓  
>> Tool  
>>   ↓  
>> Observation  

9. Action
An action is something the agent decides to do.

For example:
Search_web()
send_email()

10. Everything together

>>                   ┌───────────────┐
>>                   │     User      │
>>                   └───────┬───────┘
>>                           │
>>                           ↓
>>                   ┌───────────────┐
>>                   │      LLM      │
>>                   │               │
>>                   │ Decide next   │
>>                   │    action     │
>>                   └───────┬───────┘
>>                           │
>>                     Action│
>>                           ↓
>>                   ┌───────────────┐
>>                   │     Tool      │
>>                   └───────┬───────┘
>>                           │
>>                    Result │
>>                           ↓
>>                   ┌───────────────┐
>>                   │  Observation  │
>>                   └───────┬───────┘
>>                           │
>>                           ↓
>>                   ┌───────────────┐
>>                   │      State    │
>>                   └───────┬───────┘
>>                           │
>>                           ↓
>>                        LLM again

11. Agent vs Workflow - the interview answer

**Workflow**
>> A -> B -> C -> D

the developer defines the sequence.

**Agent**
>>        ┌→ Tool A
>>        │
>>LLM ────┼→ Tool B
>>        │
>>        └→ Tool C
>>             ↓
>>          Observe
>>             ↓
>>            LLM

The system can select the next action dynamically.

**Interview answer**
>> "The main difference is who controls the execution path. In a workflow, the developer defines the sequence of steps explicitly. In an agent, the LLM has the ability to dynamically select actions or tools based on the current goal and observations. I would prefer workflows when the process is predictable and deterministic, and agents when the next step depends on runtime context."

12. When should you use an agent?

- 1. The path isn't known beforehand
- 2. Multiple tools may be required
- 3. The task requires iterative decisions

13. When NOT to use an agent

If process is deterministic just build workflow

**Because an agent introduces:**
- additional latency 
- additional token usage 
- unpredictable behavior  
- more failure modes  
- harder debugging  
- potentially higher cost  
- security/tool-use risks

A very good interview statement is:
>> **"I wouldn't use an agent just because the problem involves an LLM."**


