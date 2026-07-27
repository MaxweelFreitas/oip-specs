# CSpec-001 — OIP Capability Specification (CSpec)

| Campo | Valor |
|-------|--------|
| **Especificação** | CSpec-001 |
| **Título** | OIP Capability Specification |
| **Versão** | 1.0.0 |
| **Status** | Stable |
| **Autores** | OIP Project |
| **Última atualização** | 2026-07-27 |
| **Próxima revisão** | Conforme evolução da plataforma |
| **Depende de** | — |
| **Utilizado por** | OCL, OES, CSS, OCS, OMS, OIP Runtime, OIP SDK |

---

# 1. Objetivo

A **Capability Specification (CSpec)** define o contrato oficial utilizado por toda Capability desenvolvida para a **Operational Intelligence Platform (OIP)**.

Ela estabelece:

- como uma Capability é identificada;
- como é instalada;
- quais interfaces ela expõe;
- quais eventos produz e consome;
- quais modelos utiliza;
- quais recursos necessita;
- quais permissões exige;
- como participa do ciclo de vida da plataforma.

Toda Capability compatível com a OIP **DEVE** possuir um manifesto CSpec.

---

# 2. Motivação

A OIP foi concebida para ser uma plataforma baseada em **Capacidades (Capabilities)**.

Isso significa que funcionalidades deixam de ser componentes internos da plataforma e passam a ser módulos independentes, instaláveis e atualizáveis.

Exemplos:

- Vehicle Intelligence
- Plate Recognition
- Fire Detection
- Smoke Detection
- Person Detection
- Face Recognition
- PPE Detection
- Voice Transcription
- Audio Classification
- Dispatch Optimizer
- LLM Summarizer

Todos seguem exatamente o mesmo contrato.

---

# 3. Definições

## Capability

Uma Capability representa uma unidade independente de inteligência ou processamento capaz de produzir conhecimento operacional para a plataforma.

Ela pode executar:

- inferência de IA;
- processamento de áudio;
- processamento de vídeo;
- análise de texto;
- enriquecimento de dados;
- integração externa;
- cálculo de métricas;
- qualquer outra lógica especializada.

---

## Runtime

O Runtime é responsável por executar uma Capability.

Ele controla:

- inicialização;
- configuração;
- comunicação;
- health check;
- métricas;
- logs;
- ciclo de vida.

---

## Processor

Uma Capability pode ser composta por um ou mais Processors.

Exemplo:

```
Vehicle Capability

↓

Object Detector

↓

Tracker

↓

Crop Generator

↓

OCR

↓

Color Detector

↓

Publisher
```

Cada Processor possui responsabilidade única.

---

## Capability Package

Uma Capability é distribuída como um pacote contendo:

```
Capability

├── capability.yaml
├── runtime/
├── models/
├── configs/
├── proto/
├── docs/
└── tests/
```

---

# 4. Princípios

Toda Capability DEVE seguir os princípios abaixo.

## 4.1 Independência

Cada Capability deve ser completamente independente.

Ela não deve depender de outra Capability para iniciar.

---

## 4.2 Baixo Acoplamento

A comunicação ocorre exclusivamente através de contratos públicos.

Nunca através de chamadas diretas entre componentes internos.

---

## 4.3 Event Driven

Capabilities publicam fatos.

Nunca executam regras de negócio da plataforma.

Exemplo:

```
✓ PlateRead

✓ VehicleDetected

✓ FireDetected

✓ PersonRecognized

✗ OpenIncident()

✗ SendNotification()

✗ DispatchPolice()
```

---

## 4.4 Stateless

Sempre que possível, Capabilities devem ser Stateless.

Estado persistente pertence à plataforma.

---

## 4.5 Observabilidade

Toda Capability deve produzir:

- logs estruturados;
- métricas;
- traces;
- health status.

---

## 4.6 Reprodutibilidade

Uma mesma entrada deve produzir a mesma saída considerando:

- mesma versão;
- mesmo modelo;
- mesma configuração.

---

## 4.7 Versionamento

Toda Capability possui:

- versão;
- autor;
- compatibilidade;
- changelog.

---

# 5. Estrutura Obrigatória

Toda Capability deve possuir no mínimo:

```
capability.yaml

runtime/

docs/

configs/

tests/
```

Caso utilize IA:

```
models/
```

Caso publique contratos:

```
proto/
```

---

# 6. Manifesto

O arquivo obrigatório da Capability chama-se:

```
capability.yaml
```

Ele representa a identidade oficial da Capability.

Exemplo simplificado:

```yaml
apiVersion: oip.io/v1

kind: Capability

metadata:
  id: vehicle-color
  name: Vehicle Color Detection
  version: 1.0.0
```

---

# 7. Identificação

Cada Capability possui um identificador único.

Formato recomendado:

```
vendor.capability
```

Exemplos:

```
oip.vehicle-color

oip.vehicle-detection

oip.fire-detection

acme.face-recognition

ifce.weapon-detector
```

O identificador nunca deve mudar.

---

# 8. Interfaces

Uma Capability pode expor:

- eventos
- APIs
- métricas
- health endpoint
- configurações

Ela nunca deve acessar componentes internos da plataforma diretamente.

---

# 9. Comunicação

Toda comunicação ocorre por:

- NATS JetStream
- gRPC
- Protobuf
- CloudEvents

Dependendo do contrato definido pela plataforma.

---

# 10. Configuração

Toda configuração deve ser externa.

Exemplo:

```
configs/

default.yaml

production.yaml
```

Nunca utilizar valores fixos no código.

---

# 11. Modelos de IA

Quando utilizar IA, os modelos devem permanecer desacoplados.

```
Capability

↓

Model Loader

↓

Model

↓

Inference
```

O modelo deve poder ser substituído sem recompilar a Capability.

---

# 12. Segurança

Nenhuma Capability deve:

- armazenar senhas;
- armazenar tokens;
- armazenar certificados.

Segredos são fornecidos exclusivamente pelo Runtime.

---

# 13. Recursos

A Capability deve declarar os recursos mínimos necessários.

Exemplo:

```
CPU

RAM

GPU

VRAM

Storage

Network
```

Essas informações permitem ao Runtime realizar o agendamento adequado.

---

# 14. Dependências

Uma Capability pode declarar dependências de:

- modelos;
- protocolos;
- versões mínimas do SDK;
- versões mínimas do Runtime.

Nunca de outra Capability específica.

---

# 15. Compatibilidade

Toda Capability deve informar:

- versão mínima do Runtime;
- versão mínima do SDK;
- versão mínima da API.

---

# 16. Observabilidade

Toda Capability deve publicar:

## Logs

Estruturados.

## Métricas

Prometheus/OpenTelemetry.

## Traces

OpenTelemetry.

## Health

Liveness.

Readiness.

Startup.

---

# 17. Estados

O ciclo de vida completo é definido na especificação **OCL (Operational Capability Lifecycle)**.

O CSpec apenas determina que toda Capability deve implementar esse ciclo.

---

# 18. Distribuição

Capabilities podem ser distribuídas por:

- repositórios Git;
- Capability Store;
- Registry privado;
- Marketplace da OIP.

O formato de distribuição é definido na especificação **OCS**.

---

# 19. Compatibilidade com SDK

Toda Capability deve utilizar um SDK oficial ou compatível.

O SDK é responsável por fornecer:

- Runtime Client;
- Logger;
- Event Publisher;
- Event Subscriber;
- Configuration;
- Metrics;
- Tracing;
- Lifecycle;
- Health Checks.

Os requisitos do SDK são definidos na especificação **CSS**.

---

# 20. Conformidade

Uma Capability é considerada compatível com a OIP quando:

- possui `capability.yaml` válido;
- implementa o ciclo de vida oficial;
- utiliza os contratos oficiais;
- respeita as políticas de segurança;
- publica observabilidade;
- segue os contratos definidos nesta especificação.

---

# 21. Especificações Relacionadas

| Documento | Descrição |
|-----------|-----------|
| **OCL** | Operational Capability Lifecycle |
| **OES** | Operational Event Specification |
| **CSS** | Capability SDK Specification |
| **OMS** | Operational Model Specification |
| **OCS** | Operational Capability Store Specification |
| **OSS** | Operational Security Specification |

---

# 22. Histórico de Versões

| Versão | Data | Alterações |
|---------|------|------------|
| **1.0.0** | 2026-07-27 | Primeira versão oficial da Capability Specification. |