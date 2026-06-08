# Integration Strategy, Patterns, Molecules and Implementation Translation — Processed Summary

## Purpose of this note

This note summarises the attached Integration Strategy documents and captures the method used to move from enterprise integration principles, to design-language molecules, to high-level integration patterns, and finally to low-level cloud implementation designs.

It is intentionally descriptive rather than evaluative. It does not yet answer whether the same approach should be applied to the Data Lake space; it prepares the ground for that analysis.

## Documents processed

| Document | Main content processed |
|---|---|
| Integration strategy | Core technical concepts: producer/consumer split, Enterprise Data Model, enterprise endpoints, molecules/patterns, cloud infrastructure and automated deployment. |
| Integration principles | Strategic principles: integrated by design, experience-led, UCL-native integration. |
| Producer and consumer flows | Rationale for splitting point-to-point integration into producer and consumer flows; producer/consumer responsibilities and principles. |
| Enterprise endpoints | Enterprise APIs and enterprise distribution channels as the two publication mechanisms. |
| Enterprise Data Model (EDM) | Common language, canonical model, ownership roles and EDM creation steps. |
| Integration patterns | Catalogue of application/system integration patterns, producer patterns, consumer patterns and ingestion patterns. |
| Molecule Catalogue | Reusable low-level design-language building blocks. |
| M6: Fetch and Combine | Example molecule, including purpose, logging, error handling, AWS implementation and Azure implementation. |
| Producer Pattern 1 | Example full integration pattern showing how molecules combine into a pattern and how the pattern translates into AWS and Azure physical implementation. |

---

# 1. Core integration strategy

## 1.1 Strategic move

The Integration Strategy introduces a deliberate move away from point-to-point integration towards a standardised, reusable, producer/consumer-based model.

The core concepts are:

- separation of point-to-point integration into two distinct flows: **producer** and **consumer**;
- use of a mandatory common language that abstracts system-specific definitions: the **Enterprise Data Model**;
- two methods of producing enterprise information through enterprise endpoints:
  - **Enterprise API**;
  - **Enterprise distribution channel**;
- reusable building blocks that simplify the design and implementation of integration flows:
  - **molecules**;
  - **patterns**;
- definition of the required infrastructure components and code for automated provisioning and de-provisioning of integration infrastructure.

The strategy therefore has three connected layers:

1. **Conceptual architecture** — producer/consumer, EDM, enterprise endpoints.
2. **Design language** — molecules and patterns.
3. **Physical implementation** — cloud-native components, automation, deployment and operational support.

## 1.2 Core architecture model

The integration platform sits between producing systems and consuming systems.

The high-level model includes:

- systems that produce or consume data;
- producer flows that expose enterprise data;
- consumer flows that retrieve or receive enterprise data;
- a catalogue layer for endpoint discovery and description;
- security controls for authentication and authorisation;
- monitoring/logging controls;
- deployment and testing automation.

The important architectural idea is that systems do not integrate directly with each other. Instead:

- producers publish enterprise-shaped information;
- consumers consume enterprise-shaped information;
- the integration platform mediates between systems using standard design and implementation components.

---

# 2. Integration principles

## 2.1 Integrated by design

The principle of **Integrated by design** means that integration endpoints are a first-class output of product and platform teams.

The documents describe this as:

- integration endpoints should evolve with the product;
- maximum integration coverage should be available for necessary system functionality and data;
- system capability and data should be exposed using automated integration processes, such as APIs, calls and channel subscriptions;
- ingestion and exposure points should be abstracted so integrations are resilient to changes such as field renames, new connectors, direct database connections, emails or manual file sharing;
- modern, technology-agnostic standards should be used, such as REST, OAuth and webhooks;
- technology-specific native connectors should be avoided where possible if they reduce interoperability.

The underlying intent is to stop integration being treated as an afterthought or bespoke add-on.

## 2.2 Experience-led

The principle of **Experience-led** means that integration should support seamless user journeys and timely updates across systems.

The documents highlight:

- business need should drive the integration approach;
- where appropriate, real-time integration is preferred over batch integration;
- low latency is important where integration directly affects user experience;
- asynchronous integration may be preferred where it improves experience, scalability or resilience.

The key point is that integration design is not purely technical. The choice between API, event, batch, synchronous or asynchronous flow depends on the user and business experience being enabled.

## 2.3 UCL-native

The principle of **UCL-native** means that integrations should work with UCL’s standards, systems and operational model.

The documents describe several implications:

- consumers should call enterprise APIs directly from the product where possible, rather than requiring additional integration wrappers;
- consumers should subscribe directly to message channels where possible, rather than requiring UCL-specific customisations;
- fail-safe defaults should be used to prevent propagation of bad changes, mass deletes, erroneous data generation or denial-of-service-style impacts;
- integrations should rely on flexible endpoints that fit UCL’s existing standards without heavy additional complexity.

This principle is important because the architecture is not just about choosing cloud services. It is about making integration sustainable in the UCL operating environment.

---

# 3. Producer and consumer flows

## 3.1 Problem with the historical model

The previous UCL integration model is described as heavily point-to-point. This created several problems:

- unclear ownership;
- poorly maintained integrations;
- lack of reusable integration capability;
- many systems and applications requiring similar data;
- significant duplication of effort;
- expensive maintenance and support;
- slow delivery because each integration was treated as a separate technical problem;
- reliance on outdated technologies.

The strategy addresses this by splitting integration into two separate concerns:

- **producer flow** — exposing enterprise information from a system;
- **consumer flow** — consuming enterprise information into another system.

## 3.2 Producer flow

A producer flow is the instantiation of a producer pattern. It contains the functions required to publish system data for another enterprise service to use.

The endpoint produced by a producer flow is either:

- an **Enterprise API**; or
- an **Enterprise distribution channel**.

The producing product/platform team owns the complete producer flow. That includes:

- design;
- development;
- testing;
- deployment;
- support;
- improvement;
- maintenance;
- all data transformation needed to expose the data in enterprise form.

The producer flow is intentionally technology-agnostic from the consumer’s perspective. It hides the producing system’s internal implementation and exposes information through a common enterprise language.

## 3.3 Producer principles

The producer principles include:

1. **Prefer simple integrations**  
   Large numbers of components may introduce unnecessary complexity. Complex integrations are more expensive to develop, test and maintain.

2. **Follow standards**  
   Endpoints must follow defined standards such as naming conventions and URL structures.

3. **Design with reusability in mind**  
   Integration is focused on data, not on applications. The producer should be agnostic to the underlying application and should expose reusable enterprise resources.

4. **Encapsulate**  
   Producer endpoints should be designed to serve all possible consumers. The producer must not customise endpoints for each consumer-specific requirement.

5. **Secure by design**  
   Sensitive information must be protected and only accessible to consumers with the required authorisation.

6. **Use system capabilities**  
   The producer should use native system capabilities where they support the required integration approach.

7. **Use the common language**  
   Producers must follow the common language defined by the Enterprise Data Model so that consuming systems remain system-agnostic.

8. **Ensure data quality**  
   The producer team is responsible for publishing quality data.

9. **Clear ownership**  
   The producer team owns the complete producer flow, including design, development, testing, deployment, support, improvement and maintenance.

10. **Data is transient**  
   Data in the integration platform is used only for transfer between systems and is not retained after the transfer is complete.

11. **Lowest latency**  
   Data should be published with the lowest viable latency using the most appropriate connectors and adapters.

## 3.4 Consumer flow

A consumer flow is the instantiation of a consumer pattern. It contains the functions required to retrieve information from an enterprise endpoint and update a system component.

The starting point of a consumer flow is always:

- an Enterprise API; or
- an Enterprise distribution channel.

The consuming team owns the complete consumer flow, including all consumer-specific logic and transformations.

## 3.5 Consumer principles

The consumer principles include:

1. **API for front-end**  
   APIs are preferred over distribution channels for application integrations requiring synchronous communication.

2. **Channel for data sync**  
   Distribution channels are preferred over APIs for system integrations requiring asynchronous communication.

3. **Encapsulate**  
   Consumers must encapsulate all consumer-specific business and transformation logic within the consumer flow.

4. **Clear ownership**  
   The consumer team owns the complete consumer flow: design, development, testing, deployment, support, improvement and maintenance.

5. **Use system capabilities**  
   The consumer should only use integration platform capabilities when the consumer system cannot support them itself.

6. **Netiquette**  
   Consumers must follow guidelines and standards to minimise load, impact and cost to the producer.

---

# 4. Enterprise endpoints

## 4.1 Why enterprise endpoints are needed

The Enterprise endpoints documents describe several challenges:

- extensive use of old technologies, such as DS links and ETL tools, for integration flows;
- over-reliance on ad hoc scripts for deploying interfaces;
- high cost and effort for real-time processing when patterns are not fully supported;
- labour-intensive delivery and support.

Enterprise endpoints address these issues by creating reusable, standard publication mechanisms.

## 4.2 Two endpoint types

The strategy defines two enterprise endpoint types:

1. **Enterprise APIs**
2. **Enterprise distribution channels**

These two methods are complementary, not competing.

## 4.3 Enterprise APIs

Enterprise APIs provide synchronous communication.

Key characteristics:

- based on REST architectural style;
- expose resources through a predefined set of operations;
- generally request/response;
- consumers expect an immediate synchronous response;
- recommended for application integration;
- expose data that conforms to the Enterprise Data Model;
- provide a common language understood by both producers and consumers;
- expected to follow standard authentication and authorisation approaches.

Enterprise APIs are therefore best suited to front-end, process or transactional scenarios where a consuming system needs to ask for something and receive a response immediately.

## 4.4 Enterprise distribution channels

Enterprise distribution channels provide asynchronous communication.

Key characteristics:

- communication service for delivering notifications or messages to specific consumers;
- common data format based on the Enterprise Data Model;
- consumers subscribe to distribution channels and are notified of new events;
- recommended for system integration;
- expose data that conforms to the Enterprise Data Model;
- provide a common language understood by producers and consumers;
- expected to follow standard authentication and authorisation approaches.

Enterprise distribution channels are therefore best suited to event-driven integration, data synchronisation and decoupled system-to-system communication.

---

# 5. Enterprise Data Model (EDM)

## 5.1 Purpose of the EDM

The Enterprise Data Model provides the common language for integration.

The documents describe its purpose as enabling UCL to:

- standardise concepts and enterprise entities across applications;
- standardise definitions across applications;
- drive application design;
- seed data models for new applications;
- provide a grand data model as a basis for application integration and data exchange.

The EDM is also referred to as the **Canonical Data Model**.

## 5.2 Why the EDM matters to integration

Systems and applications usually use their own terminology. If each integration exposes the source system’s terminology directly, consumers become tightly coupled to source systems.

The EDM addresses this by forcing a translation from source-specific language into enterprise language.

This produces several benefits:

- information is exposed using clear, understandable names;
- producer and consumer systems are decoupled;
- changes to source systems are less likely to require changes in consumer flows;
- consumers can understand enterprise data without needing to understand every source system;
- APIs and distribution channels become reusable enterprise assets rather than system-specific interfaces.

## 5.3 EDM creation approaches

The documents describe three possible approaches:

1. **Top-down approach**  
   Create the entire enterprise data model at once.

2. **Hybrid approach**  
   Define entities in the enterprise data model as and when they are needed.

3. **Bottom-up/incremental approach**  
   Create the entire data model from scratch through iterative discovery.

Product and platform teams are empowered to choose the appropriate approach for their use case.

## 5.4 EDM roles

The documents define several roles around data ownership and modelling.

### Data Owner

The Data Owner has the final and authoritative say on how data is represented. They are accountable for:

- data quality;
- policies and processes governing the data;
- whether the data is correct and represents the intended information;
- decisions around where sensitive and private information can sit.

They may not be involved in day-to-day data management, but they are accountable for correctness, privacy and representation.

### Data Steward

The Data Steward supports the Data Owner at a practical level. They are responsible for day-to-day oversight of data and are likely to be involved in:

- process implementation;
- data usage decisions;
- practical data quality questions;
- decisions around whether data should be made available and under what circumstances.

### Data Custodian

The Data Custodian is the technical role, usually within ISD, responsible for ensuring systems can provide the level of management required by business need.

They are pivotal when mapping between physical data and conceptual data model definitions.

### Data Modeller

The Data Modeller is responsible for:

- defining new entities;
- defining attributes;
- defining cardinality;
- updating and maintaining the EDM in ER Studio.

## 5.5 EDM creation steps

When creating or extending the EDM, the process includes:

1. understand and define the subject area;
2. understand and define the entities in the subject area;
3. define cardinality and relationships between entities;
4. define logical data model attributes;
5. update the relevant domain data model in ER Studio.

These steps are part of workshops run by the Enterprise Data Modeller.

---

# 6. Molecules as design-language building blocks

## 6.1 What a molecule is

A molecule is a reusable, low-level integration building block.

It represents a standard functional unit that can be combined with other molecules to create a complete producer, consumer or ingestion pattern.

A molecule is not the full integration pattern. It is a smaller design-language element that describes a repeatable technical capability, such as:

- receiving a request;
- validating a request;
- fetching data;
- transforming payloads;
- sending to an API;
- sending to a channel;
- routing;
- watermarking;
- handling events;
- converting payloads;
- saving or reading files.

In other words, molecules are the reusable verbs of the integration design language.

## 6.2 Why molecules are useful

Molecules provide a controlled vocabulary for architects and engineers.

They allow high-level designs to be described consistently without immediately specifying every implementation detail.

They also create a bridge between:

- high-level logical architecture; and
- low-level AWS or Azure implementation.

A solution can therefore be described as a combination of molecules first, then translated into implementation components later.

## 6.3 Molecule catalogue

The Molecule Catalogue includes the following molecules:

| # | Molecule | Purpose |
|---:|---|---|
| 1 | API Gateway Event | Receives an on-change event as a webhook and persists the message for greater reliability. |
| 2 | API Gateway Request | Receives an HTTP request from API consumers and performs validation. |
| 3 | Connect to Enterprise Data Model | Receives system-specific data payloads and transforms them into Enterprise Data Model format; may require one-to-one mapping and/or transformation from XML/JSON/CSV into XML/JSON. |
| 4 | Convert to System Data Model | Transforms incoming enterprise-model payloads into system-specific payloads; may transform JSON/XML/AVRO/CSV. |
| 5 | Copy File | Copies a file from one object store bucket/container to another. |
| 6 | Fetch and Combine | Calls multiple external services in sequence or in parallel and aggregates their responses into a single payload. |
| 7 | Fetch Event | Receives events from Enterprise Channel and performs validation where applicable. |
| 8 | Fetch from System | Connects to a backend system, sends a request, receives and authenticates the response. |
| 9 | Read File | Reads a file from cloud storage such as AWS S3 or Azure Blob. |
| 10 | Receive Request | Receives incoming requests from Enterprise API and performs validation where applicable. |
| 11 | Request Data | Triggers an HTTP request for data from a producer. |
| 12 | Respond to Gateway Event | Informs the storage queue about processing outcome. |
| 13 | Routing | Handles routing of data based on business rules. |
| 14 | Save File | Saves a file in cloud storage such as AWS S3 or Azure Blob. |
| 15 | Scheduled Event | Acts as a scheduled service trigger. |
| 16 | Send to API | Appends the EDM payload into the Enterprise response structure and sends it over Enterprise API. |
| 17 | Send to Channel | Publishes messages to an Enterprise Channel in reliable mode. |
| 18 | Send to System | Sends a payload to a destination system. |
| 19 | Two Phase Create | Implements a two-phase create pattern for creating a new object. |
| 20 | Watermark | Handles fetching and updating timestamp information needed to manage real-time data streaming. |
| 21 | Convert to Ingestion Payload | Converts records into the format accepted by the Ingestion API. |

The catalogue shows that the integration design language is deliberately granular. It decomposes integration into repeatable functions that can be assembled into larger patterns.

---

# 7. Example molecule: M6 Fetch and Combine

## 7.1 Purpose

Molecule 6, **Fetch and Combine**, is responsible for connecting to multiple other systems or services and aggregating their responses into a single payload.

The document describes two variants:

- connecting to multiple services in sequence;
- connecting to multiple services in parallel.

The molecule is useful for enrichment scenarios where the integration flow needs to collect additional information from other sources, typically APIs.

## 7.2 Design intent

The molecule centralises orchestration and consolidation logic in the function.

That means the pattern can state that it needs to “fetch and combine” without each design having to re-invent:

- how multiple calls are orchestrated;
- how responses are aggregated;
- how errors are managed;
- how logging is produced;
- how the cloud implementation should be structured.

## 7.3 Security

The molecule expects the consumer system to implement some level of authentication.

Examples in the document include:

- Basic Authentication;
- OAuth 2.0;
- JWT token validation.

## 7.4 Logging

The molecule includes a standard logging approach.

The example logging structure includes fields such as:

- timestamp;
- severity;
- information/message;
- class;
- correlation or request identifier.

The document shows logging being appended from the service/system call into the payload or log stream.

## 7.5 Error handling

The molecule includes a standard error-handling approach.

Client errors such as bad requests should produce an error response to the requester, including an error-handling integration service or equivalent response model.

Server errors such as gateway timeout should be retried where appropriate, with a reference to best-practice retry patterns.

## 7.6 AWS implementation

The AWS implementation shows:

- cloud monitoring through CloudWatch;
- compute service through Lambda;
- sequential execution against System A and System B;
- aggregated response handling.

The physical sequence is:

1. Lambda calls System A and System B sequentially.
2. The outcome of processing is logged into CloudWatch.

## 7.7 Azure implementation

The Azure implementation shows:

- monitoring through Azure Monitor;
- compute service through Azure Function;
- sequential execution against System A and System B;
- aggregated response handling.

The physical sequence is:

1. Azure Function calls System A and System B sequentially.
2. The outcome of processing is logged into Azure Monitor.

## 7.8 What this proves about the method

M6 demonstrates the pattern-to-implementation translation clearly:

- the same molecule exists independently of cloud provider;
- the molecule has a stable logical purpose;
- the molecule has security, logging and error-handling expectations;
- the physical implementation changes depending on AWS or Azure;
- the design language remains consistent even when implementation technology changes.

---

# 8. Integration patterns

## 8.1 Pattern categories

The Integration Patterns page groups patterns into several categories:

1. **Consumer patterns**
2. **Producer patterns**
3. **Ingestion patterns**

The page distinguishes between application integration and system integration.

### Application integration

Application integration is described as providing data to applications in the user session space, such as user-system integration.

### System integration

System integration is described as keeping data synchronised between systems. Business processes in one system maintain data mastered in another system or application. Usually, enterprise distribution channels are used to keep data synchronised.

## 8.2 Consumer patterns

The listed consumer patterns are:

| Pattern | Name |
|---|---|
| Pattern 0 | Direct |
| Pattern 1 | Enterprise Channel to System: Synchronous |
| Pattern 2 | Enterprise Channel to System: Asynchronous |
| Pattern 4 | Enterprise API to System: Synchronous |
| Pattern 4 | Enterprise API to System: Asynchronous |

Note: the source page appears to use Pattern 4 twice for the API-to-system synchronous/asynchronous entries. That may be a page numbering issue rather than an architectural distinction.

## 8.3 Producer patterns

The listed producer patterns are:

| Pattern | Name |
|---|---|
| Pattern 1 | System to Enterprise API: on-demand synchronous |
| Pattern 2 | System to Enterprise Channel: batch asynchronous |
| Pattern 3 | System to Enterprise Channel: on-change synchronous |

## 8.4 Ingestion patterns

The listed ingestion patterns are drafts:

| Pattern | Name |
|---|---|
| Pattern 1 | Database to Ingestion API — draft |
| Pattern 2 | API to Ingestion API — draft |
| Pattern 3 | DMS to Ingestion API — draft |

The important point is that ingestion was already being treated as a pattern category, separate from application/system integration.

---

# 9. Example full pattern: Producer Pattern 1

## 9.1 Pattern identity

Producer Pattern 1 is titled:

**System to Enterprise API: on-demand synchronous**

It is used to send requested data to consumers over Enterprise API synchronously.

Metadata captured in the pattern includes:

- name;
- description;
- version;
- status;
- date;
- classification;
- source type;
- target type;
- invocation style;
- whether it is a core pattern;
- tags.

This is important because it shows that patterns are treated as managed architecture assets, not informal diagrams.

## 9.2 Pattern description

In this pattern:

- the consumer sends a request to the producer gateway using the enterprise standard authentication model;
- the response from the source system is translated into the Enterprise Data Model;
- the response is returned to the Enterprise API;
- the result occurs in a single session and standard response.

Example use cases include:

- an application integration where the sign-in session requires logging into Inside UCL to book annual leave;
- an enrichment scenario where UCL photo ID app retrieves additional information about a student by calling the Student API.

## 9.3 Solution overview

The solution diagram shows a pattern assembled from molecules across phases.

The high-level phases are:

1. **Connector**
2. **Processing**
3. **Trigger**
4. **Publish**

The pattern combines the following molecules:

### Trigger phase

- **API Gateway Request**

### Processing phase

- **Connect to System Data Model**
- **Connect to Enterprise Data Model**

Extension molecules:

- **Save File**
- **Fetch and Combine**

### Publish phase

- **Send to API**

### Connector phase

- **Fetch from System**

Extension molecules:

- **Send to System**

This shows how molecules act as the internal grammar of the pattern. The pattern is not drawn directly as AWS Lambda or Azure Function. It is first expressed as a reusable molecular composition.

## 9.4 Non-functional requirements

The pattern includes non-functional requirements.

### Observability

The standard logging framework must be adopted. Correlation IDs are used for traceability.

### Reconciliation

This pattern publishes data to the Enterprise API, meaning communication is completed in the same transaction between producer and consumer. No reconciliation process is required.

### Reliability

The retry approach is being incorporated, with responsibility placed on the caller/requester when data or response has not been communicated.

### SLA

The document references a UCL SLA guideline.

### Volumes

The pattern is designed to support application and fine-tuned usage based on compute service concurrency.

It requires throughput testing against usage expectations to finalise concurrency configuration.

### Operational limits

The design lists operational limits such as maximum payload request size and maximum supported runtime for AWS and Azure variants.

## 9.5 Security considerations

Security considerations include:

- AWS communication using IAM permissions implemented via Terraform;
- cross-cloud communication using OpenID Connect;
- cross-account communication within the same cloud using OpenID Connect.

## 9.6 AWS physical implementation

The AWS implementation maps the logical pattern to:

- API Gateway;
- Lambda;
- CloudWatch;
- Secrets Manager;
- external/system endpoint;
- Enterprise API.

The physical sequence is:

1. Producer API Gateway receives the request from the consumer and validates the JWT token and request.
2. API Gateway forwards the request to Lambda for request validation and further processing.
3. Lambda checks the downstream system credentials from Secrets Manager.
4. Lambda fetches the data and connects to the system.
5. The system processes the request and responds synchronously.
6. The system response is logged into CloudWatch.
7. The response is mapped and transformed.
8. The response is sent back to API Gateway.
9. API Gateway sends the response back to the consumer in the same session.

## 9.7 Azure physical implementation

The Azure implementation maps the logical pattern to:

- API Management;
- Azure Function;
- Azure Monitor;
- Key Vault;
- external/system endpoint;
- Enterprise API.

The physical sequence is:

1. Producer API Management receives the request from the consumer and validates the JWT token and request.
2. API Management forwards the request to Azure Function for further processing.
3. Azure Function fetches the downstream system credentials from Key Vault.
4. Azure Function checks logic to connect and call the downstream system.
5. The system processes the request and responds synchronously.
6. The outcome is logged in Azure Monitor.
7. The response is mapped back from Azure Function to API Management.
8. API Management sends the response back to the consumer in the same session.

## 9.8 What Producer Pattern 1 demonstrates

Producer Pattern 1 demonstrates the full architectural chain:

1. A business/integration scenario is identified.
2. The scenario is classified as a producer pattern.
3. The pattern is expressed as a high-level molecule diagram.
4. The molecule composition defines the logical design.
5. The logical pattern carries NFRs, security and operational considerations.
6. The same logical pattern is translated into AWS physical components.
7. The same logical pattern is translated into Azure physical components.
8. The implementation can then be standardised and automated.

This is the clearest example of how the integration approach moves from architectural language to executable implementation.

---

# 10. The overall method used in Integration

## 10.1 Layered architecture method

The Integration approach uses a layered method:

| Layer | Role | Example from documents |
|---|---|---|
| Principle | Defines the design intent and constraints | Integrated by design; experience-led; UCL-native |
| Strategic construct | Defines the enterprise model | Producer/consumer split; EDM; enterprise endpoints |
| Endpoint type | Defines publication mechanism | Enterprise API; Enterprise distribution channel |
| Pattern category | Groups reusable scenarios | Producer, consumer, ingestion |
| Pattern | Defines a reusable solution structure | Producer Pattern 1: System to Enterprise API on-demand synchronous |
| Molecule | Defines reusable design-language building block | Fetch and Combine; Send to API; Connect to EDM |
| Physical implementation | Maps molecules/patterns to technology | AWS Lambda/API Gateway/CloudWatch/Secrets Manager; Azure Function/APIM/Azure Monitor/Key Vault |
| Automation | Makes implementation repeatable | Infrastructure and code for automated provisioning/de-provisioning |

## 10.2 Design-language grammar

The design language works like a grammar:

- **Molecules** are the verbs or reusable functional units.
- **Patterns** are repeatable sentences made from molecules.
- **Producer/consumer/ingestion categories** are the pattern families.
- **Enterprise APIs and distribution channels** are the publication mechanisms.
- **EDM** is the common vocabulary used in the payloads.
- **AWS/Azure implementations** are physical translations of the same logical sentence.

This is the key intellectual structure of the integration work.

## 10.3 Separation of concerns

The approach separates:

- business scenario from technical implementation;
- producer responsibility from consumer responsibility;
- system-specific model from enterprise model;
- synchronous request/response from asynchronous event distribution;
- high-level pattern from low-level cloud implementation;
- reusable architecture asset from one-off project delivery.

This separation is what allowed complexity to be hidden and standardised.

## 10.4 How complexity is hidden

Complexity is hidden in several ways:

1. **Consumers do not need to understand source systems**  
   They consume EDM-shaped APIs or channels.

2. **Producers do not build for each consumer**  
   They expose reusable enterprise endpoints.

3. **Architects use molecule diagrams rather than raw cloud diagrams first**  
   This allows high-level designs to be discussed without becoming trapped in implementation details too early.

4. **Engineers implement known molecule mappings**  
   For example, Fetch and Combine maps to Lambda/CloudWatch in AWS or Function/Azure Monitor in Azure.

5. **Patterns carry standard NFRs and security assumptions**  
   Reconciliation, observability, security, reliability and operational limits are not reinvented each time.

6. **Automation can provision standard infrastructure**  
   Once the pattern and molecules are known, the infrastructure can be repeatable.

---

# 11. Key reusable concepts to preserve

The following concepts appear central to the success of the Integration approach and should be preserved as concepts before any Data Lake comparison is attempted.

## 11.1 Common language

Integration uses the EDM as a common enterprise language. This enables consistency, decoupling and reuse.

## 11.2 Producer/consumer accountability

The approach is not just technical. It defines who owns what:

- producer owns producer flow and published data quality;
- consumer owns consumer flow and consumer-specific transformations;
- data owner/steward/custodian/modeller support enterprise data meaning and quality.

## 11.3 Pattern catalogue

Patterns are reusable architecture assets with:

- classification;
- description;
- use cases;
- solution overview;
- molecule composition;
- NFRs;
- security considerations;
- physical implementation;
- operational considerations.

## 11.4 Molecule catalogue

Molecules are reusable lower-level components that allow patterns to be assembled consistently.

They are small enough to be implementation-reusable but abstract enough to be technology-portable.

## 11.5 Technology translation layer

The same molecule or pattern can have more than one technology translation:

- AWS implementation;
- Azure implementation;
- potentially other platform implementations.

This avoids hard-coding the design language to one cloud platform.

## 11.6 Operationalisation

Patterns include operational concerns from the beginning:

- logging;
- monitoring;
- error handling;
- retries;
- reconciliation;
- security;
- payload limits;
- runtime limits;
- volume considerations;
- SLA references.

## 11.7 Automation and standard deployment

The strategy explicitly connects reusable architecture with automated provisioning and de-provisioning. This means patterns are not just guidance; they are intended to become deployable, repeatable infrastructure and code structures.

---

# 12. Condensed model of the Integration approach

The Integration approach can be summarised as:

```text
Principles
  ↓
Strategic constructs
  - Producer / Consumer split
  - Enterprise Data Model
  - Enterprise endpoints
  ↓
Pattern families
  - Producer patterns
  - Consumer patterns
  - Ingestion patterns
  ↓
Specific pattern
  - e.g. System to Enterprise API: on-demand synchronous
  ↓
Molecule composition
  - API Gateway Request
  - Fetch from System
  - Connect to System Data Model
  - Connect to Enterprise Data Model
  - Fetch and Combine
  - Send to API
  ↓
Non-functional and governance wrapper
  - Security
  - Observability
  - Reconciliation
  - Reliability
  - SLA
  - Volume
  - Operational limits
  ↓
Physical implementation
  - AWS mapping
  - Azure mapping
  ↓
Reusable implementation / automation
  - Infrastructure components
  - Code components
  - Provisioning and de-provisioning
```

---

# 13. Initial implications to carry forward for Data Lake analysis

The documents suggest that the Data Lake comparison should not start by asking: “What are the Fabric components?”

It should start by asking:

1. What are the equivalent **principles** for Data Lake and RAM?
2. What are the equivalent strategic constructs?
3. What is the equivalent of producer/consumer in the Data Lake context?
4. What is the equivalent of an enterprise endpoint?
5. What is the equivalent of the EDM/common language?
6. What are the Data Lake pattern families?
7. What are the Data Lake molecules?
8. How do high-level data patterns translate into Fabric/Purview low-level implementations?
9. Which parts can become standard code with configuration?
10. What governance, operational and assurance wrappers must be embedded in every pattern?

These questions should be answered in the next stage, using this Integration method as the reference model.

---

# 14. One-line essence

The Integration approach succeeded because it created a reusable design language — principles, producer/consumer flows, EDM, enterprise endpoints, patterns and molecules — and then translated that language into repeatable AWS/Azure implementations with standard operational controls.
