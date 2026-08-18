### 1. Summary

The [Claude Platform & Solution Design](https://anthropic-partners.skilljar.com/path/claude-certified-architect-professional/claude-platform-solution-design/486647/scorm/2gvs1pi0tq0sq) module on **Decomposition & Delegation** establishes the architectural framework for partitioning system responsibilities across three distinct execution boundaries:

* **Three-Bucket Ownership Model:**
* **What Claude Does:** Pattern-rich tasks benefiting from language comprehension, summarization, planning, drafting, and tool-mediated execution.
* **What Existing Systems Do:** Deterministic, high-reliability infrastructure such as policy engines, rule tables, databases of record, and transaction services.
* **What Humans Do:** High-stakes judgment calls, exception paths, regulatory approvals, and non-reversible actions where accuracy overrides speed.


* **Delegation Justification Framework:** Tasks are assigned based on three core evaluation vectors:
* **Reversibility:** Whether an incorrect decision can be safely rolled back.
* **Stakes:** The operational, legal, or financial cost of an erroneous output.
* **Accountability:** The regulatory requirement for verifiable provenance and human oversight.


* **Architectural Trade-offs & Anti-Patterns:** Moving deterministic logic into prompt instructions introduces severe trade-offs:
* **Cost Inflation:** Routing database queries or table lookups to an LLM incurs unnecessary token costs at volume.
* **Debugging Complexity:** Replacing table-driven code with probabilistic inference trades structured, trace-logged errors for opaque failure modes.
* **Deterministic Drift:** Relying on parametric memory for static or evolving rules leads to silent correctness degradation without throwing runtime exceptions.



---

### 2. Clear & Simple Explanation

* **Don't force Claude to do a database's job:** If an existing rule engine or database can answer a question with 100% certainty (like checking an order status or calculating tax), let the database handle it. Reserve Claude for what it does best: parsing unstructured text, summarizing data, and drafting responses.
* **Evaluate risk before delegating authority:** Before letting Claude perform an action automatically, ask: *Can this be undone? How much will a mistake cost? Who is responsible if it fails?* If the risk is high and irreversible, keep a human in the loop to review and approve.
* **Avoid hidden costs and silent failures:** Prompting Claude to perform rule-based logic makes system architectures slower, more expensive, and difficult to debug, because models will not throw standard software exceptions when making logical errors.

---

### 3. Real-World Application

**Scenario: Enterprise Mortgage Modification & Hardship Triage Engine**

* **Business Problem:** A mortgage lender needs to process high volumes of borrower loan modification requests under rapidly changing state regulations without introducing financial calculation errors or regulatory compliance violations.
* **Architecture Solution using 3-Bucket Decomposition:**
1. **Claude (Language & Drafting):** Ingests unstructured borrower hardship letters and tax documents. Extracts structured financial entities (JSON Schema) and drafts personalized borrower response letters.
2. **Existing Systems (Rules Engine & DB of Record):** The loan modification eligibility engine evaluates debt-to-income (DTI) ratios and interest rate adjustments against the core database of record via Model Context Protocol (MCP) tool calls, bypassing probabilistic model arithmetic.
3. **Humans (HITL Gateways):** Requests involving loan balances exceeding $500,000 or non-standard hardship exceptions are automatically routed to a loan officer for final approval and signature before any binding commitment is issued.


* **Outcome:** Eliminates deterministic drift on interest calculations, reduces API token overhead by 60%, and ensures compliance auditability.

---

### 4. Exam Practice

**Question 1**

An enterprise architect is designing a automated insurance claims triage system. The partner wants Claude to parse incoming claim narratives, calculate the priority score based on strict company tier rules, fetch policy coverage limits, and issue settlement payouts automatically. Which architectural decomposition best aligns with Anthropic design principles?

A. Assign all four steps to Claude using a single system prompt to minimize network latency.

B. Have Claude parse the claim narrative and draft response communications; route priority calculation and policy lookups to existing deterministic engines; mandate human approval for payout execution.

C. Route the entire pipeline to an external rule engine, using Claude only if the rule engine throws an unhandled exception.

D. Fine-tune Claude on the insurance company's historic policy database so it can independently evaluate coverage limits without tool calls.

**Question 2**

During an architecture review, a lead engineer proposes replacing a legacy Python discount calculation service with a Claude prompt that evaluates promotional codes. What key architectural risk should the CCAR-P architect raise?

A. Claude cannot parse alphanumeric promotional codes without custom tokenizers.

B. Offloading rule tables to LLM inference introduces non-deterministic drift, un-traceable logic errors, and unnecessary token cost.

C. Context window budgets will immediately be exceeded by promotional code strings.

D. Claude requires human-in-the-loop authorization for every API completion call.

**Question 3**

A customer support agent built on Claude drafts refund confirmation messages and triggers API calls to the payment gateway. Which delegation criteria dictate that high-value refund execution should remain with a human supervisor rather than fully automated tool execution?

A. Context Budget and Latency.

B. Steerability and Structured Outputs.

C. Reversibility, Stakes, and Accountability.

D. Next-token Prediction and Parametric Recall.

**Question 4**

When decomposing a complex enterprise workflow, what primary question should drive an architect’s decision to delegate a specific step to Claude?

A. "Can Claude theoretically execute this task given sufficient prompt length?"

B. "How can we maximize the total number of agentic tool calls in the pipeline?"

C. "Where do Claude's core behavioral properties argue for its use over an existing reliable system?"

D. "How can we replace all legacy microservices with single-prompt LLM architectures?"

---

#### Answer Key & Explanations

* **Question 1 Answer: B**
* *Explanation:* Language comprehension (parsing claims, drafting emails) belongs to Claude. Priority calculation and policy lookup belong to deterministic systems/rules engines to prevent knowledge boundary errors and deterministic drift. Payout execution carries high financial stakes and low reversibility, requiring Human-in-the-Loop (HITL) approval.


* **Question 2 Answer: B**
* *Explanation:* Replacing deterministic code with probabilistic language models introduces un-traceable logic errors, silent deterministic drift without runtime exceptions, and inflated inference costs.


* **Question 3 Answer: C**
* *Explanation:* High-value financial transactions have low reversibility, high financial stakes, and strict compliance accountability, making them ideal candidates for human-retained authority.


* **Question 4 Answer: C**
* *Explanation:* Effective solution design shifts the framing from "Where can Claude help?" to identifying specific steps where Claude's behavioral strengths provide clear advantages over existing deterministic infrastructure.



---

### Key Technical Terms

* **Decomposition:** The architectural practice of breaking down a complex business request into discrete steps and assigning each step to the most appropriate owner (Claude, existing systems, or humans).
* **Delegation Map:** A formal design artifact that explicitly defines ownership, boundaries, and execution authorities across an AI-enabled solution.
* **Reversibility:** A delegation evaluation metric assessing whether an action performed by a system or agent can be safely rolled back if executed incorrectly.
* **Stakes:** An architectural risk metric measuring the financial, operational, or legal consequences of an erroneous AI decision.
* **Accountability:** The legal or operational requirement for a designated human or system entity to take ultimate responsibility for a decision or action.
* **Deterministic Drift:** The silent divergence in business logic or rules that occurs when fixed, table-driven systems are replaced by probabilistic LLM outputs without runtime error alerts.
* **Database of Record:** An authoritative, single-source-of-truth enterprise database or system used for transactional and state data.
* **Human-in-the-Loop (HITL):** A control design pattern requiring human intervention or approval for high-risk, low-reversibility, or exception-path actions before execution.
