# FreightQuote AI — Milestone 3
### Final Integrated Agentic Freight Quotation and Fleet Monitoring Platform

**Infosys Springboard Internship 7.0 · Batch 4**

FreightQuote AI is a maritime logistics platform that combines agent-based freight quotation, fleet monitoring, operational risk analysis and AI-assisted knowledge retrieval. The Milestone 3 release integrates the RAG knowledge base, enhanced AI Copilot, Teach Copilot, improved agent simulators and the Milestone 2 authentication, profile, RBAC and administration features into one application.

---

## 1. Milestone 3 Objectives and Deliverables

| Milestone 3 task | Implementation in this project |
|---|---|
| Build a knowledge base with 150+ websites and PDFs | Processed 767 public sources: 203 websites and 564 PDFs. |
| Teach the Copilot 50+ unanswered questions | Added 60 freight-related questions to the taught-answer store. |
| Further fine-tune the Copilot | Improved typo handling, SQL intents, synonyms, weak-match checks and role-based topic restrictions. |
| Improve every agent simulator | Enhanced Route, Quote/Margin, Carrier, Weather and Customs simulators with assumptions, repeatable seeds, P10/P50/P90 results and Monte Carlo risk analysis. |
| Integrate Milestone 2 work | Retained the sign-in system, Profile, RBAC, Admin Dashboard and application design in the final application. |
| Validate and demonstrate the application | Added end-to-end checks for roles, access restrictions, Copilot, teaching, simulators and customer data isolation. |

## 2. Key Features

### Agentic Freight Quotation
The quotation workflow uses seven specialised agents:

1. **Route Agent** — selects feasible sea routes using Dijkstra-based routing and geographical constraints.
2. **Weather Agent** — evaluates route weather and wave conditions.
3. **Pricing Agent** — predicts the base freight price using a Random Forest model and incorporates applicable surcharges and port fees.
4. **Customs Agent** — checks HS codes, customs rules and required documents.
5. **Margin Agent** — calculates the quotation using customer category and target margin.
6. **Document Agent** — generates Bill of Lading and Packing List PDFs.
7. **Alert Agent** — evaluates results and identifies cases requiring alerts, review or approval.

The workflow can re-plan when a risk is detected. Quote decisions include Approved, On Hold and Pending Approval, depending on the applicable checks and policies.

### Fleet Monitoring
Five watchers monitor operational conditions:

- Route Watcher
- Weather Watcher
- Customs Watcher
- Quote Watcher
- Anomaly Watcher

The monitoring cycle runs every 60 seconds. The Anomaly Watcher uses Isolation Forest to identify unusual quote patterns.

### AI Copilot: SQL, RAG and Taught Answers
The Copilot routes questions to the appropriate processing layer:

- **SQL analysis:** answers supported operational questions using read-only, parameterised database queries.
- **Agent layer:** explains operational attention items and agent findings.
- **RAG knowledge retrieval:** retrieves relevant passages from the freight knowledge base and displays sources.
- **Taught answers:** responds to questions added by an authorised administrator.
- **Weak-match handling:** can respond that it does not know when supporting evidence is insufficient.

The Copilot also supports typo correction, follow-up questions and multilingual translation using NLLB-200 (35 languages).

### RAG Knowledge Base
The knowledge base covers Incoterms, customs and HS codes, shipping documents, cargo types, freight rates and surcharges, demurrage and detention, ports and congestion, weather and route risks, marine insurance and trade finance.

**Recorded knowledge-base build:**

| Metric | Result |
|---|---:|
| Seed websites | 220 |
| Unique websites processed | 203 |
| PDF documents processed | 564 |
| Total public sources processed | **767** |
| Additional synthetic knowledge documents | 29 |
| Total documents | 796 |
| Total text chunks | **16,137** |

Retrieval combines BM25 keyword search and dense vector search, followed by Reciprocal Rank Fusion and a cross-encoder reranker. Knowledge-based answers include source references.

### Teach Copilot
The taught-answer collection contains **60 freight questions** that were previously unanswered or answered incorrectly. Administrators can add answers and keywords through **Admin → Teach Copilot**. Rephrased versions of taught questions are also tested.

### Agent Simulators and Digital Twin
Each agent simulator uses database values as starting points and states its assumptions. Simulations are read-only and do not modify live operational records.

| Simulator | Example scenarios and outputs |
|---|---|
| Route | Chokepoint closures, storms, fuel or speed changes; route duration and cost |
| Quote / Margin | Margin, fuel, FX and carrier-rate changes; price range and expected profit |
| Carrier | Reliability or capacity changes; alternative carrier ranking and delay cost |
| Weather | Storm intensity and duration; delay and demurrage estimates |
| Customs | Inspection rates and missing documents; held shipments and holding cost |

Simulators report P10, P50 and P90 outcomes and support repeatable runs using a seed. The Digital Twin combines multiple scenarios, estimates potential financial impact using Monte Carlo simulation and ranks mitigation options. A selected mitigation can be sent to the approval queue.

### Approvals, Governance and Audit
The application supports configurable autonomy policies and approval chains. Actions requiring human review are routed to the appropriate approvers. The Four-Eyes Principle prevents one person from approving multiple stages of the same request or approving their own request. Approval decisions and related activity are recorded in the audit trail. SLA deadlines and escalation are supported.

### Other Platform Features
- Command Center with operational indicators, vessel and port views, agent activity, anomalies, knowledge graph and entity explorer.
- Weather and map views using Open-Meteo weather information.
- Data Feed Center for CSV, webhook and API integrations, with validation and quarantine for invalid records.
- Reports and data exports.
- Admin Dashboard, Copilot insights, Teach Copilot, governance settings and System Check.
- Profile management, customer data isolation and light/dark interface themes.
- Google Drive database backup and restore for Colab sessions.

---

## 3. Technology Stack

| Area | Technologies |
|---|---|
| Backend and API | Python, FastAPI, Uvicorn |
| Frontend | HTML, CSS, JavaScript, Apache ECharts, Leaflet |
| Agent orchestration | LangGraph |
| Machine learning | scikit-learn, NumPy, pandas, SciPy |
| Routing | Dijkstra algorithm, geographical coastline constraints |
| RAG and retrieval | FAISS, BM25, embeddings, Reciprocal Rank Fusion, cross-encoder reranker |
| Language models | Qwen, NLLB-200, Sentence Transformers |
| Database | SQLite (WAL mode) |
| Documents | ReportLab, pdfplumber |
| Runtime and hosting | Google Colab with T4 GPU, pyngrok |
| Persistence | Google Drive backup and restore |

---

## 4. User Roles and Access

The application has five roles. Access to pages and actions is controlled by Role-Based Access Control (RBAC).

| Role | Main access |
|---|---|
| **Admin** | Full platform access, user administration, governance, system checks and Copilot teaching |
| **Operations Manager** | Operational monitoring, agent execution, approvals and data feeds |
| **Freight Broker** | Commercial quotation and permitted freight operations |
| **Auditor** | Read-only access to permitted operational records |
| **Customer** | Submit and view quotations associated with their own customer account |

Customer data isolation restricts customers to their own quotation records. Restricted pages and actions are enforced by the server.

---

## 5. Running the Application

### Requirements
- Google account with access to Google Colab and Google Drive.
- A GPU runtime; the project is configured for a **T4 GPU**.
- Colab Secrets for optional integrations and credentials.

### Steps
1. Open `FreightQuote_AI_Milestone3_Final_Colab.ipynb` in Google Colab.
2. Select **Runtime → Change runtime type → T4 GPU**.
3. Run the notebook cells in order and grant Google Drive access when prompted.
4. Complete the knowledge-base build if it has not already been generated. If the knowledge-base files are already available in Drive and unchanged, proceed to the application section as described in the notebook.
5. Run the application cell. Open the Colab or ngrok URL printed when the server starts.
6. Run the validation section to execute the available Milestone 3 checks.

Optional secrets such as `JWT_SECRET_KEY`, `ADMIN_PASSWORD`, `EMAIL_ID` / `EMAIL_PASSWORD`, `NGROK_AUTHTOKEN` and `HF_TOKEN` should be configured through **Colab Secrets**, not hard-coded in the notebook or committed to GitHub.

> Demo credentials, if enabled, are displayed by the application. Change or disable demo credentials before any non-demo deployment.

---

## 6. Validation Results

The recorded validation run reported:

| Validation area | Recorded result |
|---|---|
| Role-based login and access restrictions | Passed checks for the five roles and restricted access cases |
| Agent execution | Operations Manager agent-cycle check passed |
| Copilot SQL and follow-up questions | Passed recorded checks |
| Copilot RAG answers with sources | 21 of 22 in-app questions passed |
| RAG retrieval notebook test | 22 of 22 in-scope retrieval tests passed |
| Teach Copilot | 60 of 60 taught questions answered in the recorded test |
| Agent simulators | Simulator checks completed for the recorded tabs |
| Customer data isolation | Recorded isolation checks passed |
| **Overall validation** | **79 of 81 checks passed** |

Two checks did not pass in the recorded run: one in-app RAG question returned “I do not know”, and one out-of-scope test question received an answer. These results are reported as recorded; they should not be interpreted as 100% validation.

---

## 7. Data and Deployment Notes

- Weather and wave information is retrieved from Open-Meteo.
- Vessel positions, shipment records and freight-rate training data are simulated demonstration data until external feeds are connected.
- The Data Feed Center supports integration paths for external AIS, rate and shipment data.
- Email, Slack and SMS notifications require the relevant external credentials.
- The application is demonstrated through Google Colab and a public tunnel; this setup is intended for the project demonstration, not presented as a production hosting environment.
- The agents use specialised models and rules for operational decisions. The language model primarily supports Copilot responses and explanations; it does not independently calculate the freight price.

---

## 8. Repository Structure

```text
Milestone3/
├── FreightQuote_AI_Milestone3_Final_Colab.ipynb
├── README.md
├── taught_questions.csv
└── screenshots/
    ├── aicopilot.jpg
    ├── document_translator.jpg
    ├── command_center_anomalies.jpg
    ├── knowledge_graph.png
    ├── approvals_awaiting.png
    ├── approvals_who_approves_hindi.png
    ├── approvals_decisions_shipment_map.png
    └── approvals_autonomy_policy.png
```


---

## 9. Screenshots

### AI Copilot — Ask the Agents
The Copilot chat with saved conversations and answer language selection. It lists what it can do (rank, count and average, explain, operations, overview) and understands typos and follow-ups.

<img width="1365" height="596" alt="image" src="https://github.com/user-attachments/assets/6c0c32f6-e655-48f9-b722-1b0cba401ac2" />


### AI Copilot — Document Translator
Any text or PDF is auto-detected and translated with Facebook **NLLB-200**, sentence-chunked so long documents are translated completely (here: English → Hindi).

<img width="1365" height="592" alt="image" src="https://github.com/user-attachments/assets/62a07e8f-0f5b-4732-b1a6-0924bfacd7c1" />


### Command Center — Anomaly Detection
An **IsolationForest** model checks quote economics (price ÷ base cost, margin, fuel share) and flags unusual quotes in red; click a dot to open the quote.

<img width="1363" height="616" alt="image" src="https://github.com/user-attachments/assets/0ef3136d-3b8a-4d9d-a7b2-81b6229534d3" />


### Command Center — Live Knowledge Graph
Agent → ticket type → entity graph built from the agents' tickets in real time. Nodes can be dragged and zoomed, and clicking an entity opens it.

<img width="1365" height="589" alt="image" src="https://github.com/user-attachments/assets/5e09a5d5-c339-43fb-b2e2-bd02c110906e" />


### Approvals — Awaiting Decision
Everything that needs a human: agent proposals (e.g. weather re-routes), quote approvals, ML second opinions and Digital Twin recommendations. Each item shows severity, owner, due time and approval chain, with **Approve & execute**, **Reject**, **Resolve** and **Assign** actions.

<img width="1365" height="597" alt="image" src="https://github.com/user-attachments/assets/d7976b15-b567-4045-adf6-c3acfbe9b64b" />


### Approvals — Who Approves What (Hindi interface)
Each decision type with the agent that raises it, its mode (auto / approval), sign-off chain and owner. The whole page is translated into the selected language.

<img width="1365" height="594" alt="image" src="https://github.com/user-attachments/assets/858f184a-7045-4384-9c5a-b4e76724ab62" />


### Approvals — Decisions & Shipment Detail
Recent decisions in the audit trail. Opening a shipment shows progress, wind, wave height, weather risk and the planned voyage route against storm cells on the map.

<img width="1363" height="581" alt="image" src="https://github.com/user-attachments/assets/0a8dcbcf-4909-4306-b566-c0e9b8007c06" />


### Approvals — Autonomy Policy & Approval Chains
The Admin chooses which agent actions run automatically and which wait for a human, and who signs off each kind (e.g. Operations Manager → Admin). Changes apply to the next agent cycle and are written to the audit log.

<img width="1365" height="588" alt="image" src="https://github.com/user-attachments/assets/f6d63490-6dfe-4222-8fa7-ff820e851cde" />


---


---

## 👥 Project Collaborators

The project is developed collaboratively, with each team member contributing to different modules and functionalities.

| S.No | Collaborator Name        | Role / Responsibility                 |
| :--: | ------------------------ | ------------------------------------- |
|   1  |  Md Shahabuddin      | Add 150+ websites and PDFs to the RAG                   |
|   2  | Gollapelli Harika | Find 50+questions the copilot cannot answer and feed them in |
|   3  | Yesaswi Sushma  Peela     | Fine-tune the copilot more |
|   4  | Alisetti Ranga Triveni      | Make the Simulator of every agent better |
|   5  | Sneha Tripathi           | Integration and bring Milestone 2 in final work |
