# Spring AI + Amazon Bedrock Chatbot

A production-grade AI chatbot built with Spring AI and Amazon Bedrock — not a toy demo, but a system designed with the same principles you'd apply to any high-traffic backend: guardrails, streaming, memory management, and cost control.

---

## The Problem

Most LLM integrations are bolted onto existing systems as an afterthought — no guardrails, no cost controls, no conversation memory, no streaming. They work in demos and fall apart in production. This project was built to show what a proper production LLM integration looks like in a Java/Spring ecosystem.

---

## Architecture
- - **Frontend:** React 18, WebSockets for streaming responses
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
SystemPromptTemplate systemPrompt = new SystemPromptTemplate("""
    You are a helpful assistant for {domain}.
    
    Rules:
    - Only answer questions related to {domain}
    - Always cite sources when making factual claims
    - If unsure, say so rather than guessing
    - Keep responses concise and actionable
    
    Context: {context}
    """);
```

Prompts are templated, versioned, and tested — not hardcoded strings.

---

## Guardrails Configuration

```yaml
bedrock:
  guardrails:
    enabled: true
    blocked-topics:
      - politics
      - medical-advice
      - legal-advice
    content-filters:
      hate: HIGH
      violence: HIGH
      sexual: HIGH
    pii-detection: true
```

---

## Getting Started

```bash
# Clone the repo
git clone https://github.com/sainived21/spring-ai-bedrock-chatbot.git

# Configure AWS credentials
export AWS_ACCESS_KEY_ID=your_key
export AWS_SECRET_ACCESS_KEY=your_secret
export AWS_REGION=us-east-1

# Start infrastructure
docker-compose up -d

# Run application
./mvnw spring-boot:run
```

**Prerequisites:** Java 17+, Docker, AWS account with Bedrock access

---

## Configuration

```yaml
spring:
  ai:
    bedrock:
      aws:
        region: us-east-1
      titan:
        chat:
          enabled: true
          model: amazon.titan-text-express-v1
          
conversation:
  memory:
    max-tokens: 4000
    redis-ttl-minutes: 30
    
cost:
  max-tokens-per-request: 1000
  alert-threshold-usd: 0.10
```

---

## Performance

| Metric | Result |
|--------|--------|
| Time to first token | < 800ms |
| Streaming latency | Real-time |
| Conversation memory | 30 min TTL |
| Guardrail evaluation | < 100ms overhead |
| Cost per request | ~$0.002 avg |

---

## Tech Stack

![Java](https://img.shields.io/badge/Java-17%2B-orange?style=flat-square&logo=java)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen?style=flat-square&logo=springboot)
![Spring AI](https://img.shields.io/badge/Spring%20AI-LLM%20Integration-brightgreen?style=flat-square&logo=spring)
![Amazon Bedrock](https://img.shields.io/badge/Amazon%20Bedrock-Claude%20%7C%20Titan-FF9900?style=flat-square&logo=amazonaws)
![Redis](https://img.shields.io/badge/Redis-Conversation%20Memory-DC382D?style=flat-square&logo=redis)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Audit%20Logs-336791?style=flat-square&logo=postgresql)
![React](https://img.shields.io/badge/React-18%2B-61DAFB?style=flat-square&logo=react)
![Prometheus](https://img.shields.io/badge/Prometheus-Token%20Tracking-E6522C?style=flat-square&logo=prometheus)

---

## Observability

Every LLM call is instrumented:

```java
@Observed(name = "bedrock.chat", 
          contextualName = "bedrock-chat-request",
          lowCardinalityKeyValues = {"model", "amazon.titan-text-express-v1"})
public ChatResponse chat(String userMessage, String sessionId) {
    // tracked automatically by Micrometer + Prometheus
}
```

Token usage, latency, and cost tracked per request in Grafana.

---

## Author

**Sai Nived Mula** — [LinkedIn](https://linkedin.com/in/sainived21) · [Email](mailto:sainived2721@gmail.com)
