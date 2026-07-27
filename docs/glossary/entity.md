# Entity (Entidade de Domínio)

**Status:** Aprovado  
**Categoria:** Domínio, Modelagem & Clean Architecture  
**Versão:** 1.0  

---

## Definição
Na **Operational Intelligence Platform (OIP)**, uma **Entity** (Entidade de Domínio) é um objeto que possui uma identidade única e contínua ao longo do tempo, diferenciando-se de um *Value Object* que é definido apenas por seus atributos. Na arquitetura orientada a domínio (DDD), as entidades encapsulam tanto estado quanto comportamento de negócio crítico.

---

## Papel no Ecossistema da OIP
As entidades representam os conceitos fundamentais do mundo real que compõem os contextos delimitados (*Bounded Contexts*) da plataforma:

1. **Identidade Única:** Toda entidade na OIP possui um identificador único imutável (geralmente gerado como UUID v7), garantindo rastreabilidade perene mesmo quando seu estado (como status, prioridade ou localização) é modificado.
2. **Proteção de Invariantes:** As entidades pertencem ao núcleo da *Clean Architecture* (*Domain Layer*). Elas são responsáveis por proteger suas próprias regras de negócio e invariantes (por exemplo, impedir que um recurso seja despachado para um incidente já encerrado).
3. **Emissão de Eventos de Domínio:** Ao sofrerem alterações de estado relevantes através de suas operações, as entidades registram fatos internos que posteriormente são convertidos em eventos de domínio e publicados no barramento NATS JetStream (por exemplo, `IncidentCreated`, `StatusUpdated`).

---

## Exemplo de Entidade Principal
O agregado central do sistema, o **Incident**, é modelado como uma entidade raiz (*Aggregate Root*) que gerencia o ciclo de vida da ocorrência, contendo referências para entidades associadas e coleções de evidências.

---

## Diretrizes de Implementação
- **Independência Tecnológica:** Entidades puras não devem conter anotações de banco de dados (como ORM tags), dependências de frameworks, lógica de serialização JSON ou chamadas de infraestrutura.
- **Mutabilidade Controlada:** A alteração de estado em uma entidade deve ocorrer exclusivamente através de métodos comportamentais explícitos (ex: `Close()`, `AssignResource()`), nunca por atribuição direta a seus campos internos.