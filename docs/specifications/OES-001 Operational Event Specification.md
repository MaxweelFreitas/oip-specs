# OES — Operational Event Specification

> Version: 1.0.0
> Status: Draft
> Repository: oip-specs
> Identifier: OES

---

# 1. Objetivo

O **Operational Event Specification (OES)** define o padrão oficial de comunicação assíncrona entre todos os componentes do ecossistema da **Operational Intelligence Platform (OIP)**.

Toda troca de informações entre microsserviços, runtimes, capabilities, conectores, gateways e ferramentas externas deve ocorrer através de eventos padronizados.

O objetivo do OES é garantir:

- desacoplamento entre componentes;
- interoperabilidade entre implementações de terceiros;
- rastreabilidade completa;
- versionamento de contratos;
- compatibilidade futura.

---

# 2. Filosofia

Na OIP não existem chamadas diretas entre Capabilities.

Uma Capability nunca conhece outra.

Ela apenas publica fatos.

Quem decide consumir esses fatos é a plataforma.

Exemplo:

```
Camera
    │
    ▼
Vehicle Detector
    │
    ├── vehicle.detected
    ▼
Plate Detector
    │
    ├── plate.detected
    ▼
OCR
    │
    ├── plate.read
    ▼
Correlation Engine
    │
    ▼
Vehicle Profile
```

Toda comunicação ocorre por eventos.

---

# 3. Modelo de Evento

Todo evento deve representar um fato ocorrido.

Nunca um comando.

Correto:

```
vehicle.detected
```

Errado:

```
detect.vehicle
```

Correto:

```
incident.created
```

Errado:

```
create.incident
```

Eventos descrevem acontecimentos.

Nunca intenções.

---

# 4. Envelope Canônico

Todos os eventos trafegam utilizando um envelope comum.

```text
CloudEvent
    Metadata
    Payload
```

O envelope contém:

| Campo | Obrigatório | Descrição |
|---------|------------|-----------|
| id | Sim | Identificador único |
| source | Sim | Origem do evento |
| type | Sim | Tipo do evento |
| version | Sim | Versão do contrato |
| timestamp | Sim | Data UTC |
| tenant | Sim | Organização |
| correlation_id | Sim | Correlação distribuída |
| causation_id | Não | Evento que originou este |
| trace_id | Sim | Observabilidade |
| payload | Sim | Dados do evento |

---

# 5. Convenção de Nomes

Os tipos de eventos seguem:

```
<context>.<entity>.<action>
```

Exemplos

```
vehicle.detected

vehicle.tracked

plate.detected

plate.read

person.detected

audio.transcribed

fire.detected

ppe.detected

incident.created

incident.updated

incident.closed
```

Nunca utilizar:

```
VehicleDetected

PlateRead

FireAlert
```

O padrão oficial utiliza letras minúsculas e separação por ponto.

---

# 6. Estrutura do Payload

O payload deve conter apenas informações referentes ao evento.

Exemplo:

```yaml
entity_id:
attribute:
value:
confidence:
metadata:
```

O payload nunca deve repetir informações existentes no envelope.

---

# 7. Versionamento

Cada evento possui sua própria versão.

```
vehicle.detected

v1

v2

v3
```

Mudanças incompatíveis geram nova versão.

Mudanças compatíveis mantêm a versão.

---

# 8. Imutabilidade

Eventos publicados nunca podem ser alterados.

Caso exista uma correção, publica-se um novo evento.

Exemplo

```
plate.read

↓

plate.corrected
```

Jamais editar um evento histórico.

---

# 9. Idempotência

Consumidores devem considerar que um evento pode ser entregue mais de uma vez.

Todo consumidor deve ser idempotente.

A identificação do evento ocorre através do campo:

```
id
```

---

# 10. Ordenação

Não existe garantia de ordem entre eventos.

Quando necessário, utilizar:

```
entity_id

timestamp

sequence
```

para reconstrução temporal.

---

# 11. Correlação

Eventos pertencentes ao mesmo fluxo operacional compartilham:

```
correlation_id
```

Exemplo

```
Camera

↓

vehicle.detected

↓

plate.detected

↓

plate.read

↓

watchlist.match

↓

incident.created
```

Todos possuem o mesmo

```
correlation_id
```

---

# 12. Rastreabilidade

Todo evento deve ser rastreável.

Campos mínimos:

- trace_id
- correlation_id
- producer
- model_version
- capability
- runtime

---

# 13. Eventos de Sistema

Exemplos:

```
capability.started

capability.stopped

capability.failed

runtime.started

runtime.stopped

runtime.failed

model.loaded

model.unloaded

model.updated
```

---

# 14. Eventos de Inteligência

Exemplos:

```
vehicle.detected

vehicle.classified

vehicle.color.detected

vehicle.embedding.created

plate.detected

plate.read

face.detected

face.embedding.created

person.detected

ppe.detected

weapon.detected

smoke.detected

fire.detected

audio.transcribed

speech.classified
```

---

# 15. Eventos Operacionais

```
incident.created

incident.updated

incident.closed

dispatch.created

dispatch.completed

operator.logged_in

operator.logged_out

alert.created

alert.acknowledged
```

---

# 16. Eventos de Aprendizado

```
attribute.corrected

dataset.created

dataset.exported

training.started

training.completed

training.failed

model.validated

model.promoted

model.deprecated
```

---

# 17. Compatibilidade

Toda Capability certificada deve:

- publicar eventos OES;
- consumir eventos OES;
- respeitar versionamento;
- respeitar CloudEvents;
- suportar rastreabilidade.

---

# 18. Integração com Outras Especificações

O OES trabalha em conjunto com:

- CSpec (Capability Specification)
- OCL (Capability Lifecycle)
- CSS (Capability SDK Specification)
- OMS (Operational Model Specification)
- OSS (Operational Security Specification)

Nenhuma Capability pode ser certificada sem conformidade com o OES.

---

# 19. Próximos Passos

As mensagens definidas nesta especificação são implementadas no repositório **oip-proto**, onde são descritas em Protobuf e disponibilizadas para todas as linguagens suportadas pelos SDKs oficiais.