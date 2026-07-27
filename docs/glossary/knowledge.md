# Knowledge Base (Base de Conhecimento & RAG)

**Status:** Aprovado  
**Categoria:** Inteligência Artificial, RAG & Gestão de Conhecimento  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, a **Knowledge Base** (Base de Conhecimento) refere-se ao repositório centralizado e indexado de documentos operacionais, manuais de procedimentos padrão (SOPs), transcrições históricas, relatórios de incidentes anteriores e diretrizes táticas, estruturado para alimentar motores de Recuperação Aumentada por Geração (*Retrieval-Augmented Generation* - RAG) e assistentes operacionais baseados em LLM.

---

## Papel no Ecossistema da OIP
A Base de Conhecimento desempenha um papel crítico no apoio à decisão e na redução da carga cognitiva dos operadores:

1. **Contextualização para LLMs (RAG):** Quando um incidente complexo é aberto ou um operador faz uma consulta em linguagem natural, o sistema realiza uma busca híbrida (textual + semântica via `pgvector` e OpenSearch) nos manuais e SOPs armazenados na Base de Conhecimento, injetando o contexto relevante no prompt do modelo local (Llama.cpp / Ollama) para gerar respostas precisas e baseadas em evidências.
2. **Padronização de Respostas Operacionais:** Garante que as orientações sugeridas pela IA estejam estritamente alinhadas com os protocolos oficiais da organização, minimizando erros humanos em situações de alta criticidade.
3. **Indexação Multimodal:** Textos, procedimentos em PDF, áudios transcritos e históricos de ocorrências são fragmentados (*chunking*), convertidos em vetores de alta dimensão e indexados para permitir recuperação instantânea durante o atendimento de chamados.

---

## Estrutura de Armazenamento
- **Busca Vetorial (`pgvector`):** Armazena os embeddings gerados a partir dos documentos para consultas de similaridade semântica[cite: 1, 5].
- **Indexação Textual (OpenSearch):** Permite buscas por palavras-chave e termos exatos em manuais e relatórios[cite: 1, 5].
- **Armazenamento de Arquivos Brutos (MinIO):** Mantém os documentos originais (PDFs, manuais, SOPs) seguros e acessíveis para download ou auditoria[cite: 1, 5].