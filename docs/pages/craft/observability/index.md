# Observability, logging & monitoring

This document outlines a comprehensive document classification for observability, logging, and monitoring signals within a software system.

This document classifies observability into two major areas:

1. **System / Technical observability**
2. **Business / Functional observability**

The goal is to provide clear visibility into both **system health** and **business outcomes**.


| System / Technical observability | Business / Functional observability |
|---|---|
| [1.1 Infrastructure observability](#11-infrastructure-observability)<br>[1.2 Application observability](#12-application-observability)<br>[1.3 Dependency & integration observability](#13-dependency-integration-observability)<br>[1.4 Reliability & resilience signals](#14-reliability-resilience-signals)<br>[1.5 Security & compliance observability](#15-security-compliance-observability)<br>[1.6 Operational alerts](#16-operational-alerts) | [2.1 User experience & journey observability](#21-user-experience-journey-observability)<br>[2.2 Business process monitoring](#22-business-process-monitoring)<br>[2.3 Domain & business metrics](#23-domain-business-metrics)<br>[2.4 Functional error monitoring](#24-functional-error-monitoring)<br>[2.5 Data observability](#25-data-observability)<br>[2.6 Customer impact & support Signals](#26-customer-impact-support-signals)<br>[2.7 Business alerts & notifications](#27-business-alerts-notifications) |


## 1. System / Technical observability

**Focus:**  
Is the system healthy, reliable, performant, and debuggable?

**Primary Consumers:**  
Engineering, SRE, Platform, DevOps teams

---

### 1.1 Infrastructure observability

**Scope:** Cloud, VMs, containers, networking

- CPU, memory, disk, and IO utilization
- Node / VM availability and uptime
- Kubernetes:
    * Pod restarts, crash loops, OOMKills
    * Node pressure (memory, disk)
    * Deployment health and replica count
- Network latency, packet loss
- Ingress / egress errors
- Autoscaling events

---

### 1.2 Application observability

**Scope:** Application runtime behavior

#### Metrics

- Request rate (RPS)
- Error rate (4xx / 5xx)
- Latency (p50, p95, p99)
- Thread pool usage
- JVM metrics (heap usage, GC pauses)

#### Logs

- Structured logs (e.g., JSON)
- Log levels (INFO, WARN, ERROR)
- Exception stack traces
- Correlation IDs / Trace IDs
- Startup and configuration logs

#### Traces

- Distributed traces across services
- Span duration and timing
- Downstream dependency latency
- Error propagation across services

---

### 1.3 Dependency & integration observability

**Scope:** Internal and external dependencies

- Database query latency
- Connection pool saturation
- Cache hit / miss ratio
- Message queue lag (Kafka, RabbitMQ, etc.)
- External API success / failure rates
- Circuit breaker states (open / half-open)

---

### 1.4 Reliability & resilience signals

**Scope:** System stability and failure handling

- Retry counts
- Timeouts
- Failover events
- Circuit breaker activations
- Rate limiting triggers
- Bulkhead saturation

---

### 1.5 Security & compliance observability

**Scope:** System-level security signals

- Authentication and authorization failures
- Suspicious access patterns
- Certificate expiration
- Secrets access
- Audit logs (who accessed what and when)

---

### 1.6 Operational alerts

**Scope:** Actionable alerts for engineering teams

- SLA / SLO violations
- Threshold-based alerts
- Anomaly detection alerts
- Error budget burn-rate alerts
- On-call paging alerts

---

## 2. Business / Functional observability

**Focus:**  
Are users successful? Are business processes working as expected?

**Primary Consumers:**  
Product, Operations, Business stakeholders

---

### 2.1 User experience & journey observability

**Scope:** End-user behavior and experience

- Page load time and API response time (user-perceived)
- Mobile and browser performance
- Session duration
- Drop-off points in user journeys
- Error rates per user flow
- Feature usage frequency

---

### 2.2 Business process monitoring

**Scope:** End-to-end business workflows

**Examples:**

- Order creation → payment → fulfillment
- Signup → verification → activation
- Data ingestion → processing → reporting

**Metrics:**

- Success vs failure rate
- Processing time per step
- Workflow bottlenecks
- Reprocessing and retries

---

### 2.3 Domain & business metrics

**Scope:** Core business KPIs

- Orders per hour / day
- Revenue per transaction
- Conversion rate
- Active users (DAU / MAU)
- Payment success rate
- Refund and cancellation rates

> These metrics must align with agreed business definitions.

---

### 2.4 Functional error monitoring

**Scope:** Business-meaningful failures

- Payment declined (categorized by reason)
- Inventory unavailable
- Business rule validation failures
- Data quality issues (missing, invalid, inconsistent data)
- SLA breaches for business operations

---

### 2.5 Data observability

**Scope:** Trust and reliability of business data

- Data freshness and timeliness
- Completeness (missing or partial data)
- Accuracy and validity checks
- Schema drift
- Pipeline failures impacting reports or dashboards

---

### 2.6 Customer impact & support Signals

**Scope:** Downstream business and customer impact

- Customer-impacting incidents
- Support tickets correlated with system errors
- Impact radius (number of users affected)
- Duration of customer-visible outages

---

### 2.7 Business alerts & notifications

**Scope:** Business-level notifications

- Sudden drop in transactions
- Spike in failed payments
- Unusual user behavior
- Regulatory threshold breaches
- Revenue anomalies

---

## 3. Cross-Cutting dimensions

These dimensions apply to both **System** and **Business** observability.

### 3.1 Correlation

- Logs ↔ Metrics ↔ Traces
- Business events linked to technical signals
- Correlation via Trace ID, Order ID, User ID

---

### 3.2 Ownership

- Clear ownership per signal
- Defined escalation paths
- Runbooks and remediation guides

---

### 3.3 SLIs & SLOs

- Technical SLOs (availability, latency)
- Business SLOs (checkout success rate, data freshness)

---

### 3.4 Visualization & reporting

- Engineering dashboards
- Business and executive dashboards
- Incident timelines and postmortems

---

## 4. Summary

| Dimension | System / Technical          | Business / Functional         |
|-----------|-----------------------------|-------------------------------|
| Focus     | System health & reliability | Business outcomes             |
| Consumers | Engineering, SRE            | Product, Operations           |
| Signals   | Metrics, logs, traces       | KPIs, workflows               |
| Failures  | Errors, latency, outages    | Revenue loss, failed journeys |
| Alerts    | On-call paging              | Business notifications        |
