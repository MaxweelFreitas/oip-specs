# Capability (Capacidade de IA)

**Status:** Aprovado  
**Categoria:** Inteligência Artificial & Arquitetura  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, uma **Capability** (Capacidade de IA) refere-se a uma habilidade analítica funcional padronizada (por exemplo, detecção de objetos, reconhecimento óptico de caracteres - OCR, transcrição de áudio - STT, embeddings, processamento de linguagem natural - NLP) que pode ser executada por um ou mais *AI Workers* utilizando diferentes modelos subjacentes (como YOLO, PaddleOCR, Whisper ou Llama.cpp)[cite: 1, 5].

---

## Papel no Ecossistema da OIP
O conceito de Capacidade é o pilar que desacopla a arquitetura da plataforma dos modelos de Inteligência Artificial específicos:

1. **Abstração de Modelos:** Os serviços centrais da OIP (como o *Incident Service* ou o *Correlation Engine*) interagem exclusivamente com as **Capacidades** e não com os modelos em si. Isso significa que a substituição de um modelo (por exemplo, atualizar de YOLOv10 para RT-DETR) não exige nenhuma alteração nas regras de negócio ou nos microsserviços consumidores.
2. **Padrozinzação de Contratos (`capability.schema.json`):** Toda capacidade produz eventos padronizados no barramento NATS JetStream, seguindo um formato uniforme de envelope CloudEvents combinado com um payload polimórfico (`results`) específico para aquela habilidade[cite: 1, 3, 5].
3. **Composição e Múltiplas Fontes:** Uma mesma capacidade pode ser executada por diferentes workers em paralelo ou em sequência, processando entradas de múltiplas naturezas (vídeo, áudio, texto, telemetria)[cite: 1, 5].

---

## Categorias de Capacidades Suportadas
A OIP padroniza nativamente diversas capacidades operacionais, incluindo[cite: 5]:
- `object_detection` (Detecção de objetos, armas, EPIs, fogo, pessoas, veículos)[cite: 1, 5]
- `classification` (Classificação multimodal de eventos e cenas)[cite: 1, 5]
- `segmentation` (Segmentação precisa de danos ou objetos)[cite: 1, 5]
- `ocr` (Reconhecimento óptico de caracteres em documentos e placas veiculares)[cite: 1, 5]
- `speech_to_text` (Transcrição em tempo real de chamadas telefônicas e áudios de rádio)[cite: 1, 5]
- `audio_classification` (Análise de sentimento e classificação de áudio)[cite: 5]
- `embedding` (Geração de vetores para busca semântica e visual via `pgvector`)[cite: 1, 5]
- `similarity_search` (Busca por proximidade de vetores e similaridade)[cite: 1, 5]
- `nlp` & `llm` (Processamento de texto, extração de entidades, resumos automáticos e assistente operacional via RAG)[cite: 5]

---

## Relação com Outros Componentes
- **AI Runtime / Workers:** Executam fisicamente a capacidade utilizando o motor de inferência adequado (ONNX Runtime, TensorRT ou Llama.cpp)[cite: 1, 5].
- **NATS JetStream:** Transmite os eventos de saída validados pelo schema da capacidade[cite: 1, 3, 5].
- **Model Registry:** Associa múltiplos modelos a uma única capacidade de acordo com a versão e o status em produção[cite: 1, 5].