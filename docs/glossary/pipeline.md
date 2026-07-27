# Pipeline (Pipeline de Ingestão e Processamento)

**Status:** Aprovado  
**Categoria:** Arquitetura de Dados, Streaming & IA  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, um **Pipeline** refere-se à sequência automatizada, orientada a eventos e de alta performance de etapas pelas quais os dados operacionais (vídeos, áudios, textos, telemetrias) passam desde a sua ingestão nas fontes de origem até a publicação dos resultados e geração de incidentes[cite: 1, 5].

---

## Papel no Ecossistema da OIP
Os pipelines estruturam o fluxo contínuo de informações na plataforma, garantindo baixa latência e processamento estruturado:

1. **Pipeline de Ingestão e Streaming:** Normaliza protocolos heterogêneos (RTSP, ONVIF, MQTT, gRPC) provenientes de câmeras e sensores, convertendo-os em eventos internos padronizados no NATS JetStream[cite: 1].
2. **Pipeline de Inferência de Inteligência Artificial:** Realiza de forma assíncrona as etapas de pré-processamento de quadros ou áudios (normalização, redimensionamento), execução do motor de inferência (ONNX Runtime / TensorRT / Llama.cpp), pós-processamento (NMS e thresholds) e publicação do evento de capacidade (`capability.schema.json`)[cite: 1, 5].
3. **Pipeline de Correlação e Gestão de Incidentes:** Processa e correlaciona eventos provenientes de múltiplas fontes, aplicando regras de negócio para deduplicar alarmes e promover eventos qualificados a incidentes ativos na plataforma[cite: 1, 5].

---

## Diretrizes Arquiteturais
- **Desacoplamento por Mensageria:** Cada etapa do pipeline comunica-se exclusivamente através do barramento de eventos, garantindo que falhas em um estágio não comprometam o fluxo global[cite: 1].
- **Escalabilidade Horizontal:** Os componentes de cada pipeline são conteinerizados e executados em pods independentes no Kubernetes (K3s), permitindo escalar instâncias de processamento conforme a demanda operacional[cite: 1].