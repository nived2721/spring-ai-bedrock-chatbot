# Spring AI + Amazon Bedrock Chatbot

A production-grade AI chatbot built with Spring AI and Amazon Bedrock — not a toy demo, but a system designed with the same principles you'd apply to any high-traffic backend: guardrails, streaming, memory management, and cost control.

---

## The Problem

Most LLM integrations are bolted onto existing systems as an afterthought — no guardrails, no cost controls, no conversation memory, no streaming. They work in demos and fall apart in production. This project was built to show what a proper production LLM integration looks like in a Java/Spring ecosystem.

---

## Architecture
- **Frontend:** React 18, WebSockets for streaming responses
- **Backend:** Java, Spring Boot, Spring AI
- **LLM Provider:** Amazon Bedrock (Claude / Titan models)
- **Memory:** Redis for conversation context management
- **Safety:** Amazon Bedrock Guardrails
- **Persistence:** PostgreSQL for audit logging
- **Observability:** Prometheus + Grafana for token usage and latency tracking

---

## Key Features

- **Streaming responses** — tokens stream to the frontend in real time via WebSockets, no waiting for full response
- **Conversation memory** — Redis maintains context window across turns so the model remembers what was said
- **Prompt engineering** — structured system prompts with role definitions, constraints, and output formatting
- **Guardrails** — Amazon Bedrock Guardrails filter harmful content and enforce topic boundaries
- **Cost optimization** — token usage tracked per request, conversation history pruned intelligently to control cost
- **Audit logging** — every request/response logged to PostgreSQL for compliance and debugging
- **Latency monitoring** — Prometheus tracks p50/p99 response times per model

---

## Why Spring AI over LangChain?

LangChain4j is the popular choice for Java LLM integration but Spring AI integrates natively with the Spring ecosystem — same dependency injection, same configuration model, same testing patterns. For teams already running Spring Boot in production, Spring AI means zero new abstractions to learn and seamless integration with existing security, observability, and data layers.

---

## Prompt Engineering Approach

```java
