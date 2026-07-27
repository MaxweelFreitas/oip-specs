# OKS-003 — Correlation & Aggregation Specification

**Version:** 1.0.0  
**Status:** Draft  
**Type:** Core Specification  
**Identifier:** OKS-003  
**Depends on:** OES, OMS, OKS-001, OKS-002, CSpec  
**Applies to:** Correlation Engine, Knowledge Engine, Operational Knowledge Base (OKB)

---

# 1. Objetivo

Esta especificação define como a **Operational Intelligence Platform (OIP)** correlaciona, agrega e consolida eventos produzidos por múltiplas Capabilities para formar conhecimento operacional consistente.

O objetivo não é detectar eventos.

O objetivo é transformar milhares de eventos independentes em **entidades operacionais enriquecidas**, capazes de representar corretamente pessoas, veículos, chamadas, sensores, incidentes e qualquer outro domínio monitorado pela plataforma.

---

# 2. Motivação

Capacidades trabalham isoladamente.

Exemplo:

```
Vehicle Detector

↓

Vehicle Tracker

↓

OCR

↓

Color Detector

↓

Brand Detector
```

Cada Capability conhece apenas seu domínio.

A plataforma precisa combinar esses resultados.

---

# 3. Princípios

O mecanismo de correlação deve ser:

- Event-Driven
- Stateless sempre que possível
- Idempotente
- Temporal
- Modular
- Determinístico
- Auditável
- Escalável
- Tolerante a falhas

---

# 4. Conceitos

## Evento

Representa um fato produzido por uma Capability.

Exemplo:

```
Plate Read

Vehicle Color

Fire Detection

Person Detection
```

---

## Entidade

Objeto monitorado.

```
Vehicle

Person

Call

Camera

Incident
```

---

## Perfil

Resultado consolidado da agregação.

```
Vehicle Profile

↓

Placa

↓

Cor

↓

Modelo

↓

Histórico

↓

Embeddings
```

---

## Correlação

Processo de relacionar eventos pertencentes à mesma entidade.

---

## Agregação

Processo de consolidar atributos em um único perfil.

---

# 5. Arquitetura Geral

```
Capabilities

↓

NATS

↓

Correlation Engine

↓

Aggregation Engine

↓

Knowledge Engine

↓

Operational Knowledge Base

↓

Search

↓

Decision Intelligence
```

---

# 6. Responsabilidades

O Correlation Engine deve:

- consumir eventos;
- identificar entidades;
- agrupar eventos relacionados;
- enriquecer perfis;
- publicar atualizações;
- manter histórico;
- preservar rastreabilidade.

---

# 7. O que NÃO faz

O Correlation Engine não deve:

- executar IA;
- executar OCR;
- detectar objetos;
- treinar modelos;
- acessar câmeras;
- produzir embeddings.

Ele apenas trabalha sobre eventos existentes.

---

# 8. Entrada

A entrada sempre são eventos publicados pelas Capabilities.

Exemplo.

```
vehicle.detected

↓

vehicle.tracked

↓

vehicle.color

↓

vehicle.plate

↓

vehicle.brand
```

---

# 9. Chaves de Correlação

Toda agregação depende de uma chave.

Exemplos.

```
track_id

entity_id

call_id

camera_id

incident_id

device_id

session_id
```

Uma entidade pode possuir múltiplas chaves ao longo do tempo.

---

# 10. Janelas Temporais

Nem todos os eventos chegam simultaneamente.

O mecanismo utiliza janelas temporais.

Exemplo.

```
Frame 120

↓

Detector

↓

Frame 122

↓

OCR

↓

Frame 125

↓

Cor

↓

Frame 130

↓

Marca
```

Todos pertencem ao mesmo veículo.

---

# 11. Correlation Window

Cada tipo de entidade pode possuir uma janela própria.

Exemplo.

| Entidade | Janela |
|----------|---------|
| Veículo | 30 s |
| Pessoa | 20 s |
| Chamada | duração completa |
| Incêndio | até encerramento |
| Drone | enquanto rastreado |

---

# 12. Agregação

O Aggregation Engine recebe múltiplos atributos.

```
Detector

↓

Tracker

↓

OCR

↓

Cor

↓

Modelo

↓

Embedding

↓

Vehicle Profile
```

---

# 13. Atualização Incremental

O perfil nunca é recriado.

Ele evolui continuamente.

```
Vehicle

↓

v1

↓

v2

↓

v3

↓

v4
```

---

# 14. Confidence Merge

Cada atributo possui confiança própria.

Exemplo.

```
Placa

89%

↓

94%

↓

98%
```

A plataforma mantém:

- valor atual;
- histórico;
- evolução.

---

# 15. Resolução de Conflitos

Pode haver resultados divergentes.

Exemplo.

```
Frame 1

Prata

↓

Frame 15

Cinza

↓

Frame 30

Prata
```

A política de resolução pode considerar:

- maior confiança;
- maior recorrência;
- votação temporal;
- confirmação humana;
- regras específicas da Capability.

---

# 16. Correlação Espacial

Eventos podem ser correlacionados pela localização.

Exemplo.

```
Camera A

↓

Pessoa

↓

Camera B

↓

Mesmo Embedding

↓

Mesmo indivíduo
```

---

# 17. Correlação Temporal

Eventos próximos podem estar relacionados.

```
Chamada

↓

30 segundos

↓

Veículo

↓

1 minuto

↓

Incêndio
```

---

# 18. Correlação Semântica

O mecanismo também pode utilizar contexto.

Exemplo.

```
LLM

↓

"Homem de camisa azul"

↓

Vision

↓

Pessoa

↓

Cor da roupa

↓

Relacionamento
```

---

# 19. Correlação por Embedding

Quando disponível.

```
Embedding

↓

Vector Search

↓

Pessoa semelhante

↓

Relacionamento
```

---

# 20. Correlação por Regras

Também podem existir regras declarativas.

Exemplo.

```
Placa

↓

Watchlist

↓

Alerta
```

Outro exemplo.

```
Pessoa

↓

Sem capacete

↓

Área restrita

↓

Incidente
```

---

# 21. Correlation Pipeline

Fluxo simplificado.

```
Evento

↓

Parser

↓

Validation

↓

Entity Resolver

↓

Correlation

↓

Aggregation

↓

Enrichment

↓

Knowledge Update

↓

Publish
```

---

# 22. Enriquecimento

Após consolidar os atributos.

```
Perfil

↓

Watchlist

↓

Geofencing

↓

Histórico

↓

Incidentes

↓

Contexto

↓

Knowledge Profile
```

---

# 23. Publicação

Toda atualização gera novo evento.

```
Vehicle Profile Updated

↓

NATS

↓

Outros serviços
```

Assim toda a plataforma permanece desacoplada.

---

# 24. Idempotência

Eventos duplicados devem produzir o mesmo resultado.

```
Evento

↓

Hash

↓

Já processado?

↓

Ignora
```

---

# 25. Ordenação

Nem sempre os eventos chegam na ordem correta.

```
OCR

↓

Detector

↓

Tracker
```

O mecanismo deve tolerar eventos fora de ordem.

---

# 26. Auditoria

Toda agregação registra:

- eventos utilizados;
- regras aplicadas;
- política de merge;
- versão do agregador;
- operador (quando existir);
- horário.

---

# 27. Explainability

Todo atributo deve informar sua origem.

Exemplo.

```
Marca

Toyota

↓

Capability

Brand Detector

↓

Modelo

v3.2

↓

Confidence

96%
```

---

# 28. Human Feedback

Correções humanas possuem prioridade.

```
OCR

ABC1023

↓

Operador

↓

ABC1D23

↓

Knowledge Update
```

A correção nunca substitui o histórico.

Ela cria uma nova versão.

---

# 29. Performance

O mecanismo deve:

- processar milhares de eventos por segundo;
- permitir processamento paralelo;
- escalar horizontalmente;
- utilizar cache quando necessário;
- minimizar bloqueios.

---

# 30. Escalabilidade

Múltiplos Correlation Engines podem operar simultaneamente.

```
Partition A

↓

Engine 1

Partition B

↓

Engine 2

Partition C

↓

Engine 3
```

O particionamento deve preservar a afinidade da entidade (por exemplo, utilizando `entity_id` ou `track_id` como chave de particionamento).

---

# 31. Integração com o Knowledge Graph

Toda atualização do perfil pode gerar novos relacionamentos.

```
Vehicle Profile

↓

Knowledge Graph

↓

Relacionamentos

↓

Contexto
```

---

# 32. Integração com Busca

Após a agregação.

```
Knowledge Profile

↓

OpenSearch

↓

Consulta
```

---

# 33. Integração com Active Learning

Perfis com baixa confiança podem ser enviados automaticamente para revisão.

```
Confidence

↓

Policy Engine

↓

Fila

↓

Operador

↓

Correção
```

---

# 34. Benefícios

A arquitetura de correlação permite:

- reduzir falsos positivos;
- consolidar múltiplas inferências;
- preservar histórico;
- melhorar a qualidade das buscas;
- alimentar o Knowledge Graph;
- fornecer contexto para LLMs;
- simplificar o desenvolvimento das Capabilities;
- desacoplar inferência de inteligência operacional.

---

# 35. Futuras Extensões

A especificação suporta evoluções como:

- Complex Event Processing (CEP);
- Stream Processing;
- Graph Correlation;
- Graph Neural Networks (GNN);
- Decision Intelligence;
- Rule Engines;
- Event Sourcing avançado;
- Process Mining;
- Digital Twins.

Essas funcionalidades poderão ser adicionadas sem alterar os contratos fundamentais desta especificação.

---

# Referências

- OKS-001 — Operational Knowledge Specification
- OKS-002 — Knowledge Graph Specification
- OES — OIP Event Specification
- OMS — OIP Model Specification
- CSpec — OIP Capability Specification
- ADR-0001 — Architecture Principles
- ADR-0002 — Event-Driven Architecture
- ADR-0003 — Plugin First Architecture

---

# Status

**Draft**

Esta especificação define o comportamento do **Correlation & Aggregation Engine** da OIP, responsável por transformar eventos distribuídos em perfis operacionais enriquecidos, auditáveis e continuamente evolutivos, constituindo a principal ponte entre as Capabilities e a Base de Conhecimento Operacional (OKB).