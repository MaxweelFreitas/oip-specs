# OCL — OIP Capability Lifecycle

**Version:** 1.0.0  
**Status:** Draft  
**Type:** Core Specification  
**Identifier:** OCL  
**Depends on:** CSpec, CSS, OES  
**Applies to:** Todas as Capabilities OIP

---

# 1. Objetivo

Esta especificação define o **ciclo de vida oficial** de uma Capability dentro do ecossistema da **Operational Intelligence Platform (OIP)**.

O objetivo é padronizar como uma Capability é:

- descoberta;
- instalada;
- inicializada;
- monitorada;
- atualizada;
- interrompida;
- removida.

Todas as implementações devem seguir este ciclo de vida independentemente da linguagem utilizada (Go, Python, Rust, C++, etc.).

---

# 2. Motivação

Sem um ciclo de vida padronizado surgem problemas como:

- Capabilities iniciando antes das dependências;
- atualização sem rollback;
- múltiplas versões simultâneas;
- recursos não liberados;
- inconsistência operacional;
- dificuldade de monitoramento.

O OCL resolve esses problemas definindo um conjunto de estados e transições obrigatórias.

---

# 3. Princípios

O ciclo de vida deve obedecer aos seguintes princípios:

- determinístico;
- observável;
- auditável;
- resiliente;
- reversível quando possível;
- independente da implementação.

Nenhuma Capability pode gerenciar seu próprio ciclo de vida.

Essa responsabilidade pertence ao **Capability Runtime**.

---

# 4. Máquina de Estados

```
Not Installed

↓

Installing

↓

Installed

↓

Starting

↓

Running

↓

Stopping

↓

Stopped

↓

Starting

↓

Running

↓

Updating

↓

Running

↓

Deprecated

↓

Removing

↓

Removed
```

Estados de erro podem ocorrer em qualquer transição.

---

# 5. Estados

## 5.1 Not Installed

Estado inicial.

A Capability ainda não existe no Runtime.

Características:

- não registrada;
- sem recursos alocados;
- invisível para a plataforma.

---

## 5.2 Installing

O Runtime iniciou o processo de instalação.

Operações típicas:

- download;
- validação do manifesto;
- validação de assinatura;
- verificação de compatibilidade;
- preparação do ambiente.

Ainda não executa código da Capability.

---

## 5.3 Installed

A instalação foi concluída.

A Capability:

- está registrada;
- possui configuração válida;
- ainda não iniciou processamento.

---

## 5.4 Starting

O Runtime está inicializando a Capability.

Operações comuns:

- carregar configuração;
- inicializar SDK;
- conectar ao NATS;
- registrar métricas;
- iniciar tracing;
- iniciar Health Check;
- abrir conexões externas.

Caso alguma etapa falhe:

```
Starting

↓

Failed
```

---

## 5.5 Running

Estado operacional.

A Capability está apta para:

- consumir eventos;
- publicar eventos;
- expor métricas;
- responder Health Checks;
- executar inferência;
- integrar equipamentos.

Todo processamento acontece neste estado.

---

## 5.6 Stopping

Estado de encerramento controlado.

A Capability recebe uma solicitação de parada.

Deve:

- finalizar processamento pendente;
- liberar memória;
- fechar conexões;
- persistir estado necessário;
- finalizar threads.

Não deve aceitar novos trabalhos.

---

## 5.7 Stopped

Capability parada.

Pode permanecer instalada.

Não executa nenhuma operação.

Pode retornar para:

```
Starting
```

---

## 5.8 Updating

O Runtime iniciou uma atualização.

Fluxo:

```
Running

↓

Updating

↓

Validation

↓

Restart

↓

Running
```

Caso falhe:

```
Updating

↓

Rollback

↓

Running (versão anterior)
```

O Runtime é responsável pelo rollback.

---

## 5.9 Deprecated

A Capability continua funcional, porém foi substituída.

Novas instalações não devem utilizá-la.

O Runtime pode emitir avisos.

---

## 5.10 Removing

Estado temporário.

Operações:

- remover registro;
- remover containers;
- remover configurações locais;
- liberar recursos.

---

## 5.11 Removed

Estado final.

A Capability não existe mais na plataforma.

---

## 5.12 Failed

Estado de falha.

Pode ocorrer durante:

- instalação;
- inicialização;
- execução;
- atualização;
- encerramento.

O Runtime deve registrar:

- erro;
- stack trace;
- versão;
- timestamp;
- tentativa.

O Runtime poderá reiniciar automaticamente conforme política configurada.

---

# 6. Transições Permitidas

| Origem | Destino |
|----------|----------|
| Not Installed | Installing |
| Installing | Installed |
| Installing | Failed |
| Installed | Starting |
| Starting | Running |
| Starting | Failed |
| Running | Updating |
| Running | Stopping |
| Running | Failed |
| Updating | Running |
| Updating | Failed |
| Failed | Starting |
| Stopped | Starting |
| Stopped | Removing |
| Deprecated | Removing |
| Removing | Removed |

Transições fora desta tabela são inválidas.

---

# 7. Eventos de Lifecycle

Toda mudança de estado deve gerar eventos.

Exemplo:

```
oip.capability.installing

oip.capability.installed

oip.capability.starting

oip.capability.running

oip.capability.failed

oip.capability.stopping

oip.capability.stopped

oip.capability.updating

oip.capability.updated

oip.capability.deprecated

oip.capability.removing

oip.capability.removed
```

Todos seguem o padrão definido pelo **OES**.

---

# 8. Responsabilidades do Runtime

O Runtime é responsável por:

- instalar;
- iniciar;
- parar;
- atualizar;
- reiniciar;
- monitorar;
- remover;
- registrar métricas;
- emitir eventos;
- controlar versões.

A Capability nunca altera seu próprio estado diretamente.

---

# 9. Responsabilidades da Capability

A Capability deve apenas responder às solicitações do Runtime.

Ela deve implementar:

- Initialize()
- Start()
- Stop()
- Health()
- Metadata()

Todo o restante é responsabilidade do Runtime.

---

# 10. Health Checks

Toda Capability deve implementar Health Checks.

Estados possíveis:

```
Healthy

Degraded

Unhealthy
```

Exemplo:

```
Healthy
├── Runtime OK
├── NATS Connected
├── Model Loaded
└── Dependencies OK
```

---

# 11. Readiness

Antes de entrar em **Running**, a Capability deve informar que está pronta.

Checklist:

- SDK iniciado
- configuração carregada
- modelo carregado
- NATS conectado
- métricas registradas
- tracing iniciado

Somente após isso:

```
Starting

↓

Running
```

---

# 12. Graceful Shutdown

O encerramento deve ocorrer sem perda de dados.

Fluxo:

```
Recebe Stop

↓

Para consumir eventos

↓

Finaliza processamento

↓

Publica eventos pendentes

↓

Fecha conexões

↓

Libera memória

↓

Stopped
```

---

# 13. Atualizações

Atualizações devem preservar disponibilidade sempre que possível.

Estratégias suportadas:

- Rolling Update
- Blue-Green
- Canary

O Runtime escolhe a estratégia.

---

# 14. Recuperação Automática

Em caso de falha:

```
Running

↓

Failed

↓

Restart Policy

↓

Starting

↓

Running
```

Políticas possíveis:

- Never
- Always
- OnFailure
- Exponential Backoff

---

# 15. Observabilidade

Todas as transições devem produzir:

- Logs estruturados;
- Métricas;
- Traces OpenTelemetry;
- Eventos OES.

Exemplo:

```
Capability Started

↓

Log

↓

Metric

↓

Trace

↓

Event
```

---

# 16. Auditoria

Toda alteração de estado deve registrar:

- timestamp;
- capability_id;
- versão;
- operador (quando aplicável);
- motivo;
- resultado;
- duração da operação.

---

# 17. Compatibilidade

Mudanças futuras no Lifecycle devem preservar compatibilidade retroativa sempre que possível.

Estados existentes não devem ser removidos sem um processo formal de depreciação.

---

# 18. Referências

- CSpec — OIP Capability Specification
- CSS — OIP Capability SDK Specification
- OES — OIP Event Specification
- ADR-0001 — Architecture Principles
- ADR-0002 — Event-Driven Architecture
- ADR-0003 — Plugin First Architecture

---

# Status

**Draft**

Esta especificação define o ciclo de vida oficial de todas as Capabilities da OIP e estabelece o contrato operacional entre o **Capability Runtime** e as implementações desenvolvidas por terceiros, garantindo previsibilidade, segurança operacional e interoperabilidade em todo o ecossistema.