### Agent Memory

>>                         Agent
>>                           │
>>             ┌─────────────┼─────────────┐
>>             ↓             ↓             ↓
>>         Working       Episodic       Semantic
>>          Memory        Memory          Memory
>>             │             │             │
>>          Current       Past events    Durable facts
>>           task
>>             │             │             │
>>             └─────────────┼─────────────┘
>>                           ↓
>>                    Memory Storage
>>                     /           \
>>                Redis        PostgreSQL


- But we'll make an important distinction between what the agent needs right now, what happened before, and what is worth remembering permanently.

1. Working memory
- Working memory is the information required for the current execution.
- This is our LangGraph state.

2. Episodic memory 
- Episodic memory is about past interactions/events.

3. Semantic / user memory
- Semantic memory represents durable facts that are useful across interactions.
- It should be useful beyond the current conversation.

4. 
| Type            | Question it answers                      | Lifetime     |
| --------------- | ---------------------------------------- | ------------ |
| Working memory  | "What am I doing now?"                   | Current task |
| Episodic memory | "What happened before?"                  | Longer-term  |
| Semantic memory | "What do I know about this user/domain?" | Long-term    |


5. Memory architecture
                         USER
                           │
                           ↓
                    ┌─────────────┐
                    │    AGENT    │
                    └──────┬──────┘
                           │
              ┌────────────┼─────────────┐
              ↓            ↓             ↓
          Working       Episodic      Semantic
           Memory        Memory         Memory
              │            │             │
              ↓            ↓             ↓
          LangGraph      Redis       PostgreSQL
            State

- But this isn't a hard rule.
- For example, PostgreSQL can store episodic memory too.
- The important concept is the memory abstraction, not the specific database.

6. Why Redis?
- Redis is useful for fast, short-lived or frequently accessed information.

7. Why PostgreSQL?
- PostgreSQL is useful for durable information.
- We can have something conceptually like: 
>>CREATE TABLE user_memory (  
>>    id UUID PRIMARY KEY,  
>>    user_id TEXT,  
>>    memory_type TEXT,  
>>    memory TEXT,  
>>    source TEXT,  
>>    confidence FLOAT,  
>>    created_at TIMESTAMP,  
>>    updated_at TIMESTAMP  
>>);  

- This gives us a persistent memory layer.

8. Memory should NOT mean "store everything"

- Huge context -> high cost -> slow -> noise -> bad answers

- Instead :
- Conversation -> Memory extraction -> Should this be remembered -> No -> Discard
-> Yeas -> Store

- Memory retrieval is conceptually very similar to RAG, except the corpus is the agent's accumulated experience/user context rather than enterprise documents.

