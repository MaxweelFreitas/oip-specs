# Attribute (Atributo)

**Status:** Aprovado  
**Categoria:** Domínio, Modelagem de Dados & Arquitetura  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, um **Attribute** (Atributo) refere-se a uma propriedade descritiva, metadado ou característica específica associada a uma entidade de domínio (como *Incident*, *Camera*, *Evidence*, *Operator*) ou gerada durante a inferência por um modelo de Inteligência Artificial (*AI Workers*).

---

## Papel no Ecossistema da OIP
Os atributos desempenham um papel fundamental na flexibilidade e estruturação dos dados da plataforma:

1. **Enriquecimento Operacional:** Permitem anexar informações contextuais a eventos e ocorrências (por exemplo, cor de um veículo, tipo de arma detectada, nível de urgência ou crachá de um operador).
2. **Controle de Acesso Baseado em Atributos (ABAC):** Em conjunto com o RBAC (*Role-Based Access Control*), os atributos de contexto (como horário de trabalho, setor ou nível de liberação do operador) são utilizados pelo *Identity Core* para conceder ou restringir o acesso a recursos confidenciais.
3. **Flexibilidade em Objetos Polimórficos (`attributes` JSONB):** Nos contratos de saída dos AI Workers (`capability.schema.json`), o campo `attributes` armazena metadados flexíveis adicionais gerados pela inferência (por exemplo, gênero, vestimentas ou características secundárias de um objeto detectado) sem a necessidade de alterar rigidamente o esquema principal do banco de dados.
4. **Consultas e Indexação:** Atributos críticos são indexados no PostgreSQL (via colunas tipadas ou campos `JSONB` com operadores GIN) e no OpenSearch para permitir buscas rápidas e filtragem avançada em investigações forenses.

---

## Boas Práticas de Modelagem
- **Tipagem Clara:** Sempre que um atributo possuir valores finitos e conhecidos, deve ser tratado como um *Value Object* tipado ou enumerado (`enum`) no domínio.
- **Minimização de Dados:** Em conformidade com a LGPD e o princípio de *Privacy by Design*, atributos sensíveis ou de identificação pessoal (PII) devem ser mascarados ou criptografados antes da persistência.
- **Padronização:** Nomes de atributos devem seguir o padrão estrito de nomenclatura em inglês (`snake_case` para banco de dados e chaves JSON, `camelCase` para propriedades em código Go/TypeScript).