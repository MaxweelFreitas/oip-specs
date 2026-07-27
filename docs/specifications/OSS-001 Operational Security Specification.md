# OSS — OIP Security Specification

**Version:** 1.0.0  
**Status:** Draft  
**Repository:** oip-specs

---

# 1. Objetivo

O **OIP Security Specification (OSS)** define o modelo de segurança utilizado por todo o ecossistema da Operational Intelligence Platform (OIP).

Seu objetivo é garantir que qualquer componente desenvolvido — seja oficial ou de terceiros — possa integrar-se à plataforma mantendo os mesmos padrões de:

- autenticação;
- autorização;
- confidencialidade;
- integridade;
- rastreabilidade;
- auditoria.

O OSS estabelece requisitos mínimos obrigatórios para:

- Capabilities
- SDKs
- Runtime
- Serviços Core
- Interfaces Web
- Aplicações Mobile
- APIs externas

---

# 2. Princípios

Toda implementação deve seguir os princípios abaixo.

## Zero Trust

Nenhum serviço é considerado confiável por padrão.

Toda comunicação deve ser autenticada.

---

## Least Privilege

Cada componente deve possuir apenas as permissões estritamente necessárias.

Nunca utilizar permissões globais.

---

## Defense in Depth

A segurança deve existir em múltiplas camadas.

Exemplo:

Hardware

↓

Sistema Operacional

↓

Container

↓

Runtime

↓

Capability

↓

Mensagem

↓

Payload

---

## Secure by Default

Uma Capability recém-instalada deve operar na configuração mais restritiva possível.

Toda abertura de permissões deve ser explícita.

---

## Auditability

Toda ação relevante deve ser auditável.

Nenhum evento importante pode ser perdido.

---

# 3. Identidade

Todo componente da plataforma possui uma identidade única.

Exemplo:

```
component_id

oip.identity
oip.dashboard
oip.runtime
oip.capability.color
oip.capability.lpr
```

A identidade deve ser imutável.

---

# 4. Identificação das Capabilities

Cada Capability possui:

```
publisher

name

version

capability_id
```

Exemplo

```
publisher:
    vcinova

name:
    color

version:
    1.2.0

capability_id:
    vcinova.color
```

---

# 5. Assinatura Digital

Toda Capability distribuída deverá ser assinada digitalmente.

Objetivos:

- impedir adulteração
- validar origem
- permitir cadeia de confiança

Os seguintes artefatos podem ser assinados:

- pacote
- manifesto
- modelo IA
- pesos (.pt)
- arquivos ONNX
- TensorRT
- configuração

---

# 6. Hashes

Todo artefato deverá possuir hash.

Algoritmo padrão:

```
SHA-256
```

Opcionalmente:

```
SHA-512
```

Os hashes permitem:

- verificação de integridade
- cache
- deduplicação
- auditoria

---

# 7. Comunicação Segura

Toda comunicação entre componentes deverá utilizar TLS.

Quando suportado:

```
mTLS
```

é recomendado.

---

# 8. Autenticação

A plataforma utiliza:

- PASETO
- mTLS
- API Keys
- Service Identity

Cada caso depende do tipo de integração.

### Usuários

PASETO

### Serviços

mTLS

### Runtime

Service Identity

### Integrações

API Key ou OAuth2

---

# 9. Autorização

A autorização utiliza RBAC + ABAC.

RBAC

- Admin
- Supervisor
- Operator
- Investigator

ABAC

Exemplos:

- tenant
- localização
- horário
- câmera
- capability
- classificação

---

# 10. Sandboxing

Toda Capability deve ser executada de forma isolada.

Exemplos:

Container

↓

Filesystem isolado

↓

Network Policies

↓

Limites de CPU

↓

Limites de Memória

↓

GPU isolada

---

# 11. Segredos

Nenhum segredo poderá estar embutido no código.

Devem ser utilizados:

- Vault
- Kubernetes Secrets
- Secret Manager

Nunca:

```
API_KEY="123456"
```

---

# 12. Segurança dos Modelos

Modelos de IA devem possuir:

- hash
- versão
- origem
- licença
- data de treinamento
- dataset utilizado
- framework

Exemplo

```
model:

name:
    YOLOv11

version:
    1.3.2

hash:
    SHA256...

framework:
    PyTorch

trained_at:
    2026-07-10
```

---

# 13. Segurança dos Eventos

Todo evento deve possuir:

- Event ID
- Timestamp UTC
- Producer
- Correlation ID
- Trace ID
- Tenant
- Schema Version

Opcionalmente:

- assinatura digital
- checksum

---

# 14. Auditoria

Toda ação relevante gera eventos de auditoria.

Exemplos:

Capability instalada

↓

Capability iniciada

↓

Capability atualizada

↓

Modelo carregado

↓

Modelo removido

↓

Evento publicado

↓

Evento rejeitado

↓

Correção realizada pelo operador

↓

Novo dataset criado

---

# 15. Atualizações

Toda atualização deverá preservar:

- compatibilidade
- integridade
- rollback seguro

O Runtime deverá validar:

- assinatura
- versão
- dependências
- compatibilidade do SDK

antes da instalação.

---

# 16. Requisitos Obrigatórios

Toda implementação compatível com a OIP deverá:

- possuir identidade única
- utilizar comunicação segura
- validar eventos
- registrar auditoria
- utilizar hashes
- possuir versionamento
- executar em ambiente isolado
- suportar atualização segura

---

# 17. Compatibilidade

Toda implementação compatível com o OSS poderá operar em qualquer Runtime oficialmente compatível com a OIP.

O objetivo é garantir interoperabilidade entre componentes desenvolvidos por organizações distintas mantendo o mesmo nível de segurança.

---

# 18. Evolução da Especificação

Mudanças nesta especificação seguem versionamento semântico.

- MAJOR: mudanças incompatíveis
- MINOR: novas funcionalidades compatíveis
- PATCH: correções e esclarecimentos

Cada Runtime deverá declarar explicitamente quais versões do OSS suporta.