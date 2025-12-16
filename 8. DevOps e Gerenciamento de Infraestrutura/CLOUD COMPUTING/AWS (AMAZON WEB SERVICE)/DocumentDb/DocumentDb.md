# **1. Amazon DocumentDB – Fundamentos**

## **1. Introdução ao Amazon DocumentDB**

### 1.1. O que é o Amazon DocumentDB

### 1.2. Origem e propósito do serviço

### 1.3. Compatibilidade com MongoDB (API wire protocol)

### 1.4. Casos de uso típicos

### 1.5. Quando NÃO usar Amazon DocumentDB

---

## **2. Conceitos Fundamentais**

### 2.1. Cluster

### 2.2. Instâncias

- Instância primária
    
- Réplicas
    

### 2.3. Armazenamento distribuído

### 2.4. Endpoints

- Cluster endpoint
    
- Reader endpoint
    

### 2.5. Alta disponibilidade por design

---

## **3. Arquitetura do Amazon DocumentDB**

### 3.1. Arquitetura gerenciada pela AWS

### 3.2. Separação entre compute e storage

### 3.3. Replicação automática

### 3.4. Failover automático

### 3.5. Escalabilidade de leitura

---

## **4. Modelo de Dados**

### 4.1. Banco orientado a documentos

### 4.2. Estrutura BSON (compatível com MongoDB)

### 4.3. Coleções e documentos

### 4.4. Identificador `_id`

### 4.5. Limitações do schema flexível no DocumentDB

---

## **5. Compatibilidade com MongoDB**

### 5.1. Versões compatíveis da API MongoDB

### 5.2. Operações suportadas

### 5.3. Operações parcialmente suportadas

### 5.4. Operações não suportadas

### 5.5. Impacto da compatibilidade no design

---

## **6. Operações CRUD**

### 6.1. Inserção de documentos

### 6.2. Leitura de documentos

### 6.3. Atualização de documentos

### 6.4. Remoção de documentos

### 6.5. Atomicidade em nível de documento

---

## **7. Consultas e Operadores**

### 7.1. Operadores de comparação

### 7.2. Operadores lógicos

### 7.3. Consultas em documentos aninhados

### 7.4. Consultas em arrays

### 7.5. Projeções

---

## **8. Índices**

### 8.1. Conceito de indexação no DocumentDB

### 8.2. Tipos de índices suportados

- Índice simples
    
- Índice composto
    

### 8.3. Criação e manutenção de índices

### 8.4. Diferenças de indexação em relação ao MongoDB

### 8.5. Impacto dos índices em custo e performance

---

## **9. Aggregation Framework**

### 9.1. Suporte a agregações

### 9.2. Estágios suportados

### 9.3. Limitações do Aggregation Pipeline

### 9.4. Performance de agregações

---

## **10. Transações**

### 10.1. Suporte a transações no DocumentDB

### 10.2. Atomicidade de operações

### 10.3. Limitações em transações multi-documento

---

## **11. Consistência e Confiabilidade**

### 11.1. Modelo de consistência

### 11.2. Write durability

### 11.3. Replicação síncrona

### 11.4. Leitura consistente

---

## **12. Alta Disponibilidade e Escalabilidade**

### 12.1. Escalabilidade de leitura

### 12.2. Escalabilidade de escrita

### 12.3. Limites de instâncias

### 12.4. Estratégias de dimensionamento

---

## **13. Segurança**

### 13.1. Autenticação

- Usuários e senhas
    
- Integração com IAM (controle de acesso indireto)
    

### 13.2. Autorização

- Controle por usuários e roles
    

### 13.3. Criptografia

- Em repouso (KMS)
    
- Em trânsito (TLS)
    

### 13.4. Isolamento de rede (VPC)

---

## **14. Backup e Recuperação**

### 14.1. Backups automáticos

### 14.2. Retenção de backups

### 14.3. Point-in-Time Recovery (PITR)

### 14.4. Restauração de clusters

---

## **15. Monitoramento e Observabilidade**

### 15.1. Métricas do CloudWatch

### 15.2. Logs e eventos

### 15.3. Alertas e alarmes

### 15.4. Diagnóstico de performance

---

## **16. Performance e Otimização**

### 16.1. Boas práticas de modelagem

### 16.2. Uso eficiente de índices

### 16.3. Dimensionamento de instâncias

### 16.4. Identificação de gargalos

---

## **17. Custos e Precificação**

### 17.1. Custo por instância

### 17.2. Custo de armazenamento

### 17.3. Custo de I/O

### 17.4. Estratégias de otimização de custos

---

## **18. Limitações Conhecidas**

### 18.1. Limitações de compatibilidade MongoDB

### 18.2. Limitações de performance

### 18.3. Limitações funcionais

### 18.4. Impacto arquitetural das limitações

---

## **19. Migração para Amazon DocumentDB**

### 19.1. Migração a partir de MongoDB

### 19.2. Avaliação de compatibilidade

### 19.3. Estratégias de migração

### 19.4. Testes pós-migração

---

## **20. Casos de Uso Arquiteturais**

### 20.1. Microsserviços

### 20.2. Sistemas distribuídos

### 20.3. Aplicações de leitura intensiva

### 20.4. Quando preferir MongoDB nativo

---

## **21. Boas Práticas**

### 21.1. Padrões de nomenclatura

### 21.2. Design orientado a leitura

### 21.3. Planejamento de escalabilidade

### 21.4. Anti-patterns comuns

---

## **22. Integração com Aplicações (conceitual)**

### 22.1. Drivers compatíveis com MongoDB

### 22.2. Pool de conexões

### 22.3. Separação de responsabilidades

> _Integrações com frameworks (Spring Data, Mongoose, .NET, etc.) ficam fora deste escopo._

---
