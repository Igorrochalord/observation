# Observability Platform — Open Source Cloud Monitoring

## Visão Geral

Este projeto tem como objetivo construir uma plataforma de observabilidade open source para monitorar aplicações, Kubernetes, servidores, serviços de cloud, logs, métricas, traces e eventos operacionais.

A proposta é criar uma base padronizada de observabilidade usando **OpenTelemetry** como camada central de coleta, processamento e roteamento de telemetria. A stack será validada inicialmente em ambiente local e, posteriormente, adaptada para execução em ambientes de cloud, como a Magalu Cloud.

O foco do projeto não é apenas monitorar Kubernetes, mas sim criar uma visão completa da infraestrutura e das aplicações que compõem o ambiente.

## Objetivo Principal

Criar uma plataforma open source capaz de responder rapidamente perguntas como:

* A aplicação está saudável?
* O cluster Kubernetes está funcionando corretamente?
* Algum pod está reiniciando?
* Alguma VM está com CPU, memória ou disco alto?
* Existem erros nos logs?
* Qual serviço está com latência alta?
* Houve falha em banco, storage, rede ou aplicação?
* O problema está na aplicação, no cluster ou na infraestrutura?
* Existem traces relacionados ao erro?
* Existem eventos de cloud relacionados ao incidente?

## Escopo do Projeto

A plataforma deve cobrir, de forma evolutiva:

* Aplicações.
* APIs.
* Kubernetes.
* Pods, deployments, services e namespaces.
* VMs e servidores Linux.
* Logs de sistema e aplicações.
* Métricas de CPU, memória, disco e rede.
* Traces distribuídos.
* Eventos e auditoria de cloud.
* Serviços gerenciados.
* Storage.
* Banco de dados.
* Load balancers.
* Endpoints HTTP/TCP/DNS.
* Alertas operacionais.

## Decisão Arquitetural

O projeto adotará **OpenTelemetry** como padrão de instrumentação, coleta e roteamento de telemetria.

O OpenTelemetry será usado para padronizar a entrada dos dados observáveis da plataforma, incluindo:

* Métricas.
* Logs.
* Traces.
* Metadados de aplicações.
* Metadados de infraestrutura.
* Correlação entre serviços.

A ideia é evitar dependência direta de um único fornecedor ou backend específico.

## Arquitetura Conceitual

```text
Aplicações / APIs / Serviços
        ↓
OpenTelemetry SDK / Auto Instrumentation
        ↓
OpenTelemetry Collector
        ↓
Métricas → Prometheus / VictoriaMetrics / Thanos
Logs     → Loki / OpenSearch
Traces   → Tempo / Jaeger
Alertas  → Alertmanager
        ↓
Grafana
```

## Arquitetura Ampliada

```text
Cloud / Kubernetes / VMs / Aplicações
│
├── Aplicações
│   ├── OpenTelemetry SDK
│   ├── OTLP metrics
│   ├── OTLP logs
│   └── OTLP traces
│
├── Kubernetes
│   ├── OpenTelemetry Collector
│   ├── OpenTelemetry Operator
│   ├── kube-state-metrics
│   ├── node-exporter
│   └── logs dos containers
│
├── VMs / Servidores
│   ├── OpenTelemetry Collector Agent
│   ├── hostmetrics receiver
│   ├── filelog receiver
│   └── system logs
│
├── Cloud
│   ├── eventos de auditoria
│   ├── eventos de infraestrutura
│   ├── storage
│   ├── banco de dados
│   ├── load balancer
│   └── rede
│
└── Backends
    ├── Prometheus / VictoriaMetrics / Thanos
    ├── Loki / OpenSearch
    ├── Tempo / Jaeger
    ├── Grafana
    └── Alertmanager
```

## Stack Open Source Proposta

### Coleta e Instrumentação

| Função             | Ferramenta                    | Objetivo                                            |
| ------------------ | ----------------------------- | --------------------------------------------------- |
| Coleta central     | OpenTelemetry Collector       | Receber, processar e enviar métricas, logs e traces |
| Kubernetes         | OpenTelemetry Operator        | Gerenciar collectors e auto-instrumentação          |
| Aplicações         | OpenTelemetry SDK             | Instrumentar aplicações                             |
| Servidores         | OpenTelemetry Collector Agent | Coletar métricas e logs de VMs                      |
| Kubernetes metrics | kube-state-metrics            | Coletar estado de objetos Kubernetes                |
| Node metrics       | node-exporter                 | Coletar métricas dos nodes                          |

### Métricas

| Ferramenta      | Uso                                                  |
| --------------- | ---------------------------------------------------- |
| Prometheus      | Consulta e armazenamento inicial de métricas         |
| VictoriaMetrics | Retenção longa e maior escala                        |
| Thanos          | Alta disponibilidade, multi-cluster e object storage |
| Alertmanager    | Gerenciamento e roteamento de alertas                |

### Logs

| Ferramenta              | Uso                                                |
| ----------------------- | -------------------------------------------------- |
| Loki                    | Logs operacionais e integração com Grafana         |
| OpenSearch              | Busca avançada, auditoria e análise textual pesada |
| Promtail                | Coleta simples de logs Kubernetes                  |
| OpenTelemetry Collector | Coleta padronizada de logs                         |

### Traces

| Ferramenta | Uso                                              |
| ---------- | ------------------------------------------------ |
| Tempo      | Tracing distribuído integrado ao Grafana         |
| Jaeger     | Tracing distribuído open source e vendor-neutral |

### Visualização

| Ferramenta  | Uso                                               |
| ----------- | ------------------------------------------------- |
| Grafana OSS | Dashboards, exploração de métricas, logs e traces |

### Complementares

| Ferramenta        | Uso                                       |
| ----------------- | ----------------------------------------- |
| Blackbox Exporter | Monitoramento HTTP, TCP, DNS e ICMP       |
| OpenCost          | Custo por namespace, workload e aplicação |
| Falco             | Segurança runtime em containers e hosts   |
| OPA Gatekeeper    | Policy as Code para Kubernetes            |
| Pyroscope         | Continuous profiling                      |

## Estratégia de Implantação

A implantação será feita em fases para reduzir complexidade e validar cada camada separadamente.

## Fase 1 — Ambiente Local

Objetivo: criar um laboratório local para validar a stack.

Componentes:

* Docker.
* kind.
* kubectl.
* Helm.
* Grafana.
* Prometheus.
* Alertmanager.
* OpenTelemetry Collector.
* Loki.
* Aplicação demo.

Fluxo esperado:

```text
Mac local
└── Docker
    └── kind
        └── Kubernetes local
            └── namespace observability
                ├── OpenTelemetry Collector
                ├── Prometheus
                ├── Grafana
                ├── Alertmanager
                ├── Loki
                └── aplicação demo
```

## Fase 2 — Métricas

Objetivo: coletar métricas do cluster Kubernetes local.

Componentes:

* kube-prometheus-stack.
* Prometheus.
* Grafana.
* Alertmanager.
* kube-state-metrics.
* node-exporter.

Validações:

* Nodes visíveis no Grafana.
* Pods visíveis por namespace.
* Uso de CPU e memória.
* Status dos deployments.
* Métricas de containers.
* Métricas de volumes.
* Dashboards básicos funcionando.

## Fase 3 — Logs

Objetivo: coletar logs de aplicações e componentes do cluster.

Componentes:

* Loki.
* Promtail ou OpenTelemetry Collector.
* Grafana Explore.

Validações:

* Logs dos pods visíveis.
* Logs filtrados por namespace.
* Logs filtrados por aplicação.
* Logs de erro encontrados no Grafana.
* Correlação básica entre aplicação e logs.

## Fase 4 — OpenTelemetry Collector

Objetivo: usar o OpenTelemetry Collector como camada padrão de entrada.

Responsabilidades do Collector:

* Receber dados via OTLP.
* Coletar métricas de host.
* Coletar logs de arquivos.
* Coletar métricas Prometheus.
* Processar e enriquecer telemetria.
* Enviar métricas para Prometheus.
* Enviar logs para Loki.
* Enviar traces para Tempo ou Jaeger.

Exemplo conceitual:

```text
Aplicação demo
        ↓ OTLP
OpenTelemetry Collector
        ↓
Prometheus / Loki / Tempo
        ↓
Grafana
```

## Fase 5 — Traces Distribuídos

Objetivo: instrumentar uma aplicação e visualizar traces.

Componentes:

* OpenTelemetry SDK.
* OpenTelemetry Collector.
* Tempo ou Jaeger.
* Grafana.

Validações:

* Aplicação enviando traces via OTLP.
* Trace visível no Grafana.
* Latência por requisição.
* Erros por rota.
* Correlação entre trace ID e logs.
* Visualização de chamadas entre serviços.

## Fase 6 — VMs e Servidores

Objetivo: expandir a observabilidade para fora do Kubernetes.

Componentes:

* OpenTelemetry Collector Agent.
* hostmetrics receiver.
* filelog receiver.
* Prometheus receiver.
* Loki.
* Prometheus ou VictoriaMetrics.

Coletas esperadas:

* CPU.
* Memória.
* Disco.
* Filesystem.
* Rede.
* Processos.
* Logs de sistema.
* Logs de serviços.
* Logs de NGINX, banco de dados ou aplicações.

## Fase 7 — Cloud

Objetivo: observar recursos da cloud e eventos de infraestrutura.

Escopo esperado:

* Kubernetes gerenciado.
* VMs.
* Object Storage.
* Block Storage.
* Load Balancers.
* Bancos de dados.
* Rede.
* IAM.
* Eventos de auditoria.
* Eventos operacionais.

Estratégia:

* Coletar métricas expostas pelos serviços.
* Coletar logs e eventos disponíveis.
* Criar integrações com APIs da cloud quando necessário.
* Transformar eventos de cloud em logs consultáveis.
* Criar dashboards por serviço.
* Criar alertas para eventos críticos.

## Estrutura do Repositório

```text
observability-platform/
├── README.md
├── kind/
│   └── cluster.yaml
├── helm-values/
│   ├── kube-prometheus-stack.yaml
│   ├── opentelemetry-collector.yaml
│   ├── loki.yaml
│   ├── promtail.yaml
│   ├── tempo.yaml
│   └── grafana.yaml
├── manifests/
│   ├── namespaces.yaml
│   ├── demo-app.yaml
│   └── otel-instrumentation.yaml
├── scripts/
│   ├── 01-create-kind.sh
│   ├── 02-install-monitoring.sh
│   ├── 03-install-opentelemetry.sh
│   ├── 04-install-loki.sh
│   ├── 05-install-tempo.sh
│   ├── 06-demo-app.sh
│   └── 99-cleanup.sh
├── dashboards/
│   ├── kubernetes/
│   ├── applications/
│   ├── infrastructure/
│   └── cloud/
├── alerts/
│   ├── kubernetes-rules.yaml
│   ├── infrastructure-rules.yaml
│   └── application-rules.yaml
├── runbooks/
│   ├── pod-crashloop.md
│   ├── node-disk-high.md
│   ├── high-latency.md
│   └── service-down.md
└── docs/
    ├── architecture.md
    ├── local-setup.md
    ├── cloud-setup.md
    └── troubleshooting.md
```

## Pré-requisitos Locais

Para executar o laboratório local:

* Docker.
* kubectl.
* Helm.
* kind.
* Visual Studio Code.

Instalação sugerida no macOS:

```bash
brew install kind kubectl helm
```

## Criar Cluster Local

```bash
kind create cluster --name observability-lab
```

Validar:

```bash
kubectl config current-context
kubectl get nodes
```

## Criar Namespace

```bash
kubectl create namespace observability
```

## Instalar kube-prometheus-stack

Adicionar repositório Helm:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Instalar:

```bash
helm upgrade --install monitoring prometheus-community/kube-prometheus-stack \
  --namespace observability
```

Validar:

```bash
kubectl get pods -n observability
```

## Acessar Grafana

Obter senha:

```bash
kubectl get secret -n observability monitoring-grafana \
  -o jsonpath="{.data.admin-password}" | base64 -d
```

Abrir acesso local:

```bash
kubectl -n observability port-forward svc/monitoring-grafana 3000:80
```

Acessar:

```text
http://localhost:3000
```

Credenciais:

```text
Usuário: admin
Senha: senha retornada no comando anterior
```

## Instalar OpenTelemetry Collector

Adicionar repositório:

```bash
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
```

Instalar Collector:

```bash
helm upgrade --install otel-collector open-telemetry/opentelemetry-collector \
  --namespace observability
```

Validar:

```bash
kubectl get pods -n observability
```

## Instalar Loki

Adicionar repositório Grafana:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

Instalar Loki:

```bash
helm upgrade --install loki grafana/loki \
  --namespace observability
```

## Aplicação Demo

Criar namespace:

```bash
kubectl create namespace demo
```

Criar aplicação:

```bash
kubectl create deployment nginx-demo --image=nginx -n demo
kubectl expose deployment nginx-demo --port=80 --target-port=80 -n demo
```

Acessar aplicação:

```bash
kubectl -n demo port-forward svc/nginx-demo 8080:80
```

Gerar tráfego:

```bash
curl http://localhost:8080
curl http://localhost:8080/teste
curl http://localhost:8080/erro
```

## Padrão de Logs

As aplicações devem preferencialmente emitir logs estruturados em JSON.

Exemplo:

```json
{
  "timestamp": "2026-06-26T12:00:00Z",
  "level": "error",
  "service": "checkout-api",
  "environment": "dev",
  "message": "erro ao processar requisição",
  "trace_id": "abc123",
  "span_id": "def456",
  "status_code": 500
}
```

Campos recomendados:

* timestamp.
* level.
* service.
* environment.
* message.
* trace_id.
* span_id.
* status_code.
* route.
* method.
* namespace.
* cluster.

## Padrão de Labels Kubernetes

As aplicações devem usar labels padronizadas.

```yaml
metadata:
  labels:
    app.kubernetes.io/name: minha-api
    app.kubernetes.io/part-of: plataforma
    app.kubernetes.io/component: backend
    app.kubernetes.io/environment: dev
    team: engenharia
```

## Cuidado com Cardinalidade

Evitar usar como labels:

* CPF.
* E-mail.
* User ID.
* Request ID.
* Session ID.
* Token.
* IP de cliente.
* Payload.
* Campos únicos por requisição.

Esses dados podem existir no corpo do log, mas não devem ser usados como labels de métricas ou logs.

## Alertas Iniciais

### Kubernetes

* Node indisponível.
* Pod em CrashLoopBackOff.
* Deployment com réplicas indisponíveis.
* Pod reiniciando muitas vezes.
* PVC acima de 80%.
* Namespace consumindo muitos recursos.

### Infraestrutura

* CPU alta.
* Memória alta.
* Disco alto.
* Erro de filesystem.
* Rede com erro.
* Serviço systemd parado.

### Aplicação

* Taxa de erro HTTP 5xx alta.
* Latência p95 alta.
* Requisições falhando.
* Erros recorrentes nos logs.
* Falha de dependência externa.
* Timeout em banco ou API.

### Cloud

* Evento crítico de auditoria.
* Recurso deletado.
* Alteração de IAM.
* Falha em backup.
* Volume com erro.
* Bucket com alteração sensível.
* Load balancer sem backend saudável.

## Runbooks

Cada alerta deve ter um runbook associado.

Exemplo:

```text
Alerta: Pod em CrashLoopBackOff

Verificações:
1. Verificar logs do pod.
2. Verificar eventos do Kubernetes.
3. Verificar última imagem publicada.
4. Verificar variáveis de ambiente.
5. Verificar secrets e configmaps.
6. Verificar uso de memória.
7. Verificar se houve OOMKilled.
```

## Critérios de Sucesso do MVP

O MVP local será considerado válido quando for possível:

* Criar um cluster local com kind.
* Instalar Prometheus e Grafana.
* Visualizar métricas do Kubernetes.
* Instalar OpenTelemetry Collector.
* Receber telemetria via OTLP.
* Instalar Loki.
* Visualizar logs no Grafana.
* Criar uma aplicação demo.
* Consultar logs da aplicação demo.
* Criar alertas básicos.
* Documentar o fluxo de instalação.

## Critérios de Sucesso em Cloud

A etapa em cloud será considerada válida quando for possível:

* Observar Kubernetes gerenciado.
* Observar VMs.
* Coletar logs de aplicações.
* Coletar métricas de infraestrutura.
* Receber traces de aplicações.
* Visualizar tudo no Grafana.
* Ter alertas funcionais.
* Ter retenção configurada.
* Ter dashboards por serviço.
* Ter runbooks operacionais.
* Ter integração com eventos de auditoria da cloud.

## Próximos Passos

1. Criar estrutura inicial do repositório.
2. Criar cluster local com kind.
3. Instalar kube-prometheus-stack.
4. Instalar OpenTelemetry Collector.
5. Instalar Loki.
6. Criar aplicação demo.
7. Instrumentar aplicação com OpenTelemetry.
8. Adicionar Tempo ou Jaeger.
9. Criar dashboards customizados.
10. Criar alertas e runbooks.
11. Preparar implantação na cloud.
12. Avaliar retenção longa com object storage.
13. Avaliar OpenSearch para logs avançados.
14. Adicionar OpenCost, Falco e Blackbox Exporter.

## Status do Projeto

```text
Status: fase inicial
Ambiente atual: local
Cluster local: kind
Foco atual: OpenTelemetry, métricas, logs e Grafana
Objetivo final: plataforma open source de observabilidade para cloud
```
