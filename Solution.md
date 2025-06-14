# 💡 Cost Optimization Challenge: Managing Billing Records in Azure Serverless Architecture

## My Solution:

We can use hybrid data storage model that means:
1. We can use Cosmos DB to store last 3 months data for fast access.
2. For Older records, we can use azure blob storage (using ADLS Gen2 with hierarchical namespaces).

To achieve this, we can use azure Function that will check Cosmos DB first, and if not found, it fetches the record from Blob Storage — seamlessly, without changing the API interface.

This approach can reduce our Cosmos DB usage and storage costs while keeping all records accessible to the system and users.

## Architecture Diagram
             
             +-----------------------+
             |     Client/API        |
             +-----------------------+
                       |
                 Read / Write
                       |
             +-----------------------+
             |  Azure Function App   |
             |  (Data Access Layer)  |
             +-----------------------+
              /                     \
       Read from Hot            Read from Cold
       (≤ 3 months)             (> 3 months)
      Cosmos DB                Azure Blob Storage
       (Core SQL)                (ADLS Gen2)
              \                     /
               \__ Missing in Cosmos? ____/
                           |
               Trigger Archival Read Logic


## Working Logic:
1. Writing data: New records are still saved in Cosmos DB (no cha
2. Reading data: Our read function:
   a. First checks Cosmos DB.
   b. If not found and the record is old, it fetches from Blob Storage.
3. Archiving: A nightly job moves older records from Cosmos DB to Blob Storage.

## Benefits: 
1. Major cost savings by reducing Cosmos DB storage and RUs.
2. No user impact — reads and writes still work as they did before.
3. Easy to maintain — serverless and automated.

## ChatGPT snippet:

1. Can you design a scalable event-driven system in Azure?
2. What’s the best way to store and query logs in a serverless app?
3. How do I implement a retry pattern in Azure Durable Functions?
