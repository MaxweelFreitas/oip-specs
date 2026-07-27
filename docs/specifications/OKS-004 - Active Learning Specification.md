# OKS-004 — Active Learning Specification

**Version:** 1.0.0  
**Status:** Draft  
**Type:** Core Specification  
**Identifier:** OKS-004  
**Depends on:** OES, OMS, OKS-001, OKS-002, OKS-003, OCS, CSS  
**Applies to:** Operational Knowledge Base (OKB), Capability Runtime, Dataset Builder, Training Pipeline

---

# 1. Objetivo

Esta especificação define a arquitetura oficial de **Active Learning** da **Operational Intelligence Platform (OIP)**.

O objetivo é permitir que a plataforma **aprenda continuamente durante sua operação**, utilizando o feedback dos operadores para melhorar modelos de Inteligência Artificial, reduzir falsos positivos e aumentar a qualidade das inferências, sem interromper a operação da plataforma. O Active Learning prioriza exemplos de baixa confiança ou maior valor informacional para revisão humana, reduzindo o esforço de anotação e acelerando a evolução dos modelos. :contentReference[oaicite:0]{index=0}

---

# 2. Princípios

O Active Learning da OIP deve ser:

- Human-in-the-loop
- Event-Driven
- Incremental
- Auditável
- Explicável
- Assíncrono
- Multi-Capability
- Multi-Tenant
- Independente do modelo de IA
- Integrado ao ecossistema MLOps

---

# 3. Visão Geral

A operação da plataforma nunca é interrompida para treinamento.

```
Inferência

↓

Baixa confiança

↓

Fila de revisão

↓

Operador

↓

Correção

↓

Dataset Builder

↓

Treinamento

↓

Validação

↓

Model Registry

↓

Deploy

↓

Nova Inferência
```

---

# 4. Filosofia

A plataforma **não aprende automaticamente durante a inferência**.

Ela aprende através da observação do uso.

Cada interação do operador representa conhecimento operacional.

---

# 5. Conceitos

## Inferência

Resultado produzido por uma Capability.

---

## Evidência

Imagem, áudio, vídeo ou documento utilizado para produzir uma inferência.

---

## Correção

Alteração realizada pelo operador.

---

## Feedback

Evento indicando confirmação ou rejeição de uma inferência.

---

## Dataset

Conjunto organizado de exemplos para treinamento.

---

## Modelo

Artefato treinado e registrado no Model Registry.

---

# 6. Arquitetura

```
Capability Runtime

↓

Inference Event

↓

Correlation Engine

↓

Knowledge Engine

↓

Dashboard

↓

Operador

↓

Feedback

↓

Dataset Builder

↓

Training Pipeline

↓

Model Registry

↓

Capability Runtime
```

---

# 7. Tipos de Feedback

O sistema suporta diferentes categorias.

## Correção

```
Placa

ABC1D23

↓

ABC1D28
```

---

## Confirmação

```
Pessoa

Capacete Branco

↓

Confirmado
```

---

## Rejeição

```
Incêndio

↓

Falso Positivo
```

---

## Complementação

```
Marca

↓

Toyota
```

---

## Classificação

```
Veículo

↓

SUV
```

---

## Segmentação

Correção de máscaras.

---

## Bounding Box

Correção de localização.

---

# 8. Fontes de Feedback

O feedback pode ser proveniente de:

- operador;
- supervisor;
- investigador;
- integração externa;
- sistema especialista;
- revisão posterior.

---

# 9. Confidence Policy

Nem toda inferência precisa ser revisada.

A política oficial utiliza três níveis.

| Nível | Ação |
|--------|------|
| HIGH | Aceita automaticamente |
| MEDIUM | Disponível para revisão |
| LOW | Prioridade máxima de revisão |

Os limiares são configuráveis por Capability.

---

# 10. Seleção Inteligente

O Active Learning **não envia exemplos aleatórios**.

São priorizados exemplos com:

- baixa confiança;
- conflito entre modelos;
- divergência temporal;
- baixa qualidade visual;
- classes raras;
- novos ambientes;
- novas câmeras;
- novos operadores.

Essa estratégia reduz significativamente o número de exemplos que precisam ser rotulados para melhorar o modelo. :contentReference[oaicite:1]{index=1}

---

# 11. Query Strategies

A plataforma suporta múltiplas estratégias.

## Uncertainty Sampling

Seleciona exemplos de menor confiança.

---

## Margin Sampling

Seleciona previsões próximas entre duas classes.

---

## Entropy Sampling

Seleciona inferências com maior incerteza probabilística.

---

## Diversity Sampling

Evita exemplos muito semelhantes.

---

## Novelty Detection

Prioriza situações nunca vistas.

---

## Drift Detection

Prioriza mudanças no ambiente.

---

## Human Priority

Permite seleção manual.

---

# 12. Active Learning Queue

Toda revisão entra em uma fila.

```
Inference

↓

Policy Engine

↓

Active Learning Queue

↓

Operador
```

---

# 13. Priorização

Cada item recebe um score.

Exemplo.

```
Confidence

↓

Class Weight

↓

Novelty

↓

Business Priority

↓

Final Score
```

Itens com maior score aparecem primeiro.

---

# 14. Interface do Operador

O operador pode:

- confirmar;
- corrigir;
- rejeitar;
- complementar;
- ignorar;
- justificar.

Nenhuma ação substitui o histórico.

---

# 15. Correção por Atributo

A revisão ocorre no menor nível possível.

Exemplo.

```
Veículo

↓

Placa

↓

ABC1D23

↓

ABC1D28
```

Sem necessidade de alterar toda a entidade.

---

# 16. Auditoria

Toda correção registra:

- operador;
- data;
- justificativa;
- valor original;
- novo valor;
- Capability;
- versão do modelo;
- evidência utilizada.

---

# 17. Dataset Builder

O Dataset Builder é responsável por transformar feedback em dados de treinamento.

```
Feedback

↓

Validação

↓

Exportação

↓

Dataset
```

---

# 18. Estrutura do Dataset

Cada amostra deve conter:

```
Imagem

↓

Label

↓

Metadata

↓

Capability

↓

Model Version

↓

Camera

↓

Timestamp

↓

Confidence

↓

Operator
```

---

# 19. Versionamento

Nenhum dataset é alterado.

Cada atualização gera nova versão.

```
Dataset v1

↓

v2

↓

v3
```

---

# 20. Qualidade dos Dados

Antes do treinamento o Dataset Builder valida:

- duplicidade;
- consistência;
- integridade;
- resolução;
- formato;
- distribuição.

---

# 21. Auto Dataset Builder

O serviço pode montar datasets automaticamente.

```
Correções

↓

Agrupamento

↓

Exportação

↓

YOLO

↓

COCO

↓

OCR

↓

Parquet

↓

JSONL
```

---

# 22. Treinamento

O treinamento ocorre fora da plataforma operacional.

```
Dataset

↓

Training Pipeline

↓

Validation

↓

Registry
```

---

# 23. Validação

Antes do deploy o modelo deve atingir critérios mínimos.

Exemplo.

```
mAP

Precision

Recall

F1

Latency

Memory

GPU Usage
```

---

# 24. Model Registry

Todo modelo aprovado recebe:

- ID;
- versão;
- hash;
- métricas;
- dataset utilizado;
- data;
- responsável.

---

# 25. Deploy

O deploy deve utilizar estratégias como:

- Canary;
- Blue-Green;
- Rolling Update.

---

# 26. Aprendizado Contínuo

O ciclo nunca termina.

```
Inferência

↓

Feedback

↓

Dataset

↓

Treinamento

↓

Deploy

↓

Nova Inferência
```

---

# 27. Integração com o Knowledge Graph

Correções podem alterar relações.

```
Correção

↓

Knowledge Update

↓

Graph Update
```

---

# 28. Integração com o Correlation Engine

O Correlation Engine pode solicitar revisão.

Exemplo.

```
Conflito

↓

Fila

↓

Operador
```

---

# 29. Integração com o Capability Runtime

Cada Capability informa:

- confiança;
- modelo;
- versão;
- qualidade da inferência;
- métricas.

---

# 30. Explainability

Toda revisão informa:

- motivo;
- inferência original;
- evidência;
- decisão humana.

---

# 31. Segurança

Todo feedback deve respeitar:

- RBAC;
- ABAC;
- LGPD;
- Auditoria;
- Criptografia.

---

# 32. Benefícios

O Active Learning permite:

- redução de falsos positivos;
- melhoria contínua;
- adaptação ao ambiente;
- redução do custo de anotação;
- maior precisão dos modelos;
- aprendizado baseado em operação real;
- rastreabilidade completa;
- evolução independente de cada Capability.

---

# 33. Futuras Extensões

A arquitetura suporta:

- Reinforcement Learning from Human Feedback (RLHF);
- Federated Learning;
- Continual Learning;
- Semi-Supervised Learning;
- AutoML;
- Auto Labeling;
- Synthetic Data;
- Query-by-Committee;
- Multi-Modal Active Learning.

---

# Referências

- OKS-001 — Operational Knowledge Specification
- OKS-002 — Knowledge Graph Specification
- OKS-003 — Correlation & Aggregation Specification
- OES — OIP Event Specification
- OMS — OIP Model Specification
- OCS — OIP Capability Store Specification
- CSS — OIP SDK Specification
- ADR-0001 — Architecture Principles
- ADR-0002 — Event-Driven Architecture
- ADR-0003 — Plugin First Architecture

---

# Status

**Draft**

Esta especificação define a arquitetura oficial de **Active Learning da OIP**, estabelecendo um ciclo contínuo de melhoria onde operadores, Capabilities, mecanismos de correlação e pipelines de MLOps colaboram para evoluir os modelos de IA de forma auditável, incremental e desacoplada, preservando a operação em tempo real da plataforma.