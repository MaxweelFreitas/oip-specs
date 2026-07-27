# Processor (Processador / Componente de Transformação)

**Status:** Aprovado  
**Categoria:** Arquitetura de Software, Streaming & Clean Architecture  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, um **Processor** (Processador) refere-se a um componente modular, microsserviço ou rotina especializada encarregada de receber dados brutos ou normalizados de uma fonte, executar transformações lógicas, enriquecimentos, validações ou cálculos analíticos, e retransmitir o resultado adiante no fluxo operacional[cite: 1].

---

## Papel no Ecossistema da OIP
Os processadores atuam como elos fundamentais nos fluxos assíncronos da plataforma:

1. **Transformação de Protocolos:** No *Ingestion Core*, processadores converte dados de protocolos heterogêneos (como RTSP, MQTT ou gRPC) em eventos padronizados no barramento NATS JetStream[cite: 1].
2. **Enriquecimento de Contexto:** Adicionam metadados cruciais aos eventos brutos (por exemplo, associando coordenadas geográficas de PostGIS a uma detecção de câmera ou injetando o contexto do tenant e da organização)[cite: 1].
3. **Isolamento de Responsabilidades:** Seguem estritamente o princípio de responsabilidade única (*Single Responsibility Principle*), garantindo que tarefas como transcodificação de vídeo, extração de metadados de áudio ou normalização de texto sejam tratadas por componentes independentes e escaláveis[cite: 1].

---

## Diretrizes de Implementação
- **Stateless (Sem Estado):** Os processadores devem ser projetados preferencialmente como componentes sem estado (*stateless*), permitindo escalabilidade horizontal imediata via Kubernetes (K3s)[cite: 1].
- **Idempotência:** Todo processador deve ser capaz de lidar com a reentrega de mensagens (*at-least-once delivery*), garantindo que o reprocessamento de um mesmo evento não gere efeitos colaterais duplicados no sistema[cite: 1].
- **Resiliência e Tratamento de Erros:** Mensagens que falhem no processamento após o esgotamento das tentativas de reenvio (*retry*) devem ser encaminhadas automaticamente para uma *Dead Letter Queue* (DLQ) para análise posterior[cite: 1].