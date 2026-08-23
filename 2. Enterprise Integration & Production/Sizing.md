Determining production feasibility for Claude applications requires evaluating system reliability alongside output quality through rigorous use-case sizing, feasibility assessments, and business ROI mapping.

**1. Summary & Key Architectural Takeaways**

* **Production Readiness Standard**: Production readiness requires satisfying two independent validation bars: output quality (validated via evals) and system reliability (validated via architecture controls like retry logic, fallbacks, and circuit breakers).

* **Feasibility Verdict Classification**:
  * *Feasible as Scoped*: Business goals align with the four AI properties (next-token prediction, knowledge, working memory, steerability), meeting cost ceilings and p95 latency SLAs without extra controls.
  * *Feasible with Constraints*: Viable only under explicit operational boundaries (e.g., document page-count thresholds, index refresh frequencies, human review gates for confidence thresholds). These constraints and their associated failure modes must be documented in the Statement of Work (SOW).
  * *Not Feasible*: Core requirements encounter uncompensable AI property limitations or exceed cost ceilings beyond the reach of model tier adjustments or caching.


* **Sizing & Cost Modeling Inputs**:
  * *Call Volume*: Sourced directly from business owner requirements rather than sample datasets or developer intuition.
  * *Token Budget & Distribution*: Modeled using token distributions (accounting for heavy-tail input variations) rather than misleading token averages.
  * *Sensitivity Analysis*: Stress-tests cost models against volume surges or distribution shifts toward long-tail inputs.


* **Cost Optimization Architectural Patterns**:
  * *Prompt Caching*: Utilizes explicit `cache_control` markers for long, stable system prompts. Requires accounting for initial cache write fees versus lower cache read rates. The default Time-To-Live (TTL) is 5 minutes; workloads with request frequencies below TTL will not realize caching savings. Check current rates on the official [Claude pricing](https://platform.claude.com/docs/en/about-claude/pricing) page.
  * *Batch API*: Delivers a ~50% cost reduction for asynchronous workloads (up to 100,000 requests per batch). Requires verifying Business Associate Agreement (BAA) and compliance configurations when handling Protected Health Information (PHI) or governed data.


* **Feasibility Assessment & Compensating Controls**: Evaluates tasks against the four AI properties to identify drift (e.g., complex calculations or long reasoning chains) and applies compensating controls like explicit output schemas, code execution for numerical accuracy, and evaluator-optimizer loops.
* **Business Value & ROI Mapping**: Maps technical capabilities to five business pillars (efficiency, transformation, productivity, solution cost, performance SLAs). ROI is calculated by measuring the baseline state in business units, projecting post-deployment states (factoring in human-in-the-loop labor), subtracting sizing run costs, and calculating payback period sensitivity.

---

**2. Clear & Simple Explanation**

* **Size Before Coding**: Creating a realistic cost model before writing software prevents budget blowouts caused by unusually long documents or sudden traffic spikes.
* **Feasibility Means Guardrails**: Determining feasibility is not just asking "Can Claude generate this text?" It requires proving the system can operate within cost, speed, and safety limits—and specifying compensating tools (like code execution or human review) when it cannot.
* **Avoid Common ROI Errors**: Never assume 100% labor savings if your design includes a human review queue, and never base baseline metrics on intuition instead of audited operational data.

---

**3. Real-World Application**

An enterprise healthcare payer designs an automated prior-authorization request processing pipeline for patient medical claims.

  * **Capability Decomposition & Architecture Sketch**: The system splits incoming claims into three distinct capabilities: clinical text extraction (Claude), medical code validation (legacy code execution engine for deterministic mathematical/boolean logic), and patient eligibility lookup (database query).
  * **Sizing & Cost Optimization**: System prompts incorporate 18,000 tokens of clinical guidelines tagged with `cache_control` markers. Because claim submission frequency exceeds the 5-minute TTL, cache reads drastically cut input token costs. Non-urgent claims submitted after business hours are routed to the Batch API for a 50% discount after verifying BAA compliance coverage for PHI data.
  * **Boundary Conditions & Human-in-the-Loop**: The SOW specifies a 25-page boundary limit per submission. Extractions with low confidence scores bypass automated approval and route to a nurse reviewer queue.
  * **ROI & Sensitivity Mapping**: The ROI model calculates savings against historical nurse review hours, deducting ongoing token run costs derived from heavy-tail sizing distributions and accounting for remaining nurse labor on routed edge cases.

---

**4. CCAR-P Exam Practice Questions**

* **Question 1**
An architect is sizing an enterprise document parsing pipeline using Claude. The system prompt contains 20,000 tokens of static compliance rules using `cache_control` headers. Requests arrive randomly once every 30 minutes. Why will this deployment fail to realize expected prompt caching savings?
* A) Prompt caching requires dynamic system prompts and cannot process static text over 10,000 tokens.
* B) The request arrival interval exceeds the 5-minute Time-To-Live (TTL), causing recurring cache write penalties rather than cache read discounts.
* C) Prompt caching is strictly limited to synchronous single-turn user prompts.
* D) The Batch API must be enabled to activate `cache_control` markers in production.


* **Question 2**
A team is scoping a non-real-time medical records processing workflow that handles Protected Health Information (PHI) with a 12-hour turnaround SLA. Which architecture decision minimizes operational token costs while maintaining enterprise compliance?
* A) Route requests through the standard synchronous API using lower-tier models without BAA validation.
* B) Route requests through the Batch API for a 50% discount after confirming batch endpoints are covered under the organization's BAA.
* C) Apply prompt caching without `cache_control` headers and handle retries via local client loops.
* D) Declare the project Feasible as Scoped and eliminate human-in-the-loop review queues to reduce call volume.


* **Question 3**
A financial services firm builds an ROI business case for an automated loan decisioning agent. The business owner projects 100% analyst labor elimination based on average token counts from a sample set of clean documents. Why will finance reject this business case?
* A) The ROI model targeted p95 latency instead of median latency.
* B) The model used average token counts instead of tail distributions and assumed full automation while ignoring required human review costs.
* C) The design selected the Batch API for asynchronous requests and documented boundary conditions in the SOW.
* D) Sensitivity analysis was performed on request call volume.


* **Question 4**
An architect assesses a multi-step financial reasoning task where Claude must execute precise multi-currency conversions and interest calculations. The model frequently introduces numerical drift. Which compensating control directly resolves this technical feasibility issue?
* A) Increase the token budget and remove output schema validation.
* B) Offload numerical calculations to a deterministic code execution engine while using Claude for structured intent extraction.
* C) Route all requests to the Batch API with exponential backoff retries.
* D) Classify the system as Feasible as Scoped without modifying the prompt template.



**Answer Key & Explanations**

* **Question 1 Correct Answer: B**
* *Explanation*: Prompt caching has a default TTL of 5 minutes. If requests arrive 30 minutes apart, every request hits an expired cache, incurring higher initial cache write costs without benefiting from cache read pricing.


* **Question 2 Correct Answer: B**
* *Explanation*: The Batch API provides a 50% discount for asynchronous workloads where SLAs permit up to 24-hour turnaround. For healthcare workloads handling PHI, architects must verify BAA coverage before routing data through batch endpoints.


* **Question 3 Correct Answer: B**
* *Explanation*: Reusing average token costs understates recurring costs on heavy-tailed distributions. Furthermore, projecting 100% labor elimination when the architecture requires human-in-the-loop review for edge cases overstates net savings.


* **Question 4 Correct Answer: B**
* *Explanation*: Next-token prediction models struggle with precise numerical computation. The standard compensating control is offloading calculations to deterministic code execution while utilizing Claude for steerability and structured intent parsing.



---

**Key Technical Terms**

* **Production Readiness Checklist**: A dual-validation framework that confirms both output quality (via evals) and system reliability (via architectural controls).
* **Feasible as Scoped**: A feasibility verdict indicating that requirements fit within cost, SLA, and AI property limits without extra compensating controls.
* **Feasible with Constraints**: A verdict indicating a system is viable only under documented operational guardrails (e.g., page limits, confidence thresholds) listed in the Statement of Work (SOW).
* **Not Feasible**: A verdict indicating that core requirements breach uncompensable AI property limits or project costs exceed budget ceilings.
* **Prompt Caching**: An optimization strategy using `cache_control` markers to store prompt prefixes in memory, incurring an initial cache write fee but offering lower cache read costs on repeated calls within the 5-minute TTL.
* **Batch API**: An asynchronous processing endpoint offering a 50% price reduction for batch jobs up to 100,000 requests when real-time SLAs are not required.
* **Four AI Properties**: The core assessment dimensions for LLM capability: next-token prediction, knowledge, working memory, and steerability.
* **Compensating Controls**: Architectural mechanisms (e.g., code execution engines, evaluator-optimizer loops, human review gates) designed to overcome LLM capability limitations.
* **Sensitivity Analysis**: Stress-testing cost and ROI models against shifts in call volume or token distribution tails.
* **ROI Mapping**: The methodology of connecting technical architecture costs and operational gains (efficiency, productivity, transformation) to measurable business outcomes and payback periods.
