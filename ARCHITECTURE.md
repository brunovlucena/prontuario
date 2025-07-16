# Prontuário - Arquitetura Híbrida Local/Cloud

## 🎯 Visão Geral da Arquitetura

Prontuário utiliza uma arquitetura híbrida que combina processamento de IA local em Mac Minis com serviços em nuvem GCP, otimizada para dispositivos iPhone únicos e operações hospitalares de 200 usuários diários.

---

## 🏗️ Arquitetura de Alto Nível

```mermaid
flowchart TD
    iPhone["📱 DISPOSITIVOS iPhone<br/>200 usuários diários<br/>Interface médica nativa"]
    
    LocalCluster["🖥️ CLUSTER K8S LOCAL<br/>3x Mac Mini M4<br/>Processamento IA On-Premise"]
    
    GCPServices["☁️ SERVIÇOS GCP<br/>GKE | Cloud Storage | Cloud SQL<br/>Dados & Orquestração"]

    iPhone --> LocalCluster
    LocalCluster <--> GCPServices

    style iPhone fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style LocalCluster fill:#C62828,stroke:#B71C1C,stroke-width:4px,color:#fff
    style GCPServices fill:#00695C,stroke:#004D40,stroke-width:4px,color:#fff
```

---

# 🖥️ INFRAESTRUTURA LOCAL KUBERNETES

## 🔧 Cluster Mac Mini Local

### **Especificações Hardware**

| **Componente** | **Especificação** | **Quantidade** | **Função** |
|----------------|-------------------|----------------|------------|
| **🖥️ Mac Mini M4** | **CPU 10-core, GPU 10-core, 24GB RAM** | **3 unidades** | **Nós Worker K8s** |
| **🖥️ Mac Mini M4 Pro** | **CPU 12-core, GPU 16-core, 32GB RAM** | **1 unidade** | **Nó Control Plane** |
| **💾 Network Storage** | **2TB NVMe cada** | **4 unidades** | **Armazenamento distribuído** |
| **🌐 Network Switch** | **10Gb Ethernet** | **1 unidade** | **Rede cluster** |

### **Configuração Kubernetes**

```mermaid
flowchart TD
    ControlPlane["🎛️ CONTROL PLANE<br/>Mac Mini M4 Pro<br/>API Server | etcd | Scheduler"]
    
    Worker1["⚙️ WORKER NODE 1<br/>Mac Mini M4<br/>AI Inference Pods"]
    Worker2["⚙️ WORKER NODE 2<br/>Mac Mini M4<br/>API Backend Pods"]
    Worker3["⚙️ WORKER NODE 3<br/>Mac Mini M4<br/>Data Processing Pods"]
    
    Storage["💾 DISTRIBUTED STORAGE<br/>Longhorn CSI<br/>Replicação 3x"]
    
    LoadBalancer["⚖️ LOAD BALANCER<br/>MetalLB<br/>IP Pool Local"]

    ControlPlane --> Worker1
    ControlPlane --> Worker2
    ControlPlane --> Worker3
    
    Worker1 --> Storage
    Worker2 --> Storage
    Worker3 --> Storage
    
    LoadBalancer --> Worker1
    LoadBalancer --> Worker2
    LoadBalancer --> Worker3

    style ControlPlane fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style Worker1 fill:#C62828,stroke:#B71C1C,stroke-width:3px,color:#fff
    style Worker2 fill:#C62828,stroke:#B71C1C,stroke-width:3px,color:#fff
    style Worker3 fill:#C62828,stroke:#B71C1C,stroke-width:3px,color:#fff
    style Storage fill:#00695C,stroke:#004D40,stroke-width:3px,color:#fff
    style LoadBalancer fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
```

---

# ☁️ SERVIÇOS GOOGLE CLOUD PLATFORM

## 🔗 Integração GCP/GKE

### **Serviços Utilizados**

| **Serviço GCP** | **Função** | **Configuração** |
|-----------------|------------|------------------|
| **🚀 GKE Autopilot** | **Orquestração Cloud** | **Multi-zona us-central1** |
| **🗄️ Cloud SQL** | **Banco Dados Principal** | **PostgreSQL 15, HA** |
| **📦 Cloud Storage** | **Armazenamento Objetos** | **Multi-regional, HIPAA** |
| **🔐 Secret Manager** | **Gestão Credenciais** | **Rotação automática** |
| **📊 Cloud Monitoring** | **Observabilidade** | **Métricas + Logs** |
| **🌐 Cloud Load Balancing** | **Distribuição Tráfego** | **Global HTTPS** |

### **Arquitetura Híbrida Local-Cloud**

```mermaid
flowchart LR
    LocalK8s["🖥️ CLUSTER LOCAL<br/>Mac Minis K8s<br/>AI Workloads"]
    
    VPN["🔒 VPN SITE-TO-SITE<br/>WireGuard<br/>Conexão segura"]
    
    GKE["☁️ GKE CLUSTER<br/>Autopilot<br/>Serviços Backend"]
    
    CloudSQL["🗄️ CLOUD SQL<br/>PostgreSQL<br/>Dados Principais"]
    
    CloudStorage["📦 CLOUD STORAGE<br/>Buckets<br/>Documentos/Imagens"]

    LocalK8s <--> VPN
    VPN <--> GKE
    GKE <--> CloudSQL
    GKE <--> CloudStorage
    LocalK8s -.-> CloudSQL
    LocalK8s -.-> CloudStorage

    style LocalK8s fill:#C62828,stroke:#B71C1C,stroke-width:4px,color:#fff
    style VPN fill:#2E7D32,stroke:#1B5E20,stroke-width:3px,color:#fff
    style GKE fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style CloudSQL fill:#00695C,stroke:#004D40,stroke-width:3px,color:#fff
    style CloudStorage fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
```

---

# 📱 ARQUITETURA APLICAÇÃO

## 🎯 Stack Tecnológico

### **Backend Services (Golang)**

```mermaid
flowchart TD
    iPhone["📱 iPhone App<br/>Swift/SwiftUI<br/>Interface nativa"]
    
    APIGateway["🌐 API GATEWAY<br/>Go + Gin/Fiber<br/>Autenticação & Routing"]
    
    UserService["👥 USER SERVICE<br/>Golang<br/>Gestão usuários/auth"]
    
    PatientService["🏥 PATIENT SERVICE<br/>Golang<br/>Dados pacientes"]
    
    AIService["🤖 AI SERVICE<br/>Python FastAPI<br/>Processamento IA"]

    iPhone --> APIGateway
    APIGateway --> UserService
    APIGateway --> PatientService
    APIGateway --> AIService

    style iPhone fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style APIGateway fill:#2E7D32,stroke:#1B5E20,stroke-width:4px,color:#fff
    style UserService fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
    style PatientService fill:#C62828,stroke:#B71C1C,stroke-width:3px,color:#fff
    style AIService fill:#6A1B9A,stroke:#4A148C,stroke-width:3px,color:#fff
```

### **AI Workloads (Python)**

```mermaid
flowchart TD
    MedGemma["🧠 MEDGEMMA MODEL<br/>Python/PyTorch<br/>Conversação médica"]
    
    VoiceProcessor["🎤 VOICE PROCESSOR<br/>Python/Whisper<br/>Speech-to-Text"]
    
    DocumentAI["📄 DOCUMENT AI<br/>Python/spaCy<br/>Análise documentos"]
    
    DrugChecker["💊 DRUG CHECKER<br/>Python/pandas<br/>Interações medicamentosas"]
    
    Scheduler["⏰ AI SCHEDULER<br/>Python/Celery<br/>Jobs assíncronos"]

    MedGemma --> Scheduler
    VoiceProcessor --> Scheduler
    DocumentAI --> Scheduler
    DrugChecker --> Scheduler

    style MedGemma fill:#C62828,stroke:#B71C1C,stroke-width:4px,color:#fff
    style VoiceProcessor fill:#1565C0,stroke:#0D47A1,stroke-width:3px,color:#fff
    style DocumentAI fill:#00695C,stroke:#004D40,stroke-width:3px,color:#fff
    style DrugChecker fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
    style Scheduler fill:#6A1B9A,stroke:#4A148C,stroke-width:3px,color:#fff
```

---

# 🗄️ ARQUITETURA DADOS

## 📊 Estratégia Dados Híbrida

### **Distribuição de Dados**

| **Tipo Dado** | **Localização** | **Tecnologia** | **Justificativa** |
|---------------|-----------------|----------------|-------------------|
| **🔒 Dados Pacientes Sensíveis** | **Local K8s** | **PostgreSQL + Encryption** | **Compliance LGPD** |
| **📊 Metadados & Analytics** | **Cloud SQL** | **PostgreSQL HA** | **Análises agregadas** |
| **📁 Documentos/Imagens** | **Cloud Storage** | **Buckets Regionais** | **Escalabilidade** |
| **🧠 Modelos IA** | **Local Storage** | **Longhorn CSI** | **Performance inference** |
| **📈 Logs/Métricas** | **Cloud Logging** | **Stackdriver** | **Observabilidade** |

### **Fluxo de Dados**

```mermaid
flowchart TD
    iPhone["📱 DADOS iPhone<br/>Entrada usuário<br/>Voz/Texto/Imagens"]
    
    LocalDB["🗄️ DB LOCAL<br/>PostgreSQL<br/>Dados sensíveis pacientes"]
    
    CloudDB["☁️ CLOUD SQL<br/>PostgreSQL<br/>Metadados/Analytics"]
    
    CloudStorage["📦 CLOUD STORAGE<br/>GCS Buckets<br/>Documentos/Backups"]
    
    AIModels["🧠 MODELOS IA<br/>Local Storage<br/>MLflow Registry"]

    iPhone --> LocalDB
    LocalDB --> CloudDB
    iPhone --> CloudStorage
    LocalDB --> AIModels
    CloudDB --> CloudStorage

    style iPhone fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style LocalDB fill:#C62828,stroke:#B71C1C,stroke-width:4px,color:#fff
    style CloudDB fill:#00695C,stroke:#004D40,stroke-width:3px,color:#fff
    style CloudStorage fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
    style AIModels fill:#6A1B9A,stroke:#4A148C,stroke-width:3px,color:#fff
```

---

# 🔐 SEGURANÇA & COMPLIANCE

## 🛡️ Modelo Segurança Zero Trust

### **Autenticação & Autorização**

```mermaid
flowchart TD
    iPhone["📱 iPhone Device<br/>Biometric + PIN<br/>Device Certificate"]
    
    OAuth["🔐 OAuth 2.0/OIDC<br/>Google Identity<br/>JWT Tokens"]
    
    RBAC["👥 RBAC System<br/>Kubernetes RBAC<br/>Role-based access"]
    
    mTLS["🔒 mTLS<br/>Mutual TLS<br/>Service-to-service"]
    
    SecretMgmt["🗝️ SECRET MANAGEMENT<br/>Google Secret Manager<br/>Vault Integration"]

    iPhone --> OAuth
    OAuth --> RBAC
    RBAC --> mTLS
    mTLS --> SecretMgmt

    style iPhone fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style OAuth fill:#2E7D32,stroke:#1B5E20,stroke-width:4px,color:#fff
    style RBAC fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
    style mTLS fill:#C62828,stroke:#B71C1C,stroke-width:3px,color:#fff
    style SecretMgmt fill:#6A1B9A,stroke:#4A148C,stroke-width:3px,color:#fff
```

### **Compliance LGPD/HIPAA**

| **Requisito** | **Implementação** | **Tecnologia** |
|---------------|-------------------|----------------|
| **🔒 Criptografia em Repouso** | **AES-256** | **LUKS + TLS** |
| **🚀 Criptografia em Trânsito** | **TLS 1.3** | **cert-manager** |
| **📋 Auditoria Completa** | **Logs estruturados** | **Fluent Bit + Loki** |
| **🗑️ Direito ao Esquecimento** | **Soft delete + Purge** | **Kubernetes Jobs** |
| **📊 Relatórios Compliance** | **Dashboards automatizados** | **Grafana + Prometheus** |

---

# 📱 APLICAÇÃO IPHONE

## 🎯 Arquitetura Mobile

### **Aplicação iPhone Nativa**

```mermaid
flowchart TD
    UI["🖼️ SwiftUI INTERFACE<br/>Interface médica nativa<br/>Dark/Light mode"]
    
    Core["⚙️ CORE LAYER<br/>Swift<br/>Business Logic"]
    
    Network["🌐 NETWORK LAYER<br/>URLSession + Combine<br/>API Communication"]
    
    Storage["💾 LOCAL STORAGE<br/>Core Data + Keychain<br/>Cache + Security"]
    
    Voice["🎤 VOICE MODULE<br/>Speech Framework<br/>Real-time transcription"]
    
    Camera["📷 CAMERA MODULE<br/>AVFoundation<br/>Document scanning"]

    UI --> Core
    Core --> Network
    Core --> Storage
    Core --> Voice
    Core --> Camera

    style UI fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style Core fill:#2E7D32,stroke:#1B5E20,stroke-width:4px,color:#fff
    style Network fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
    style Storage fill:#C62828,stroke:#B71C1C,stroke-width:3px,color:#fff
    style Voice fill:#6A1B9A,stroke:#4A148C,stroke-width:3px,color:#fff
    style Camera fill:#00695C,stroke:#004D40,stroke-width:3px,color:#fff
```

### **Features iPhone App**

| **Feature** | **Tecnologia** | **Descrição** |
|-------------|----------------|---------------|
| **🎤 Documentação por Voz** | **Speech + NLP** | **Transcrição médica em tempo real** |
| **📱 Interface Médica** | **SwiftUI + HealthKit** | **UI otimizada para workflows médicos** |
| **🔒 Autenticação Biométrica** | **Face ID + Touch ID** | **Segurança máxima dispositivo** |
| **📊 Visualização Dados** | **Charts + Core Graphics** | **Gráficos laboratório/sinais vitais** |
| **📷 Scan Documentos** | **VisionKit + ML** | **OCR documentos médicos** |
| **🔄 Sync Offline** | **Core Data + CloudKit** | **Funcionamento sem conexão** |

---

# 🚀 DEPLOYMENT & DEVOPS

## 🔄 Pipeline CI/CD

### **GitOps Workflow**

```mermaid
flowchart LR
    Dev["👨‍💻 DEVELOPER<br/>Local development<br/>Go/Python/Swift"]
    
    Git["📂 GIT REPOSITORY<br/>GitHub<br/>Source control"]
    
    CI["🔄 GITHUB ACTIONS<br/>Build/Test/Scan<br/>Multi-language pipeline"]
    
    Registry["📦 CONTAINER REGISTRY<br/>Google Artifact Registry<br/>Multi-arch images"]
    
    ArgoCD["🚀 ARGOCD<br/>GitOps deployment<br/>K8s manifests"]
    
    LocalK8s["🖥️ LOCAL K8S<br/>Mac Mini cluster<br/>AI workloads"]
    
    GKE["☁️ GKE<br/>Cloud cluster<br/>Backend services"]

    Dev --> Git
    Git --> CI
    CI --> Registry
    Registry --> ArgoCD
    ArgoCD --> LocalK8s
    ArgoCD --> GKE

    style Dev fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style Git fill:#2E7D32,stroke:#1B5E20,stroke-width:3px,color:#fff
    style CI fill:#F57C00,stroke:#E65100,stroke-width:3px,color:#fff
    style Registry fill:#C62828,stroke:#B71C1C,stroke-width:3px,color:#fff
    style ArgoCD fill:#6A1B9A,stroke:#4A148C,stroke-width:4px,color:#fff
    style LocalK8s fill:#00695C,stroke:#004D40,stroke-width:3px,color:#fff
    style GKE fill:#37474F,stroke:#263238,stroke-width:3px,color:#fff
```

### **Estratégia Deployment**

| **Ambiente** | **Localização** | **Estratégia** | **Rollback** |
|--------------|-----------------|----------------|--------------|
| **🧪 Development** | **Local K8s** | **Rolling update** | **Automático** |
| **🔍 Staging** | **GKE** | **Blue/Green** | **Manual approval** |
| **🏥 Production** | **Híbrido** | **Canary** | **Automatic rollback** |

---

# 📊 OBSERVABILIDADE

## 🔍 Monitoring Stack

### **Métricas & Alertas**

```mermaid
flowchart TD
    Apps["📱 APPLICATIONS<br/>Go/Python/Swift<br/>Métricas customizadas"]
    
    Prometheus["📊 PROMETHEUS<br/>Métricas collection<br/>PromQL queries"]
    
    Grafana["📈 GRAFANA<br/>Dashboards<br/>Visualização"]
    
    AlertManager["🚨 ALERTMANAGER<br/>Alertas inteligentes<br/>Slack/PagerDuty"]
    
    Loki["📝 LOKI<br/>Log aggregation<br/>LogQL queries"]
    
    Tempo["🔍 TEMPO<br/>Distributed tracing<br/>Performance analysis"]

    Apps --> Prometheus
    Apps --> Loki
    Apps --> Tempo
    
    Prometheus --> Grafana
    Prometheus --> AlertManager
    Loki --> Grafana
    Tempo --> Grafana

    style Apps fill:#1565C0,stroke:#0D47A1,stroke-width:4px,color:#fff
    style Prometheus fill:#C62828,stroke:#B71C1C,stroke-width:4px,color:#fff
    style Grafana fill:#F57C00,stroke:#E65100,stroke-width:4px,color:#fff
    style AlertManager fill:#2E7D32,stroke:#1B5E20,stroke-width:3px,color:#fff
    style Loki fill:#6A1B9A,stroke:#4A148C,stroke-width:3px,color:#fff
    style Tempo fill:#00695C,stroke:#004D40,stroke-width:3px,color:#fff
```

---

---

**🏥 ARQUITETURA HÍBRIDA PARA MÁXIMA PERFORMANCE E COMPLIANCE**

**📱 IPHONE-FIRST | 🖥️ K8S LOCAL | ☁️ GCP CLOUD**