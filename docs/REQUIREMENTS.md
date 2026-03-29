# Distributed Booking Engine — Requirements

Functional requirements (FR) and non-functional requirements (NFR).

---

## 🇺🇸 English Version

### Functional requirements (FR)

What the system must do from a user and business perspective.

| ID | Requirement | Description |
|----|---------------|-------------|
| **RF01** | Resource management | The system shall allow registration of bookable resources (e.g. seats, rooms, lockers). |
| **RF02** | Availability query | The system shall return resources available within a specific time window. |
| **RF03** | Resource booking | Users shall be able to book a resource. New bookings shall start in `PENDING_PAYMENT` status. |
| **RF04** | Automatic expiration | Unpaid bookings after *X* minutes shall be cancelled automatically, releasing the resource. |
| **RF05** | Booking confirmation | After a simulated payment webhook, the booking shall transition to `CONFIRMED`. |
| **RF06** | Success notification | The system shall publish an event (via Outbox) to notify the user of confirmation. |
| **RF07** | My bookings query | The system shall expose an endpoint to list bookings filtered by user and status, using efficient pagination to limit database overhead. |

### Non-functional requirements (NFR)

Architecture, quality, and operational constraints.

| ID | Requirement | Description |
|----|-------------|-------------|
| **RNF01** | High concurrency *(critical)* | The system shall ensure a resource is never booked by two users at the same time (double-booking prevention) using **distributed locking**. |
| **RNF02** | Eventual consistency | The **transactional outbox pattern** shall ensure that if the database commits the booking, the notification event will be sent even if the broker (RabbitMQ) is temporarily unstable. |
| **RNF03** | Performance / latency | The checkout path shall not exceed **200 ms** (strict latency SLA). |
| **RNF04** | Idempotency | Booking creation and payment confirmation APIs shall be **idempotent** to avoid duplicates from network failures. |
| **RNF05** | Observability | The API shall expose **Prometheus metrics** and **structured logs** for health and bottleneck monitoring. |
| **RNF06** | Horizontal scalability | The service shall be **stateless** and **Docker-containerized**, supporting multiple instances behind a load balancer. |

### Constants and glossary (reference)

- **`PENDING_PAYMENT`**: initial booking state after RF03.
- **`CONFIRMED`**: state after payment confirmation (RF05).
- **`X` (RF04)**: maximum minutes before unpaid bookings auto-expire—a business parameter (config or environment variable).

---

## 🇧🇷 Versão em Português

### Requisitos funcionais (RF)

O que o sistema precisa fazer do ponto de vista do usuário e do negócio.

| ID | Requisito | Descrição |
|----|-----------|-----------|
| **RF01** | Gestão de recursos | O sistema deve permitir o cadastro de recursos reserváveis (ex.: assentos, salas, lockers). |
| **RF02** | Consulta de disponibilidade | O sistema deve retornar recursos disponíveis em uma janela de tempo específica. |
| **RF03** | Reserva de recurso | O usuário deve conseguir reservar um recurso. A reserva deve nascer com o status `PENDING_PAYMENT`. |
| **RF04** | Expiração automática | Reservas não pagas em *X* minutos devem ser canceladas automaticamente, liberando o recurso. |
| **RF05** | Confirmação de reserva | Após a simulação de um webhook de pagamento, a reserva deve passar para `CONFIRMED`. |
| **RF06** | Notificação de sucesso | O sistema deve disparar um evento (via Outbox) para notificar o usuário sobre a confirmação. |
| **RF07** | Consulta de meus agendamentos | Endpoint para listagem de reservas filtradas por usuário e status, seguindo paginação eficiente para evitar overhead no banco. |

### Requisitos não funcionais (RNF)

Aspectos de arquitetura, qualidade e operação do sistema.

| ID | Requisito | Descrição |
|----|-----------|-----------|
| **RNF01** | Alta concorrência *(crítico)* | O sistema deve garantir que um recurso nunca seja reservado por dois usuários simultaneamente (prevenção de double-booking), usando **Distributed Locking**. |
| **RNF02** | Consistência eventual | O **Transactional Outbox Pattern** deve garantir que, se o banco de dados confirmar a reserva, o evento de notificação será enviado, mesmo que o broker (RabbitMQ) oscile momentaneamente. |
| **RNF03** | Performance / latência | O processo de check-out não deve ultrapassar **200 ms** (SLA de latência elevado). |
| **RNF04** | Idempotência | As APIs de criação de reserva e de confirmação de pagamento devem ser **idempotentes**, evitando duplicidade por falhas de rede. |
| **RNF05** | Observabilidade | A API deve expor **métricas (Prometheus)** e **logs estruturados** para monitoramento de saúde e gargalos. |
| **RNF06** | Escalabilidade horizontal | O serviço deve ser **stateless** e **containerizado com Docker**, permitindo múltiplas instâncias atrás de um load balancer. |

### Constantes e glossário (referência)

- **`PENDING_PAYMENT`**: estado inicial da reserva após RF03.
- **`CONFIRMED`**: estado após confirmação de pagamento (RF05).
- **`X` (RF04)**: tempo máximo em minutos para pagamento antes do cancelamento automático—valor de negócio a parametrizar (configuração ou variável de ambiente).
