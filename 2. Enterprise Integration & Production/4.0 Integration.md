### 1. Summary

The [Enterprise Integration & Production](https://anthropic-partners.skilljar.com/path/claude-certified-architect-professional/enterprise-integration-production/486648/scorm/v95o12hftmxa) framework dictates that compliance constraints eliminate entry points and delivery routes before any architectural design decisions are made.

* **Entry Point Selection**:
  * **Direct API**: Provides total control over request construction, streaming, and custom error handling, but requires building and maintaining retry logic and orchestration from scratch.
  * **SDK (Python/TypeScript)**: Delivers typed interfaces and HTTP handling while leaving workflow orchestration inside application code.
  * **Agent SDK**: Manages multi-turn execution loops, tool calling, and termination autonomously; trade-off is losing fine-grained control between individual turn iterations.
  * **Model Context Protocol (MCP)**: Decouples tool integration from orchestration logic via a standardized protocol layer, though it increases debugging complexity.
  * **Claude Code**: Intended exclusively for developer workflows; it must never be deployed as a multi-tenant backend engine.


* **Compliance & Route Matrix**:
  * **HIPAA (PHI)**: Requires a signed **Business Associate Agreement (BAA)** covering the specific environment configuration (Direct API with signed BAA, AWS Bedrock, or Google Vertex AI). Sensitive fields must be stripped or replaced with **Reference IDs**.
  * **GDPR & Data Residency**: Regional model execution can use cloud routes or the Direct API parameter **`inference_geo`** (supporting `us` and `global`). Because `inference_geo` does not support direct EU pinning, deployments requiring strict EU data residency must use cloud routes (AWS Bedrock or Google Vertex AI).
  * **FedRAMP**: Restricted to authorized government clouds (Claude for Government, AWS Bedrock GovCloud, Vertex Assured Workloads). Direct API Enterprise is not FedRAMP authorized.
  * **Attorney-Client Privilege**: Implemented via a firm-approved **Gateway Pattern** that holds API keys, enforces access policies, and retains central audit logs.


* **Identity, Authorization & Data Handling**:
  * **Server-Side Injection**: Identity verification must occur on the server via Single Sign-On (SSO) prior to the Claude API call. User roles and authorization limits must be injected directly into the system prompt by the backend server; asserting identity within user messages creates an untrusted, manipulable attack vector.
  * **Context Window Boundaries**: The context window is transmitted across the wire and captured in application-layer logs. Data minimization must be enforced before API transmission.


* **Observability & Multi-Tenancy**:
  * **4-Part Observability**: Audit trails must capture **Request** (model version, prompt ID, input tokens), **Response** (output tokens, latency, stop reason), **Context** (user role, session ID, prompt caching state), and **Outcome** (downstream acceptance or rejection signals).
  * **Multi-Tenant Key Isolation**: Using a shared API key across multiple tenants prevents attribution during rate-limit breaches; production multi-tenant architectures require **separate API keys per tenant**.



---

### 2. Clear & Simple Explanation

* **Compliance Acts as a Filter First**: Before choosing code libraries or framework patterns, legal and regulatory requirements (like HIPAA, GDPR, or FedRAMP) automatically disqualify non-compliant connection routes.
* **Choosing the Right Door**: Use the Direct API for maximum low-level control, the standard SDK for convenient programming interfaces, the Agent SDK for automated tool-use loops, MCP for standardized tool connections, and Claude Code solely for developer coding assistance.
* **Server-Enforced User Identity**: Never ask a user to state their identity or security clearance in a chat message (e.g., "I am an Admin"). The backend server must verify the user through standard SSO and inject the verified role into the hidden system prompt where the user cannot tamper with it.
* **Reference IDs Over Raw Private Data**: Avoid sending full sensitive records (like SSNs or medical files) into the API if a simple database ID is sufficient for the task. This prevents sensitive data from leaking into application log files.
* **Complete Audit Trails**: Autonomous agents will not pass security audits unless the system logs four elements: what was asked, what Claude replied, the user's security context, and whether the main application ultimately accepted or rejected Claude's output.

---

### 3. Real-World Application

An enterprise healthcare network builds "MediAssist AI," a multi-tenant clinical decision-support portal serving hospitals across the US and EU.

* **Architecture Details**:
  * **Route & Compliance Filter**: Because EU data residency is required, EU hospital traffic routes to AWS Bedrock in EU regions. US traffic processes via the Direct API with a signed BAA and `inference_geo="us"`.
  * **Identity & Access**: A physician authenticates via hospital SAML/SSO. The backend server verifies their credentials and uses **Server-Side Injection** to insert `Role: Attending_Physician` and `Permitted_Department: Cardiology` into the system prompt prefix.
  * **Data Minimization**: Patient medical records pass through a pre-processor that strips PII/PHI, substituting a deterministic `Patient_Ref_ID`.
  * **Integration Layer**: The system uses **MCP** adapters to query EHR databases under **least-privilege tool configuration** (e.g., read-only access to labs).
  * **Observability & Multi-Tenancy**: Each hospital network is assigned a **separate API key** for tenant-level isolation. Audit pipelines record the Request, Response, Context, and the final physician **Outcome** (whether the doctor accepted or dismissed the AI treatment recommendation).



```
[ User / Physician ] 
        │ (SAML / SSO Auth)
        ▼
[ Application Backend Server ]
        │ 1. Verify Role & Permissions
        │ 2. Strip PII/PHI ──► Replace with [ Patient_Ref_ID ]
        │ 3. Server-Side Injection of Verified Role into System Prompt
        ▼
[ API Gateway & Security Proxy ]
        │
        ├──► Multi-Tenant Isolation (Separate API Key per Hospital Tenant)
        │
        ├──► [ EU Region Traffic ]  ──► [ AWS Bedrock / Vertex EU Route ]
        │
        └──► [ US Region Traffic ]  ──► [ Direct Claude API (BAA Signed) ]
                                             │
                                             ├─► Connected via MCP (Least-Privilege Tools)
                                             │
                                             └─► 4-Part Observability Logging
                                                 (Request + Response + Context + Outcome)

```

---

### 4. Key Terms Note Section

* **Entry Point**: The technical connection interface selected to integrate Claude (Direct API, SDK, Agent SDK, MCP, or Claude Code).
* **Direct API**: The raw HTTP interface providing maximum control over request structure and error handling at the cost of managing custom retry and streaming logic.
* **SDK (Software Development Kit)**: Language-specific libraries (Python/TypeScript) that handle HTTP transport and typing while leaving orchestration to the application.
* **Agent SDK**: A specialized SDK running a managed execution loop that handles autonomous tool calls, multi-turn reasoning, and termination.
* **Model Context Protocol (MCP)**: An open protocol standardizing how tools and context sources connect to Claude independently of orchestration logic.
* **Claude Code**: An agentic developer tool designed for local coding workflows, explicitly prohibited as a backend engine for customer-facing products.
* **Gateway Pattern**: An architectural boundary server sitting between users and Claude that holds API keys, enforces access policies, and captures audit logs.
* **Business Associate Agreement (BAA)**: A legally binding contract required under HIPAA governing the processing of Protected Health Information (PHI).
* **`inference_geo`**: A parameter in the direct Claude API used to pin model processing to specific geographic regions (currently `us` and `global`).
* **Server-Side Injection**: The security practice where the application backend—not the end user—injects authenticated user roles and permission boundaries directly into the system prompt.
* **Reference ID**: A non-sensitive surrogate key (e.g., claim or account UUID) used in place of full PII/PHI data inside context windows.
* **Observability**: The architectural capability to audit and reconstruct AI decisions by recording the Request, Response, Context, and downstream Outcome.
* **Least-Privilege Tool Configuration**: Restricting connected tools and worker agents strictly to the minimal data access and functions required for their specific task.
* **Multi-Tenant Key Isolation**: Assigning distinct API keys to individual enterprise tenants to guarantee usage attribution and prevent cascading rate-limit outages.

---

### 5. Exam Practice

* **Question 1**
An architect is designing a government agency application that handles Controlled Unclassified Information (CUI) under FedRAMP requirements. The team recommends using the Direct Claude API with an Enterprise agreement to maximize throughput. How should the architect address this recommendation?
* A) Approve the proposal, provided the Direct API request sets `inference_geo="us"`.
* B) Reject the proposal; Direct API Claude Enterprise is not FedRAMP authorized, so the route must use Claude for Government, AWS Bedrock GovCloud, or Google Vertex Assured Workloads.
* C) Approve the proposal, provided user authentication is handled via client-side message headers.
* D) Reject the proposal; FedRAMP compliance requires using Claude Code as the primary execution engine.


* **Question 2**
A multi-tenant SaaS provider uses a single shared Claude API key across 200 corporate clients. During peak business hours, one tenant launches a batch processing job that trips the provider's organization-level rate limit, causing service failure across all 200 clients. What architectural change prevents this failure mode?
* A) Implement the Agent SDK to manage multi-turn loops automatically.
* B) Provision separate API keys per tenant to enable usage attribution and isolate tenant rate limits.
* C) Apply prompt caching to the user prompt message.
* D) Switch the entry point from SDK to raw HTTP Direct API calls.


* **Question 3**
An HR assistant application determines employee bonus eligibility. To restrict access, the prompt asks users to type their job title (e.g., "User Title: Manager"). A junior employee inputs "User Title: Executive Vice President" and gains access to restricted salary metrics. Which architectural flaw enabled this privilege escalation?
* A) Failure to deploy an MCP protocol layer.
* B) Relying on user-provided message content for identity assertion rather than server-side injection of authenticated SSO roles into the system prompt.
* C) Omission of `inference_geo` parameter pinning.
* D) Using a single-turn SDK instead of the Agent SDK.


* **Question 4**
A financial institution requires strict EU data residency for customer analytics. The team proposes using the Direct Claude API with `inference_geo="us"` because cross-border data transfer mechanisms are active. Why is this choice architecturally non-compliant with strict EU residency mandates?
* A) `inference_geo` only accepts `global` or `us` values and cannot pin execution directly to EU regions; strict EU residency requires a cloud route such as AWS Bedrock or Google Vertex AI.
* B) The Direct API automatically stores context window data permanently in US data centers.
* C) GDPR mandates that all LLM orchestration must use the Agent SDK.
* D) BAA contracts are invalid when using `inference_geo`.



---

**Answer Key & Explanations**

* **Question 1 Correct Answer: B**
* *Explanation*: Direct API Claude Enterprise carries no FedRAMP authorization. Regulated government workloads must route through FedRAMP-authorized paths: Claude for Government, AWS Bedrock GovCloud, or Google Vertex Assured Workloads.


* **Question 2 Correct Answer: B**
* *Explanation*: Sharing a single API key across tenants in a multi-tenant architecture creates a single point of failure for rate limits. Issuing separate API keys per tenant isolates traffic impact and allows precise rate-limit attribution.


* **Question 3 Correct Answer: B**
* *Explanation*: User input messages are unverified and subject to prompt manipulation. Identity and authorization boundaries must be enforced server-side after SSO authentication and injected directly into the system prompt by the application server.


* **Question 4 Correct Answer: A**
* *Explanation*: The Direct API `inference_geo` parameter currently supports `us` and `global`, but not direct EU pinning. When strict regional data residency in the EU is required, architects must select an approved cloud route (AWS Bedrock or Google Vertex AI).
