# Distributed Booking Engine

[![Node.js](https://img.shields.io/badge/node.js-22-339933?logo=nodedotjs&logoColor=white)](https://nodejs.org/)
[![NestJS](https://img.shields.io/badge/NestJS-E0234E?logo=nestjs&logoColor=white)](https://nestjs.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white)](https://redis.io/)
[![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?logo=rabbitmq&logoColor=white)](https://www.rabbitmq.com/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)

---

## 🇺🇸 English Version

### What problem this solves

This project is a **Distributed Booking Engine**. It targets **race conditions** in **high-concurrency** domains—seat booking, locker allocation, inventory holds, and similar flows—where many clients compete for the same scarce resources. The goal is to keep data **correct under stress**: no double bookings, no lost updates, and predictable behavior when load spikes.

### Architecture

The codebase follows **Layered Architecture**, with clear boundaries between:

| Layer | Responsibility |
|--------|------------------|
| **Domain** | Entities, value objects, domain rules; no framework or infrastructure details. |
| **Application** | Use cases, orchestration, ports (interfaces); coordinates domain and infrastructure. |
| **Infrastructure** | Adapters: persistence, messaging, caches, external APIs. |
| **Presentation** | HTTP (REST), DTOs, validation, and API-specific concerns. |

Dependencies point **inward**: Presentation → Application → Domain; Infrastructure implements ports defined by Application/Domain.

### Engineering patterns

- **Distributed locking (Redis, Redlock-style)**  
  Coordinates critical sections across instances so two concurrent requests cannot both “win” the same resource—**preventing double-booking** when multiple app nodes run in parallel.

- **Transactional Outbox**  
  Persists domain events in the **same database transaction** as the business write, then a separate process publishes to **RabbitMQ**. This gives **at-least-once** friendly delivery and **eventual consistency** with the broker without risking “message sent but DB rolled back.”

- **Richardson Maturity Model — Level 3 (Hypermedia / HATEOAS)**  
  REST routes expose **resources** with **hypermedia controls** (e.g. links to next actions), not just JSON payloads—so clients can discover transitions from the API itself.

### Testing strategy

- **Test pyramid**: many **fast unit tests** at the base, fewer **integration tests** in the middle, and a small set of **end-to-end** checks at the top—balancing speed, confidence, and maintenance cost.
- **Testcontainers** spins up **real PostgreSQL and Redis** (and related dependencies as needed) for **integration tests**, so locking, transactions, and persistence behavior are validated against actual services—not only mocks.

### Stack (at a glance)

Node.js **22**, **NestJS**, **TypeScript**, **PostgreSQL**, **Redis**, **RabbitMQ**, **Docker** (for local parity and CI-friendly environments).

### Author note

All design and implementation of this codebase were **conceived and written by hand** by the author. **AI-assisted tools** were used **only** for technical assistance and documentation (for example, drafting or refining this README)—not as the primary author of the system’s architecture or production code.

---

## 🇧🇷 Versão em Português

### Qual problema estamos resolvendo

Este projeto é um **Distributed Booking Engine** (motor de reservas distribuído). O foco são **condições de corrida** em cenários de **alta concorrência**—reserva de assentos, alocação de lockers, bloqueio de estoque e fluxos parecidos—onde muitos clientes disputam os mesmos recursos limitados. O objetivo é manter a **integridade dos dados sob estresse**: sem reservas duplicadas, sem atualizações inconsistentes e com comportamento previsível sob pico de carga.

### Arquitetura

O código segue **Arquitetura em Camadas** (**Layered Architecture**), com limites explícitos entre:

| Camada | Responsabilidade |
|--------|---------------------|
| **Domain** | Entidades, objetos de valor, regras de negócio; sem detalhes de framework ou infraestrutura. |
| **Application** | Casos de uso, orquestração, portas (interfaces); coordena domínio e infraestrutura. |
| **Infrastructure** | Adaptadores: persistência, mensageria, caches, APIs externas. |
| **Presentation** | HTTP (REST), DTOs, validação e preocupações específicas da API. |

As dependências apontam **para dentro**: Presentation → Application → Domain; a **Infrastructure** implementa portas definidas pela Application/Domain.

### Padrões de engenharia

- **Distributed Locking com Redis (estilo Redlock)**  
  Coordena seções críticas entre instâncias para que duas requisições concorrentes não “ganhem” o mesmo recurso—**evitando double-booking** com vários nós da aplicação.

- **Transactional Outbox**  
  Persiste eventos de domínio na **mesma transação** do banco que a escrita de negócio; em seguida, um processo separado publica no **RabbitMQ**. Isso favorece entrega **at-least-once** e **consistência eventual** com o broker, sem o risco de “mensagem enviada e transação desfeita”.

- **Richardson Maturity Model — Nível 3 (Hypermedia / HATEOAS)**  
  As rotas REST expõem **recursos** com **controles de hipermídia** (por exemplo, links para próximas ações), não apenas payloads JSON—permitindo que o cliente descubra transições a partir da própria API.

### Estratégia de testes

- **Pirâmide de testes**: muitos **testes unitários** rápidos na base, menos **testes de integração** no meio e um conjunto reduzido de testes **ponta a ponta** no topo—equilibrando velocidade, confiança e custo de manutenção.
- **Testcontainers** sobe **PostgreSQL e Redis reais** (e dependências correlatas conforme necessário) nos **testes de integração**, validando locking, transações e persistência contra serviços de verdade—not só mocks.

### Stack (visão geral)

Node.js **22**, **NestJS**, **TypeScript**, **PostgreSQL**, **Redis**, **RabbitMQ**, **Docker** (paridade local e ambientes amigáveis a CI).

### Nota de autoria

Toda a concepção e implementação deste código foram **idealizadas e escritas à mão** pelo autor. **Ferramentas de IA** foram usadas **apenas** para assistência técnica e documentação (por exemplo, elaboração ou refinamento deste README)—não como autora principal da arquitetura ou do código de produção.
