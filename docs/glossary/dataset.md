# Dataset (Conjunto de Dados)

**Status:** Aprovado  
**Categoria:** Inteligência Artificial & Governança de Dados  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, um **Dataset** refere-se ao conjunto estruturado de dados rotulados, imagens, quadros de vídeo (*frames*), áudios, textos ou telemetrias utilizado para o treinamento, validação, teste e ajuste fino (*fine-tuning*) dos modelos de Inteligência Artificial executados pelos *AI Workers*.

---

## Papel no Ecossistema da OIP
Os datasets são elementos centrais no ciclo de vida de desenvolvimento e evolução contínua da IA na plataforma:

1. **Aprendizado Contínuo (*Feedback Loop*):** O sistema coleta dados operacionais reais (incluindo falsos positivos corrigidos por operadores no dashboard) e os armazena de forma estruturada em buckets no MinIO para realimentar os ciclos de retreinamento dos modelos.
2. **Versionamento e Rastreabilidade:** Assim como os artefatos de código e modelos, os datasets devem ser rigorosamente versionados. Isso garante reprodutibilidade científica e conformidade com auditorias técnicas, permitindo saber exatamente com quais dados um determinado modelo foi treinado.
3. **Métricas de Qualidade:** Datasets na OIP são acompanhados por metadados de distribuição de classes, cenários operacionais cobertos (ex: iluminação noturna, condições climáticas adversas) e limitações conhecidas, garantindo que o modelo atenda aos critérios de aceite antes de ser promovido ao *Model Registry*.

---

## Diretrizes de Governança
- **Privacidade e LGPD:** Dados contendo informações pessoais identificáveis (PII) coletados de fontes operacionais devem ser anonimizados ou mascarados antes de compor datasets de treinamento.
- **Armazenamento:** Datasets históricos de grande volume são mantidos em formato otimizado (como Apache Parquet) no *Cold Storage* (MinIO) para fins de auditoria e Big Data.