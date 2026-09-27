# Alensi Insure

### Intelligent Insurance Policy & Claims Management System

Alensi Insure is an intelligent insurance platform for managing the policy and claims lifecycle while using AI-assisted decision support to help insurance teams assess risk, validate coverage, triage claims, process documents, and identify potential anomalies.

The platform combines deterministic insurance business rules with AI capabilities, keeping critical decisions explainable, auditable, and subject to human review.

## Overview

Alensi Insure focuses on the core insurance lifecycle:

```text
Customer
   ↓
Insurance Product
   ↓
Policy
   ↓
Coverage
   ↓
Premium
   ↓
Payment
   ↓
Claim
   ↓
Assessment
   ↓
Decision Support
   ↓
Approval / Rejection
   ↓
Settlement
```

A customer holds one or more policies with configured coverage. Coverage and policy terms determine applicable premiums and benefits. When an insured event occurs, a claim can be submitted and assessed.

The platform combines traditional domain rules with an intelligence layer that assists users throughout the claims and policy lifecycle.

## Core Capabilities

### Customer Management

* Customer profiles
* Contact information
* Customer-policy relationships
* Customer claims history
* Customer document management
* Customer activity history

### Policy Management

* Insurance product configuration
* Policy creation and issuance
* Policy lifecycle management
* Coverage configuration
* Policy renewals
* Policy cancellation
* Policy status tracking
* Policy document management

### Coverage Management

* Coverage definitions
* Coverage limits
* Deductibles
* Exclusions
* Eligibility rules
* Coverage validation

### Premium Management

* Premium calculation
* Premium schedules
* Payment tracking
* Outstanding premium tracking
* Premium adjustments

### Claims Management

* Claim submission
* Claim lifecycle management
* Supporting document management
* Coverage validation
* Claim assessment
* Claim approval and rejection
* Claim settlement
* Claim history and audit trail

### Workflow & Approvals

* Configurable claim workflows
* Role-based approval
* Manual review queues
* Escalation
* Approval and rejection decisions
* Decision audit trails

## Intelligence Layer

AI is treated as a decision-support capability rather than a replacement for the insurance domain engine.

```text
                    Alensi Insure
                         │
             ┌───────────┴───────────┐
             │                       │
       Policy Engine          Claims Engine
             │                       │
             └───────────┬───────────┘
                         ↓
                 Intelligence Layer
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
   Risk Assessment   Claim Triage    Anomaly Detection
        │                │                │
        ├────────────────┼────────────────┤
        ↓                ↓                ↓
 Document Processing   RAG          Decision Support
                         │
                         ↓
                    Human Review
```

The intelligence layer will use AI where it provides meaningful value while leaving deterministic business rules responsible for critical policy and claims decisions.

## AI-Assisted Capabilities

### Intelligent Claims Triage

When a claim is submitted, the platform can analyze the available information and assist with:

* Claim categorization
* Priority assessment
* Missing-information detection
* Coverage-related context
* Complexity assessment
* Recommended review path
* Manual-review recommendations

The output is a recommendation for the claims team rather than an automatic final decision.

### Coverage Validation

The platform can combine deterministic policy rules with retrieved policy information to help answer questions such as:

> Is this incident covered under the customer's policy?

The AI layer can retrieve relevant policy documents and clauses using Retrieval Augmented Generation (RAG), while the policy engine remains responsible for authoritative coverage rules.

Spring AI provides abstractions for RAG and vector stores, allowing relevant policy information to be retrieved before generating a response.

### Intelligent Risk Assessment

The system can generate an explainable risk assessment using factors such as:

* Claim characteristics
* Policy history
* Customer history
* Claim frequency
* Claim amount
* Policy coverage
* Supporting documentation
* Historical patterns

Example:

```json
{
  "riskScore": 72,
  "classification": "REVIEW_REQUIRED",
  "reasons": [
    "Claim amount is significantly above historical average",
    "Multiple claims were submitted within a short period",
    "Supporting documentation is incomplete"
  ],
  "requiresManualReview": true
}
```

AI-generated assessments remain recommendations and are recorded with their reasoning and supporting evidence.

### Anomaly Detection

The platform can identify potentially unusual patterns such as:

* Duplicate claims
* Repeated claims for similar incidents
* Unusual claim frequency
* Abnormally high claim amounts
* Inconsistent customer information
* Suspicious timing patterns
* Relationships between related entities

The system flags anomalies for investigation rather than automatically determining that fraud has occurred.

### Intelligent Document Processing

Insurance workflows frequently involve documents such as:

* Policy documents
* Identification documents
* Medical reports
* Accident reports
* Receipts
* Invoices
* Assessment reports

The intelligence layer can extract structured information from documents and make it available to downstream workflows.

For example:

```text
Document
   ↓
Extraction
   ↓
Structured Claim Data
   ↓
Validation
   ↓
Claims Workflow
```

### Claims Summarization

The system can generate concise claim summaries for adjusters and reviewers.

A summary can include:

* Customer information
* Policy information
* Incident description
* Coverage information
* Previous claims
* Supporting documents
* Assessment findings
* Outstanding information
* Recommended next action

## Spring AI

Alensi Insure uses **Spring AI** as the integration layer between the Spring Boot application and AI models.

Spring AI provides a portable API across AI model providers together with abstractions for chat models, vector stores, tool calling, advisors, structured output, and MCP.

### ChatClient

The `ChatClient` API provides a fluent Spring-style interface for interacting with AI models and supports synchronous and streaming responses.

Conceptually:

```text
Alensi Insure
      ↓
Spring AI ChatClient
      ↓
AI Model
      ↓
Structured Response
      ↓
Insurance Application
```

### Structured AI Output

AI responses that participate in business workflows should be represented as structured domain objects rather than arbitrary text.

For example:

```java
public record ClaimAssessment(
    String classification,
    int riskScore,
    boolean requiresManualReview,
    List<String> reasons
) {}
```

Spring AI supports mapping model responses to Java types and provider-native structured output where supported.

This allows AI results to flow through normal application validation, persistence, workflow, and audit mechanisms.

### Retrieval Augmented Generation

Policy documents, coverage definitions, claims procedures, and other reference material can be indexed into a vector store.

```text
Policy Documents
       ↓
Document Processing
       ↓
Chunking
       ↓
Embeddings
       ↓
Vector Store
       ↓
Similarity Search
       ↓
Relevant Context
       ↓
AI Model
       ↓
Grounded Response
```

This allows the application to retrieve relevant insurance information before generating an answer rather than relying entirely on the model's existing knowledge.

### Tool Calling

AI capabilities can interact with controlled application services through tool calling.

Potential tools include:

```text
getCustomerPolicy()
getPolicyCoverage()
getClaimHistory()
getClaimDocuments()
checkCoverage()
calculateClaimExposure()
getPaymentHistory()
```

The application remains responsible for executing tools and controlling access to underlying APIs and data. Spring AI's tool-calling architecture explicitly separates model requests from application-side tool execution.

This enables workflows such as:

```text
User
 ↓
AI Assistant
 ↓
Spring AI
 ↓
Tool Call
 ↓
Alensi Insure Service
 ↓
Database / Domain Logic
 ↓
Tool Result
 ↓
AI
 ↓
Structured Response
```

## Human-in-the-Loop

AI recommendations do not automatically become business decisions.

```text
AI Assessment
      ↓
Recommendation
      ↓
Human Review
      ↓
Business Decision
      ↓
Audit Trail
```

The platform is designed so that insurance teams can review:

* AI recommendation
* Risk score
* Supporting reasons
* Retrieved information
* Relevant policy clauses
* Documents considered
* Final human decision

This keeps important insurance decisions explainable and auditable.

## Architecture Principles

### Domain Logic First

Insurance rules remain deterministic and are implemented within the core domain services.

AI supplements the domain rather than replacing it.

### Explainability

AI-generated recommendations should include supporting reasons and relevant context where possible.

### Auditability

AI assessments, recommendations, decisions, and important workflow transitions are recorded for traceability.

### Human Oversight

High-impact decisions can be routed to human reviewers.

### Separation of Concerns

```text
Domain Services
      │
      ├── Policy Rules
      ├── Coverage Rules
      ├── Premium Calculation
      └── Claims Rules

Intelligence Services
      │
      ├── AI Assessment
      ├── RAG
      ├── Document Processing
      ├── Anomaly Detection
      └── Summarization
```

The two layers interact through explicit application interfaces.

### Security

Sensitive customer and insurance data must not be exposed unnecessarily to AI providers.

The platform will apply:

* Authentication and authorization
* Role-based access control
* Data minimization
* Secure document handling
* Provider credential isolation
* Audit logging
* Input validation
* Controlled tool access

## Planned Architecture

```text
                         Clients
                            │
                            ↓
                     API Gateway
                            │
                            ↓
                  Alensi Insure API
                            │
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
     Policy Service    Claims Service    Customer Service
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ↓
                    Intelligence Layer
                            │
             ┌──────────────┼──────────────┐
             ↓              ↓              ↓
        Spring AI          RAG        AI Tools
             │              │              │
             ↓              ↓              ↓
          AI Model      Vector Store    Domain APIs
                            │
                            ↓
                       PostgreSQL
```

Additional infrastructure will support asynchronous processing, document processing, notifications, observability, and background workflows.

## Technology Stack

### Backend

* Java
* Spring Boot
* Spring AI
* Spring Data JPA
* Hibernate
* REST APIs

### AI

* Spring AI
* LLM providers
* Embeddings
* Retrieval Augmented Generation
* Structured output
* Tool calling
* AI-assisted document processing

### Data

* PostgreSQL
* Vector store
* Redis

### Messaging

* RabbitMQ
* Event-driven processing

### Security

* Spring Security
* OAuth2
* OpenID Connect
* JWT
* Role-Based Access Control

### Infrastructure

* Docker
* Kubernetes
* AWS
* GitHub Actions
* Terraform
* Helm

### Observability

* OpenTelemetry
* Prometheus
* Grafana
* Loki
* Distributed tracing

## Engineering Focus

Alensi Insure is designed as a reference implementation for exploring:

* Domain-driven backend architecture
* Insurance domain modeling
* AI-assisted business workflows
* Retrieval Augmented Generation
* Structured AI output
* Tool calling
* Human-in-the-loop systems
* Event-driven architecture
* Document processing
* Explainable decision support
* Secure AI integration
* API design
* Database design
* Distributed systems
* Observability
* Production reliability

## Project Status

This repository represents the planned architecture and implementation of Alensi Insure.

The project will be developed incrementally, starting with the core insurance domain before introducing the intelligence layer.

Planned development stages:

```text
1. Domain Model
       ↓
2. Policy & Coverage Management
       ↓
3. Claims Management
       ↓
4. Workflow & Approvals
       ↓
5. Document Management
       ↓
6. Spring AI Integration
       ↓
7. RAG & Knowledge Retrieval
       ↓
8. AI-Assisted Claims Assessment
       ↓
9. Tool Calling
       ↓
10. Observability & Production Hardening
```

## Documentation

Architecture and engineering documentation will be maintained alongside the implementation.

Planned documentation includes:

```text
docs/
├── architecture/
│   ├── system-context.md
│   ├── container-diagram.md
│   └── component-diagram.md
│
├── database/
│   ├── erd.md
│   └── data-dictionary.md
│
├── api/
│   └── api-spec.md
│
├── ai/
│   ├── intelligence-architecture.md
│   ├── rag-architecture.md
│   ├── tool-calling.md
│   └── ai-decisioning.md
│
├── flows/
│   ├── policy-lifecycle.md
│   ├── claim-lifecycle.md
│   └── claim-assessment.md
│
└── decisions/
    └── ADRs
```

## License

This project is licensed under the MIT License.
