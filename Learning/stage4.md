### Agentic RAG

1. Normal RAG:
Traditional RAG looks like:

>> User  
>>  ↓  
>> Retriever  
>>  ↓  
>> Relevant documents  
>>  ↓  
>> LLM  
>>  ↓  
>> Answer  

The application automatically searches the knowledge base.

>> Question
>>  ↓
>> Embedding
>>  ↓
>> Vector search
>>  ↓
>> Top-k chunks
>>   ↓
>> LLM
>>   ↓
>> Answer

The retrieval decision is essentially predetermined.

2. Agentic RAG

Normal RAG:

>> Question  
>>   ↓  
>> Retrieve  
>>   ↓  
>>  LLM  

Agentic RAG:

>> Question  
>>  ↓  
>> Agent  
>>  ↓  
>>  Should I retrieve?  
>>   ├── NO → Answer  
>>   └── YES  
>>         ↓  
>>      Retrieve  
>>         ↓  
>>      Evaluate  
>>         ↓  
>>       Agent  

3. Why is this useful?
- Because retrieval isn't always necessary.
- Some questions needs to use RAG to answer and some dont. they might need other tools


Agentic RAG Graph

                         START
                           ↓
                        AGENT
                           ↓
                  Need knowledge?
                    /          \
                  NO            YES
                  ↓              ↓
              VALIDATOR      RETRIEVER
                                 ↓
                              EVALUATOR
                                 ↓
                           Good evidence?
                           /           \
                         YES            NO
                          ↓              ↓
                       AGENT        QUERY REWRITE
                                        ↓
                                     RETRIEVER



