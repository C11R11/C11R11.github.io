# Domain 1: Development with AWS Services - Complete Concept Guide

## 🏗️ 1. Modern Architectural Patterns (Tasks 1 & 2)
* **Microservices vs. Monolithic:** Moving from single, massive codebases to smaller, independently deployable and scalable services.
    * *AWS Services:* AWS Lambda, Amazon ECS/EKS, Amazon API Gateway.
* **Event-Driven Architecture:** Systems that act in response to state changes or "events" (like a file upload or a database update) rather than direct API calls.
    * *AWS Services:* Amazon EventBridge, AWS Lambda, Amazon S3 Event Notifications.
* **Orchestration vs. Choreography:** * *Orchestration:* A central controller dictates steps (like a conductor). -> *AWS Step Functions*
    * *Choreography:* Services independently react to events without a central boss. -> *Amazon SNS / EventBridge*
* **Fanout Pattern:** Pushing a single message to multiple, parallel downstream destinations simultaneously.
    * *AWS Services:* Amazon SNS publishing to multiple Amazon SQS queues.
* **Tightly Coupled vs. Loosely Coupled:** Designing systems using queues or buffers so that if one component fails, the rest of the system survives.
    * *AWS Services:* Amazon SQS, Amazon SNS, Amazon MQ.

## 🔄 2. Communication & Fault Tolerance (Tasks 1 & 2)
* **Synchronous vs. Asynchronous:** * *Sync:* Waiting for a response (HTTP API). -> *Amazon API Gateway, Application Load Balancer*
    * *Async:* Dropping off a task for later processing. -> *Amazon SQS, AWS Lambda (async invocation)*
* **Retries with Exponential Backoff and Jitter:** When an API fails, retrying immediately can crash the server. *Exponential backoff* increases the wait time between retries. *Jitter* adds randomness to prevent multiple clients from retrying at the exact same millisecond.
    * *AWS Services:* AWS SDKs (built-in feature), AWS Step Functions.
* **Dead-Letter Queues (DLQs):** A holding area for messages or events that repeatedly fail to process, saving them for manual inspection so they aren't lost forever.
    * *AWS Services:* Amazon SQS, AWS Lambda Destinations.
* **Idempotency:** Ensuring that if a request is accidentally duplicated (e.g., a network blip causes a retry), the end result remains the same (e.g., not charging a credit card twice).
    * *AWS Services:* AWS Lambda (handling SQS retries), AWS SDKs (Idempotency tokens).

## ⚡ 3. Serverless Execution & Networking (Task 2)
* **Stateful vs. Stateless:** Stateless applications do not store local data between requests. This is crucial for cloud scalability because any server/container can handle any request.
    * *AWS Services:* AWS Lambda, Amazon Fargate.
* **Event Source Mapping:** A Lambda feature that continuously polls a stream or queue and automatically triggers your function when records are found.
    * *AWS Services:* AWS Lambda polling Amazon Kinesis, Amazon SQS, or DynamoDB Streams.
* **VPC Access from Serverless:** Understanding how a serverless function securely accesses private resources (like a database) that are locked inside a Virtual Private Cloud (VPC).
    * *AWS Services:* AWS Lambda (VPC configuration, Elastic Network Interfaces - ENIs, Security Groups).

## 🗄️ 4. Data Stores & Consistency (Task 3)
* **Relational vs. Non-Relational (NoSQL):** * *Relational:* Strict schemas, complex joins, scaling vertically (SQL). -> *Amazon RDS, Amazon Aurora*
    * *Non-Relational:* Flexible schemas, key-value pairs, scaling horizontally infinitely. -> *Amazon DynamoDB*
* **High-Cardinality Partition Keys:** Choosing a database key that distributes data evenly across many physical partitions (e.g., using `UserID` instead of `Status=Active`) to prevent "hot partitions" and throttling.
    * *AWS Services:* Amazon DynamoDB.
* **Database Consistency Models:**
    * *Strongly Consistent:* A read always returns the most recent, up-to-date write (slower, more expensive).
    * *Eventually Consistent:* A read might return slightly stale data, but is faster and cheaper (default for NoSQL).
    * *AWS Services:* Amazon DynamoDB (Read capacity settings).
* **Query vs. Scan Operations:**
    * *Query:* Extremely fast, highly targeted search using the primary key.
    * *Scan:* Reads the *entire* table to find data. Slow, expensive, and generally an anti-pattern.
    * *AWS Services:* Amazon DynamoDB.
* **Ephemeral vs. Persistent Data Storage:**
    * *Ephemeral:* Temporary storage that is wiped out when the compute environment shuts down. -> *AWS Lambda `/tmp` directory, EC2 Instance Store.*
    * *Persistent:* Data that survives compute shutdowns. -> *Amazon S3, Amazon EBS, Amazon EFS.*

## 🚀 5. Performance & Caching Strategies (Task 3)
* **Write-Through Caching:** Data is written into the cache and the database at the same time. Pro: Cache is always up-to-date. Con: Writes take longer.
* **Lazy Loading (Read-Through):** The application checks the cache first. If it's a "miss," it fetches from the database, and *then* writes it to the cache for next time.
* **Time-to-Live (TTL):** Setting an expiration timer on cached data or database rows so they automatically delete themselves when they become stale or irrelevant.
    * *AWS Services for Caching:* Amazon ElastiCache (Redis/Memcached), Amazon DynamoDB Accelerator (DAX), Amazon API Gateway caching.