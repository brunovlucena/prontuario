# Prontuário - Arquitetura Híbrida Local/Cloud

## 🎯 Visão Geral da Arquitetura

Prontuário utiliza uma arquitetura híbrida que combina processamento de IA local em Mac Minis com serviços em nuvem GCP, otimizada para dispositivos iPhone únicos e operações hospitalares de usuários diários.

---

## 🏗️ Arquitetura de Alto Nível

```
┌─────────────────────────────────────┐
│ 📱 DISPOSITIVOS iPhone              │
│                                     │
│ 👨‍⚕️200 usuários diários             │
│ 🩺 Interface médica nativa          │
│ 🎤 Comandos de voz                  │
│                                     │
│          │                         │
│          ▼                         │
└─────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────┐
│ 🖥️ CLUSTER K8S LOCAL               │
│                                     │
│ 3x Mac Mini M4                      │
│ 🤖 Processamento IA On-Premise     │
│ 📊 APIs Backend                    │
│ 🔄 Orquestração local              │
│                                     │
│          ▲                         │
│          ▼                         │
└─────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────┐
│ ☁️ SERVIÇOS GCP                    │
│                                     │
│ 🎛️ GKE + Pulumi                    │
│ 💾 Cloud Storage                   │
│ 🗄️ Cloud SQL                       │
│ 📋 Dados & Orquestração            │
│                                     │
└─────────────────────────────────────┘
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

```
┌─────────────────────────────────────┐
│ 🎛️ CONTROL PLANE                    │
│                                     │
│ Mac Mini M4 Pro                     │
│ • API Server                        │
│ • etcd                              │
│ • Scheduler                         │
│ • Controller Manager                │
│                                     │
│          │                         │
│          ▼                         │
└─────────────────────────────────────┘
          │
   ┌──────┼──────┐
   ▼      ▼      ▼
┌─────┐ ┌─────┐ ┌─────┐
│⚙️ W1│ │⚙️ W2│ │⚙️ W3│
│     │ │     │ │     │
│🤖 AI│ │📡API│ │📊Data│
│Pods │ │Pods │ │Pods │
└─────┘ └─────┘ └─────┘
Mac M4   Mac M4  Mac M4
```

---

## 🤖 Distribuição de Workloads IA

### **Especialização por Nó**

```
┌─────────────────────────────────────┐
│ 🤖 WORKER NODE 1 - IA INFERENCE     │
├─────────────────────────────────────┤
│ • 🧠 Llama 3.2 1B                   │
│ • 🎤 Whisper Small                  │
│ • 🗣️ Core ML Voice                  │
│ • 📊 Health Analytics               │
│                                     │
│ Memory: 24GB                        │
│ GPU: 10-core (IA especializada)     │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ 📡 WORKER NODE 2 - API BACKEND      │
├─────────────────────────────────────┤
│ • 🌐 FastAPI Server                 │
│ • 🔐 Auth Service                   │
│ • 📝 Document Service               │
│ • 🔄 Queue Processing               │
│                                     │
│ Memory: 24GB                        │
│ CPU: 10-core (API otimizada)        │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ 📊 WORKER NODE 3 - DATA PROCESSING  │
├─────────────────────────────────────┤
│ • 📈 Analytics Engine               │
│ • 🔍 Search Service                 │
│ • 💾 Cache Layer                    │
│ • 📋 Report Generator               │
│                                     │
│ Memory: 24GB                        │
│ Storage: 2TB NVMe                   │
└─────────────────────────────────────┘
```

---

## 🧠 Stack de IA Local

### **Modelos e Processamento**

```
┌─────────────────────────────────────┐
│ 🧠 STACK IA - INFERENCE LOCAL      │
├─────────────────────────────────────┤
│                                     │
│ 🎤 SPEECH-TO-TEXT                   │
│  ├─► Whisper Small (244MB)          │
│  ├─► Core ML optimized              │
│  └─► Latência: <200ms               │
│                                     │
│ 🤖 LANGUAGE MODEL                   │
│  ├─► Llama 3.2 1B                  │
│  ├─► Medical fine-tuning            │
│  └─► Contexto médico 4K tokens     │
│                                     │
│ 🗣️ TEXT-TO-SPEECH                  │
│  ├─► Core ML Voice                  │
│  ├─► Voz naturalizada              │
│  └─► Latência: <300ms               │
│                                     │
│ 📊 ANALYTICS                        │
│  ├─► Health insights               │
│  ├─► Pattern recognition           │
│  └─► Real-time processing          │
│                                     │
└─────────────────────────────────────┘
```

---

## 📊 Fluxo de Dados

### **Pipeline Processamento**

```
┌─────────────────────────────────────┐
│ 📊 PIPELINE DADOS E PROCESSAMENTO   │
├─────────────────────────────────────┤
│                                     │
│ 📱 iPhone App                       │
│  │                                  │
│  ▼                                  │
│ 🎤 Audio Capture                    │
│  │                                  │
│  ▼                                  │
│ 🖥️ Local K8s Cluster               │
│  │                                  │
│  ├─► 🧠 Whisper STT                │
│  ├─► 🤖 Llama 3.2 LLM              │
│  └─► 📊 Health Analytics           │
│  │                                  │
│  ▼                                  │
│ ☁️ GCP Services                     │
│  │                                  │
│  ├─► 💾 Cloud Storage              │
│  ├─► 🗄️ Cloud SQL                  │
│  └─► 📋 Patient Records            │
│  │                                  │
│  ▼                                  │
│ 📱 Response to iPhone               │
│                                     │
└─────────────────────────────────────┘
```

---

# ☁️ SERVIÇOS GOOGLE CLOUD PLATFORM

## 🎛️ Infraestrutura GCP com Pulumi

### **Componentes Cloud**

```
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

```
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

```
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
│  │  (Nginx Ingress)                │
│  │                                  │
│  ▼  🔀 Route to services            │
│ ⚙️ Kubernetes Services              │
│  │                                  │
│  ├─► 🤖 AI Service                 │
│  │    (Whisper + Llama)             │
│  │                                  │
│  ├─► 📡 API Service                │
│  │    (FastAPI backend)             │
│  │                                  │
│  └─► 📊 Analytics Service          │
│       (Data processing)             │
│  │                                  │
│  ▼  ☁️ Sync to cloud                │
│ 🗄️ GCP Services                     │
│    (Storage + Database)             │
│                                     │
└─────────────────────────────────────┘
```

---

# 🔧 IMPLEMENTAÇÃO E DEPLOYMENT

## 📦 Estratégia de Deployment

### **Ambientes e Pipeline**

```
┌─────────────────────────────────────┐
│ 🚀 PIPELINE DEPLOYMENT              │
├─────────────────────────────────────┤
│                                     │
│ 👨‍💻 Development                      │
│  │  Local Mac dev                   │
│  │  Docker Compose                  │
│  │                                  │
│  ▼  📤 Git Push                     │
│ 🧪 Testing                          │
│  │  Automated tests                 │
│  │  K8s staging cluster             │
│  │                                  │
│  ▼  ✅ Tests pass                   │
│ 🎯 Production                       │
│  │                                  │
│  ├─► 🖥️ Local K8s Cluster          │
│  │    (Mac Mini farm)               │
│  │                                  │
│  └─► ☁️ GCP Services                │
│       (Managed services)            │
│                                     │
│ 📱 App Store                        │
│    iOS app deployment               │
│                                     │
└─────────────────────────────────────┘
```

---

Esta arquitetura híbrida garante processamento de IA rápido e privado localmente, enquanto aproveita a escalabilidade e confiabilidade dos serviços gerenciados do GCP para persistência de dados e orquestração.