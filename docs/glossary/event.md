# Event (Evento de Domínio / Mensageria)

**Status:** Aprovado  
**Categoria:** Arquitetura Orientada a Eventos (EDA) & Mensageria  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, um **Event** (Evento) representa a ocorrência imutável de um fato relevante no domínio de negócio ou no ecossistema técnico (por exemplo, a detecção de um objeto por IA, a criação de um incidente ou a alteração de status de uma câmera)[cite: 1]. 

---

## Papel no Ecossistema da OIP
Os eventos são a base da arquitetura orientada a eventos (*Event-Driven Architecture*) da plataforma:

1. **Desacoplamento de Microsserviços:** Nenhuma comunicação direta síncrona é permitida entre serviços de negócio[cite: 1]. Os produtores publicam eventos no barramento *NATS JetStream*, e os consumidores reagem de forma independente utilizando *Consumer Groups*[cite: 1].
2. **Padrão CloudEvents (`event.schema.json`):** Todos os eventos assíncronos publicados na OIP seguem estritamente o envelope canônico do *CloudEvents 1.0*[cite: 1]. Isso padroniza metadados essenciais como `id`, `type`, `source`, `time`, `tenant_id`, `correlation_id` e o payload específico no campo `data`[cite: 1].
3. **Event Sourcing e Auditoria:** Eventos críticos de negócio são armazenados de forma imutável (Event Store), permitindo a reconstrução completa de estados (*replay*), auditoria forense inalterável e rastreabilidade perene de todas as decisões automatizadas da plataforma[cite: 1].

---

## Convenções de Nomenclatura e Versionamento
- **Passado Obrigatório:** Como eventos representam fatos ocorridos, seus tipos seguem o particípio passado (ex: `incident.created`, `alert.triggered`, `ai.detection.completed`)[cite: 1].
- **Hierarquia de Tópicos (Subjects):** Os eventos são publicados em tópicos estruturados (ex: `domain.aggregate.action`), permitindo roteamento granular e filtragem eficiente no NATS[cite: 1].
- **Compatibilidade Retroativa:** Modificações nos schemas de eventos devem ser estritamente retrocompatíveis (*Backward-Only*). Quebras de contrato exigem a criação de um novo tópico versionado (ex: `incident.created.v2`)[cite: 1].