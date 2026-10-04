### MCP - Model Context Protocol

>> Function calling ≠ REST API ≠ MCP

Our target architecture:


>>                         Agent
>>                           │
>>                           ↓
>>                      MCP Client
>>                           │
>>                     MCP Protocol
>>                           │
>>                           ↓
>>                    ┌─────────────┐
>>                    │ MCP Server  │
>>                    └──────┬──────┘
>>                           │
>>          ┌────────────────┼────────────────┐
>>          ↓                ↓                ↓
>>     get_sales()     get_customer()   query_database()
>>          │                │                │
>>          └────────────────┼────────────────┘
>>                           ↓
>>                    Enterprise systems


Our MCP server will expose:

>> MCP Server  
>> │  
>> ├── get_sales()  
>> ├── get_customer()  
>> ├── query_database()  
>> ├── search_products()  
>> └── generate_report()  

>> **MCP standardizes how an AI application discovers and interacts with external capabilities and context.**


1. Why do we need MCP?
>> MCP gives us a standardized interface between the AI application and external capabilities.

2. MCP has a client-server architecture

>>┌───────────────────────────────────────────┐
>>│               AI APPLICATION              │
>>│                                           │
>>│   Agent / LLM                             │
>>│        │                                  │
>>│        ↓                                  │
>>│   MCP Client                              │
>>└────────┼──────────────────────────────────┘
>>         │
>>         │ MCP
>>         ↓
>>┌───────────────────────────────────────────┐
>>│               MCP SERVER                  │
>>│                                           │
>>│ Tools                                     │
>>│ Resources                                 │
>>│ Prompts                                   │
>>└────────┬──────────────────────────────────┘
>>         │
>>         ↓
>>┌───────────────────────────────────────────┐
>>│         External systems                  │
>>│                                           │
>>│ DB / CRM / APIs / Files / Services        │
>>└───────────────────────────────────────────┘


3. MCP tools

4. MCP resources
- MCP can expose more than executable tools.

>> MCP Server  
>>│  
>>├── Tools  
>>│   ├── get_sales()  
>>│   └── generate_report()  
>>│  
>>├── Resources  
>>│   ├── product_catalog  
>>│   ├── sales_schema  
>>│   └── company_policy  
>>│  
>>└── Prompts  
>>    ├── sales_analysis  
>>    └── customer_analysis  


5. MCP server

>> retail-mcp-server/  
>> │  
>> ├── server.py  
>> ├── tools/  
>> │   ├── sales.py  
>> │   ├── customer.py  
>> │   ├── product.py  
>> │   └── reporting.py  
>> │  
>> ├── database/  
>> │   └── ...  
>> │  
>> └── config/  
>>     └── ...  

6. Agent shouldn't know implementation details
- It only calls tools dont know MCP server

7. MCP vs function calling
- Function calling
>> Function calling is primarily an LM interaction mechanism

- MCP
>> MCP defines a standardized client-server protocol for exposing capabilities to AI applications.

- Answer
>> Function calling is a model-to-application mechanism for requesting structured function execution, whereas MCP is a standardized protocol for connecting AI applications to external tools, resources, and prompts through MCP servers.

- MCP vs REST API 
>> REST is a general-purpose service/API architecture.
>> MCP is specifically designed around AI application ↔ external capability/context integration.
>> MCP doesn't replace REST.

9. Function calling can be used to decide to invoke an MCP tool.

10. 

- What is MCP?

>> MCP is a standardized protocol for connecting AI applications to external tools, resources, and prompts through MCP servers.

- MCP vs function calling?

>> Function calling provides a mechanism for an LLM to request structured function execution from an application. MCP provides a standardized client-server protocol for discovering and interacting with external AI capabilities and context.

- MCP vs REST?

>> REST is a general-purpose API architecture. MCP is specifically designed around AI application integration and provides standardized concepts for tools, resources, and prompts. An MCP server can itself call REST APIs.

- Why use MCP?

>> It decouples AI applications from individual tool implementations and provides a standardized integration interface, making capabilities easier to expose and reuse across different AI clients.

- Does MCP replace APIs?

>> No. MCP can sit above existing APIs and services. An MCP server can translate an AI-oriented tool request into REST, SQL, SDK calls, or other backend operations.

- Where does the LLM fit?
>> User  
>>  ↓  
>> Agent / LLM  
>>  ↓  
>> MCP Client  
>>  ↓  
>> MCP Server  
>>  ↓  
>> Tool  
>>  ↓  
>> External system  

- The MCP server isn't itself the agent.



>>                             USER
>>                               │
>>                               ↓
>>                        ┌─────────────┐
>>                        │   Agent     │
>>                        │  LangGraph  │
>>                        └──────┬──────┘
>>                               │
>>              ┌────────────────┼─────────────────┐
>>              ↓                ↓                 ↓
>>           Memory             RAG             MCP
>>              │                │                 │
>>       PostgreSQL/Redis   pgvector/Azure    MCP Client
>>                                                │
>>                                   ┌────────────┼────────────┐
>>                                   ↓            ↓            ↓
>>                              Retail MCP   Finance MCP   Search MCP
>>                                   │            │            │
>>                                   ↓            ↓            ↓
>>                                  DB          APIs        Services


- That's a very strong foundation for the production agentic-AI system you're preparing to discuss in interviews.

- We can use tools to access external data but if enterprise level if we have 100's of application 
- can use single MCP common server across applications

- If agents can already call tools, why do we need MCP?
>> "We don't necessarily need MCP for every application. Direct tool calling is often simpler for a small, tightly coupled system. MCP becomes valuable when we need a standardized way to expose and discover tools and other capabilities across multiple AI applications and services. It decouples the agent from the implementation of those capabilities and makes integrations reusable, governed and easier to manage at enterprise scale."

