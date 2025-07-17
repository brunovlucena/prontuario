# Prontuário - Arquitetura Híbrida Local/Cloud com M3 Ultra

## 🎯 Visão Geral da Arquitetura

Prontuário utiliza uma arquitetura híbrida que combina processamento de IA local em Mac Studio M3 Ultra com serviços em nuvem GCP, otimizada para dispositivos iPhone únicos e operações hospitalares de 200 usuários diários.

---

## 🏗️ Arquitetura de Alto Nível

```sh
┌─────────────────────────────────────┐
│ 📱 DISPOSITIVOS iPhone              │
│                                     │
│ 👨‍⚕️200 usuários diários              │
│ 🩺 Interface médica nativa          │
│ 🎤 Comandos de voz                  │
│                                     │
└─────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────┐
│ 🖥️ MAC STUDIO M3 ULTRA              │
│                                     │
│ 🧠 32-core CPU + 80-core GPU        │
│ 🤖 Processamento IA Massivo         │
│ 📊 3x Kubernetes Nodes (Virtual)    │
│ 🔄 Orquestração Unificada           │
│                                     │
└─────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────┐
│ ☁️ SERVIÇOS GCP                     │
│                                     │
│ 🎛️ GKE + Pulumi                     │
│ 💾 Cloud Storage                    │
│ 🗄️ Cloud SQL                        │
│ 📋 Dados & Orquestração             │
│                                     │
└─────────────────────────────────────┘
```

---

## 🖥️ Infraestrutura Local Kubernetes + GCP

### 🔧 Mac Studio M3 Ultra Single Machine Cluster

### **Especificações Hardware**

| **Componente** | **Especificação** | **Quantidade** | **Função** |
|----------------|-------------------|----------------|------------|
| **🖥️ Mac Studio M3 Ultra** | **CPU 32-core (24P+8E), GPU 80-core, 512GB RAM** | **1 unidade** | **Host Principal K8s** |
| **🧠 Neural Engine** | **32-core, 36 TOPS** | **1 unidade** | **IA Acelerada** |
| **💾 Internal Storage** | **8TB SSD** | **1 unidade** | **Armazenamento local** |
| **🌐 Network** | **10Gb Ethernet + Thunderbolt 5** | **Integrado** | **Conectividade ultra-rápida** |

#### **Configuração Kubernetes Multi-Node Virtual**

```sh
┌─────────────────────────────────────┐
│ 🖥️ MAC STUDIO M3 ULTRA              │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ 🎛️ KUBERNETES CONTROL PLANE     │ │
│ │ • API Server                    │ │
│ │ • etcd                          │ │
│ │ │ • Scheduler                   │ │
│ │ • Controller Manager            │ │
│ └─────────────────────────────────┘ │
│          │                          │
│    ┌─────┼─────┼─────┐              │
│    ▼     ▼     ▼     ▼              │
│ ┌─────┐ ┌─────┐ ┌─────┐             │
│ │🤖AI │ │📡API│ │📊Data│            │
│ │Node1│ │Node2│ │Node3│             │
│ │     │ │     │ │     │             │
│ │300GB│ │100GB│ │100GB│             │
│ └─────┘ └─────┘ └─────┘             │
│ Virtual K8s Nodes                   │
└─────────────────────────────────────┘
```

---

## 🤖 Distribuição de Workloads IA Avançada

### **Especialização por Node Virtual**

```sh
┌─────────────────────────────────────┐
│ 🤖 VIRTUAL NODE 1 - IA INFERENCE    │
├─────────────────────────────────────┤
│ • 🧠 MedGemma 4B (Multimodal)       │
│ • 🎤 Whisper Large                  │
│ • 🗣️ Core ML Voice (Avançado)       │
│ • 🔬 Medical Image Analysis         │
│                                     │
│ Memory: 300GB (60% total)           │
│ GPU: 50-core allocation (62% total) │
│ Neural Engine: 20-core allocation   │
│ CPU: 16-core allocation             │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ 📡 VIRTUAL NODE 2 - API BACKEND     │
├─────────────────────────────────────┤
│ • 🌐 FastAPI Server                 │
│ • 🔐 Advanced Auth Service          │
│ • 📝 Document Processing Service    │
│ • 🔄 Real-time Queue Processing     │
│                                     │
│ Memory: 100GB (20% total)           │
│ CPU: 8-core allocation              │
│ GPU: 15-core allocation (19% total) │
│ Neural Engine: 6-core allocation    │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ 📊 VIRTUAL NODE 3 - DATA PROCESSING │
├─────────────────────────────────────┤
│ • 📈 Advanced Analytics Engine      │
│ • 🔍 Vector Search Service          │
│ • 💾 Distributed Cache Layer        │
│ • 📋 Real-time Report Generator     │
│                                     │
│ Memory: 100GB (20% total)           │
│ CPU: 8-core allocation              │
│ GPU: 15-core allocation (19% total) │
│ Neural Engine: 6-core allocation    │
│ Storage: 8TB SSD allocation         │
└─────────────────────────────────────┘

### **📊 Justificativa da Alocação de Recursos**

| **Recurso** | **AI Node 1** | **API Node 2** | **Data Node 3** | **Total** | **Disponível M3 Ultra** |
|-------------|---------------|-----------------|------------------|-----------|--------------------------|
| **Memory** | 300GB (60%) | 100GB (20%) | 100GB (20%) | 500GB | 512GB total |
| **GPU Cores** | 50 (62%) | 15 (19%) | 15 (19%) | 80 | 80 total |
| **CPU Cores** | 16 (50%) | 8 (25%) | 8 (25%) | 32 | 32 total |
| **Neural Engine** | 20 (62%) | 6 (19%) | 6 (19%) | 32 | 32 total |

**🎯 Razões da Distribuição:**
- **AI Node 1**: MedGemma 4B requer ~54GB + KV cache para 200 usuários (~246GB) = 300GB total
- **API Node 2**: Serviços web leves, processamento de requisições = 100GB suficiente  
- **Data Node 3**: Analytics e cache, não precisa de GPU intensiva = 100GB adequado
- **12GB restantes**: Sistema operacional e overhead do Kubernetes

```

---

## 🧠 Stack de IA Avançado

### **Modelos e Processamento de Alto Desempenho**

```sh
┌─────────────────────────────────────┐
│ 🧠 STACK IA - INFERENCE MASSIVO     │
├─────────────────────────────────────┤
│                                     │
│ 🎤 SPEECH-TO-TEXT                   │
│  ├─► Whisper Large (1.5GB)          │
│  ├─► Core ML optimized              │
│  └─► Latência: <100ms               │
│                                     │
│ 🤖 LANGUAGE MODEL                   │
│  ├─► MedGemma 4B (Multimodal)       │
│  ├─► Medical + Image understanding  │
│  └─► Contexto médico 128K tokens    │
│                                     │
│ 🔬 MEDICAL IMAGE AI                 │
│  ├─► SigLIP Medical encoder         │
│  ├─► Radiology analysis             │
│  └─► Pathology classification       │
│                                     │
│ 🗣️ TEXT-TO-SPEECH                   │
│  ├─► Core ML Voice Advanced         │
│  ├─► Natural medical terminology    │
│  └─► Latência: <200ms               │
│                                     │
│ 📊 ANALYTICS                        │
│  ├─► Real-time health insights      │
│  ├─► Advanced pattern recognition   │
│  └─► Parallel processing (80 cores) │
│                                     │
└─────────────────────────────────────┘
```

---

## 📊 Fluxo de Dados Otimizado

### **Pipeline Processamento Ultra-Rápido**

```sh
┌─────────────────────────────────────┐
│ 📊 PIPELINE DADOS E PROCESSAMENTO   │
├─────────────────────────────────────┤
│                                     │
│ 📱 iPhone App (200 usuários)        │
│  │ 🏥 MVP: WLAN-ONLY ACCESS         │
│  ▼ 📡 Hospital WiFi 6E (Internal)   │
│ 🎤 Audio/Image Capture              │
│  │                                  │
│  ▼ 🔒 HTTPS (Internal Network)      │
│ 🖥️ Mac Studio M3 Ultra              │
│  │                                  │
│  ├─► 🧠 Whisper Large STT           │
│  ├─► 🤖 MedGemma 4B Medical LLM     │
│  ├─► 🔬 Medical Image Analysis      │
│  └─► 📊 Advanced Health Analytics   │
│  │                                  │
│  │ (819GB/s memory bandwidth)       │
│  ▼                                  │
│ 🚫 GCP Services (MVP: DISABLED)     │
│  │ ⚠️ NO CLOUD ACCESS DURING MVP    │
│  ├─► 💾 Local Storage Only          │
│  ├─► 🗄️ Local Database              │
│  └─► 📋 On-Premises Records         │
│  │                                  │
│  ▼ 📡 Internal WiFi Response        │
│ 📱 Enhanced Response to iPhone      │
│                                     │
└─────────────────────────────────────┘
```

### **🔧 Conectividade iPhone: Especificações Técnicas**

```sh
┌─────────────────────────────────────┐
│ 📡 CONECTIVIDADE iPhone INTERNAL    │
│ 🏥 MVP PHASE: HOSPITAL-ONLY ACCESS  │
├─────────────────────────────────────┤
│                                     │
│ 📱 iPhone 15 Pro (iOS 18+)          │
│  │ ⚠️ INTERNAL NETWORK ONLY         │
│  ├─► 📡 Hospital WiFi 6E            │
│  ├─► 🚫 5G/Cellular DISABLED        │
│  ├─► 🚫 External Internet BLOCKED   │
│  └─► 🏥 Internal-only connectivity  │
│  │                                  │
│  ▼ 🔐 TLS 1.3 (Internal CA)         │
│ 🏥 Hospital Internal Network        │
│  │                                  │
│  ├─► 🛡️ Air-gapped from Internet    │
│  ├─► ⚖️ Internal Load Balancer      │
│  └─► 🔒 No VPN (direct access)      │
│  │                                  │
│  ▼ 🎯 Direct to M3 Ultra            │
│ 🖥️ Mac Studio M3 Ultra (Local)      │
│                                     │
│ ⚡ Latência Total: 2-5ms             │
│ 📊 Throughput: 1-9.6Gb/s            │
│ 🔒 Security: Internal-only isolated │
│                                     │
│ 📋 MVP RESTRICTIONS:                │
│ • No external internet access       │
│ • Hospital WiFi network only        │
│ • Air-gapped during MVP phase       │
│ • All data stays on-premises        │
│                                     │
└─────────────────────────────────────┘
```

---

# ☁️ SERVIÇOS GOOGLE CLOUD PLATFORM

## 🎛️ Infraestrutura GCP com Pulumi

### **Componentes Cloud**

```sh
┌─────────────────────────────────────┐
│ ☁️ GOOGLE CLOUD PLATFORM            │
├─────────────────────────────────────┤
│                                     │
│ 🎛️ GKE STANDARD CLUSTER             │
│  ├─► 3 nodes e2-standard-4          │
│  ├─► Auto-scaling: 1-10 nodes       │
│  └─► Regional deployment            │
│                                     │
│ 💾 CLOUD STORAGE                    │
│  ├─► Patient documents              │
│  ├─► Medical images                 │
│  └─► Backup & archives              │
│                                     │
│ 🗄️ CLOUD SQL                        │
│  ├─► PostgreSQL 15                  │
│  ├─► High availability              │
│  └─► Automated backups              │
│                                     │
│ 🔐 SECURITY & COMPLIANCE            │
│  ├─► IAM & RBAC                     │
│  ├─► VPC Private networking         │
│  └─► Audit logging                  │
│                                     │
└─────────────────────────────────────┘
```

### **Gestão Infraestrutura como Código**

| **Componente** | **Tecnologia** | **Função** |
|----------------|----------------|------------|
| **🏗️ Infraestrutura Principal** | **Pulumi TypeScript** | **GCP resources, networking, security** |
| **⚙️ Kubernetes Resources** | **Pulumi Python** | **K8s deployments, services, configs** |
| **📊 Monitoramento** | **Pulumi YAML** | **Observability stack, dashboards** |
| **🔐 Segurança** | **Pulumi Go** | **IAM policies, secrets, compliance** |

---

# 📱 APLICATIVO iPhone NATIVO

## 🧩 Arquitetura iOS

### **Stack Tecnológico**

| **Camada** | **Tecnologia** | **Função** |
|------------|----------------|------------|
| **🎨 Interface** | **SwiftUI** | **UI declarativa moderna** |
| **🧠 Lógica** | **Swift** | **Business logic, coordination** |
| **🔊 Áudio** | **AVFoundation** | **Recording, playback, processing** |
| **🤖 IA Local** | **Core ML** | **On-device inference** |
| **🌐 Network** | **URLSession** | **HTTP client, data sync** |
| **💾 Storage** | **Core Data** | **Local database** |
| **📊 Visualização Dados** | **Charts + Core Graphics** | **Gráficos laboratório/sinais vitais** |

### **Arquitetura de Módulos**

```sh
┌─────────────────────────────────────┐
│ 📱 APLICATIVO iPhone NATIVO         │
├─────────────────────────────────────┤
│                                     │
│ 🎨 PRESENTATION LAYER               │
│  ├─► SwiftUI Views                  │
│  ├─► ViewModels                     │
│  └─► Navigation                     │
│  │                                  │
│  ▼                                  │
│ 🧠 BUSINESS LOGIC                   │
│  ├─► Medical Services               │
│  ├─► Audio Processing               │
│  └─► Data Management                │
│  │                                  │
│  ▼                                  │
│ 🔗 INTEGRATION LAYER                │
│  ├─► Network Client                 │
│  ├─► Core ML Integration            │
│  └─► Local Storage                  │
│  │                                  │
│  ▼                                  │
│ ⚙️ INFRASTRUCTURE                   │
│  ├─► Core Data                      │
│  ├─► AVFoundation                   │
│  └─► URLSession                     │
│                                     │
└─────────────────────────────────────┘
```

---

## 📡 Integração e Comunicação

### **Fluxo de Comunicação iPhone ↔ Cluster Local**

```sh
┌─────────────────────────────────────┐
│ 📡 COMUNICAÇÃO iPhone ↔ LOCAL K8s   │
├─────────────────────────────────────┤
│                                     │
│ 📱 iPhone App                       │
│  │  🎤 Audio recorded               │
│  │  📝 Medical notes                │
│  │  👤 Patient selection            │
│  │                                  │
│  ▼  📡 HTTPS/WebSocket              │
│ 🌐 Load Balancer                    │
│  │  (Nginx Ingress)                 │
│  │                                  │
│  ▼  🔀 Route to services            │
│ ⚙️ Kubernetes Services              │
│  │                                  │
│  ├─► 🤖 AI Service                  │
│  │    (Whisper + Llama)             │
│  │                                  │
│  ├─► 📡 API Service                 │
│  │    (FastAPI backend)             │
│  │                                  │
│  └─► 📊 Analytics Service           │
│       (Data processing)             │
│  │                                  │
│  ▼  ☁️ Sync to cloud                │
│ 🗄️ GCP Services                     │
│    (Storage + Database)             │
│                                     │
└─────────────────────────────────────┘
```

---

## 🚀 MVP vs Production: Phased Architecture Approach

### **📋 MVP Phase: Internal-Only (Months 1-6)**

```sh
┌─────────────────────────────────────┐
│ 🏥 MVP: HOSPITAL INTERNAL ONLY      │
├─────────────────────────────────────┤
│                                     │
│ ✅ ENABLED FEATURES:                │
│ • iPhone App (Hospital WiFi only)   │
│ • MedGemma 4B AI inference          │
│ • Whisper speech-to-text            │
│ • Local database storage            │
│ • 200 concurrent users              │
│                                     │
│ 🚫 DISABLED FEATURES:               │
│ • Internet connectivity             │
│ • Cloud services (GCP)              │
│ • External API access               │
│ • Remote monitoring                 │
│ • Mobile data/5G usage              │
│                                     │
│ 🎯 MVP OBJECTIVES:                  │
│ • Validate AI accuracy              │
│ • Test workflow integration         │
│ • Gather user feedback              │
│ • Ensure data security              │
│ • Prove ROI with 20 pilot doctors   │
│                                     │
└─────────────────────────────────────┘
```

### **🌐 Production Phase: Hybrid Cloud (Months 7+)**

```sh
┌─────────────────────────────────────┐
│ 🌍 PRODUCTION: HYBRID ARCHITECTURE  │
├─────────────────────────────────────┤
│                                     │
│ ✅ ADDITIONAL FEATURES:             │
│ • GCP cloud integration             │
│ • External internet access          │
│ • Multi-hospital deployment         │
│ • Remote monitoring & analytics     │
│ • Mobile 5G connectivity            │
│                                     │
│ 🔄 MIGRATION STRATEGY:              │
│ • Gradual cloud services enablement │
│ • Data backup to GCP                │
│ • Cross-hospital data sharing       │
│ • External monitoring tools         │
│ • Scaling to 500+ users             │
│                                     │
│ 📊 PRODUCTION BENEFITS:             │
│ • Multi-site deployment             │
│ • Advanced analytics & reporting    │
│ • Disaster recovery & backup        │
│ • External integrations             │
│ • Enterprise monitoring             │
│                                     │
└─────────────────────────────────────┘
```

---

## ⚡ Especificações de Performance M3 Ultra

### **Especificações de Hardware M3 Ultra**

| **Métrica** | **Mac Studio M3 Ultra** | **Capacidade IA Médica** |
|-------------|-------------------------|---------------------------|
| **CPU Cores** | 32 cores (24P+8E) | Processamento médico de alta performance |
| **GPU Cores** | 80 cores | Aceleração avançada de inferência IA |
| **Memory Total** | 512GB | Suporte a modelos médicos grandes |
| **Memory Bandwidth** | 819GB/s | Processamento de dados ultra-rápido |
| **Neural Engine** | 32-core, 36 TOPS | IA médica acelerada |
| **AI Model Support** | MedGemma 4B + Large Models | IA médica empresarial |
| **Storage** | 8TB unified | Dados médicos abrangentes |
| **Power Efficiency** | 180W max | Operação eficiente em energia |

### **Capacidades de IA Médica Avançada**

```sh
┌─────────────────────────────────────┐
│ 🧠 CAPACIDADES IA MÉDICA M3 ULTRA   │
├─────────────────────────────────────┤
│                                     │
│ 🤖 LARGE LANGUAGE MODELS            │
│  ├─► MedGemma 4B: ~24GB VRAM        │
│  ├─► Possível MedGemma 27B          │
│  └─► Contexto: 128K tokens médicos  │
│                                     │
│ 🔬 MEDICAL IMAGE ANALYSIS           │
│  ├─► Radiology: X-ray, CT, MRI      │
│  ├─► Pathology: Histologia          │
│  └─► Dermatology: Lesão cutânea     │
│                                     │
│ 📊 REAL-TIME ANALYTICS              │
│  ├─► 200 usuários simultâneos       │
│  ├─► Latência: <100ms               │
│  └─► Throughput: 1000+ req/min      │
│                                     │
│ 🎤 VOICE PROCESSING                 │
│  ├─► Whisper Large: 99% precisão    │
│  ├─► Múltiplos idiomas              │
│  └─► Terminologia médica            │
│                                     │
└─────────────────────────────────────┘
```

---

## 🔍 Arquitetura de Observabilidade e Conformidade Brasileira

### **Stack de Observabilidade Integrada**

```sh
┌─────────────────────────────────────┐
│ 📊 OBSERVABILIDADE & MONITORAMENTO  │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ 📈 PROMETHEUS + ALERTMANAGER    │ │
│ │ • Métricas sistema M3 Ultra     │ │
│ │ • Métricas médicas SUS/CFM      │ │
│ │ • Alertas conformidade ANVISA   │ │
│ │ • KPIs qualidade ANS            │ │
│ └─────────────────────────────────┘ │
│          │                          │
│    ┌─────┼─────┼─────┐              │
│    ▼     ▼     ▼     ▼              │
│ ┌─────┐ ┌─────┐ ┌─────┐             │
│ │📊   │ │📋   │ │🎯   │             │
│ │Grafana│ Loki │Tempo│              │
│ │       │(Infra│     │              │
│ │       │&Sys) │     │              │
│ │Medical│Audit │IA   │              │
│ │Dashboards│Logs│Traces│            │
│ └─────┘ └─────┘ └─────┘             │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ 🤖 LANGSMITH + LOGFIRE          │ │
│ │    (PRIMARY MEDICAL LOGGING)    │ │
│ │ • Monitoramento MedGemma 4B     │ │
│ │ • Performance Whisper Large     │ │
│ │ • Métricas FaceNet biométrico   │ │
│ │ • Logs estruturados LGPD        │ │
│ └─────────────────────────────────┘ │
└─────────────────────────────────────┘
```

### **Conformidade Regulatória Brasileira Automatizada**

| **Lei/Regulamentação** | **Componente** | **Automação Implementada** |
|------------------------|----------------|----------------------------|
| **🏥 SUS/DATASUS** | **Prometheus + Grafana** | **Métricas hospitalares automáticas para DATASUS** |
| **⚕️ CFM Resolução 1821/2007** | **Logfire (Primary Medical) + Auditoria** | **Logs prontuários digitais com assinatura ICP-Brasil** |
| **💊 ANVISA RDC 302/2005** | **Tempo + SNGPC** | **Rastreamento substâncias controladas automático** |
| **📋 TISS/ANS Resolução 305** | **Prometheus** | **Indicadores qualidade ANS em tempo real** |
| **🔒 LGPD Lei 13.709** | **Sanitização Global** | **Proteção automática dados sensíveis pacientes** |
| **🌐 Lei 13.787/2018** | **Compliance Engine** | **Telemedicina e portabilidade dados saúde** |

### **Métricas Específicas Saúde Brasileira**

```sh
┌─────────────────────────────────────┐
│ 🇧🇷 MÉTRICAS CONFORMIDADE BRASIL     │
│                                     │
│ 📊 SUS OBRIGATÓRIAS                 │
│  ├─► CNS: Taxa validação 99.5%      │
│  ├─► CNES: Conformidade 100%        │
│  ├─► Ocupação leitos: Tempo real    │
│  └─► Mortalidade: Por departamento  │
│                                     │
│ ⚕️ CFM DIGITAIS                     │
│  ├─► Assinaturas ICP-Brasil 100%    │
│  ├─► Integridade prontuários        │
│  ├─► Auditoria médica 20 anos       │
│  └─► Validação CRM automática       │
│                                     │
│ 💊 ANVISA FARMACÊUTICA              │
│  ├─► SNGPC: Relatórios automáticos  │
│  ├─► NOTIVISA: Eventos adversos     │
│  ├─► Substâncias controladas A1-C1  │
│  └─► Farmacovigilância 15 anos      │
│                                     │
│ 📋 ANS QUALIDADE                    │
│  ├─► TISS: Transações tempo real    │
│  ├─► TUSS: Validação códigos        │
│  ├─► Satisfação paciente            │
│  └─► Indicadores 7 anos retenção    │
│                                     │
│ 🔒 LGPD PROTEÇÃO                    │
│  ├─► Consentimento rastreado        │
│  ├─► Portabilidade FHIR R4 BR       │
│  ├─► Pseudonimização automática     │
│  └─► Direitos titular dados         │
└─────────────────────────────────────┘
```

### **Dashboards Médicos Brasileiros**

**📊 Dashboard Executivo Hospital:**
- Indicadores SUS obrigatórios tempo real
- Conformidade regulatória % por área
- Custos operacionais vs meta orçamentária
- Qualidade assistencial ANS

**⚕️ Dashboard Médico Departamental:**
- Métricas específicas especialidade
- Performance IA médica por caso
- Tempo atendimento vs protocolo
- Satisfação paciente departamento

**💊 Dashboard Farmacêutico:**
- Dispensação substâncias controladas
- Eventos adversos NOTIVISA
- Estoque medicamentos críticos
- Interações medicamentosas IA

**🔍 Dashboard Conformidade Legal:**
- Status LGPD por categoria dados
- Auditoria CFM prontuários digitais
- Relatórios ANVISA pendentes
- Métricas SUS para DATASUS

### **Alertas Críticos Regulatórios**

```yaml
# Alertas Conformidade Brasileira
alertas_criticos:
  sus_compliance:
    - "Taxa ocupação leitos >95% (SUS)"
    - "Tempo permanência >10 dias (DATASUS)"
    
  cfm_compliance:
    - "Assinatura digital inválida (CFM)"
    - "Integridade prontuário violada"
    
  anvisa_compliance:
    - "Substância controlada não relatada (SNGPC)"
    - "Evento adverso não notificado (NOTIVISA)"
    
  lgpd_compliance:
    - "Dados sensíveis não pseudonimizados"
    - "Solicitação titular não atendida"
```

### **Retenção Dados Conformidade Legal**

```yaml
# Políticas Retenção Brasil
retencao_legal:
  prontuarios_medicos: "20 anos"     # CFM 1821/2007
  substancias_controladas: "5 anos"  # ANVISA RDC 344/1998
  eventos_adversos: "15 anos"        # ANVISA Farmacovigilância
  dados_sus: "5 anos"                # DATASUS
  auditoria_cfm: "10 anos"           # CFM supervisão
  indicadores_ans: "7 anos"          # ANS qualidade
  logs_lgpd: "5 anos"                # LGPD consentimento
```
