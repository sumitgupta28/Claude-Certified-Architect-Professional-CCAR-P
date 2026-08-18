### 1. Summary

The [Claude Platform & Solution Design](https://anthropic-partners.skilljar.com/path/claude-certified-architect-professional/claude-platform-solution-design/486647/scorm/2gvs1pi0tq0sq) platform mapping framework establishes a three-tier architectural taxonomy that separates user interaction, developer programming contracts, and hosting environments:

* **Entry Points:** User- and developer-facing interaction wrappers that determine access modalities, user personas, and interface workflows. These include end-user web/desktop clients (**claude.ai**, **Claude Desktop**) and developer terminal/CLI tooling (**Claude Code**).
* **Build-Time Interfaces:** Programmatic contracts and abstractions used by software engineers to integrate model capabilities into broader codebases. These comprise the **Direct API**, language-specific **SDKs**, standardized tool integration protocols like **Model Context Protocol (MCP)**, and orchestration layers like the **Agent SDK**.
* **Delivery Routes:** Managed enterprise cloud infrastructure endpoints where model inference requests terminate (**Bedrock / Vertex / Foundry** alongside Anthropic Direct). Delivery routes dictate network security boundaries, zero-data-retention (ZDR) policies, regional data residency, and enterprise cloud spend commitments.
* **Architectural Takeaway:** Solutions architects must treat these three layers as orthogonal design choices. Conflating an interface abstraction (e.g., **MCP**) with a delivery route (e.g., **AWS Bedrock**) or picking an inappropriate entry point for a user persona creates severe compliance, security, and operational failure modes.

---

### 2. Clear & Simple Explanation

* **Entry Points (Who & How):** These are the front doors. Non-technical staff use web apps (`claude.ai`), power users use desktop clients (`Claude Desktop`), and developers use CLI tools (`Claude Code`).
* **Build-Time Interfaces (Developer Tools):** These are the building blocks programmers use when writing code. They include basic API connections (`Direct API`), helper code libraries (`SDKs`), tool-connecting protocols (`MCP`), and multi-step workflow managers (`Agent SDK`).
* **Delivery Routes (Where It Runs):** This is the underlying cloud engine hosting the model. Enterprise apps run on cloud providers (`AWS Bedrock`, `GCP Vertex AI`, `Microsoft Foundry`) to ensure data privacy and regulatory compliance.
* **Core Architectural Rule:** Choosing *where* your app runs (Delivery Route) does not dictate *how* your engineers write code (Build-Time Interface) or *how* end-users access it (Entry Point).

---

### 3. Preserve Technical Accuracy

To maintain technical precision across solution architectures, platform primitives must be mapped strictly to their corresponding architectural layer:

* **Entry Point Layer:** Encompasses **claude.ai**, **Claude Desktop**, **Claude Code**, and custom web/mobile user interface wrappers.
* **Build-Time Interface Layer:** Encompasses **Direct API**, **SDKs**, **Model Context Protocol (MCP)** servers, and the **Agent SDK**.
* **Delivery Route Layer:** Encompasses cloud hosting infrastructure endpoints including **AWS Bedrock**, **GCP Vertex AI**, **Microsoft Foundry**, and **Anthropic Direct**.

---

### 4. Real-World Application

An enterprise wealth management firm is building an AI agent to assist research analysts in summarizing portfolio risk and querying private customer databases:

* **Entry Point Selection:** Financial analysts interact through an internal custom React dashboard wrapped around API endpoints, while developers use **Claude Code** strictly within CI/CD development pipelines to iterate on agent code.
* **Build-Time Interface Implementation:** Developers construct multi-turn research workflows using the **Agent SDK** for complex decision loops and deploy **MCP (Model Context Protocol)** servers to securely bridge Claude with PostgreSQL financial databases and internal REST APIs.
* **Delivery Route Execution:** All API calls are configured to terminate on **AWS Bedrock** over PrivateLink VPC endpoints to comply with FINRA security guidelines, leverage existing cloud commitment budgets, and enforce strict zero-data-retention (ZDR) agreements.

---

### 5. Exam Practice

**Question 1**

An enterprise solution architect is auditing a proposed platform architecture for a regulated healthcare provider. The lead developer categorizes **Model Context Protocol (MCP)** as a **Delivery Route** and **AWS Bedrock** as a **Build-Time Interface**. How should the architect correct this classification?

A) MCP is an Entry Point; AWS Bedrock is a Build-Time Interface.

B) MCP is a Build-Time Interface; AWS Bedrock is a Delivery Route.

C) Both MCP and AWS Bedrock are Delivery Routes operating at different network OSI layers.

D) MCP is an Entry Point; AWS Bedrock is a Delivery Route.

**Question 2**

A developer team wants to automate code refactoring across a large monolithic repository. They propose deploying **Claude Code** as an entry point for non-technical product managers to run database migrations via natural language prompts. Why does this design fail CCAR-P platform mapping guidelines?

A) Claude Code lacks support for language-specific SDKs.

B) Claude Code is a developer CLI/terminal entry point designed for engineering workflows and creates high risk when assigned to non-technical user personas.

C) Claude Code can only route traffic through GCP Vertex AI, violating AWS compatibility.

D) Product managers must always use the Agent SDK rather than entry points.

**Question 3**

A multinational bank requires all AI model invocation traffic to execute within its existing Google Cloud Platform (GCP) tenant to satisfy European Union data sovereignty laws. Which platform layer decision directly addresses this requirement?

A) Specifying the Agent SDK at the Build-Time Interface layer.

B) Configuring GCP Vertex AI as the Delivery Route.

C) Deploying Claude Desktop as the primary Entry Point.

D) Implementing an MCP server proxy layer.

**Question 4**

An engineering team needs to standardize tool integrations across 15 proprietary microservices so that multiple internal Claude applications can reuse the same database connectors without custom integration code. Which **Build-Time Interface** primitive should the architect specify?

A) Microsoft Foundry managed endpoints.

B) Claude.ai Enterprise workspace settings.

C) Model Context Protocol (MCP) servers.

D) Claude Code environment configurations.

---

#### Answer & Explanation Key

* **Question 1 Correct Answer:** **B**
*Explanation:* **MCP** is an open protocol standard functioning as a **Build-Time Interface** for tool and data integration. **AWS Bedrock** is a managed cloud infrastructure environment functioning as a **Delivery Route**.
* **Question 2 Correct Answer:** **B**
*Explanation:* **Claude Code** is an entry point tailored specifically for developers operating in terminal/CLI environments. Assigning a CLI developer tool to non-technical product managers for high-risk operations violates persona-to-entry-point alignment guidelines.
* **Question 3 Correct Answer:** **B**
*Explanation:* **Delivery Routes** govern where API traffic terminates and where inference executes. Selecting **GCP Vertex AI** ensures compliance with cloud-specific enterprise boundaries, VPC controls, and regional data sovereignty regulations.
* **Question 4 Correct Answer:** **C**
*Explanation:* **Model Context Protocol (MCP)** is the open standard **Build-Time Interface** designed specifically for creating reusable, secure client-server tool connections across heterogeneous data sources and services.

---

### Key Technical Terms

* **Entry Points:** User- or developer-facing interface wrappers through which interactions with Claude occur (e.g., **claude.ai**, **Claude Desktop**, **Claude Code**).
* **Build-Time Interfaces:** Developer-facing programming abstractions, libraries, and protocols used to write code against Claude (e.g., **Direct API**, **SDKs**, **MCP**, **Agent SDK**).
* **Delivery Routes:** Cloud infrastructure execution environments where model traffic terminates (e.g., **AWS Bedrock**, **GCP Vertex AI**, **Microsoft Foundry**, **Anthropic Direct**).
* **Model Context Protocol (MCP):** An open standard protocol in the Build-Time Interface layer that enables secure, standardized connections between LLMs and external tools or databases.
* **Agent SDK:** A specialized build-time library used by software engineers to programmatically construct, orchestrate, and manage multi-turn autonomous agent loops.
* **Claude Code:** A terminal-based developer entry point tool designed for command-line coding, debugging, and repository navigation.
