# OKS-002 — Knowledge Graph Specification

**Version:** 1.0.0  
**Status:** Draft  
**Type:** Core Specification  
**Identifier:** OKS-002  
**Depends on:** OKS-001, OES, OMS, OCS, CSpec  
**Applies to:** Operational Knowledge Base (OKB)

---

# 1. Objetivo

Esta especificação define o **Knowledge Graph (KG)** oficial da **Operational Intelligence Platform (OIP)**.

O objetivo do Knowledge Graph é representar o conhecimento operacional produzido pela plataforma através de um grafo de entidades, relacionamentos e evidências, permitindo responder perguntas complexas, correlacionar eventos aparentemente desconexos e fornecer contexto para mecanismos de busca, apoio à decisão, inteligência artificial e análise investigativa.

O Knowledge Graph **não substitui** bancos relacionais, índices de busca ou Event Store. Ele atua como uma camada de conhecimento construída sobre eles.

---

# 2. Motivação

Sistemas tradicionais armazenam informações em registros independentes.

```
Pessoa

Veículo

Chamada

Incidente
```

Sem relacionamento entre eles.

Na OIP, todo dado possui contexto.

```
Pessoa

↓

Entrou em

↓

Veículo

↓

Que passou por

↓

Câmera

↓

Durante

↓

Incidente
```

Esse relacionamento gera conhecimento.

---

# 3. Princípios

O Knowledge Graph deve ser:

- Event-Driven
- Incremental
- Temporal
- Distribuído
- Auditável
- Explicável
- Versionado
- Independente da implementação

---

# 4. Conceitos Fundamentais

O grafo é composto por três elementos.

## Node (Entidade)

Representa qualquer objeto conhecido pela plataforma.

Exemplos:

```
Pessoa

Veículo

Incidente

Áudio

Placa

Viatura

Drone

Sensor

Câmera

Operador

Telefone

Organização
```

---

## Edge (Relacionamento)

Representa uma ligação entre dois nós.

Exemplo:

```
Pessoa

↓

dirigia

↓

Veículo
```

Outro exemplo:

```
Veículo

↓

foi visto por

↓

Câmera
```

---

## Evidence (Evidência)

Toda relação deve possuir evidências.

```
Imagem

↓

OCR

↓

Operador

↓

LLM

↓

Áudio

↓

IoT
```

Nada pode existir apenas por inferência sem rastreabilidade.

---

# 5. Estrutura Geral

```
Pessoa

↓

dirigia

↓

Veículo

↓

passou_por

↓

Câmera

↓

gerou

↓

Incidente

↓

despachou

↓

Viatura
```

Todo relacionamento pode ser percorrido.

---

# 6. Nós (Nodes)

Cada entidade possui um identificador global.

Exemplo:

```
entity_id

entity_type

created_at

updated_at

attributes

confidence

metadata
```

Exemplo:

```
vehicle_48192

Pessoa_883

Camera_21

Incident_9983
```

---

# 7. Tipos de Entidades

A especificação inicial define os seguintes tipos.

## Pessoas

```
Person
```

---

## Veículos

```
Vehicle
```

---

## Incidentes

```
Incident
```

---

## Câmeras

```
Camera
```

---

## Chamadas

```
Call
```

---

## Operadores

```
Operator
```

---

## Áudios

```
Audio
```

---

## Localizações

```
Location
```

---

## Sensores

```
Sensor
```

---

## Organizações

```
Organization
```

Novos tipos podem ser adicionados sem alterar esta especificação.

---

# 8. Atributos

Cada nó possui atributos.

Exemplo de veículo.

```
Marca

Modelo

Cor

Placa

Categoria

Embedding

Confidence

Histórico
```

Pessoa.

```
Roupa

Capacete

Mochila

Embedding

ReID

Cor

Sexo estimado

Faixa etária
```

---

# 9. Relacionamentos

Os relacionamentos representam fatos.

Exemplos.

```
DRIVES

OWNS

ENTERED

LEFT

DETECTED_BY

GENERATED

PARTICIPATED

ASSIGNED

CALLED

LOCATED_AT

CONNECTED_TO

HAS_ATTRIBUTE

RELATED_TO
```

Cada relacionamento possui direção.

---

# 10. Propriedades dos Relacionamentos

Todo relacionamento possui metadados.

```
confidence

timestamp

source

capability

version

evidence_id
```

---

# 11. Temporalidade

O conhecimento muda ao longo do tempo.

Exemplo.

```
08:10

↓

Pessoa entrou no veículo

↓

08:15

↓

Veículo saiu

↓

08:30

↓

Veículo detectado novamente
```

Nenhum relacionamento é sobrescrito.

---

# 12. Confidence

Cada relação possui confiança.

```
Pessoa

↓

dirigia

↓

Veículo

96%
```

Essa confiança pode evoluir.

```
60%

↓

72%

↓

89%

↓

97%
```

---

# 13. Proveniência

Todo relacionamento deve informar sua origem.

Exemplo.

```
Capability

Vehicle OCR

↓

Modelo

v2.1

↓

Frame

812

↓

Confidence

94%
```

---

# 14. Evidências

Relacionamentos apontam para evidências.

Exemplo.

```
Pessoa

↓

utilizava

↓

Capacete Branco

↓

Evidence

↓

Imagem

↓

Frame

↓

Bounding Box
```

Assim toda conclusão pode ser auditada.

---

# 15. Correlação

O grafo é atualizado continuamente.

```
Evento

↓

Correlation Engine

↓

Knowledge Graph

↓

Atualização
```

Cada Capability adiciona novas conexões.

---

# 16. Inferências

O Knowledge Graph pode gerar inferências.

Exemplo.

```
Pessoa

↓

Entrou

↓

Veículo

↓

Veículo passou

↓

Local do roubo
```

O sistema pode inferir participação.

Essas inferências **devem ser marcadas explicitamente como inferidas**.

Nunca devem ser tratadas como fatos observados.

---

# 17. Tipos de Relações

## Observadas

Produzidas diretamente por sensores.

```
Camera

↓

Detected
```

---

## Inferidas

Produzidas por algoritmos.

```
Likely Owner

Same Person

Possible Route
```

---

## Humanas

Criadas por operadores.

```
Confirmed

Rejected

Corrected
```

---

# 18. Atualização

Toda atualização segue o fluxo.

```
Evento

↓

Capability

↓

Correlation Engine

↓

Knowledge Engine

↓

Knowledge Graph
```

Nenhuma Capability modifica diretamente o grafo.

---

# 19. Consultas

O Knowledge Graph deve responder perguntas como:

```
Onde esse veículo foi visto?

↓

Quem estava dirigindo?

↓

Quais câmeras visualizaram?

↓

Em quais incidentes apareceu?

↓

Qual viatura o abordou?

↓

Quais chamadas mencionaram essa placa?
```

---

# 20. Integração com Busca

O OpenSearch continua responsável pela busca textual.

O Graph fornece contexto.

```
Busca

↓

Resultado

↓

Knowledge Graph

↓

Contexto Completo
```

---

# 21. Integração com Vetores

Embeddings também fazem parte do grafo.

```
Pessoa

↓

Embedding

↓

Vector Store

↓

Relacionamento
```

Isso permite conectar entidades semelhantes.

---

# 22. Integração com LLM

O grafo pode servir como contexto para modelos de linguagem.

Fluxo.

```
Pergunta

↓

Graph Retrieval

↓

LLM

↓

Resposta
```

Exemplo.

```
Mostre todos os veículos
que circularam próximos ao incêndio
entre 18h e 19h
e que já apareceram em ocorrências anteriores.
```

---

# 23. Segurança

Toda consulta deve respeitar:

- RBAC
- ABAC
- Escopo organizacional
- Auditoria
- LGPD

Nem todo operador pode visualizar todos os relacionamentos.

---

# 24. Armazenamento

A especificação não exige um banco específico.

Implementações possíveis.

```
Neo4j

JanusGraph

Apache AGE

Memgraph

ArangoDB

PostgreSQL + AGE

Amazon Neptune
```

Também é permitido manter o grafo lógico sem banco de grafos dedicado.

---

# 25. Escalabilidade

O grafo deve suportar:

- bilhões de relacionamentos;
- múltiplas organizações;
- múltiplos tenants;
- atualização contínua;
- ingestão em tempo real.

---

# 26. Benefícios

O Knowledge Graph permite:

- investigação forense;
- correlação automática;
- busca contextual;
- apoio à decisão;
- Active Learning;
- Explainable AI;
- análise temporal;
- reconstrução de incidentes;
- navegação entre entidades;
- recomendações inteligentes.

---

# 27. Futuras Extensões

O modelo foi projetado para suportar:

- Graph Neural Networks (GNN);
- Link Prediction;
- Community Detection;
- Graph Embeddings;
- Reasoning Engines;
- Ontologias;
- CEP (Complex Event Processing);
- Digital Twins;
- Federated Knowledge Graphs.

Essas capacidades poderão ser incorporadas sem alterar a estrutura básica do grafo.

---

# Referências

- OKS-001 — Operational Knowledge Specification
- OES — OIP Event Specification
- OMS — OIP Model Specification
- CSpec — OIP Capability Specification
- ADR-0001 — Architecture Principles
- ADR-0002 — Event-Driven Architecture
- ADR-0003 — Plugin First Architecture

---

# Status

**Draft**

Esta especificação define o **Knowledge Graph** oficial da OIP como a camada responsável por representar, correlacionar e contextualizar o conhecimento operacional produzido pelas Capabilities, fornecendo uma base para investigação, busca contextual, Active Learning, inteligência operacional e futuras funcionalidades avançadas de apoio à decisão.