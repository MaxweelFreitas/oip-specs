# OIP Specifications

> The open specification ecosystem for building modular Operational Intelligence capabilities.

---

## Overview

**OIP Specifications** define o conjunto de padrões oficiais utilizados pela **Operational Intelligence Platform (OIP)** para garantir interoperabilidade entre componentes, microsserviços, modelos de Inteligência Artificial, Capabilities e aplicações desenvolvidas tanto pela equipe da plataforma quanto por terceiros.

O objetivo deste repositório é estabelecer uma especificação aberta, extensível e versionada, permitindo que novas funcionalidades possam ser desenvolvidas independentemente da implementação interna da plataforma, preservando compatibilidade entre versões e reduzindo o acoplamento entre componentes.

Assim como o **CloudEvents** define um padrão para eventos e o **OpenTelemetry** define um padrão para observabilidade, o **OIP Specifications** define os contratos fundamentais do ecossistema da OIP.

---

## OIP Design Philosophy

> A OIP não é um sistema de monitoramento, nem um conjunto de modelos de IA. A OIP é uma plataforma de construção de conhecimento operacional em tempo real. Cada Capability produz observações; essas observações são correlacionadas para formar conhecimento, que apoia operadores e sistemas na tomada de decisão.:

---

# Objetivos

As especificações da OIP possuem cinco objetivos principais:

- Definir contratos independentes de linguagem de programação.
- Garantir interoperabilidade entre Capabilities.
- Padronizar eventos, modelos de dados e ciclo de vida dos componentes.
- Facilitar o desenvolvimento de plugins e extensões por terceiros.
- Permitir evolução da plataforma preservando compatibilidade retroativa sempre que possível.

---

# Princípios

Toda especificação publicada neste repositório segue os seguintes princípios arquiteturais.

## Event First

Toda comunicação entre componentes deve ocorrer através de eventos padronizados.

Nenhuma Capability depende diretamente da implementação interna de outra.

---

## Capability First

A plataforma não conhece detectores específicos.

A plataforma conhece apenas **Capabilities**.

Exemplos:

- Vehicle Intelligence
- Color Recognition
- License Plate Recognition
- Face Recognition
- Fire Detection
- Audio Classification
- Speech Transcription
- Decision Support

Cada Capability pode evoluir independentemente.

---

## Language Agnostic

As especificações não pertencem a nenhuma linguagem.

Uma Capability poderá ser desenvolvida em:

- Go
- Python
- Rust
- Java
- C#
- C++
- outras

desde que respeite os contratos definidos.

---

## Vendor Neutral

As especificações não dependem de frameworks ou fornecedores específicos.

Por exemplo:

A especificação define:

```
Event Bus
```

mas não exige:

- NATS
- Kafka
- RabbitMQ

Da mesma forma:

```
Vector Database
```

não significa obrigatoriamente:

- Qdrant
- Milvus
- pgvector

---

## Open by Design

Todo componente deve poder ser substituído sem alterar a arquitetura da plataforma.

A OIP não depende de um modelo específico de IA.

Não depende de um banco específico.

Não depende de um framework específico.

---

# Arquitetura do Ecossistema

```
                    +----------------------+
                    |    OIP Specifications|
                    +----------------------+
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
        ▼                      ▼                      ▼
   OIP Proto              OIP SDK              OIP Runtime
        │                      │                      │
        └──────────────────────┼──────────────────────┘
                               ▼
                     Operational Intelligence Platform
                               │
        ┌───────────────┬───────────────┬───────────────┐
        ▼               ▼               ▼
 Capability A     Capability B     Capability C
```

---

# Repositórios Oficiais

## oip-specs

Define todas as especificações oficiais.

Não contém código de produção.

---

## oip-proto

Contratos Protobuf compartilhados entre todos os componentes.

---

## oip-sdk

SDKs oficiais para desenvolvimento de Capabilities.

Exemplos:

- Go SDK
- Python SDK
- Rust SDK

---

## oip-runtime

Runtime responsável pela execução, gerenciamento e ciclo de vida das Capabilities.

---

## oip

Implementação oficial da Operational Intelligence Platform.

---

## oip-capability-*

Repositórios independentes contendo Capabilities.

Exemplos:

- oip-capability-color
- oip-capability-plate
- oip-capability-fire
- oip-capability-ppe

Essas Capabilities podem ser desenvolvidas tanto pela equipe da plataforma quanto por terceiros.

---

# Organização das Especificações

As especificações são organizadas em documentos independentes.

Cada documento descreve apenas um domínio específico.

Exemplo:

```
OES
Operational Event Specification

CSpec
Capability Specification

CSS
Capability SDK Specification

OCL
Operational Capability Lifecycle

OMS
Operational Model Specification

OCS
Operational Capability Store Specification

OSS
Operational Security Specification

OKS
Operational Knowledge Specification
```

Cada especificação possui versionamento próprio.

---

# Compatibilidade

Toda especificação segue versionamento semântico.

```
MAJOR.MINOR.PATCH
```

Mudanças incompatíveis incrementam:

```
MAJOR
```

Novas funcionalidades compatíveis incrementam:

```
MINOR
```

Correções incrementam:

```
PATCH
```

---

# Governança

As especificações evoluem através de:

- RFC (Request For Comments)
- ADR (Architecture Decision Records)

Nenhuma mudança significativa é introduzida diretamente.

---

# Público-Alvo

Este repositório destina-se a:

- Desenvolvedores da OIP
- Desenvolvedores de Capabilities
- Pesquisadores
- Empresas parceiras
- Fabricantes de dispositivos
- Integradores
- Comunidade Open Source

---

# Licenciamento

As especificações são públicas e independentes da implementação da plataforma.

Consulte o arquivo `LICENSE` para informações sobre licenciamento.