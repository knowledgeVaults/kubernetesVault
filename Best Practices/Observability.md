## Metrics Collection

Deploy a metrics pipeline from day one (Do not wait until production issues arise)

* Use **Prometheus** for metrics scraping; pair with **Grafana** for dashboards and alerting
* Deploy **[[Metrics Server]]** for `kubectl top` support and HPA/VPA autoscaler integration
* Instrument application pods with Prometheus annotations (`prometheus.io/scrape`, `prometheus.io/port`) for automatic discovery
* Set appropriate scrape intervals (15s–60s) based on metric volatility (Too-short intervals create load)

## Log Aggregation

Centralize logs from all pods and nodes into a searchable store

* Use **[[Fluent Bit]]** as the lightweight node-level log shipper ([[Daemon Sets]]) forwarding to [[Elasticsearch]], Loki, or a cloud log service
* Avoid writing logs to files inside containers — always write to stdout/stderr so the [[Container Runtime]] captures them
* Apply structured JSON logging in applications to enable efficient log filtering and querying
* Retain logs for at least 30 days; define retention policies in the log backend to control storage costs

## Distributed Tracing

Add distributed tracing for microservice architectures to identify latency bottlenecks

* Instrument services with **OpenTelemetry** SDKs for vendor-neutral trace propagation
* Deploy a tracing backend (Jaeger, Tempo) and configure the OTel Collector as an aggregation layer
* Trace sampling rates should be tuned — 100% sampling is rarely needed in production at scale

## Alerting

Define alerts on symptoms, not just causes, to reduce alert fatigue

* Alert on SLO burn rate, high error rate, and pod restart storms rather than raw CPU
* Use `PrometheusRule` CRDs if using the Prometheus Operator to manage alerting rules declaratively
* Route critical alerts to PagerDuty or equivalent; route warning-level alerts to Slack
* Always define a dead-man's switch alert to detect when the alerting pipeline itself is broken

## Health Probes

Define `livenessProbe`, `readinessProbe`, and `startupProbe` on all containers

* `readinessProbe` prevents traffic from reaching pods that are not yet ready to serve requests
* `livenessProbe` restarts containers that are alive but stuck in an unrecoverable state
* `startupProbe` gives slow-starting containers time to initialize before liveness checks begin
