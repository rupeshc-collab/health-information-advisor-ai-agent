# Architecture — Health Information Advisor

This document explains how the Health Information Advisor agent processes a user query end-to-end, from question to cited answer.

## High-Level Flow

```
User Question
     │
     ▼
[1] Intent Understanding
     │  The agent interprets what the user is asking —
     │  a health condition, a treatment, or general
     │  healthy-living advice.
     ▼
[2] Reasoning Step ("Thinking...")
     │  The agent reasons through the query before acting,
     │  visible in Copilot Studio's test interface.
     ▼
[3] Knowledge Retrieval ("Search Sources")
     │  The agent searches its connected knowledge base,
     │  grounded in trusted external sources.
     ▼
[4] Source Validation
     │  Retrieved content is checked against the agent's
     │  knowledge configuration (Mayo Clinic, CDC, etc.)
     ▼
[5] Response Synthesis
     │  The agent combines retrieved information into a
     │  clear, structured answer — citing its sources.
     ▼
Final Answer (with citations)
```

## Component Breakdown

### 1. Intent Understanding
The agent's instructions (see [`agent-instructions.md`](../instructions/agent-instructions.md)) define its scope: health conditions, treatments, and healthy-living advice. Anything outside this scope is redirected or declined, keeping the agent from overstepping into diagnosis or prescription.

### 2. Reasoning Step
Before retrieving information, the agent visibly reasons through the query — for example, recognizing "How can I improve my heart health?" as a healthy-living question requiring comprehensive, multi-point guidance. This step is transparent in Copilot Studio's test panel rather than hidden from the user.

### 3. Knowledge Retrieval
The agent is connected to a knowledge layer in Copilot Studio configured with trusted medical sources. When a query comes in, the agent issues a search against this knowledge base rather than relying solely on the underlying model's training data.

### 4. Source Validation
Each retrieved result includes its originating source (e.g., `mayoclinic.org`, `cdc.gov`). This is what allows the final response to cite specific, verifiable sources rather than presenting unattributed claims.

### 5. Response Synthesis
The underlying model (Claude Sonnet 4.6) takes the retrieved, source-grounded content and synthesizes it into a clear, structured response — using headers, short explanations, and a tone defined by the agent's instructions (professional, empathetic, supportive).

## Why This Architecture Matters

The core design principle behind this agent is **grounding over generation** — rather than letting the model answer purely from its training data (which risks hallucination, especially in a health context), every response is tied to a retrieval step against trusted sources. This mirrors the broader industry pattern often called **RAG (Retrieval-Augmented Generation)**, applied here through Copilot Studio's built-in knowledge configuration rather than a custom-built vector database.

## Tools Used

| Component | Tool |
|---|---|
| Agent orchestration & UI | Microsoft Copilot Studio |
| Reasoning & language generation | Claude Sonnet 4.6 |
| Knowledge grounding | Copilot Studio Knowledge (configured with Mayo Clinic, CDC sources) |
| Testing & validation | Copilot Studio Test panel |
