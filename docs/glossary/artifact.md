# Artifact (Artefato de IA)

**Status:** Aprovado  
**Categoria:** Inteligência Artificial & Model Registry  
**Versão:** 1.0  

---

## Definição
Um **Artifact** (Artefato de IA) na **Operational Intelligence Platform (OIP)** refere-se ao pacote binário imutável contendo os pesos treinados, arquivos de configuração, grafos de computação e metadados de um modelo de Inteligência Artificial pronto para ser implantado em produção (por exemplo, arquivos `.onnx`, `.gguf`, `.pt`, `engine` do TensorRT ou arquivos compactados em `.tar.gz`).

---

## Papel no Ecossistema da OIP
Os artefatos de IA são o resultado final do ciclo de treinamento e validação realizados pelas equipes de ciência de dados. Na OIP, eles desempenham um papel crítico:

1. **Model Registry (`model.schema.json`):** Todo artefato deve ser devidamente registrado no catálogo central de modelos da plataforma, vinculando sua versão semântica (SemVer), capacidade, framework e métricas de benchmark validadas.
2. **Armazenamento de Longo Prazo (MinIO):** Os arquivos binários de pesos dos modelos não são armazenados em bancos de dados relacionais; eles são persistidos em buckets S3-compatíveis (MinIO) isolados e criptografados.
3. **Integridade Criptográfica (SHA-256):** Para garantir segurança (Zero Trust e Supply Chain Security), todo artefato possui um hash SHA-256 obrigatório validado pelo *AI Runtime* no momento do download e carregamento na memória (VRAM/RAM).
4. **Deploy Automatizado via GitOps:** O versionamento do artefato aciona fluxos no ArgoCD para distribuição automática aos nós de inferência do cluster K3s on-premises.

---

## Estrutura do Artefato
Um artefato típico gerenciado pela OIP contém:
- **Pesos do Modelo:** Arquivo binário otimizado (ex: `model.onnx` ou `model.gguf`).
- **Configurações de Pré e Pós-processamento:** Parâmetros de redimensionamento, normalização, limiares de confiança (*thresholds*) e mapeamento de classes.
- **Model Card:** Documentação associada detalhando dataset de treinamento, limitações conhecidas e métricas de desempenho (Precision, Recall, latência P95).

---

## Relação com Outros Componentes
- **Model Registry:** Gerencia os metadados do artefato.
- **MinIO:** Armazena o binário físico do artefato.
- **AI Runtime:** Baixa, verifica o hash e carrega o artefato na VRAM/RAM para execução de inferências.
- **GitOps / ArgoCD:** Automatiza a propagação de novas versões de artefatos para o ambiente de produção.