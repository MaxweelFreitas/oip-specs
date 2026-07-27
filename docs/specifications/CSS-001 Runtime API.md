# CSS — OIP Capability SDK Specification

> **Version:** 1.0.0
> **Status:** Draft
> **Repository:** oip-specs
> **Identifier:** CSS

---

# CSS — OIP Capability SDK Specification

## 1. Objetivo

O **Capability SDK Specification (CSS)** define os requisitos mínimos que toda implementação oficial do SDK da Operational Intelligence Platform (OIP) deve oferecer.

O objetivo é garantir que Capabilities desenvolvidas em diferentes linguagens mantenham comportamento consistente, interoperabilidade, observabilidade e integração transparente com o ecossistema OIP.

O CSS não define APIs específicas de linguagem.

Ele define **comportamentos obrigatórios**.

---

# 2. Objetivos do SDK

Todo SDK oficial deve fornecer:

* integração transparente com o Runtime
* abstração do barramento de eventos
* abstração da configuração
* logging estruturado
* métricas
* tracing distribuído
* gerenciamento do ciclo de vida
* health checks
* publicação de eventos
* consumo de eventos
* gerenciamento de erros
* validação da Capability

O desenvolvedor da Capability deve focar apenas na lógica de negócio.

---

# 3. Responsabilidades do SDK

O SDK é responsável por:

* carregar configurações

* validar CSpec

* conectar ao Runtime

* conectar ao Broker

* registrar métricas

* registrar tracing

* registrar logs

* publicar eventos

* consumir eventos

* controlar shutdown

* controlar retries

* controlar heartbeat

* enviar health status

* reportar versão

* registrar informações do modelo

---

# 4. Arquitetura

```
            Capability
                 │
                 ▼
        OIP Capability SDK
                 │
      ┌──────────┼──────────┐
      │          │          │
      ▼          ▼          ▼
   Logging    Metrics    Tracing
      │          │          │
      └──────────┼──────────┘
                 │
                 ▼
          Runtime Services
                 │
                 ▼
          NATS JetStream
```

---

# 5. Componentes Obrigatórios

Todo SDK deve implementar os seguintes módulos.

## Config

Responsável por:

* leitura de arquivos

* variáveis de ambiente

* secrets

* validação

Interface esperada:

```
Load()

Get()

MustGet()

Validate()
```

---

## Logger

Logging estruturado.

Características:

* JSON

* Contexto

* Correlation ID

* Trace ID

* Span ID

* Capability ID

* Model Version

Níveis:

```
Debug

Info

Warn

Error

Fatal
```

---

## Event Bus

Abstração completa do Broker.

Operações:

```
Publish()

Subscribe()

Request()

Reply()
```

Suporte obrigatório a:

* CloudEvents

* Protobuf

* Retry

* Dead Letter Queue

---

## Health

Todo SDK deve expor:

```
Ready()

Live()

Health()
```

Essas informações serão utilizadas pelo Runtime.

---

## Metrics

Integração nativa com OpenTelemetry.

Métricas mínimas:

```
events_received_total

events_published_total

processing_latency

processing_errors

memory_usage

cpu_usage

gpu_usage

queue_size
```

---

## Tracing

Todo processamento deve gerar automaticamente:

```
Trace

Span

Attributes

Events
```

Compatível com:

OpenTelemetry.

---

## Lifecycle

O SDK controla automaticamente:

```
Initialize

Start

Run

Stop

Shutdown
```

O desenvolvedor apenas implementa:

```
Process()
```

---

## Model Registry Client

Quando houver IA.

O SDK deve registrar automaticamente:

* modelo

* versão

* hash

* dataset

* device

* provider

* tempo de inferência

---

## Configuration Watcher

Opcional.

Permite:

* reload automático

* troca de configuração

* atualização de thresholds

sem reiniciar a Capability.

---

# 6. Interface Base

Toda Capability deverá implementar uma interface semelhante a:

```go
type Capability interface {

    Metadata() Metadata

    Configure(Config) error

    Initialize(Context) error

    Start() error

    Process(Event) error

    Stop() error

}
```

Cada linguagem poderá adaptar a sintaxe.

---

# 7. Contexto de Execução

O Runtime fornece automaticamente:

```
Capability Context

Configuration

Secrets

Logger

Metrics

Tracer

Publisher

Subscriber

Storage

Feature Store

Vector Store
```

O desenvolvedor nunca deve criar essas dependências manualmente.

---

# 8. Eventos

O SDK publica automaticamente:

```
capability.started

capability.ready

capability.processing

capability.warning

capability.failed

capability.stopped
```

---

# 9. Tratamento de Erros

Erros são classificados em:

## Recoverable

Exemplo:

* timeout

* perda temporária do broker

* recurso indisponível

O SDK executa retry automático.

---

## Non Recoverable

Exemplo:

* configuração inválida

* modelo inexistente

* protobuf incompatível

O Runtime altera o estado para:

```
Failed
```

---

# 10. Shutdown

O encerramento deve seguir:

```
Receber SIGTERM

↓

Parar novos eventos

↓

Concluir eventos em processamento

↓

Flush de métricas

↓

Flush de logs

↓

Fechar conexões

↓

Finalizar processo
```

---

# 11. Observabilidade

Todo SDK deve integrar automaticamente:

* OpenTelemetry

* Prometheus

* Loki

* Tempo

* Jaeger

Sem necessidade de configuração adicional pelo desenvolvedor.

---

# 12. Extensibilidade

O SDK deve permitir plugins para:

* novos Brokers

* novos Storages

* novos Registries

* novos Loggers

* novos Tracers

* novos Exporters

---

# 13. Compatibilidade

Os SDKs oficiais devem manter compatibilidade funcional entre diferentes linguagens.

Implementações previstas:

| Linguagem | Status    |
| --------- | --------- |
| Go        | Oficial   |
| Python    | Oficial   |
| Rust      | Planejado |
| Java      | Planejado |
| C#        | Planejado |
| C++       | Planejado |
| Node.js   | Planejado |

---

# 14. Versionamento

O SDK segue Semantic Versioning.

```
MAJOR.MINOR.PATCH
```

Mudanças incompatíveis exigem incremento de versão major.

---

# 15. Conformidade

Uma implementação somente pode ser considerada um **SDK Oficial OIP** quando atender integralmente aos requisitos definidos neste documento e for compatível com:

* CSpec
* OES
* OCL
* OMS
* OCS

---

# 16. Roadmap

Próximas evoluções previstas:

* SDK Generator baseado em Protobuf
* Geração automática de Clients
* Hot Reload de Capabilities
* Remote Debug
* Capability Sandbox
* Plugin Marketplace Integration
* Capability Testing Framework
* SDK Certification Suite

