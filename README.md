# aws-solutions-architect-saa-c03

# AWS Certified Solutions Architect – Associate (SAA-C03)

## Guia de Estudo Completo

> Domínios, serviços, insights-chave e pegadinhas da prova

---

## Índice

1. [Visão Geral da Certificação](#1-visão-geral-da-certificação)
2. [Infraestrutura Global AWS](#2-infraestrutura-global-aws)
3. [Domínio 1 — Design Secure Architectures (30%)](#3-domínio-1--design-secure-architectures-30)
4. [Domínio 2 — Design Resilient Architectures (26%)](#4-domínio-2--design-resilient-architectures-26)
5. [Domínio 3 — Design High-Performing Architectures (24%)](#5-domínio-3--design-high-performing-architectures-24)
6. [Domínio 4 — Design Cost-Optimized Architectures (20%)](#6-domínio-4--design-cost-optimized-architectures-20)
7. [Insights-Chave Transversais](#7-insights-chave-transversais-alta-frequência-na-prova)
8. [Apêndice — Serviços In-Scope da Prova](#8-apêndice--serviços-in-scope-da-prova)
9. [Estratégia de Estudo Recomendada](#9-estratégia-de-estudo-recomendada)

---

## 1. Visão Geral da Certificação

A **AWS Certified Solutions Architect – Associate (SAA-C03)** valida a capacidade de projetar soluções na AWS aplicando o Well-Architected Framework. É uma das certificações mais valorizadas do mercado e o foco do exame está em escolher a melhor combinação de serviços para atender a requisitos de segurança, resiliência, performance e custo.

### 1.1 Estrutura da Prova

- **Questões:** 65 no total (50 contam para a nota, 15 são experimentais e não pontuam)
- **Tipos de questão:** múltipla escolha (1 correta) e múltiplas respostas (2+ corretas)
- **Duração:** 130 minutos
- **Pontuação:** escala de 100 a 1.000; mínimo para aprovação é **720**
- **Custo:** USD 150
- **Modalidade:** Pearson VUE (presencial) ou online proctored
- **Validade:** 3 anos

### 1.2 Domínios e Pesos

| Domínio | Foco | Peso |
|---------|------|------|
| 1. Design Secure Architectures | IAM, criptografia, segurança de rede, conformidade | **30%** |
| 2. Design Resilient Architectures | Alta disponibilidade, tolerância a falhas, desacoplamento | **26%** |
| 3. Design High-Performing Architectures | Performance de compute, storage, banco e rede | **24%** |
| 4. Design Cost-Optimized Architectures | Otimização de custos em todas as camadas | **20%** |

> 💡 **INSIGHT-CHAVE:** O Domínio 1 (Segurança) é o mais pesado da prova. Dominar IAM, KMS, VPC security (SGs, NACLs) e criptografia at-rest/in-transit pode garantir muitos pontos. Não subestime o peso de Segurança.

### 1.3 Pilares do AWS Well-Architected Framework

Toda decisão de arquitetura na prova deve ser justificada por estes pilares:

- **Operational Excellence:** operar e monitorar sistemas para entregar valor.
- **Security:** proteger informações, sistemas e ativos.
- **Reliability:** recuperar de falhas e atender demanda.
- **Performance Efficiency:** usar recursos de forma eficiente.
- **Cost Optimization:** evitar custos desnecessários.
- **Sustainability:** minimizar impacto ambiental (pilar mais novo).

> 💡 **INSIGHT-CHAVE:** Quando uma questão tem múltiplas respostas tecnicamente corretas, escolha a que MELHOR alinha com o pilar destacado no enunciado. Palavras como *'most cost-effective'*, *'highest availability'*, *'minimum operational overhead'* são pistas diretas do pilar.

---

## 2. Infraestrutura Global AWS

Conceito fundamental que aparece em quase todas as questões. Você precisa entender como Regiões, AZs e Edge Locations se relacionam.

- **Region:** área geográfica com múltiplas AZs isoladas (ex.: `us-east-1`, `sa-east-1`). Cada serviço pode ou não estar disponível em uma região.
- **Availability Zone (AZ):** um ou mais data centers fisicamente separados em uma Região, com energia, refrigeração e rede independentes. Conectados por links de baixa latência.
- **Edge Location:** ponto de presença para CloudFront, Route 53, Global Accelerator e WAF. Há mais Edge Locations que Regiões.
- **Local Zones:** extensão de Região mais próxima de grandes centros urbanos para latência ultrabaixa.
- **Wavelength Zones:** AWS dentro de redes 5G de operadoras para aplicações móveis com latência ultrabaixa.
- **Outposts:** rack AWS dentro do data center do cliente (modelo híbrido).

### 2.1 Escopo dos Serviços

- **Globais:** IAM, Route 53, CloudFront, WAF, Shield, Organizations.
- **Regionais:** S3 (bucket é regional, namespace é global), DynamoDB, Lambda, SQS, SNS, RDS, KMS.
- **Por AZ:** EC2, EBS, Subnets. Um EBS volume só anexa em EC2 da mesma AZ.

> ⚠️ **PEGADINHA DA PROVA:** S3 é **REGIONAL**, mas o nome do bucket é **GLOBAL** (único no mundo). EBS volumes ficam em uma única AZ — para usar em outra AZ, você precisa criar snapshot (que vai para o S3, regional) e restaurar na AZ destino.

---

## 3. Domínio 1 — Design Secure Architectures (30%)

Maior peso da prova. Cobre IAM, criptografia, proteção de rede, governança multi-conta e proteção de dados.

### 3.1 Modelo de Responsabilidade Compartilhada

- **AWS é responsável pela segurança DA nuvem:** hardware, software global, instalações, infraestrutura.
- **Cliente é responsável pela segurança NA nuvem:** configuração de IAM, dados, criptografia, patching de SO em EC2, regras de SG, etc.
- **Modelos:** IaaS (EC2) — cliente faz mais; PaaS (RDS, Lambda) — AWS faz mais; SaaS (S3 console) — AWS faz quase tudo, cliente cuida de dados e acesso.

### 3.2 IAM (Identity and Access Management)

#### Componentes Principais

- **Users:** identidades humanas ou de aplicação com credenciais de longo prazo.
- **Groups:** coleção de users que recebem políticas em conjunto. **NÃO** podem conter outros groups.
- **Roles:** identidades temporárias assumíveis por serviços, usuários ou contas externas. **SEM** credenciais de longo prazo.
- **Policies:** documentos JSON que definem permissões.

#### Tipos de Políticas

- **Identity-based** (managed/inline): anexadas a users, groups, roles.
- **Resource-based:** anexadas a recursos (S3 bucket policy, KMS key policy, SQS, Lambda).
- **Permissions Boundary:** limite máximo de permissões para um IAM principal.
- **Service Control Policies (SCPs):** no AWS Organizations — limite máximo para uma OU/conta inteira. Não concede permissão, apenas restringe.
- **Session Policies:** passadas em runtime ao assumir uma role via STS.

#### Lógica de Avaliação de Política

A AWS avalia em ordem:

1. **Explicit DENY** sempre vence
2. Organizations SCP
3. Resource-based
4. Identity-based
5. Permissions Boundary
6. Session Policy

Padrão é **DENY implícito**.

> 💡 **INSIGHT-CHAVE:** Memorize: **'Explicit Deny > Explicit Allow > Implicit Deny'**. Se a SCP do Organizations nega, NEM o root da conta filha pode executar.

#### Boas Práticas IAM

- **NUNCA** use o root da conta no dia a dia — apenas para tarefas que exigem (alterar suporte, fechar conta).
- Habilite MFA no root e em todos os usuários privilegiados.
- Use **Roles** ao invés de Access Keys em EC2/Lambda/ECS.
- Aplique princípio do menor privilégio.
- Rotacione credenciais regularmente.
- Use **IAM Identity Center** (antigo SSO) para acesso humano federado.
- Para acesso entre contas, use Roles e STS AssumeRole, **NÃO** compartilhe credenciais.

#### AWS STS (Security Token Service)

- **AssumeRole:** para acesso entre contas ou role-switching dentro da conta.
- **AssumeRoleWithSAML:** federação com SAML 2.0 (Active Directory corporativo).
- **AssumeRoleWithWebIdentity:** federação com Google, Facebook, Cognito (geralmente substituído por Cognito Identity Pools).
- **GetSessionToken:** para usuários com MFA temporário.

> ⚠️ **PEGADINHA DA PROVA:** EC2 Instance Profile é o 'envelope' que entrega a IAM Role para uma instância EC2. Você anexa a Role à instância, mas internamente é via Instance Profile. Em CloudFormation isso aparece explicitamente.

### 3.3 AWS Organizations e Governança Multi-Conta

- **Organizations:** agrupa múltiplas contas AWS sob uma master/management account. Suporta consolidated billing.
- **Organizational Units (OUs):** agrupamentos lógicos de contas (ex.: Prod, Dev, Sandbox).
- **Service Control Policies (SCPs):** limites máximos de permissões por OU/conta. Aplicam-se a IAM users e roles, mas **NÃO** ao service-linked roles.
- **AWS Control Tower:** configuração turnkey de Landing Zone com guardrails pré-definidos. Usa Organizations + IAM Identity Center + Config + CloudTrail.
- **AWS Resource Access Manager (RAM):** compartilha recursos entre contas (subnets, Transit Gateway, License Manager).

> 💡 **INSIGHT-CHAVE:** Para governança em larga escala: **Organizations + Control Tower + SCPs + IAM Identity Center** é o combo padrão recomendado pela AWS.

### 3.4 Criptografia e KMS

#### AWS KMS (Key Management Service)

- **Customer Master Keys (CMKs / KMS Keys):** simétricas (AES-256) ou assimétricas (RSA, ECC).
- **Tipos:** AWS-managed (gratuitas, controle limitado), Customer-managed (você controla rotação, política, auditoria), AWS-owned (totalmente AWS).
- **Rotação automática:** 1 ano para CMKs simétricas customer-managed (opt-in).
- **Key Policy:** obrigatória; controla quem pode usar/administrar a chave. Sem key policy, ninguém usa, NEM o root.
- **Envelope Encryption:** KMS gera Data Keys (DEK); a DEK criptografa os dados, e a CMK criptografa a DEK. Padrão para dados grandes.
- **Limites:** key policy ≤ 32KB; chamadas KMS têm limite de TPS — em alta escala, use Data Key caching.
- **Multi-Region Keys:** permitem criptografar em uma Região e descriptografar em outra sem rede entre Regiões.

#### CloudHSM

HSM dedicado com **FIPS 140-2 Level 3**. Use quando há requisito regulatório de tenant único ou controle exclusivo de chaves. Mais caro e complexo que KMS.

#### AWS Certificate Manager (ACM)

- **Funcionalidade:** provisiona, gerencia e renova certificados TLS gratuitamente.
- **Integração:** ELB, CloudFront, API Gateway, App Runner.
- **Restrição:** certificados públicos do ACM **NÃO** podem ser exportados nem instalados em EC2 diretamente.
- **Para CloudFront:** o certificado deve estar em `us-east-1` (N. Virginia).

#### AWS Secrets Manager vs Systems Manager Parameter Store

| Característica | Secrets Manager | SSM Parameter Store |
|---|---|---|
| Custo | Pago por segredo + chamadas | Standard gratuito; Advanced pago |
| Rotação automática | Sim, nativa (RDS, Redshift, DocDB) | Não nativa (precisa Lambda) |
| Tamanho | Até 64KB | Standard 4KB / Advanced 8KB |
| Caso de uso | Senhas DB, API keys com rotação | Configurações, parâmetros, segredos sem rotação |
| Cross-account | Sim | Sim (Advanced) |

> ⚠️ **PEGADINHA DA PROVA:** Quando a questão menciona 'rotação automática de senhas RDS' — resposta é **Secrets Manager**, não Parameter Store. Se for apenas 'guardar uma string de configuração', Parameter Store é mais barato.

### 3.5 Segurança de Rede

#### Security Groups (SGs)

- **Stateful:** tráfego de retorno é automaticamente permitido.
- Permitem **APENAS** regras allow (não há deny).
- Avaliam todas as regras antes de decidir.
- Aplicam-se em **ENI** (interface de rede), não em subnet.
- Podem referenciar outros SGs (princípio do menor privilégio entre tiers).

#### Network ACLs (NACLs)

- **Stateless:** tráfego de retorno precisa ser explicitamente permitido.
- Permitem regras allow **E** deny.
- Avaliadas por ordem numérica (menor número primeiro).
- Aplicam-se a nível de **SUBNET**.
- Default NACL permite tudo; NACL customizada nega tudo por padrão.

> 💡 **INSIGHT-CHAVE:** Diferença CRUCIAL: **SG é stateful e aplicado em ENI. NACL é stateless e aplicado em subnet.** Quando precisar bloquear um IP malicioso específico, use NACL (SG não tem deny).

#### AWS WAF (Web Application Firewall)

- **Funcionalidade:** protege na camada 7 (HTTP/HTTPS) contra SQL injection, XSS, bots.
- **Integra com:** CloudFront, ALB, API Gateway, AppSync, Cognito User Pools.
- **Não integra com:** Network Load Balancer (NLB é camada 4).
- **Regras:** managed rule groups (AWS, Marketplace), rate-based, geo-match, IP set.

#### AWS Shield

- **Shield Standard:** gratuito, protege contra DDoS L3/L4 (SYN flood, reflection).
- **Shield Advanced:** USD 3.000/mês por organização, proteção L3/L4/L7, DDoS Response Team, custos de scaling absorvidos durante ataques, dashboards detalhados.

#### AWS Network Firewall

Firewall stateful gerenciado a nível de VPC, com filtragem em camada 3-7 e detecção de intrusão (IDS/IPS). Mais robusto que NACL/SG, ideal para inspeção centralizada de tráfego em uma arquitetura com Transit Gateway.

#### AWS Firewall Manager

Console central para gerenciar WAF, Shield Advanced, SGs, Network Firewall e Route 53 Resolver DNS Firewall através de toda a Organization.

### 3.6 Detecção e Auditoria

| Serviço | Descrição / Caso de Uso |
|---------|--------------------------|
| **AWS CloudTrail** | Auditoria de API calls (quem fez o quê, quando, de onde). Habilitado por padrão por 90 dias. Para retenção longa, crie Trail entregando para S3. |
| **AWS Config** | Avalia, audita e registra configurações de recursos. Detecta drift e envia alertas. Usado para compliance (PCI, HIPAA). |
| **Amazon GuardDuty** | Threat detection contínuo usando ML, analisa CloudTrail, VPC Flow Logs, DNS logs, EKS logs. |
| **Amazon Inspector** | Avaliação de vulnerabilidades em EC2, ECR (containers) e Lambda. CVEs e network reachability. |
| **Amazon Macie** | ML para descobrir e proteger PII/PHI no S3 (CPF, cartões de crédito, etc.). |
| **AWS Security Hub** | Dashboard centralizado que agrega findings do GuardDuty, Inspector, Macie, IAM Access Analyzer, etc. Suporta CIS, PCI-DSS frameworks. |
| **Amazon Detective** | Investigação automatizada de incidentes de segurança usando grafos. |
| **AWS Audit Manager** | Coleta evidências para auditorias (SOC, PCI, GDPR). |
| **AWS Artifact** | Repositório de relatórios de compliance da AWS (SOC, ISO, PCI). |

> 💡 **INSIGHT-CHAVE:** Mnemônico para detecção: **GuardDuty = ameaças** (threats); **Inspector = vulnerabilidades** (CVEs); **Macie = dados sensíveis em S3**; **Config = compliance de configuração**; **Security Hub = dashboard único**.

---

## 4. Domínio 2 — Design Resilient Architectures (26%)

### 4.1 Conceitos Fundamentais

- **High Availability (HA):** sistema continua operando apesar de falhas. Geralmente Multi-AZ.
- **Fault Tolerance:** sistema continua operando SEM perda de funcionalidade durante falhas. Mais caro que HA.
- **Disaster Recovery (DR):** recuperação após desastre, geralmente Multi-Region.
- **Loose Coupling:** componentes independentes que se comunicam por filas/eventos — falha em um não derruba os demais.
- **Stateless:** aplicação não armazena sessão localmente — pode escalar horizontalmente livremente.

### 4.2 Estratégias de Disaster Recovery

Decoradas pela ordem crescente de custo, complexidade e RTO/RPO menores:

| Estratégia | RTO/RPO | Descrição |
|------------|---------|-----------|
| **Backup & Restore** | Horas / Horas | Mais barata. Backups periódicos restaurados após desastre. |
| **Pilot Light** | 10s minutos / Minutos | Versão mínima sempre rodando (DB replicado, app desligada). Liga em DR. |
| **Warm Standby** | Minutos / Segundos | Versão reduzida e funcional rodando. Escala em DR. |
| **Multi-Site Active-Active** | Segundos / ~0 | Versão completa rodando em 2+ regiões. Mais cara, menor RTO/RPO. |

- **RTO (Recovery Time Objective):** tempo MÁXIMO aceitável para restaurar serviço.
- **RPO (Recovery Point Objective):** perda MÁXIMA aceitável de dados (em tempo).

> 💡 **INSIGHT-CHAVE:** Quanto MENOR o RTO/RPO, MAIOR o custo. Na prova: 'critical app, 99.99% uptime' → Multi-AZ + Multi-Region active-active. 'Compliance backup at low cost' → S3 Glacier ou backup & restore.

### 4.3 Compute para Resiliência

#### Auto Scaling Group (ASG)

- Lança/encerra instâncias EC2 com base em políticas, em múltiplas AZs.
- **Scaling policies:** Target Tracking (mais simples), Step Scaling, Simple Scaling, Scheduled, Predictive.
- **Cooldown:** período de espera após ação para evitar oscilações.
- **Health checks:** EC2 (status check) ou ELB (mais robusto, faz HTTP).
- **Lifecycle Hooks:** permite executar ação custom em launch/terminate (ex.: instalar software, drenar conexões).
- **Termination policies:** padrão é manter equilíbrio entre AZs e remover a instância mais velha.

> ⚠️ **PEGADINHA DA PROVA:** ASG **NÃO** move instâncias entre AZs — ele **LANÇA** novas em outras AZs e **ENCERRA** as antigas. Para isso, garanta que sua instância seja stateless e use AMI atualizada.

#### Elastic Load Balancing (ELB)

| Tipo | Camada | Caso de Uso |
|------|--------|-------------|
| **Application LB (ALB)** | Layer 7 (HTTP/HTTPS) | Routing por path, host, headers; WebSockets; HTTP/2; integra WAF; targets podem ser EC2, IP, Lambda, container. |
| **Network LB (NLB)** | Layer 4 (TCP/UDP/TLS) | Performance extrema (milhões de req/s); IP estático por AZ; preserva IP do cliente; conexões long-lived. |
| **Gateway LB (GWLB)** | Layer 3+4 (IP) | Insere appliances de terceiros (firewall, IDS) no caminho do tráfego. |
| **Classic LB (CLB)** | Layer 4 e 7 (legado) | Não usar em novos projetos. |

> 💡 **INSIGHT-CHAVE:** Decisão rápida: **HTTP routing avançado → ALB. Performance extrema/IP estático/UDP → NLB. Inserção transparente de appliance de segurança → GWLB.**

#### Cross-Zone Load Balancing

- **ALB:** habilitado por padrão, sem custo extra.
- **NLB:** desabilitado por padrão, com custo de transferência de dados entre AZs.

### 4.4 Desacoplamento e Mensageria

#### Amazon SQS (Simple Queue Service)

- **Standard:** throughput ilimitado, ordem best-effort, entrega at-least-once.
- **FIFO:** ordem garantida, exactly-once, até 300 msg/s (3.000 com batching). Nome termina em `.fifo`.
- **Visibility Timeout:** tempo que a mensagem fica invisível após ser lida (padrão 30s, máx 12h).
- **Long Polling:** WaitTimeSeconds até 20s para reduzir chamadas vazias.
- **Dead Letter Queue (DLQ):** captura mensagens não processáveis após N tentativas.
- **Retenção:** 1 min a 14 dias (padrão 4 dias).
- **Tamanho de mensagem:** até 256KB; para maior, use Extended Client Library com S3.

#### Amazon SNS (Simple Notification Service)

- Pub/sub — um publisher envia para tópico, múltiplos subscribers recebem (fan-out).
- **Subscribers:** SQS, Lambda, HTTP/S, email, SMS, mobile push, Kinesis Data Firehose.
- **FIFO topics:** garante ordem e dedup, integra apenas com SQS FIFO.
- **Message Filtering:** subscribers filtram com policies para receber só o relevante.

> 💡 **INSIGHT-CHAVE:** Padrão fan-out: **SNS → múltiplos SQS**. Permite que cada serviço consuma no seu próprio ritmo, com retentativas independentes.

#### Amazon EventBridge

Sucessor do CloudWatch Events. Bus de eventos serverless para SaaS partners e suas aplicações. Suporta schema registry, archive/replay, custom buses, e cross-account event bus.

#### Amazon MQ

Broker gerenciado de Apache ActiveMQ e RabbitMQ. Use quando você está **MIGRANDO** uma aplicação on-premises que já usa esses brokers (preserva protocolos como AMQP, MQTT, STOMP). Para apps novas, prefira SQS/SNS.

#### AWS Step Functions

Orquestração visual de workflows usando state machines. Coordena Lambda, ECS, SNS, DynamoDB, etc. Dois tipos:

- **Standard:** execuções até 1 ano, exactly-once, ideal para workflows longos com auditoria.
- **Express:** execuções até 5 min, alto throughput, at-least-once, ideal para event-driven de alto volume.

### 4.5 Containers e Serverless

#### Amazon ECS (Elastic Container Service)

- **EC2 Launch Type:** você gerencia as instâncias EC2 do cluster.
- **Fargate Launch Type:** serverless — AWS gerencia a infraestrutura, você paga por vCPU/memória.
- **Task Definition:** blueprint do container (imagem, CPU, memória, IAM role, networking).
- **Service:** mantém N tasks rodando, integra com ALB.

#### Amazon EKS (Elastic Kubernetes Service)

Kubernetes gerenciado. Use quando seu time já tem expertise em K8s ou precisa de portabilidade multi-cloud. Suporta Fargate. Mais caro e complexo que ECS.

#### AWS Lambda

- **Runtime:** Node, Python, Java, Go, Ruby, .NET, custom (containers até 10GB).
- **Limites:** 15 min de execução, 10GB memória, /tmp 10GB, 1.000 execuções concorrentes (soft limit).
- **Triggers:** API Gateway, S3, DynamoDB Streams, Kinesis, SQS, SNS, EventBridge, Cognito, CloudFront (Lambda@Edge), ALB.
- **Provisioned Concurrency:** elimina cold start, mantém N execuções aquecidas.
- **Reserved Concurrency:** limita o máximo de concorrência por função.
- **VPC:** Lambda pode rodar em VPC para acessar RDS/EC2 privados — use com Lambda Hyperplane ENIs (sem cold start adicional).

> ⚠️ **PEGADINHA DA PROVA:** Para SQS triggando Lambda, configure o Visibility Timeout do SQS para pelo menos **6× o timeout da Lambda**. Reserved Concurrency da Lambda também limita quantas mensagens SQS podem ser processadas simultaneamente.

### 4.6 Bancos de Dados Resilientes

#### Amazon RDS Multi-AZ

- Cria uma standby replica **SÍNCRONA** em outra AZ.
- Failover automático em caso de falha (60-120s).
- Standby **NÃO** é acessível para reads (use Read Replica para isso).
- Endpoint DNS é o mesmo — o cliente não precisa mudar.

#### Amazon RDS Read Replicas

- Replicação **ASSÍNCRONA**, até 15 réplicas.
- Pode estar em mesma AZ, outra AZ, ou outra Região.
- Pode ser promovido a standalone DB.
- Endpoint próprio para reads.

#### Amazon Aurora

- Compatível com MySQL e PostgreSQL, 5x mais rápido que MySQL, 3x mais rápido que PostgreSQL.
- Storage compartilhado em **6 cópias através de 3 AZs** (auto-healing).
- Suporta até 15 Aurora Replicas (failover em < 30s).
- **Aurora Global Database:** 1 região primária + até 5 secundárias, replicação < 1s, RPO < 1s, failover < 1 min.
- **Aurora Serverless v2:** scaling automático em segundos, granularidade fina.
- **Aurora Multi-Master:** todas as instâncias são read+write (nicho específico).

> 💡 **INSIGHT-CHAVE:** Diferença Multi-AZ vs Read Replica vs Aurora Global: **Multi-AZ é HA dentro da Região (síncrono, sem read). Read Replica é escala de leitura (assíncrono). Aurora Global é DR multi-Região com latência sub-segundo.**

#### Amazon DynamoDB

- NoSQL key-value e document, single-digit ms latency, escala ilimitadamente.
- **Capacity modes:** On-demand (pay per request) ou Provisioned (RCU/WCU; suporta auto scaling).
- **Global Tables:** replicação multi-Região active-active com last-writer-wins.
- **DAX (DynamoDB Accelerator):** cache in-memory que reduz latência para microssegundos em reads.
- **Streams:** captura mudanças para Lambda processar.
- **PITR (Point-in-Time Recovery):** restore a qualquer ponto nos últimos 35 dias.
- **TTL:** expira itens automaticamente sem custo.

> ⚠️ **PEGADINHA DA PROVA:** DynamoDB tem limite de **400KB por item**. Para documentos maiores, guarde no S3 e armazene apenas o pointer no DynamoDB.

### 4.7 Backup e Replicação

#### AWS Backup

Serviço centralizado de backup para EBS, RDS, DynamoDB, EFS, FSx, Storage Gateway, S3, Aurora, Neptune, DocumentDB. Cria políticas com schedule, retenção e cross-Region/cross-account replication. Suporta **Vault Lock** para imutabilidade (compliance).

#### Snapshots EBS

- Incrementais, armazenados no S3 (não visível no console).
- Podem ser copiados para outras Regiões (DR).
- Suportam **Fast Snapshot Restore (FSR)** para baixar latência inicial após restore.

---

## 5. Domínio 3 — Design High-Performing Architectures (24%)

### 5.1 Storage de Alta Performance

#### Amazon S3

- **Durabilidade:** 11 noves (99.999999999%) em todas as classes.
- **Disponibilidade:** varia por classe (99.99% Standard até 99.5% One Zone).
- **Tamanho de objeto:** 0 bytes a 5TB (single PUT até 5GB; multipart obrigatório > 100MB recomendado).
- **Performance:** 3.500 PUT/COPY/POST/DELETE e 5.500 GET/HEAD por segundo **POR PREFIXO**. Múltiplos prefixos para paralelizar.

#### Classes de Armazenamento S3

| Classe | Caso de Uso | Características |
|--------|-------------|-----------------|
| **S3 Standard** | Acesso frequente | 99.99% disponibilidade, multi-AZ |
| **S3 Intelligent-Tiering** | Padrão de acesso desconhecido/variável | Move automaticamente entre tiers; sem retrieval fee |
| **S3 Standard-IA** | Acesso infrequente | Mais barato, retrieval fee, multi-AZ, mín. 30 dias |
| **S3 One Zone-IA** | IA não-crítico, regenerável | 20% mais barato que IA, mas em UMA AZ |
| **S3 Glacier Instant Retrieval** | Arquivamento com acesso ms | Acesso rápido, mín. 90 dias |
| **S3 Glacier Flexible Retrieval** | Arquivamento, retrieve em min/horas | Retrieve: Expedited 1-5min, Standard 3-5h, Bulk 5-12h, mín. 90 dias |
| **S3 Glacier Deep Archive** | Arquivamento longo prazo (compliance) | Mais barato, retrieve 12h-48h, mín. 180 dias |

> 💡 **INSIGHT-CHAVE:** Use **Lifecycle Policies** para mover objetos automaticamente. Padrão recomendado: Standard (30d) → Standard-IA (60d) → Glacier Flexible (180d) → Glacier Deep Archive.

#### Recursos S3 Avançados

- **Versioning:** mantém versões anteriores; protege contra delete acidental. Habilitar antes de bucket ter dados.
- **MFA Delete:** exige MFA para deletar versões; só root pode habilitar.
- **Replication (CRR/SRR):** Cross-Region ou Same-Region, assíncrona, requer versioning.
- **Object Lock:** WORM (Write Once Read Many) para compliance — Governance ou Compliance mode.
- **Pre-signed URLs:** acesso temporário sem credenciais.
- **Transfer Acceleration:** uploads via Edge Locations (CloudFront).
- **Multipart Upload:** obrigatório > 5GB; recomendado > 100MB.
- **S3 Select / Glacier Select:** queries SQL em objetos sem download.
- **Storage Lens:** analytics organization-wide.
- **Access Points:** endpoints separados com policies por aplicação.
- **Multi-Region Access Points:** endpoint global com routing inteligente.

> ⚠️ **PEGADINHA DA PROVA:** S3 Standard-IA tem mínimo de **30 dias de cobrança** E mínimo de **128KB por objeto**. Se você guardar arquivos minúsculos (ex.: logs de 1KB), ainda paga 128KB por arquivo — pode ficar mais caro que Standard.

#### EBS (Elastic Block Store)

| Tipo | Caso de Uso | Performance |
|------|-------------|-------------|
| **gp3 (SSD)** | Geral; default novo | 3.000 IOPS / 125 MB/s base; até 16.000 IOPS / 1.000 MB/s independentes |
| **gp2 (SSD)** | Geral, legado | 3 IOPS por GB, max 16.000 IOPS, burst até 3.000 |
| **io1/io2 (SSD)** | Alta performance, DBs críticos | Até 64.000 IOPS (io1) / 256.000 IOPS (io2 Block Express) |
| **st1 (HDD)** | Throughput streaming, big data | Até 500 MB/s, NÃO bootable |
| **sc1 (HDD)** | Cold storage, baixo custo | Até 250 MB/s, NÃO bootable |

- **Multi-Attach:** io1/io2 podem ser anexados a até 16 instâncias na mesma AZ (clustering).
- **Encryption:** AES-256 com KMS, sem impacto significativo de performance.
- **Snapshots:** incrementais para S3, podem ser copiados cross-Region.

#### EFS (Elastic File System)

- NFS v4 totalmente gerenciado, escala elasticamente.
- **Multi-AZ por padrão** (mount targets em cada AZ).
- **Performance modes:** General Purpose (latência baixa) ou Max I/O (alta concorrência).
- **Throughput modes:** Bursting (escala com tamanho), Provisioned, Elastic.
- **Storage classes:** Standard, Standard-IA, One Zone, One Zone-IA (mais barato).
- Compatível com **Linux apenas** (não Windows).

#### FSx — Família de Sistemas de Arquivos

| Tipo | Caso de Uso | Característica |
|------|-------------|----------------|
| **FSx for Windows File Server** | Aplicações Windows com SMB | Active Directory, dedup, shadow copies |
| **FSx for Lustre** | HPC, ML, mídia | Centenas de GB/s, integra com S3 |
| **FSx for NetApp ONTAP** | Multi-protocolo (NFS, SMB, iSCSI) | Snapshots, replicação, dedup, compressão |
| **FSx for OpenZFS** | Workloads Linux exigentes | ZFS features, compatível com NFS |

> 💡 **INSIGHT-CHAVE:** Decisão de storage: **arquivos compartilhados Linux → EFS. Compartilhados Windows → FSx for Windows. HPC/ML → FSx for Lustre. Object/blob → S3. Block para EC2 → EBS.**

#### AWS Storage Gateway

- **File Gateway:** monta S3 via NFS/SMB on-prem.
- **Volume Gateway:** iSCSI block storage com cached ou stored mode.
- **Tape Gateway:** VTL para substituir tape libraries físicas, backup para Glacier.

### 5.2 Compute de Alta Performance

#### EC2 — Famílias de Instância

- **General Purpose (T, M):** balanceadas. T-series tem CPU credits (burstable).
- **Compute Optimized (C):** alta CPU — batch, gaming servers, ML inference.
- **Memory Optimized (R, X, z1d):** in-memory DBs, big data analytics, SAP HANA.
- **Storage Optimized (I, D, H):** NoSQL DBs, data warehouse, distributed FS.
- **Accelerated (P, G, F, Inf, Trn):** GPU/FPGA — ML training, inference, gráficos 3D.

#### Placement Groups

- **Cluster:** instâncias em mesma AZ com baixa latência (HPC, MPI).
- **Spread:** instâncias em hardware distinto (max 7 por AZ por grupo).
- **Partition:** grupos lógicos em hardware separado (HDFS, Kafka, Cassandra) — até 7 partitions por AZ.

#### EC2 Hibernation

Salva o conteúdo da RAM em EBS root e desliga. Ao iniciar, retoma exatamente onde parou. Útil para apps com longo warmup. Limites: instance type específicos, root volume EBS criptografado, max 60 dias hibernando.

#### AWS Batch

Job scheduling para batch computing em larga escala. Provisiona EC2/Fargate automaticamente conforme a fila. Bom para scientific computing, transcoding em massa.

#### AWS Compute Optimizer

Usa ML para recomendar tipos/tamanhos de EC2, EBS, Lambda, ECS Fargate baseado em métricas reais. Recomenda right-sizing para reduzir custos.

### 5.3 Networking de Alta Performance

#### Amazon VPC

- **CIDR:** /16 a /28. NÃO sobrepor com on-prem se planeja VPN/Direct Connect.
- **Subnets:** /16 a /28. AWS reserva 5 IPs por subnet (.0, .1, .2, .3, .255).
- **Public Subnet:** tem rota para Internet Gateway.
- **Private Subnet:** sem rota para IGW; usa NAT Gateway para sair para internet.
- **Route Tables:** associadas a subnets; rota mais específica vence.

#### Componentes de Rede

| Serviço | Descrição / Caso de Uso |
|---------|--------------------------|
| **Internet Gateway (IGW)** | Permite saída/entrada da internet em VPC. Um por VPC. Highly available e horizontalmente escalável por padrão. |
| **NAT Gateway** | Permite que subnets privadas acessem internet (saída). Gerenciado, escala até 45 Gbps. Em UMA AZ — para HA, deploye um por AZ. |
| **NAT Instance** | EC2 com source/dest check desabilitado fazendo NAT. Legado e mais barato, mas você gerencia. Cobra menos, mas SPOF. |
| **Egress-only IGW** | IPv6 outbound-only (equivalente ao NAT para IPv6). |
| **VPC Peering** | Conexão 1-a-1 entre VPCs. NÃO transitivo. Suporta cross-Region e cross-account. |
| **Transit Gateway** | Hub central para conectar até milhares de VPCs e on-prem. **TRANSITIVO**. Suporta multicast e cross-Region peering. |
| **VPC Endpoint (Gateway)** | S3 e DynamoDB; via route table; gratuito. |
| **VPC Endpoint (Interface)** | Outros serviços via PrivateLink (ENI). Cobra por hora e GB. |
| **AWS PrivateLink** | Expõe seu serviço para outras VPCs/contas via NLB sem expor pela internet. |

> ⚠️ **PEGADINHA DA PROVA:** VPC Peering **NÃO é transitivo**. Se VPC A peer VPC B, e B peer VPC C, A NÃO acessa C automaticamente. Para malha completa de muitas VPCs, use **Transit Gateway**.

#### VPN e Direct Connect

- **Site-to-Site VPN:** IPSec sobre internet pública. Setup rápido (minutos), criptografado, mas latência variável.
- **AWS Direct Connect (DX):** conexão dedicada de 1/10/100 Gbps. Latência consistente, banda alta. Setup leva semanas. **NÃO criptografado por padrão** (use VPN sobre DX para isso).
- **DX Gateway:** conecta uma DX a múltiplas VPCs em múltiplas Regiões (mesma conta ou cross-account via TGW).
- **AWS Client VPN:** VPN OpenVPN gerenciada para usuários remotos.

> 💡 **INSIGHT-CHAVE:** Padrão DR para conectividade: **DX como primária + VPN como backup** (failover automático). Para setup rápido sem investimento de hardware, comece com VPN.

#### Edge Networking

| Serviço | Descrição / Caso de Uso |
|---------|--------------------------|
| **Amazon CloudFront** | CDN com 400+ POPs. TTL configurável, signed URLs/cookies, OAC para origem S3 privada, Lambda@Edge e CloudFront Functions para edge compute. |
| **AWS Global Accelerator** | Anycast IPs estáticos que entram na rede AWS pela Edge mais próxima e roteiam pela backbone. Ideal para apps NÃO HTTP (TCP/UDP), failover entre Regiões em segundos. |
| **Amazon Route 53** | DNS gerenciado com routing policies (Simple, Weighted, Latency, Failover, Geolocation, Geoproximity, Multivalue Answer). Registro de domínio + health checks. |

#### Route 53 — Routing Policies

- **Simple:** um único record.
- **Weighted:** tráfego proporcional a pesos (canary, A/B).
- **Latency:** roteia para Região com menor latência ao usuário.
- **Failover (active-passive):** primário + standby com health check.
- **Geolocation:** baseado no país/continente do usuário.
- **Geoproximity:** baseado na distância geográfica + bias (requer Traffic Flow).
- **Multivalue Answer:** até 8 records saudáveis, client-side load balancing.

> 💡 **INSIGHT-CHAVE:** Diferença CloudFront vs Global Accelerator: **CloudFront cacheia conteúdo HTTP/HTTPS na Edge** (foco em entrega de conteúdo). **Global Accelerator NÃO cacheia** — apenas roteia tráfego TCP/UDP pela backbone AWS para a Região mais próxima/saudável.

### 5.4 Caching

#### Amazon ElastiCache

| Engine | Caso de Uso | Características |
|--------|-------------|-----------------|
| **Redis** | Cache + estruturas avançadas, sessões, leaderboards, pub/sub | Multi-AZ com failover, replicação, persistence, cluster mode |
| **Memcached** | Cache simples, multi-thread | Sem persistence, sem replicação, sharding manual |

- **Caching strategies:** Lazy Loading (cache miss → DB → cache), Write-Through (escreve em cache E DB), TTL para evitar dados stale.

#### DynamoDB DAX

Cache fully-managed para DynamoDB. Reduz reads de ms para microssegundos. Compatível com a API DynamoDB existente — apenas troca o endpoint.

### 5.5 Data Analytics e Streaming

| Serviço | Descrição / Caso de Uso |
|---------|--------------------------|
| **Amazon Athena** | Queries SQL serverless em S3. Pay per query (TB scaneados). Suporta Parquet, ORC para reduzir custo. |
| **Amazon Redshift** | Data warehouse petabyte-scale. Colunar, MPP. Redshift Spectrum consulta direto S3. RA3 separa storage de compute. |
| **Amazon EMR** | Hadoop/Spark/Hive/Presto gerenciado. Para big data customizado. |
| **AWS Glue** | ETL serverless com Data Catalog (Hive metastore). Glue Crawlers descobrem schemas. |
| **Amazon Kinesis Data Streams** | Streaming real-time, retenção 1-365 dias, shards. Você gerencia consumers. |
| **Amazon Kinesis Data Firehose** | Streaming serverless para S3, Redshift, OpenSearch, Splunk. Buffering near-real-time (mín. 60s). |
| **Amazon Kinesis Data Analytics** | SQL/Apache Flink em streams (renomeado Amazon Managed Service for Apache Flink). |
| **Amazon MSK** | Apache Kafka gerenciado. Use quando o ecossistema já é Kafka. |
| **AWS Lake Formation** | Data lake gerenciado em S3 com governança fine-grained (linha/coluna). |
| **Amazon QuickSight** | BI dashboards. SPICE engine in-memory. ML insights nativos. |
| **Amazon OpenSearch** | Search e analytics (sucessor de Elasticsearch Service). |
| **AWS DataSync** | Migra dados on-prem ↔ AWS (10x mais rápido que ferramentas open source). |
| **AWS Snow Family** | Snowcone (8TB), Snowball Edge (80TB), Snowmobile (100PB). Para dados que não cabem em rede. |

> 💡 **INSIGHT-CHAVE:** Padrão de pipeline analytics: **Kinesis (ingestão) → S3 (storage) → Glue (ETL) → Athena/Redshift (query) → QuickSight (visualização)**. Para real-time near, troque parte por Kinesis Firehose + Kinesis Analytics.

> ⚠️ **PEGADINHA DA PROVA:** **Kinesis Data Streams vs SQS**: Kinesis preserva ORDEM por shard, permite múltiplos consumers RELENDO os dados, retenção até 365 dias. SQS é fila — uma mensagem é processada por um consumer e some.

---

## 6. Domínio 4 — Design Cost-Optimized Architectures (20%)

### 6.1 Modelos de Compra EC2

| Modelo | Desconto | Caso de Uso |
|--------|----------|-------------|
| **On-Demand** | 0% | Uso esporádico, dev/test, workloads imprevisíveis |
| **Reserved Instances (RI) - 1y** | Até 40% | Workload estável previsível |
| **Reserved Instances (RI) - 3y** | Até 60% | Workload estável de longo prazo |
| **Savings Plans** | Até 72% | Compute commitment $/hora; flexível entre EC2/Fargate/Lambda |
| **Spot Instances** | Até 90% | Fault-tolerant, interruptíveis (batch, big data, CI/CD, stateless web) |
| **Dedicated Host** | Conforme uso | Compliance, BYOL (Bring Your Own License - Windows, Oracle) |
| **Dedicated Instance** | Premium | Hardware dedicado sem visibilidade do host |
| **Capacity Reservation** | Sem desconto | Garante capacidade em uma AZ específica |

- **Convertible RI:** trocável por outro tipo/família (mais flexível, menos desconto).
- **Standard RI:** menos flexível, maior desconto.
- **Savings Plans tipos:** Compute SP (mais flexível, EC2/Fargate/Lambda), EC2 Instance SP (mais desconto, locked em família+Região), SageMaker SP.
- **Spot Fleet:** mistura Spot + On-Demand para atender capacidade-alvo com lances diversificados.

> 💡 **INSIGHT-CHAVE:** Estratégia de mix recomendada: **baseline com Savings Plans/RI** (workload constante) **+ Spot para picos batch/stateless + On-Demand para o resto**. Pode reduzir até 70% do custo.

> ⚠️ **PEGADINHA DA PROVA:** Spot pode ser interrompido com **2 minutos de aviso**. NUNCA use Spot para workloads stateful sem checkpoint (ex.: banco de dados primário, processo que precisa terminar). Para workloads que toleram interrupção, é bala de prata.

### 6.2 Otimização de Storage

#### S3 Lifecycle Policies

Automatize transições e expirações. Exemplo de regra:

- Standard por 30 dias → Standard-IA
- Standard-IA por 60 dias → Glacier Flexible
- Glacier por 365 dias → Deep Archive
- Expirar objetos antigos > 7 anos
- Expirar versões não-atuais e multipart uploads incompletos

#### S3 Intelligent-Tiering

Quando o padrão de acesso é **IMPREVISÍVEL**, use Intelligent-Tiering — AWS move objetos entre tiers Frequent/Infrequent/Archive automaticamente. Cobra pequena taxa de monitoramento, mas elimina a necessidade de definir policies manualmente. **NÃO há retrieval fee**.

#### EBS Otimização

- Escolha **gp3** ao invés de gp2 — mesma performance custa ~20% menos.
- Delete EBS volumes não usados (após terminate de EC2).
- **Snapshots EBS Archive:** até 75% mais barato para snapshots raramente acessados (retrieve em até 72h).
- Use Compute Optimizer para right-sizing de volumes.

### 6.3 Otimização de Compute

- Use **AWS Compute Optimizer** para identificar EC2 super-provisionadas.
- Migre workloads stateless para Lambda/Fargate (paga só pelo uso).
- Use Auto Scaling para scale-in em horários de baixa demanda.
- Schedule (Instance Scheduler) para desligar dev/test fora do expediente.
- Use **Graviton (ARM)** para 20-40% menos custo com performance equivalente ou superior.

### 6.4 Otimização de Banco de Dados

- **Aurora Serverless v2** para workloads intermitentes/imprevisíveis.
- **DynamoDB On-Demand** para tráfego imprevisível, Provisioned com Auto Scaling para tráfego previsível.
- **RDS Reserved Instances** para workloads estáveis (até 69% de desconto).
- Use **Read Replicas** para offload de queries de leitura ao invés de upgrade do master.
- **Aurora I/O-Optimized:** melhor para alta atividade de I/O (sem cobrança por I/O).

### 6.5 Otimização de Rede

- Tráfego DENTRO da mesma AZ é gratuito; entre AZs cobra; para internet cobra mais.
- Use **VPC Endpoints** (Gateway gratuito para S3/DynamoDB) para evitar tráfego pelo NAT Gateway.
- CloudFront reduz custos de egress da origem (data transfer out do CloudFront é mais barato).
- Comprima respostas e use cache na Edge.
- Em arquiteturas com muitas VPCs, **Transit Gateway** pode ser mais barato e simples que mesh de VPC Peering.
- NAT Gateway custa por GB processado — evite tráfego desnecessário; um por AZ para HA mas avalie consolidar em ambientes não-críticos.

> 💡 **INSIGHT-CHAVE:** Evite o erro mais comum: rotear tráfego S3 de subnet privada via NAT Gateway. Use **VPC Gateway Endpoint para S3 e DynamoDB** — é **GRATUITO** e elimina o custo de NAT.

### 6.6 Ferramentas de Cost Management

| Serviço | Descrição / Caso de Uso |
|---------|--------------------------|
| **AWS Cost Explorer** | Visualiza custos e uso com filtros, projeção até 12 meses, recomendações de RI/SP. |
| **AWS Budgets** | Alertas quando custo/uso atinge thresholds. Suporta ações automáticas. |
| **AWS Cost and Usage Report (CUR)** | Detalhamento granular em S3 — pode ser analisado com Athena/QuickSight. |
| **AWS Pricing Calculator** | Estimativa antes de provisionar. |
| **AWS Trusted Advisor** | Recomendações de cost, performance, security, fault tolerance, service limits. Plano Business/Enterprise libera todos os checks. |
| **AWS Compute Optimizer** | Right-sizing baseado em ML para EC2, EBS, Lambda, Fargate. |
| **Cost Allocation Tags** | Tags para alocar custos por projeto/equipe/centro de custo. Ative no Billing console. |

---

## 7. Insights-Chave Transversais (alta frequência na prova)

### 7.1 Padrões de Decisão Recorrentes

#### Fila ou Streaming?

- **SQS:** filas, processamento sequencial por consumidor único, mensagens descartadas após consumo.
- **Kinesis:** streams de eventos com múltiplos consumidores, replay, ordenação por shard.
- **EventBridge:** roteamento de eventos por regras (event-driven).
- **SNS:** fan-out pub/sub a múltiplos targets.

#### Banco Relacional ou NoSQL?

- **RDS/Aurora:** ACID, joins, schema rígido, transações complexas.
- **DynamoDB:** key-value, escala ilimitada, latência ms, schema flexível, padrões de acesso conhecidos.
- **DocumentDB:** MongoDB compatible — quando time já usa MongoDB.
- **Neptune:** grafos (relacionamentos complexos: redes sociais, fraud detection).
- **Timestream:** séries temporais (IoT, métricas).
- **QLDB:** ledger imutável e auditável.
- **Keyspaces:** Cassandra compatible.

#### Como expor uma API?

- **API Gateway REST:** APIs públicas com features avançadas (cache, throttling, API keys).
- **API Gateway HTTP:** mais barato e rápido, features reduzidas.
- **API Gateway WebSocket:** comunicação bidirecional persistente.
- **ALB:** para tráfego HTTP simples a backends EC2/ECS/Lambda dentro da VPC.
- **AppSync:** GraphQL gerenciado.

### 7.2 Palavras-Chave que Apontam Respostas

| Palavra-chave no enunciado | Tendência da resposta | Por quê |
|----------------------------|------------------------|---------|
| 'Least operational overhead' | Serverless / managed | AWS gerencia infra |
| 'Most cost-effective' | Spot / Lifecycle / Lambda | Pay-per-use ou desconto |
| 'Highest availability' | Multi-AZ / Multi-Region | Redundância geográfica |
| 'Real-time' | Kinesis Streams / Lambda / DynamoDB | Latência baixa |
| 'Near real-time' | Kinesis Firehose | Buffering de 60s |
| 'Globally distributed users' | CloudFront / Global Accelerator / Aurora Global / DynamoDB Global Tables | Edge ou multi-Região |
| 'Compliance / immutable' | S3 Object Lock / Glacier Vault Lock / WORM | Imutabilidade |
| 'Encrypt at rest' | KMS / SSE-S3 / SSE-KMS | Criptografia |
| 'Encrypt in transit' | TLS / ACM / VPN | Criptografia em trânsito |
| 'On-prem to AWS frequently' | Direct Connect / DataSync | Conectividade dedicada |
| 'One-time large data transfer' | Snowball / Snowmobile | Transferência física |
| 'Decouple components' | SQS / SNS / EventBridge | Mensageria |
| 'Lift and shift' | EC2 / VMware Cloud / MGN | Mínima refatoração |
| 'Existing MongoDB workload' | DocumentDB | Compatibilidade |
| 'Event-driven' | Lambda / EventBridge / Step Functions | Eventos |
| 'Stateless web at any scale' | ASG + ALB + Lambda + Fargate | Horizontal scaling |

### 7.3 Pegadinhas Frequentes

> ⚠️ **PEGADINHA DA PROVA:** S3 Cross-Region Replication **NÃO replica deletes** por padrão (delete markers podem ser opcionalmente replicados). NÃO replica objetos pré-existentes (apenas novos), exceto se usar S3 Batch Replication.

> ⚠️ **PEGADINHA DA PROVA:** KMS — uma chave fica em **UMA Região**. Para criptografar em A e descriptografar em B, use **Multi-Region Keys** (replicas) ou re-encrypt com chave diferente em cada Região.

> ⚠️ **PEGADINHA DA PROVA:** Lambda dentro de VPC adiciona ENI Hyperplane — historicamente havia cold start adicional, hoje quase imperceptível. Mas se você acessar a internet de Lambda em VPC, ainda precisa de **NAT Gateway** na subnet.

> ⚠️ **PEGADINHA DA PROVA:** ALB targets podem ser EC2, IP, Lambda, container ECS. NLB targets podem ser EC2, IP, ALB (**NÃO Lambda diretamente**).

> ⚠️ **PEGADINHA DA PROVA:** RDS Read Replica pode ser cross-Region, mas a replicação é **assíncrona** — em desastres, há perda potencial de dados (RPO > 0). Para RPO ≈ 0 cross-Region, use **Aurora Global Database**.

> ⚠️ **PEGADINHA DA PROVA:** CloudFront com origem S3: para tornar o bucket privado, use **Origin Access Control (OAC, novo padrão)** ou Origin Access Identity (OAI, legado).

> ⚠️ **PEGADINHA DA PROVA:** EFS é **Linux-only** (NFS). Para Windows compartilhado, use **FSx for Windows File Server**.

> ⚠️ **PEGADINHA DA PROVA:** DynamoDB Streams retém eventos por **24h** (não é configurável). Se quiser mais, use **Kinesis Data Streams** como destino de change capture.

### 7.4 Estratégias de Migração (6 R's)

- **Rehost (Lift and Shift):** AWS Application Migration Service (MGN).
- **Replatform (Lift and Reshape):** ex.: migrar de MySQL on-prem para RDS gerenciado.
- **Repurchase:** trocar por SaaS.
- **Refactor / Re-architect:** refazer em serverless/microsserviços.
- **Retain:** manter on-prem por enquanto.
- **Retire:** desligar.

#### Ferramentas de Migração

- **AWS Application Discovery Service:** descobre o que existe on-prem.
- **AWS Migration Hub:** console central.
- **AWS Application Migration Service (MGN):** rehost de servidores físicos/VMs.
- **AWS Database Migration Service (DMS):** migra DBs (homogêneo MySQL→MySQL ou heterogêneo Oracle→Aurora com SCT).
- **AWS Schema Conversion Tool (SCT):** converte schemas entre engines.
- **AWS DataSync:** migra files (NFS, SMB, HDFS, S3).
- **AWS Snow Family:** transferência física (Snowcone 8TB, Snowball Edge 80TB, Snowmobile 100PB).

---

## 8. Apêndice — Serviços In-Scope da Prova

Lista oficial dos serviços que podem aparecer no SAA-C03, conforme guia AWS:

### Analytics

Amazon Athena, AWS Data Exchange, AWS Data Pipeline, Amazon EMR, AWS Glue, Amazon Kinesis, AWS Lake Formation, Amazon MSK, Amazon OpenSearch Service, Amazon QuickSight, Amazon Redshift

### Application Integration

Amazon AppFlow, AWS AppSync, Amazon EventBridge, Amazon MQ, Amazon SNS, Amazon SQS, AWS Step Functions

### AWS Cost Management

AWS Budgets, AWS Cost and Usage Report, AWS Cost Explorer, Savings Plans

### Compute

AWS Batch, Amazon EC2, Amazon EC2 Auto Scaling, AWS Elastic Beanstalk, AWS Outposts, AWS Serverless Application Repository, VMware Cloud on AWS, AWS Wavelength

### Containers

Amazon ECS Anywhere, Amazon EKS Anywhere, Amazon EKS Distro, Amazon ECR, Amazon ECS, Amazon EKS

### Database

Amazon Aurora, Amazon Aurora Serverless, Amazon DocumentDB, Amazon DynamoDB, Amazon ElastiCache, Amazon Keyspaces, Amazon Neptune, Amazon QLDB, Amazon RDS, Amazon Redshift

### Developer Tools

AWS X-Ray

### Front-End Web and Mobile

AWS Amplify, Amazon API Gateway, AWS Device Farm, Amazon Pinpoint

### Machine Learning

Amazon Comprehend, Amazon Forecast, Amazon Fraud Detector, Amazon Kendra, Amazon Lex, Amazon Polly, Amazon Rekognition, Amazon SageMaker, Amazon Textract, Amazon Transcribe, Amazon Translate

### Management and Governance

AWS Auto Scaling, AWS CloudFormation, AWS CloudTrail, Amazon CloudWatch, AWS CLI, AWS Compute Optimizer, AWS Config, AWS Control Tower, AWS Health Dashboard, AWS License Manager, Amazon Managed Grafana, Amazon Managed Service for Prometheus, AWS Management Console, AWS Organizations, AWS Proton, AWS Service Catalog, AWS Systems Manager, AWS Trusted Advisor, AWS Well-Architected Tool

### Media Services

Amazon Elastic Transcoder, Amazon Kinesis Video Streams

### Migration and Transfer

AWS Application Discovery Service, AWS Application Migration Service, AWS DMS, AWS DataSync, AWS Migration Hub, AWS Snow Family, AWS Transfer Family

### Networking and Content Delivery

AWS Client VPN, Amazon CloudFront, AWS Direct Connect, Elastic Load Balancing, AWS Global Accelerator, AWS PrivateLink, Amazon Route 53, AWS Site-to-Site VPN, AWS Transit Gateway, Amazon VPC

### Security, Identity, and Compliance

AWS Artifact, AWS Audit Manager, AWS Certificate Manager (ACM), AWS CloudHSM, Amazon Cognito, Amazon Detective, AWS Directory Service, AWS Firewall Manager, Amazon GuardDuty, AWS IAM Identity Center, AWS IAM, Amazon Inspector, AWS KMS, Amazon Macie, AWS Network Firewall, AWS Resource Access Manager, AWS Secrets Manager, AWS Security Hub, AWS Shield, AWS WAF

### Serverless

AWS AppSync, AWS Fargate, AWS Lambda

### Storage

AWS Backup, Amazon EBS, Amazon EFS, Amazon FSx (todos os tipos), Amazon S3, Amazon S3 Glacier, AWS Storage Gateway

---

## 9. Estratégia de Estudo Recomendada

### 9.1 Cronograma Sugerido (4-8 semanas)

- **Semana 1-2:** fundamentos — IAM, VPC, EC2, S3, EBS. Pratique no console.
- **Semana 3-4:** bancos (RDS, Aurora, DynamoDB), ELB, Auto Scaling, Route 53, CloudFront.
- **Semana 5:** serverless (Lambda, API Gateway, SQS, SNS, EventBridge, Step Functions).
- **Semana 6:** segurança avançada (KMS, Secrets Manager, GuardDuty, Macie, Shield, WAF), Organizations.
- **Semana 7:** DR, migração, analytics (Kinesis, Athena, Glue), monitoring (CloudWatch, X-Ray).
- **Semana 8:** simulados intensivos, revisão de pegadinhas, calibração de tempo.

### 9.2 Recursos Recomendados

- **AWS Skill Builder** — Exam Prep oficial (gratuito, oficial)
- **Stephane Maarek (Udemy)** — curso prático com hands-on
- **Adrian Cantrill** — abordagem profunda em arquitetura
- **Tutorials Dojo (Jon Bonso)** — simulados altamente recomendados
- **AWS Whitepapers essenciais:** Well-Architected Framework, AWS Storage Services Overview, Disaster Recovery on AWS
- **AWS FAQs** dos serviços principais (S3, EC2, RDS, DynamoDB, VPC, IAM, Lambda)
- **Hands-on com Free Tier** — laboratórios reais valem mais que vídeos passivos

### 9.3 Estratégia no Dia da Prova

- **130 min para 65 questões = ~2 min/questão**. Não trave.
- Marque para revisar e siga em frente quando ficar em dúvida.
- Leia o **ENUNCIADO ATÉ O FIM** antes das opções — palavras como 'most cost-effective', 'least overhead', 'highest availability' são decisivas.
- Elimine 2 alternativas obviamente erradas e escolha a melhor entre as 2 restantes.
- Cuidado com 'distractors' que mencionam serviços fora de escopo (ex.: CodeCommit, SageMaker para questões de arquitetura básica).
- Se a questão diz 'minimal effort' ou 'managed service', prefira soluções serverless ou totalmente gerenciadas.
- Quando duas respostas são tecnicamente válidas, escolha a que respeita o pilar Well-Architected destacado.

> 💡 **INSIGHT-CHAVE:** Nas últimas 48h antes da prova: revise apenas suas anotações pessoais e simulados errados. **NÃO estude conteúdo novo**. Descanse bem na noite anterior.

---

## Boa sorte na sua certificação! 🎯

> Lembre-se: a prática hands-on no console AWS é o que diferencia quem passa de quem só decora.
