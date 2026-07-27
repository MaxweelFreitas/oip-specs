# RFC-0001: Multi-Tenant Logical & Physical Isolation Strategy

**Status:** Proposta  
**Autor:** Equipe de Arquitetura OIP  
**Data:** 2026-07-27  

---

## 1. Contexto e Objetivo
A **Operational Intelligence Platform (OIP)** foi concebida para atender tanto operações corporativas privadas quanto múltiplos órgãos de segurança pública ou unidades governamentais sob um mesmo cluster Kubernetes físico (on-premises). O objetivo desta RFC é definir a estratégia de isolamento de dados (*Multi-Tenancy*) para garantir que informações confidenciais de um inquilino (*tenant*) jamais sejam expostas ou acessadas por outro, mantendo alto desempenho e eficiência operacional.

---

## 2. Decisão Proposta: Isolamento Lógico com Colunas de Escopo e Row-Level Security (RLS)
Adotaremos o modelo de **Isolamento Lógico em Banco de Dados Compartilhado** com forte separação baseada em `tenant_id` e políticas de *Row-Level Security (RLS)* no PostgreSQL, combinada com isolamento de streams no NATS JetStream e namespaces dedicados no Kubernetes para ambientes críticos de governo.

### 2.1 Banco de Dados (PostgreSQL + PostGIS)
- Todas as tabelas transacionais e de projeção (`incidents`, `cameras`, `devices`, `audit_logs`) incluirão obrigatoriamente a coluna `tenant_id UUID NOT NULL`.
- O acesso a nível de aplicação injetará automaticamente o `tenant_id` extraído do token de autenticação (PASETO) no contexto de execução das queries via SQLC / ORM.
- Adicionalmente, ativaremos o *Row-Level Security (RLS)* do PostgreSQL para garantir que mesmo em consultas diretas, o banco aplique filtros restritivos por tenant.

### 2.2 Mensageria (NATS JetStream)
- Os tópicos de eventos utilizarão o `tenant_id` ou prefixos de organização na hierarquia de *subjects* (ex: `tenant.<tenant_id>.incident.created`).
- Os consumidores (*Consumer Groups*) serão configurados com filtros estritos de subjects, impedindo que workers de um tenant processem eventos de outro.

---

## 3. Consequências e Trade-offs
- **Vantagens:** Redução significativa de custos operacionais e de infraestrutura se comparado a bancos de dados totalmente isolados por tenant; facilidade de manutenção de migrações e backups unificados.
- **Desafios:** Exige rigor absoluto nos testes de integração e validação de contexto nas APIs para evitar vazamento de dados por falha de filtragem em nível de aplicação.