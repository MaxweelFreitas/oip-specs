# OMS — OIP Model Specification

Version: 1.0.0

Status: Draft

---

# 1. Objetivo

A **OIP Model Specification (OMS)** define o contrato padrão para publicação, distribuição, execução e versionamento de modelos de Inteligência Artificial utilizados pela Operational Intelligence Platform (OIP).

O objetivo desta especificação é tornar qualquer modelo de IA interoperável, independentemente da linguagem, framework ou fabricante.

A plataforma não executa modelos específicos.

Ela executa modelos que obedecem ao OMS.

---

# 2. Filosofia

Na OIP um modelo representa apenas uma implementação de inferência.

Ele nunca contém regras de negócio.

Ele nunca conhece a plataforma.

Ele apenas recebe uma entrada e produz uma saída.

Toda orquestração pertence ao Runtime.

Frame
↓
Modelo
↓
Inferência
↓
Resultado

```

---

# 3. Objetivos

A especificação busca garantir:

- independência de framework
- versionamento
- reprodutibilidade
- auditoria
- compatibilidade
- portabilidade
- distribuição

---

# 4. Estrutura de um Modelo

Todo modelo possui obrigatoriamente:

```

model/
├── model.yaml
├── weights/
├── metadata/
├── labels/
├── README.md
└── LICENSE

````

---

# 5. model.yaml

Todo modelo deve possuir um manifesto.

Exemplo:

```yaml
apiVersion: oip.io/v1

kind: Model

metadata:

  id: vehicle-color

  version: 1.2.0

  author: Vision Labs

  license: Apache-2.0

spec:

  framework: pytorch

  runtime: python

  task: classification

  input:

    image:

      width: 224

      height: 224

      channels: 3

  output:

    classes:

      - white

      - black

      - gray

      - blue

      - red
````

---

# 6. Informações Obrigatórias

Todo modelo deve informar:

* ID
* Nome
* Versão
* Framework
* Runtime
* Autor
* Licença
* Tipo da tarefa
* Entradas
* Saídas
* Classes
* Dependências

---

# 7. Tipos de Tarefa

A OMS define inicialmente:

```
classification

object_detection

segmentation

ocr

speech_to_text

translation

embedding

re_identification

anomaly_detection

tracking

llm

nlp

summarization

entity_extraction

audio_classification

face_recognition

pose_estimation
```

Novos tipos poderão ser adicionados sem quebrar compatibilidade.

---

# 8. Frameworks Suportados

O OMS não restringe frameworks.

Exemplos:

* PyTorch
* TensorFlow
* ONNX
* TensorRT
* OpenVINO
* Paddle
* Darknet
* Scikit-Learn
* XGBoost
* CatBoost
* LightGBM

---

# 9. Formatos de Peso

Os pesos podem ser distribuídos em qualquer formato suportado pelo Runtime.

Exemplos:

```
.pt

.onnx

.engine

.bin

.pb

.safetensors
```

---

# 10. Versionamento

Todo modelo possui:

```
Major.Minor.Patch
```

Exemplo

```
1.0.0
```

Mudanças incompatíveis:

```
2.0.0
```

Novas classes:

```
1.1.0
```

Correções:

```
1.1.1
```

---

# 11. Compatibilidade

Todo modelo declara:

```yaml
compatibility:

  runtime:

    min: 1.0

    max: 2.x

  sdk:

    min: 1.0
```

---

# 12. Assinatura

Modelos podem ser assinados digitalmente.

```yaml
signature:

  algorithm: ed25519

  fingerprint: SHA256...
```

A assinatura permite verificar integridade e autenticidade antes da instalação.

---

# 13. Metadados de Treinamento

Opcionalmente o modelo pode informar:

* dataset utilizado
* data de treinamento
* quantidade de imagens
* classes
* hardware
* framework
* epochs
* batch size
* learning rate

Essas informações facilitam auditoria científica e reprodutibilidade.

---

# 14. Métricas

O manifesto pode incluir métricas obtidas durante validação.

Exemplo:

```yaml
metrics:

  map50: 0.94

  map95: 0.82

  precision: 0.95

  recall: 0.92

  f1: 0.93
```

---

# 15. Capacidades Compatíveis

Um modelo pode declarar quais Capabilities o utilizam.

```yaml
usedBy:

- vehicle-color

- ppe

- fire-smoke
```

Isso permite reutilização entre múltiplas capacidades.

---

# 16. Princípios

A OMS estabelece que:

* modelos são desacoplados da plataforma;
* modelos são substituíveis;
* modelos são auditáveis;
* modelos são versionáveis;
* modelos são reproduzíveis;
* modelos nunca implementam regras de negócio;
* modelos apenas executam inferência;
* todo comportamento operacional pertence ao Runtime e às Capabilities.