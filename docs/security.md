# Security Architecture (Arquitetura de Segurança da OIP)

**Status:** Aprovado  
**Categoria:** Segurança, Governança & Conformidade  
**Versão:** 1.0  

---

## 1. Visão Geral
A **Operational Intelligence Platform (OIP)** adota uma postura de **Zero Trust** (Confiança Zero), assumindo que a rede interna pode estar comprometida e que todo acesso, requisição e comunicação entre serviços deve ser autenticado, autorizado, criptografado e rigorosamente auditado. A segurança está integrada em todas as camadas da arquitetura, desde o perímetro até o armazenamento de dados em repouso[cite: 1].

---

## 2. Modelo Zero Trust e Comunicação Segura
- **mTLS Obrigatório:** Toda a comunicação entre microsserviços no barramento de rede (NATS / gRPC) é protegida por Mutual TLS (mTLS 1.3), garantindo a autenticidade e confidencialidade do tráfego em trânsito por meio de certificados digitais gerenciados internamente[cite: 1].
- **Criptografia em Trânsito:** Acesso externo e chamadas de API utilizam obrigatoriamente TLS 1.3[cite: 1].
- **API Gateway:** Ponto único de entrada na plataforma com mecanismos de *throttling*, rate limiting, proteção contra DDoS e validação rigorosa de tokens de autenticação[cite: 1].

---

## 3. Gestão de Identidade e Acesso (RBAC / ABAC)
- **Autenticação Baseada em PASETO:** A plataforma adota *PASETO (Platform-Agnostic Security Tokens)* nas versões v2/v4 para gerenciar sessões e autenticação de operadores, eliminando vulnerabilidades comuns de tokens JWT.
- **RBAC (Role-Based Access Control):** Controle de acesso baseado em papéis operacionais (Operador, Supervisor, Investigador, Administrador)[cite: 1].
- **ABAC (Attribute-Based Access Control):** Controle granular baseado em atributos de contexto, permitindo restringir o acesso a recursos específicos (como câmeras de um determinado setor) com base em horários de trabalho, turnos e localização[cite: 1].

---

## 4. Criptografia e Proteção de Dados (Data-at-Rest)
- **Primitivas Criptográficas Inspiradas na Libsodium:** Uso de bibliotecas otimizadas em Go (`golang.org/x/crypto`) para operações sensíveis.
- **Argon2id:** Algoritmo padrão e resistente a ataques de força bruta baseados em GPU para armazenamento e verificação de senhas de operadores.
- **XChaCha20-Poly1305:** Utilizado para criptografar credenciais sensíveis (como acessos RTSP de câmeras, chaves de API e tokens) antes da persistência no banco de dados transacional (PostgreSQL).
- **Assinatura Digital (Ed25519):** Utilizada para assinar evidências e fragmentos de vídeo armazenados no MinIO, garantindo integridade e validade jurídica por meio de uma cadeia de custódia imutável[cite: 1].

---

## 5. Gestão de Segredos e Conformidade (LGPD)
- **HashiCorp Vault:** Armazenamento seguro, injeção dinâmica e rotação automatizada de segredos, credenciais e chaves criptográficas mestras (*Master Keys / KEK / DEK*).
- **Trilha de Auditoria e Access Logs:** Registro imutável de todas as ações executadas na plataforma (`audit_logs` e `access_logs`), rastreando quem visualizou vídeos, exportou evidências ou alterou configurações do sistema[cite: 1].
- **Conformidade com a LGPD:** Mecanismos automáticos de anonimização, mascaramento de dados sensíveis em dashboards e políticas rígidas de retenção e expurgo de dados operacionais e forenses[cite: 1].