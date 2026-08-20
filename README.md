# LoopKB
### A self-updating knowledge layer for enterprise support agents
**Track 2 — Platform Agent Skills & Knowledge**
**The Great Agent Hackathon | TGPF 2026**

> Every difficult support ticket should make the next one easier. 
 
---  

## The Problem  

Enterprise support teams have a silent knowledge leak.

When a support agent — human or AI — solves a novel problem, the solution often disappears the moment the ticket is closed.

Knowledge bases are created once and gradually go stale. Manually documenting every new resolution takes time support teams don't have, so the same knowledge gap gets rediscovered — and re-investigated — again and again.

The cycle looks like this:

```text
Novel Support Issue
       ↓
AI can't find an answer
       ↓
Human / Specialist investigates
       ↓
Problem gets solved
       ↓
Ticket gets closed
       ↓
❌ New knowledge stays trapped in the ticket
       ↓
Another customer hits the same issue
       ↓
The problem gets investigated again
```

This creates:

- Repeated support effort
- Longer resolution times
- Stale knowledge bases
- Repeated escalations
- Dependence on a small number of experienced support agents
- Loss of valuable organizational knowledge

Most AI support systems focus on the *answering* problem — better retrieval, better chat, faster triage. But there's a second, quieter problem:

**How does the system learn from the problems it couldn't solve?**

That's the gap LoopKB addresses.

---

## Our Solution

LoopKB closes the loop between resolution and knowledge. Instead of treating a resolved ticket as the end of a workflow, LoopKB treats it as a potential source of new organizational knowledge.

```text
Existing Knowledge
       ↓
Resolver Agent
       ↓
Incoming Ticket
       ↓
 ┌─────┴─────┐
 │           │
Answer     No Answer
 │           │
 ▼           ▼
Resolve    Escalate
             │
             ▼
      Human / Specialist
             │
             ▼
       Resolution
             │
             ▼
       Gap Detector
             │
             ▼
      Knowledge Writer
             │
             ▼
      Validation / Review
             │
             ▼
       Updated Knowledge
             │
             └──────────────→ back to Resolver Agent
```

The knowledge base gets smarter *because of* the tickets flowing through it — not despite them.

---

## How LoopKB Works

LoopKB uses three purpose-built agents.

### 1. Resolver Agent

Handles incoming support tickets. It:

1. Understands the issue
2. Searches the existing knowledge base using RAG
3. Retrieves relevant knowledge
4. Generates a candidate answer
5. Evaluates whether the retrieved evidence is sufficient

If confident:

```text
Ticket → Retrieve Knowledge → High Confidence → Answer
```

If it cannot find a reliable answer:

```text
Ticket → Retrieve Knowledge → Low Confidence → Escalate
```

Critically, LoopKB never forces the AI to guess an answer when the knowledge base doesn't actually contain one.

### 2. Gap Detector Agent

Activates when the Resolver can't confidently answer and the issue gets resolved by a human or specialist. It compares the original problem against the actual resolution and asks a single question:

> What did the resolver learn that the current knowledge base didn't already know?

**Example**

Existing KB entry:
```text
After renewing the VPN certificate, reconnect to the VPN.
```

Human resolution:
```text
Reconnecting is not enough. The SecureTunnel service must be
restarted to refresh the certificate context.
```

Extracted by the Gap Detector:
```text
Knowledge Gap: YES
Reusable Solution: Restart the SecureTunnel service after certificate renewal.
Source: Ticket #4821
```

### 3. Knowledge Writer Agent

Converts the extracted resolution into a clean, structured, reusable knowledge entry — not a raw transcript dump:

```text
Title: VPN returns 403 after certificate renewal
Problem: VPN connects successfully, but internal applications
         return HTTP 403 after certificate renewal.
Resolution: Restart the SecureTunnel service after certificate renewal.
Evidence: Ticket #4821
Confidence: 94%
Status: Verify
```

Before publishing, LoopKB checks the proposed entry against existing knowledge for:

- Duplicate articles
- Similar existing solutions
- Conflicting instructions
- Missing evidence
- Low-confidence extractions

The generated entry is then queued for human review.

---

## The Loop, End to End

**Ticket #1** — a customer reports: *"VPN connects, but internal applications return 403 after certificate renewal."*

The Resolver Agent searches the KB:
```text
Confidence: 34% → No reliable answer found → Escalate
```

A human investigates and discovers: *"Restarting the SecureTunnel service refreshes the certificate context and resolves the 403 error."* The ticket is resolved.

Normally, that knowledge would stay buried in the closed ticket. LoopKB activates instead:

```text
Human Resolution
      ↓
Gap Detector → Knowledge Gap Found
      ↓
Knowledge Writer
      ↓
Duplicate / Conflict Check
      ↓
Knowledge Entry Created
```

### The Demo Moment

**Ticket #2** — we ask the same question again: *"I renewed my VPN certificate. VPN connects, but internal applications show 403. How do I fix it?"*

```text
Knowledge Found ✓
Confidence: 96%
Source: KB-183 (created from Ticket #4821)
```

The agent answers immediately, using the knowledge it just wrote. The audience watches the system learn, live:

```text
TICKET #1
❌ AI couldn't answer
       ↓
👤 Human solved it
       ↓
🧠 LoopKB extracted the knowledge
       ↓
📚 KB updated
       ↓
TICKET #2
✅ AI answers automatically
```

No slides required — the system demonstrates its own learning loop.

---

## Why This Is Different

Most support agents optimize for **answering the current ticket**. LoopKB optimizes for **making future tickets easier**.

Traditional workflow:
```text
Ticket → Answer → Close
```

LoopKB:
```text
Ticket → Answer / Escalate → Resolution → Learn → Verify → Knowledge → Better Future Answers
```

The core innovation isn't another chatbot — it's the feedback loop between operational resolution and organizational knowledge.

---

## Agentic Architecture

LoopKB deliberately separates responsibilities across focused agents rather than relying on one general-purpose chatbot:

| Agent | Responsibility |
|---|---|
| **Resolver Agent** | Resolve incoming support tickets using existing knowledge |
| **Gap Detector Agent** | Identify knowledge missing from the KB |
| **Knowledge Writer Agent** | Convert human resolutions into reusable knowledge entries |
| **Knowledge Validation Layer** | Detect duplicates/conflicts and assess confidence |

## Platform Agent Skills

LoopKB exposes its knowledge operations as reusable skills/tools, so the capability isn't locked to one support workflow:

```text
search_knowledge()
get_ticket()
get_ticket_history()
get_resolution()
detect_knowledge_gap()
generate_kb_entry()
check_duplicate()
check_conflict()
publish_kb_draft()
```

This makes the knowledge layer reusable by multiple enterprise agents, not just the support bot that happened to build it first.

---

## Architecture

```text
                    ┌─────────────────────┐
                    │   Support Ticket    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Resolver Agent    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Existing KB / RAG │
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
                    │                     │
               Confident              Uncertain
                    │                     │
                    ▼                     ▼
                 Answer              Escalation
                                          │
                                          ▼
                                Human / Specialist
                                          │
                                          ▼
                                   Resolution
                                          │
                                          ▼
                                ┌─────────────────┐
                                │  Gap Detector   │
                                └────────┬────────┘
                                         │
                                         ▼
                                ┌─────────────────┐
                                │ Knowledge Writer│
                                └────────┬────────┘
                                         │
                                         ▼
                                ┌─────────────────┐
                                │    Validator    │
                                └────────┬────────┘
                                         │
                                  Human Verification
                                         │
                                         ▼
                                ┌─────────────────┐
                                │   Updated KB    │
                                └────────┬────────┘
                                         │
                                         └───────────────► Resolver
```

---

## Human-in-the-Loop Knowledge Safety

LoopKB does not blindly turn every AI-generated extraction into trusted company knowledge:

```text
Resolution
    ↓
AI extracts knowledge
    ↓
Evidence check
    ↓
Duplicate check
    ↓
Conflict check
    ↓
Confidence score
    ↓
Human verification
    ↓
Trusted KB
```

Every generated entry keeps its lineage:

```text
Knowledge Article
      ↓
Source Ticket
      ↓
Human Resolution
      ↓
Extracted Solution
      ↓
Validation Results
      ↓
Human Verification
```

This gives enterprise teams a clear answer to: *where did this knowledge come from?*

---

## Technology Stack

**AI / Agent Layer**
- LLM-based agents
- Retrieval-Augmented Generation (RAG)
- Multi-agent orchestration
- Structured outputs

**Platform**
- Freshworks Agent Studio
- MCP integrations
- Reusable agent skills

**Backend**
- Python
- FastAPI

**Knowledge Layer**
- Vector database
- Embeddings
- Semantic retrieval
- Knowledge validation

**Frontend**
- React / JavaScript
- Support ticket interface
- Agent activity view
- Knowledge review dashboard

**Development**
- GitHub
- REST APIs
- JSON-based agent communication

---

## Why Our Team

LoopKB is closely aligned with systems we've already built and the engineering problems we enjoy solving.

**Distributed memory & vector infrastructure** — We've previously built a shared distributed memory layer for AI agents using a database-backed architecture with vector indexing, giving us direct experience with the storage and retrieval patterns LoopKB requires.

**Resolution summarization** — In a prior AI knowledge assistant project, we worked on extracting useful information from noisy conversational content before storing it — directly relevant to LoopKB's Gap Detector and Knowledge Writer agents.

**Multi-stage AI pipelines** — We've built multi-stage classification and triage pipelines involving intent classification, risk scoring, and readiness evaluation, which maps naturally onto LoopKB's agent workflow and validation stages.

**Rapid full-stack delivery** — Our experience spans Python, FastAPI, RAG, vector databases, LLM applications, agentic workflows, frontend development, Git/GitHub, and API integrations. We're comfortable taking an AI concept from architecture to a working end-to-end prototype under tight hackathon constraints.

### Team

| Name | Role | Relevant Experience |
|---|---|---|
| Niveditha | Backend & Agent Logic | Full-stack AI/ML engineer working with FastAPI, RAG pipelines, vector databases, and multi-agent systems; experience building agent memory layers and multi-stage AI pipelines |
| Gowthami | ML and gen AI |  |

---

## Expected Impact

LoopKB's goal isn't simply better chatbot responses — it's reducing repeated human effort. Key metrics we can evaluate:

- **Knowledge Capture Rate** — percentage of previously unresolved tickets that result in a reusable knowledge candidate
- **Knowledge Reuse Rate** — percentage of newly created KB entries that successfully resolve future tickets
- **Escalation Reduction** — how many tickets become automatically resolvable after the system learns from prior human resolutions
- **Time-to-Knowledge** — how quickly a human resolution becomes reusable organizational knowledge

---

## Future Vision

The initial prototype focuses on enterprise support, but the same knowledge loop applies across IT support, customer support, employee operations, product troubleshooting, HR operations, finance operations, and internal enterprise workflows generally.

The long-term vision: a continuously improving enterprise knowledge layer that learns from real operational experience, while keeping humans in control of what becomes trusted knowledge.

---

## Hackathon Track

**Track 2 — Platform Agent Skills & Knowledge**

LoopKB directly addresses the track's focus on reusable agent skills, enterprise knowledge, MCP integrations, and platform-level agent capabilities. Rather than building another single-purpose support bot, LoopKB provides a knowledge capability that multiple enterprise agents can use — and continuously improve.

---



---

### The One-Line Pitch

> LoopKB turns every unresolved support ticket into a chance to permanently improve the enterprise's knowledge — automatically extracting, validating, and reusing what humans learned.

**LoopKB — the knowledge base that learns from the work.**
