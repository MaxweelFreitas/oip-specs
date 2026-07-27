# RFC-0002: Edge-to-Cloud Event Sync and Autonomous Operations

**Status:** Proposta  
**Autor:** Equipe de Arquitetura OIP  
**Data:** 2026-07-27  

---

## 1. Contexto e Objetivo
Em cenários de segurança pública e proteção patrimonial distribuída, é comum a necessidade de operar em localidades remotas ou de borda (*Edge*) onde a conectividade com o datacenter central ou nuvem pode ser intermitente ou de baixa largura de banda. O objetivo desta RFC é definir a estratégia de sincronização de eventos e operação autônoma entre nós de borda e o ambiente central na OIP.

---

## 2. Decisão Proposta: Arquitetura Hub-and-Spoke com NATS Leaf Nodes
Adotaremos o padrão de **NATS Leaf Nodes** para interligar os nós de borda (*Spokes*) ao barramento central (*Hub*). 

### 2.1 Operação Autônoma na Borda
- Cada nó de borda executa um cluster K3s local contendo instâncias locais do NATS JetStream, Ingestion Core, AI Runtime e microsserviços essenciais (*Incident Service* local).
- O nó de borda opera 100% de forma autônoma: se a conexão com a rede central cair, a detecção de incidentes, o processamento de IA por GPU local e a notificação de operadores locais continuam funcionando sem interrupção.

### 2.2 Sincronização Assíncrona via Leaf Nodes
- O NATS Leaf Node estabelece uma conexão segura (mTLS) de saída (*outbound*) com o NATS Central Hub assim que a conectividade é restabelecida.
- Os eventos persistidos localmente (como criação de incidentes, logs de auditoria e evidências) são sincronizados em lotes para o cluster central, garantindo consistência eventual e replicação de dados para relatórios gerenciais e visão nacional/regional.

---

## 3. Consequências e Trade-offs
- **Vantagens:** Resiliência máxima em campo frente a quedas de link de internet; menor latência local de processamento; sincronização automática e transparente gerenciada pelo próprio protocolo NATS.
- **Desafios:** Requer armazenamento local robusto na borda (NVMe/SSD) para reter eventos durante longos períodos de desconexão até que o link seja recuperado.