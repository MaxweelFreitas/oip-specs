# OCS — OIP Capability Store Specification

**Versão:** 1.0.0 (Draft)  
**Status:** Draft  
**Namespace:** `specs.oip.dev/ocs/v1`

---

# 1. Objetivo

A **OIP Capability Store Specification (OCS)** define o padrão para distribuição, descoberta, instalação, atualização, validação e gerenciamento de Capabilities dentro do ecossistema OIP.

O Capability Store é equivalente a uma **App Store**, porém voltada exclusivamente para componentes compatíveis com a arquitetura da OIP.

Seu objetivo é permitir que organizações, empresas e desenvolvedores publiquem novas capacidades sem qualquer alteração no código da plataforma principal.

O OCS garante que toda Capability instalada seja:

- compatível com a versão da plataforma;
- assinada digitalmente;
- verificável;
- rastreável;
- atualizável;
- isolada;
- auditável.

---

# 2. Filosofia

A plataforma OIP nunca conhece previamente uma Capability.

Ela apenas conhece a especificação.

Toda Capability deve ser instalada exatamente da mesma forma, independentemente de ela executar:

- IA
- OCR
- NLP
- GIS
- Áudio
- Analytics
- Dashboards
- Integrações
- Machine Learning
- qualquer tecnologia futura

A plataforma trabalha apenas com contratos.

---

# 3. Conceitos

## Capability

Unidade distribuível da plataforma.

Possui:

- manifesto
- imagem/container
- documentação
- modelos
- eventos
- configurações

---

## Capability Package

Pacote instalável contendo todos os recursos necessários para execução.

Exemplo:

```
vehicle-color.cap
```

ou

```
vehicle-color.tar.gz
```

ou

```
oci://registry.oip.dev/capabilities/vehicle-color:1.2.0
```

---

## Capability Registry

Repositório onde as Capabilities são publicadas.

Pode ser:

- público
- privado
- corporativo
- offline

Exemplos:

```
registry.oip.dev
```

```
registry.ciops.ce.gov.br
```

```
registry.local
```

---

## Capability Store

Serviço responsável por:

- listar Capabilities
- pesquisar
- instalar
- atualizar
- remover
- validar assinaturas
- baixar dependências

---

# 4. Componentes

```
Developer
     │
     ▼
Capability Package
     │
     ▼
Capability Registry
     │
     ▼
Capability Store
     │
     ▼
Capability Manager
     │
     ▼
Capability Runtime
```

---

# 5. Estrutura de um Package

```
vehicle-color/

├── capability.yaml
├── README.md
├── LICENSE
├── CHANGELOG.md

├── models/
├── configs/
├── proto/
├── runtime/
├── docs/
├── examples/

└── signature.sig
```

---

# 6. capability.yaml

Toda Capability deve possuir obrigatoriamente um manifesto.

O manifesto segue o padrão definido pela **CSpec**.

Sem ele a instalação deve ser recusada.

---

# 7. Registro

Ao ser publicada, a Capability recebe um identificador único.

Exemplo:

```
publisher: vcinova

name: vehicle-color

id: vcinova.vehicle-color

version: 1.4.2
```

O identificador nunca muda.

---

# 8. Versionamento

Segue Semantic Versioning.

```
MAJOR.MINOR.PATCH
```

Exemplo:

```
1.0.0
```

```
1.2.0
```

```
2.0.1
```

---

# 9. Dependências

Uma Capability pode depender de:

- outra Capability
- versão mínima do SDK
- versão mínima da plataforma
- modelos específicos

Exemplo

```yaml
dependencies:

  sdk:
    go: ">=1.0.0"

  platform:
    oip: ">=2.0"

  capabilities:

    - vehicle-detector
    - tracker

  models:

    - yolov11n
```

---

# 10. Compatibilidade

Antes da instalação o Store deve validar:

- versão da plataforma
- versão do SDK
- dependências
- conflitos
- assinatura
- arquitetura suportada

Caso alguma validação falhe:

A instalação deve ser interrompida.

---

# 11. Arquiteturas

Uma Capability pode declarar múltiplas arquiteturas.

Exemplo

```yaml
architectures:

- linux/amd64
- linux/arm64
```

---

# 12. Plataformas

Também pode restringir sistemas operacionais.

```yaml
platforms:

- linux
- windows
```

---

# 13. Distribuição

O Store deve suportar:

- OCI Registry
- Git Repository
- Arquivo local
- Mirror corporativo

Exemplo

```
oci://registry.oip.dev
```

```
git://github.com/...
```

```
file:///packages/
```

---

# 14. Assinatura Digital

Todo pacote publicado deve ser assinado.

O Store valida:

- integridade
- assinatura
- certificado
- cadeia de confiança

Capabilities não assinadas não devem ser instaladas em ambientes de produção.

---

# 15. Verificação de Integridade

Após download deve ser validado:

- SHA256
- tamanho
- assinatura

---

# 16. Atualizações

O Store deve verificar periodicamente:

- novas versões
- correções
- vulnerabilidades
- deprecações

Políticas suportadas:

- manual
- automática
- somente patches
- somente segurança

---

# 17. Instalação

Fluxo:

```
Download

↓

Verificação

↓

Validação

↓

Instalação

↓

Registro

↓

Inicialização

↓

Health Check

↓

Running
```

---

# 18. Remoção

Ao remover uma Capability o Store deve:

- interromper execução
- remover containers
- limpar configurações temporárias
- preservar auditoria
- preservar histórico

A remoção nunca deve apagar registros históricos.

---

# 19. Descoberta

O Store deve permitir pesquisas por:

- nome
- categoria
- publisher
- evento publicado
- evento consumido
- tags
- versão
- licença
- arquitetura
- hardware

Exemplo

```
category=vision
```

```
publisher=vcinova
```

```
event=oip.ai.attribute.detected
```

---

# 20. Segurança

O Store deve impedir:

- downgrade malicioso
- instalação duplicada
- conflitos
- assinaturas inválidas
- dependências incompatíveis
- execução de Capabilities comprometidas

---

# 21. Auditoria

Toda operação gera eventos padronizados.

Exemplos:

```
oip.capability.install.started
```

```
oip.capability.install.completed
```

```
oip.capability.updated
```

```
oip.capability.removed
```

```
oip.capability.failed
```

```
oip.capability.signature.invalid
```

---

# 22. Capability Marketplace

Implementações podem disponibilizar um catálogo visual contendo:

- descrição
- screenshots
- documentação
- notas de versão
- changelog
- compatibilidade
- requisitos
- métricas
- avaliações
- número de instalações
- publisher
- certificações

O Marketplace é uma implementação do OCS, não parte obrigatória da especificação.

---

# 23. Capability Hub Corporativo

Organizações podem manter um Store privado.

Exemplo:

```
CIOPS Store
```

contendo apenas Capabilities homologadas.

Fluxo:

```
Capability

↓

Homologação

↓

Assinatura

↓

Store Corporativo

↓

Instalação
```

---

# 24. Integração com outras Especificações

O OCS depende diretamente de:

- **CSpec** — Manifesto da Capability
- **CSS** — SDK Oficial
- **OCL** — Ciclo de Vida
- **OES** — Eventos
- **OMS** — Modelos
- **OSS** — Segurança

---

# 25. Objetivos da Especificação

O OCS estabelece um ecossistema distribuído para a OIP, permitindo que novas capacidades sejam desenvolvidas, distribuídas e atualizadas de forma segura, auditável e independente da plataforma principal.

A adoção desta especificação garante:

- distribuição padronizada de Capabilities;
- instalação automatizada e verificável;
- atualização controlada;
- rastreabilidade completa;
- segurança baseada em assinatura digital;
- suporte a repositórios públicos e privados;
- evolução independente da plataforma principal;
- formação de um ecossistema aberto de desenvolvedores, parceiros e organizações.