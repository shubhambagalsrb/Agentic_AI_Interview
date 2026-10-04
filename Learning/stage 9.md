### Agent Evaluations

- Goal :
>> "How do I know my agent actually works, and that a new version hasn't made it worse?"

1. Build the evaluation dataset

| ID   | User question                           | Expected behavior               | Expected tools             | Ground truth     |
| ---- | --------------------------------------- | ------------------------------- | -------------------------- | ---------------- |
| E001 | What were P1001's sales in September?   | Retrieve sales                  | `get_sales`                | ₹1.24 Cr         |
| E002 | Why did P1001 sales fall?               | Sales + product + possibly docs | `get_sales`, `get_product` | Known drivers    |
| E003 | What's our travel reimbursement policy? | Retrieve documents              | `search_documents`         | Policy answer    |
| E004 | Calculate 15% growth on ₹10 Cr          | Calculate                       | `calculate`                | ₹11.5 Cr         |
| E005 | Give me P1001's details                 | Retrieve product                | `search_products`          | Product metadata |


- But importantly, don't only store the final expected answer.
- Store the expected behavior/trajectory too.
>> evaluation_case/  
>>      question  
>>      expected_behavior  
>>      expected_tools  
>>      expected_arguments  
>>      expected_result  
>>      ground_truth  
>>      acceptable_variations  

- That lets us evaluate the agent at multiple levels.

2. What exactly do we evaluate?
- Think of evaluation as a hierarchy:
>>                    AGENT  
>>                      │  
>>       ┌──────────────┼──────────────┐  
>>       ▼              ▼              ▼  
>>   Did it act?    Did it act      Did it answer  
>>                  correctly?       correctly?  
>>       │              │              │  
>>       ▼              ▼              ▼  
>> Task success    Tool behavior   Answer quality  

2. 1. Task success
- Did the agent actually accomplish the user's task?
- Example:
>> "Why did sales fall?"

>> Agent gives a correct, supported explanation → pass

>> Agent retrieves irrelevant data and gives a confident guess → fail

2. 2. Tool selection accuracy
- Did it choose the right tools?
- Expected: and Actual : 
- Tool-selection failure even if the final answer happens to look reasonable.

2. 3. Tool argument accuracy
- Expected 
>> {  
>>  "product_id": "P1001",  
>>  "month": "2026-09"  
>> }    

- Actual 
>> {  
>>  "product_id": "P1001",  
>>  "month": "2026-08"  
>> }  

2. 4. Answer correctness

- Compare the final answer against ground truth.
>> Question:
>> What were September sales?
>>
>> Ground truth:
>> ₹12.4 crore
>>
>> Agent:
>> ₹12.4 crore
>> 
>> → PASS

2. 5. Groundedness
- Especially important because we're building Agentic RAG.
- Ask
>>"Are the claims in the answer actually supported by the retrieved documents/data?"

Example:
- Retrieved document:
>> Travel reimbursement limit = 2,500/day
- Agent:
>> "the company reimburses 5,000/day."

Answer may sound excelent.
Groundedness = Fail

2. 6. Trajectory quality 
- This is particularly important for agentic systems.
- Suppose the question only requires:
>> get_sales()
- Agent does:
>> get_customer()
>> search_products()
>> query_database()
>> get_sales()
>> search_documents()
>> calculate()

- eventually get correct answer but trajectory is inefficient 

3. Deterministic evaluation

- These tests should not require an LLM judge.
- example 
```python 
    assert actual_tool == "get_sales"
    assert actual_product_id == "P1001"
    assert actual_month == "2026-09"
    assert actual_value == expected_value
    assert response["status"] == "success"
```
- cheap + fast + reproducible
- And therefore ideal for CI/CD regression tests.

4. LLM-as-Judge

- Now consider 
>> "Explain why sales declined."
- There may be many valid answers.
- You can't reasonably do:
>> actual_answer == expected_answer
- Instead, give a judge model:
>> Question  
>> Ground truth  
>> Retrieved evidence  
>> Agent answer  

and ask it to evaluate:
>> Correctness: 1–5  
>> Groundedness: 1–5  
>> Completeness: 1–5  
>> Relevance: 1–5  

- For example:

>> Question:  
>> Why did P1001 sales decline?  
>>   
>> Ground truth:  
>> Sales declined primarily because distributor X  
>> reduced orders by 18%.  
>>   
>> Agent answer:  
>> Sales declined because distributor X reduced  
>> orders by 18%, while other distributors were stable.  
>>   
>> Judge:  
>> Correctness: 5  
>> Groundedness: 5  
>> Completeness: 4  
>> Relevance: 5  

- But we don't blindly trust LLM judges. We'll calibrate them against human-reviewed examples and use deterministic checks wherever possible.


5. Offline evaluation 

>> Code change  
>>      ↓  
>> Run evaluation dataset  
>>      ↓  
>> 50 / 100 / 500 test cases  
>>      ↓  
>> Calculate metrics  
>>      ↓  
>> Compare with previous version  
>>      ↓  
>> PASS / FAIL  
>>      ↓  
>> Deploy  

Example:

| Metric            |     v1 |     v2 |
| ----------------- | -----: | -----: |
| Task success      |    82% |    89% |
| Tool selection    |    91% |    95% |
| Argument accuracy |    88% |    94% |
| Groundedness      |    86% |    92% |
| Avg latency       |   4.2s |   4.8s |
| Avg tokens        |  2,800 |  3,400 |
| Cost/task         | $0.012 | $0.017 |

- Now you can make an engineering decision:
>> v2 improved accuracy by 7 percentage points but increased cost by 42%.


6. Online evaluation

- Real users will ask things you didn't anticipate
- So after deployment:
>> Production traffic  
>>        ↓  
>> Agent  
>>        ↓  
>> Logs / traces  
>>        ↓  
>> Evaluation pipeline  
>>        ↓  
>> Quality metrics  

- Monitor
task success  
tool failures  
hallucination/groundedness  
latency  
token consumption  
cost  
retries  
loop count  
user feedback  
escalation rate  

- This gives you a production quality signal.


7. Regression testing

- This is the big production concept.

- Suppose today:
>> Tool selection = 95%

- You modify the system prompt.
- Tomorrow:
>> Tool selection = 82%

- Your normal unit tests may still pass.
- Your evaluation suite catches:
>> Agent quality regression drops

- So we'll create a gate

>>                    Git commit  
>>                        ↓  
>>                  Run test suite  
>>                        ↓  
>>                Run agent evaluations  
>>                        ↓  
>>             ┌──────────┴──────────┐  
>>             ↓                     ↓  
>>          Metrics OK           Metrics worse  
>>             ↓                     ↓  
>>          Deploy                 Block  


8. LangSmith / MLflow

>> "I don't evaluate only the final response. I evaluate the complete agent trajectory, including tool selection, tool arguments, retrieval quality, groundedness and final answer quality, while also tracking latency and cost."




