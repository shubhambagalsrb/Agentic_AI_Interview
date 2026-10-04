### Cloud Architecture

>> Given a requirement, choose the cloud service based on architecture, constraints, cost, latency, security, ecosystem and existing infrastructure.

- This stage around one system deployed across three clouds for learning, while still making the architecture realistic.

- Our logical architecture remains cloud-neutral:

                         USER
                           │
                           ▼
                    API / Application
                           │
                           ▼
                    LangGraph Agent
                    /      │       \
                   /       │        \
              Memory      RAG       MCP
                 │          │         │
                 ▼          ▼         ▼
             Database    Search    MCP Servers
                                      │
                              APIs / Databases

- Then we map those logical components to cloud services.

- Azure — Primary AI stack

Azure
│
├── Azure OpenAI
│       └── LLM
│
├── Azure AI Search
│       └── RAG / vector / hybrid search
│
├── Azure Blob Storage
│       └── Documents
│
├── Azure Key Vault
│       └── Secrets
│
└── Azure Monitor
        └── Logs / metrics / monitoring


- This makes sense as our primary enterprise AI architecture, especially given your existing Azure/Databricks experience.


- AWS — infrastructure + model alternative

AWS
│
├── Bedrock
│      └── Foundation models
│
├── S3
│      └── Documents / data
│
├── Lambda
│      └── Serverless tools
│
├── IAM
│      └── Identity / permissions
│
└── CloudWatch
       └── Monitoring

- "What changes if the company is AWS-first?"

>> MCP Server  
>>     ↓  
>> AWS Lambda  
>>     ↓  
>> DynamoDB / RDS / S3 / API  


- GCP — data + ML alternative


GCP
│
├── Vertex AI
│      └── Models / AI
│
├── BigQuery
│      └── Analytics / warehouse
│
├── Cloud Storage
│      └── Documents
│
└── Cloud Run
       └── Agent/MCP services

- A particularly useful architecture to understand is:

>> Agent  
>>   ↓  
>> MCP  
>>   ↓  
>> Cloud Run  
>>   ↓  
>> BigQuery  

- because it teaches you how an agent can interact with a modern analytical data platform.

| Capability         | Azure            | AWS                        | GCP                              |
| ------------------ | ---------------- | -------------------------- | -------------------------------- |
| LLM/GenAI          | Azure OpenAI     | Bedrock                    | Vertex AI                        |
| Object storage     | Blob Storage     | S3                         | Cloud Storage                    |
| Serverless compute | Functions        | Lambda                     | Cloud Run / Functions            |
| Secrets            | Key Vault        | Secrets Manager            | Secret Manager                   |
| Monitoring         | Azure Monitor    | CloudWatch                 | Cloud Monitoring                 |
| Data warehouse     | Synapse / Fabric | Redshift                   | BigQuery                         |
| AI search          | Azure AI Search  | OpenSearch / other options | Vertex AI Search / other options |



- But don't make the interview answer a lookup table.

- Example interview question

> "You need document RAG for an enterprise agent. Which cloud service would you choose?"

- Wear answer:
> "Azure AI Search because it's Azure."

- Better:
> "I'd choose a managed search service if the organization wants vector plus keyword/hybrid retrieval, metadata filtering and managed scaling. If the organization is already heavily invested in Azure's identity, storage and AI stack, Azure AI Search would be a natural choice. But I would also consider the existing cloud ecosystem, expected scale, latency, security requirements and cost before making the decision."

- Another example 
> "Why Lambda vs Cloud Run vs Azure Functions?"
- Don't memorize:
> Lambda = AWS  
> Cloud Run = GCP  
> Functions = Azure  

- Instead ask:

What workload?  
       ↓  
Stateless?  
       ↓  
Execution duration?  
       ↓  
Traffic pattern?  
       ↓  
Container required?  
       ↓  
Cold-start sensitivity?  
       ↓  
Scaling requirements?  
       ↓  
Existing cloud ecosystem?  


- We'll deliberately create architecture decisions

- for each component, we will evaluate 

Requirement  
    ↓  
Candidate services  
    ↓  
Decision criteria  
    ↓  
Chosen service  
    ↓  
Why?  
    ↓  
Trade-offs  

- For example
  - Requirement :
  > Store enterprise PDFs for RAG
  - Candidates: 
  > Azure Blob
  > S3
  > GCS
  - Decision:
  > Company already Azure-first
  > +
  > Azure AI Search
  > +
  > Azure OpenAI
  > +
  > Azure identity
  >         ↓
  > Azure Blob Storage
  - But if the company is Azure-first:
  > Blob is better choice



                      AGENT
                      │
       ┌──────────────┼──────────────┐
       │              │              │
     Azure           AWS            GCP
       │              │              │
 Azure OpenAI       Bedrock       Vertex AI
 Azure Search       S3           BigQuery
 Blob               Lambda       Cloud Run
 Key Vault          IAM          Secret Manager
 Monitor            CloudWatch   Cloud Monitoring


 - But a sensible production deployment might instead be
                     AGENT
                      │
                      ▼
                  Azure
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       OpenAI       Search      Storage

- One cloud is perfectly reasonable.

- Multi-cloud introduces:
  - networking complexity
  - identity complexity
  - observability complexity
  - data movement
  - higher operational overhead
  - potentially higher cost



11.1 Cloud architecture fundamentals
        ↓
11.2 Deploy Agent API
        ↓
11.3 Azure OpenAI
        ↓
11.4 Azure AI Search
        ↓
11.5 Azure Storage
        ↓
11.6 Azure Key Vault
        ↓
11.7 Azure Monitor
        ↓
11.8 AWS equivalents
        ↓
11.9 Bedrock + S3 + Lambda + IAM
        ↓
11.10 GCP equivalents
        ↓
11.11 Vertex AI + BigQuery + Cloud Run
        ↓
11.12 Cloud architecture comparison
        ↓
11.13 Cost / latency / security trade-offs
        ↓
11.14 Multi-cloud architecture
        ↓
11.15 Production architecture decision


> What problem does it solve?

> Why would I choose it?

> What are the alternatives?

> What trade-off am I accepting?