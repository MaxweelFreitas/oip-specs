# OKS-001 — Operational Knowledge Specification

**Version:** 1.0.0  
**Status:** Draft  
**Type:** Core Specification  
**Identifier:** OKS-001  
**Depends on:** OES, OMS, OCS, CSpec, CSS  
**Applies to:** Todo o ecossistema OIP

---

# 1. Objetivo

A **Operational Knowledge Specification (OKS)** define como o conhecimento operacional é produzido, enriquecido, armazenado, consultado e evoluído dentro da **Operational Intelligence Platform (OIP)**.

O objetivo da especificação é transformar a OIP em uma **Knowledge-Driven Platform**, onde todos os eventos produzidos pelas Capabilities passam a compor uma base de conhecimento operacional continuamente enriquecida.

Enquanto o **Operational Database** armazena o estado da plataforma e o **Event Bus** transporta fatos em tempo real, o **Operational Knowledge Base (OKB)** representa a memória operacional da plataforma.

---

# 2. Motivação

Em sistemas tradicionais de monitoramento, cada detector gera um evento isolado.

Exemplo:

```
Pessoa detectada

↓

Evento descartado
```

Nenhum conhecimento é preservado.

Na OIP a abordagem é diferente.

Cada evento contribui para construir conhecimento.

```
Evento

↓

Correlação

↓

Enriquecimento

↓

Conhecimento

↓

Busca

↓

Decisão
```

O objetivo não é apenas detectar acontecimentos.

É compreender o contexto operacional.

---

# 3. Princípios

Toda implementação deve obedecer aos seguintes princípios:

- Event-Driven
- Knowledge-Centric
- Incremental
- Temporal
- Auditável
- Explicável
- Modular
- Evolutiva

---

# 4. Definições

## Evento

Representa um fato observado.

Exemplo:

```
Placa lida

Pessoa detectada

Áudio classificado

Incêndio detectado
```

---

## Atributo

Representa uma informação derivada.

Exemplo:

```
Cor

Marca

Modelo

Capacete

Uniforme

Placa

Latitude
```

---

## Entidade

Representa um objeto monitorado.

Exemplo:

```
Pessoa

Veículo

Drone

Animal

Câmera

Viatura

Chamada

Incêndio
```

---

## Perfil

Conjunto consolidado de atributos pertencentes a uma entidade.

Exemplo:

```
Veículo

↓

Marca
Modelo
Cor
Placa
Última localização
Histórico
Embedding
```

---

## Conhecimento

Resultado da agregação de múltiplos eventos relacionados.

---

# 5. Arquitetura

```
Capacidades

↓

Eventos

↓

Correlation Engine

↓

Knowledge Engine

↓

Operational Knowledge Base

↓

Busca

↓

Alertas

↓

Decisão
```

O Knowledge Engine é responsável por transformar fatos em conhecimento.

---

# 6. Operational Knowledge Base (OKB)

O OKB representa a memória operacional da plataforma.

Ele não substitui bancos transacionais.

Ele consolida conhecimento.

---

## Responsabilidades

- consolidar atributos;
- enriquecer entidades;
- armazenar histórico;
- manter contexto;
- disponibilizar busca;
- alimentar ML;
- fornecer contexto para LLMs.

---

# 7. Modelo de Conhecimento

Cada entidade possui um perfil operacional.

Exemplo:

```
Vehicle Profile

├── ID
├── Marca
├── Modelo
├── Cor
├── Embedding
├── OCR
├── Confiança
├── Histórico
├── Incidentes
└── Linha do Tempo
```

---

Outro exemplo:

```
Person Profile

├── Face Embedding
├── Roupa
├── Cor
├── Capacete
├── ReID
├── Última localização
├── Histórico
└── Eventos
```

---

# 8. Fontes de Conhecimento

O conhecimento pode ser produzido por qualquer Capability.

Exemplos:

```
Vehicle Capability

↓

Cor

↓

OCR

↓

Tracker

↓

LLM

↓

Áudio

↓

GIS

↓

IoT

↓

Operador
```

Todas possuem o mesmo peso arquitetural.

---

# 9. Correlação

Eventos isolados raramente possuem significado suficiente.

O Correlation Engine agrupa eventos relacionados.

Exemplo:

```
Vehicle Detected

↓

Vehicle Tracked

↓

Plate OCR

↓

Vehicle Color

↓

Vehicle Model

↓

Vehicle Profile
```

O resultado é uma entidade enriquecida.

---

# 10. Enriquecimento

Após a correlação, o perfil pode receber novos atributos.

Exemplo:

```
Placa

↓

Consulta externa

↓

Veículo furtado

↓

Watchlist

↓

Prioridade alta
```

Outro exemplo:

```
Pessoa

↓

Face Match

↓

Lista de procurados

↓

Alerta
```

---

# 11. Linha do Tempo

Todo conhecimento possui histórico.

```
08:30

↓

Veículo detectado

↓

08:31

↓

OCR executado

↓

08:32

↓

Cor identificada

↓

08:33

↓

Watchlist encontrada

↓

08:34

↓

Alerta enviado
```

Nada é sobrescrito.

Apenas novas versões são adicionadas.

---

# 12. Confidence Timeline

Cada atributo possui evolução temporal.

Exemplo:

```
Frame 1

ABC1D2?

68%

↓

Frame 5

ABC1D23

84%

↓

Frame 12

ABC1D23

97%
```

A plataforma preserva toda essa evolução.

---

# 13. Versionamento

Conhecimento nunca é alterado diretamente.

Cada modificação gera uma nova versão.

```
Vehicle Profile

v1

↓

v2

↓

v3

↓

v4
```

Permite auditoria completa.

---

# 14. Human Feedback

Correções realizadas por operadores tornam-se conhecimento.

Exemplo:

```
OCR

ABC1023

↓

Operador

↓

ABC1D23

↓

Knowledge Update

↓

Dataset Builder

↓

Treinamento futuro
```

Nada é descartado.

---

# 15. Active Learning

Conhecimento alimenta continuamente o treinamento.

Fluxo:

```
Evento

↓

Confidence

↓

Fila de revisão

↓

Correção

↓

Dataset

↓

Treinamento

↓

Novo Modelo

↓

Nova Inferência
```

---

# 16. Busca

Toda informação enriquecida deve ser pesquisável.

Exemplos:

```
Veículos vermelhos

↓

Capacete branco

↓

Pessoa usando mochila

↓

Placa parcial

↓

Camisa azul

↓

Sem capacete

↓

Fumaça

↓

Chamada com arma

↓

Áudio classificado como roubo
```

---

# 17. Busca Semântica

A plataforma pode utilizar embeddings.

```
Consulta

↓

Embedding

↓

Vector Search

↓

Resultados semelhantes
```

Aplicável para:

- imagens;
- áudio;
- texto;
- descrições.

---

# 18. Explicabilidade

Toda conclusão deve informar sua origem.

Exemplo:

```
Cor = Azul

↓

Capability

Color Detection

↓

Modelo

YOLO-World

↓

Versão

2.3

↓

Confiança

96%
```

Nenhuma informação deve ser "mágica".

---

# 19. Armazenamento

O conhecimento pode utilizar diferentes tecnologias.

Exemplo:

| Camada | Tecnologia |
|---------|------------|
| Operational DB | PostgreSQL |
| Feature Store | Feast |
| Cache | Valkey |
| Vector Store | pgvector / Qdrant |
| Search | OpenSearch |
| Event Store | NATS JetStream |

A especificação não obriga uma implementação específica.

---

# 20. Relação com Outras Especificações

| Especificação | Responsabilidade |
|---------------|------------------|
| OES | Eventos produzidos |
| OMS | Modelos utilizados |
| OCS | Registro das Capabilities |
| CSS | SDK |
| CSpec | Manifesto |
| OCL | Lifecycle |

---

# 21. Benefícios

A arquitetura baseada em conhecimento proporciona:

- contexto operacional;
- menor quantidade de falsos positivos;
- buscas muito mais eficientes;
- Active Learning contínuo;
- explicabilidade;
- auditoria completa;
- suporte a Decision Intelligence;
- reutilização de conhecimento entre Capabilities;
- integração simples de novos algoritmos.

---

# 22. Futuras Extensões

O OKS foi projetado para suportar:

- Knowledge Graphs;
- Ontologias;
- RAG (Retrieval-Augmented Generation);
- Federated Learning;
- Digital Twins;
- Data Lineage;
- Explainable AI (XAI);
- Reasoning Engines;
- Complex Event Processing (CEP).

Esses componentes podem ser adicionados sem alterar os contratos existentes.

---

# Referências

- ADR-0001 — Architecture Principles
- ADR-0002 — Event-Driven Architecture
- ADR-0003 — Plugin First Architecture
- OES — OIP Event Specification
- OCS — OIP Capability Store Specification
- OMS — OIP Model Specification
- CSpec — OIP Capability Specification
- CSS — OIP Capability SDK Specification

---

# Status

**Draft**

Esta especificação estabelece os princípios e contratos para a construção da **Operational Knowledge Base (OKB)** da OIP, transformando eventos isolados em conhecimento operacional persistente, auditável e continuamente enriquecido, formando a base para funcionalidades avançadas de busca, apoio à decisão, aprendizado ativo e inteligência operacional.