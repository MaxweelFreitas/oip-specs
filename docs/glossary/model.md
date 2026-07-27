# Model (Modelo de Inteligência Artificial)

**Status:** Aprovado  
**Categoria:** Inteligência Artificial & Model Registry  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, um **Model** (Modelo de Inteligência Artificial) refere-se ao algoritmo matemático treinado, pesos estatísticos, grafos computacionais e parâmetros otimizados (como redes neurais YOLO, PaddleOCR, Whisper ou LLMs locais) capazes de realizar inferências especializadas sobre dados operacionais heterogêneos (vídeo, áudio, texto ou telemetria)[cite: 1, 5].

---

## Papel no Ecossistema da OIP
Os modelos são componentes executáveis vitais para a extração de inteligência em tempo real, operando sob uma governança estrita:

1. **Abstração via Capacidades (*Capabilities*):** Os microsserviços da OIP nunca interagem diretamente com o modelo bruto. Eles interagem com as **Capacidades** (como `object_detection` ou `speech_to_text`), permitindo trocar ou atualizar o modelo subjacente sem modificar as regras de negócio[cite: 1, 5].
2. **Governança no Model Registry (`model.schema.json`):** Todo modelo possui um ciclo de vida controlado (`dev`, `test`, `staging`, `production`, `deprecated`), sendo catalogado com sua versão semântica (SemVer), framework de treinamento (PyTorch, Ultralytics, Transformers) e motor de inferência obrigatório (`onnx_runtime`, `tensorrt`, `llama_cpp`)[cite: 5].
3. **Execução Otimizada no AI Runtime:** Os modelos são carregados de forma persistente na memória (VRAM/RAM) pelo *AI Runtime*, evitando instanciações repetitivas e garantindo baixa latência de inferência em ambientes on-premises[cite: 1].

---

## Diretrizes de Utilização
- **Formato Padrão:** Sempre que possível, os modelos devem ser exportados para formatos universais otimizados, como **ONNX** (para visão computacional e OCR) ou **GGUF/Ollama** (para modelos de linguagem e áudio), garantindo portabilidade entre diferentes hardwares locais.
- **Rastreabilidade (Model Card):** Cada modelo em produção deve estar associado a um *Model Card* detalhando suas métricas de validação (Precision, Recall, latência P95), dataset de treinamento e limitações conhecidas[cite: 5].