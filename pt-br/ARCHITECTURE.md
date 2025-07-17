# Prontuário - Arquitetura Híbrida de Alta Disponibilidade com Dual M3 Ultra

## 🎯 Visão Geral da Arquitetura

Prontuário utiliza uma **arquitetura híbrida de alta disponibilidade** que combina **sistemas duplos Mac Studio M3 Ultra** com **fallback automatizado GCP**, otimizada para dispositivos iPhone únicos e operações hospitalares de 200 usuários diários com **garantia de 99,9% de uptime**.

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
          ▼ (Balanceamento de Carga)
┌─────────────────────────────────────┐
│ ⚖️ BALANCEADOR HA                   │
│ • HAProxy/NGINX ingress             │
│ • Health checks a cada 5 segundos   │
│ • Failover automático <30 segundos  │
│ • Fallback GCP se ambos falharem    │
└─────────────────────────────────────┘
    │                     │
    ▼                     ▼
┌─────────────────┐ ┌─────────────────┐
│ 🖥️ PRIMARY      │ │ 🖥️ SECONDARY    │
│ MAC STUDIO      │ │ MAC STUDIO      │
│ M3 ULTRA        │ │ M3 ULTRA        │
│                 │ │                 │
│ 🧠 32-core CPU  │ │ 🧠 32-core CPU  │
│ 🤖 80-core GPU  │ │ 🤖 80-core GPU  │
│ 📊 512GB RAM    │ │ 📊 512GB RAM    │
│ 💾 8TB SSD      │ │ 💾 8TB SSD      │
│                 │ │                 │
│ Status: ATIVO   │ │ Status: STANDBY │
└─────────────────┘ └─────────────────┘
          │                     │
          └─────────┬───────────┘
                    │ (Replicação em tempo real)
          ▼
┌─────────────────────────────────────┐
│ ☁️ SERVIÇOS FALLBACK GCP            │
│                                     │
│ 🎛️ Cluster GKE Autopilot            │
│ 💾 Cloud Storage                    │
│ 🗄️ Cloud SQL                        │
│ 📋 Operações Emergenciais Apenas    │
│                                     │
└─────────────────────────────────────┘
```

---

## 🖥️ Infraestrutura Local de Alta Disponibilidade

### 🔧 Configuração Cluster Dual Mac Studio M3 Ultra

### **Especificações Hardware (Por Nó)**

| **Componente** | **Especificação** | **Quantidade** | **Função** |
|----------------|-------------------|----------------|------------|
| **🖥️ Mac Studio M3 Ultra** | **CPU 32-core (24P+8E), GPU 80-core, 512GB RAM** | **2 unidades** | **Hosts Kubernetes HA** |
| **🧠 Neural Engine** | **32-core, 36 TOPS** | **2 unidades** | **Processamento IA Acelerado** |
| **💾 Internal Storage** | **8TB SSD** | **2 unidades** | **Armazenamento local replicado** |
| **🌐 Network** | **10Gb Ethernet + Thunderbolt 5** | **Integrado** | **Conectividade HA ultra-rápida** |

#### **Configuração Kubernetes de Alta Disponibilidade**

```sh
┌─────────────────────────────────────────────────────────┐
│ 🔄 CLUSTER KUBERNETES DE ALTA DISPONIBILIDADE           │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🎛️ CONTROL PLANE (Replicado entre ambos Mac Studios)   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ MAC STUDIO PRIMÁRIO       │  MAC STUDIO SECUNDÁRIO   │ │
│ │ ┌─────────────────────┐   │  ┌─────────────────────┐ │ │
│ │ │ 🎛️ API Server       │◄──┼──┤ 🎛️ API Server       │ │ │
│ │ │ 📊 etcd (Leader)     │◄──┼──┤ 📊 etcd (Follower)  │ │ │
│ │ │ ⚙️ Scheduler         │◄──┼──┤ ⚙️ Scheduler         │ │ │
│ │ │ 🔧 Controller Mgr    │◄──┼──┤ 🔧 Controller Mgr    │ │ │
│ │ └─────────────────────┘   │  └─────────────────────┘ │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 🔄 WORKER NODES (Cargas Distribuídas)                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🤖 CARGAS IA             │  🤖 CARGAS IA (Réplica)    │ │
│ │ ├─► MedGemma 4B          │  ├─► MedGemma 4B (Sync)    │ │
│ │ ├─► Whisper Large        │  ├─► Whisper Large (Sync)  │ │
│ │ └─► FaceNet Auth         │  └─► FaceNet Auth (Sync)   │ │
│ │                          │                           │ │
│ │ 📡 SERVIÇOS API          │  📡 SERVIÇOS API (Réplica) │ │
│ │ ├─► API Chat Médico      │  ├─► API Chat Médico       │ │
│ │ ├─► Processamento Voz    │  ├─► Processamento Voz     │ │
│ │ └─► Autenticação         │  └─► Autenticação          │ │
│ │                          │                           │ │
│ │ 📊 SERVIÇOS DADOS        │  📊 SERVIÇOS DADOS (Sync)  │ │
│ │ ├─► PostgreSQL Primário  │  ├─► PostgreSQL Réplica    │ │
│ │ ├─► Redis Cluster        │  ├─► Redis Cluster         │ │
│ │ └─► Document Storage     │  └─► Document Storage      │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### **🔄 Configuração Replicação Tempo Real**

```yaml
# Replicação Dados Alta Disponibilidade
replication_config:
  postgresql:
    type: "streaming_replication"
    sync_mode: "synchronous"
    replica_lag_max: "100ms"
    auto_failover: true
    
  redis:
    type: "redis_cluster"
    master_slave_replication: true
    sentinel_monitoring: true
    auto_failover: true
    
  storage:
    type: "real_time_sync"
    method: "rsync_continuous"
    sync_interval: "5_seconds"
    conflict_resolution: "primary_wins"
    
  ai_models:
    type: "model_sync"
    method: "checksum_validation"
    sync_frequency: "on_update"
    model_integrity_check: true
```

---

## 🔄 Implementação MVP Alta Disponibilidade

### **🏥 Fase MVP: Configuração Dual Mac Studio**

```sh
┌─────────────────────────────────────┐
│ 📱 iPhone App (200 usuários)        │
│  │ 🏥 MVP: WLAN + ACESSO HA         │
│  ▼ 📡 Hospital WiFi 6E (Internal)   │
│ ⚖️ Load Balancer (HAProxy)          │
│  │                                  │
│  ├─► 🖥️ Mac Studio M3 Ultra PRIMÁRIO│
│  │   ├─► 🧠 Whisper Large STT       │
│  │   ├─► 🤖 MedGemma 4B Medical LLM │
│  │   ├─► 🔬 Análise Imagem Médica   │
│  │   └─► 📊 Analytics Saúde Avançado│
│  │                                  │
│  └─► 🖥️ Mac Studio M3 Ultra SECUNDÁRIO│
│      ├─► 🧠 Whisper Large STT (Sync) │
│      ├─► 🤖 MedGemma 4B (Réplica)   │
│      ├─► 🔬 Análise Imagem (Réplica)│
│      └─► 📊 Analytics Saúde (Sync)  │
│                                     │
│ 🚫 Serviços GCP (MVP: EMERGÊNCIA APENAS)│
│  │ ⚠️ FALLBACK SE AMBOS MAC FALHAREM│
│  ├─► 💾 Cloud Storage Emergencial   │
│  ├─► 🗄️ Cloud Database Emergencial  │
│  └─► 📋 Operações Médicas Básicas   │
│                                     │
│  ▼ 📡 Resposta WiFi Interna         │
│ 📱 Resposta Aprimorada para iPhone  │
│                                     │
└─────────────────────────────────────┘
```

### **🔄 Processo Failover Automático**

```yaml
# Configuração Failover
failover_config:
  health_checks:
    interval: "5_segundos"
    timeout: "2_segundos"
    failure_threshold: 3
    
  primary_to_secondary:
    trigger: "falha_health_primario"
    failover_time: "< 30_segundos"
    data_sync_check: true
    user_notification: false  # Transparente
    
  dual_failure_to_gcp:
    trigger: "ambos_mac_studios_down"
    fallback_mode: "operacoes_emergencia"
    gcp_services:
      - "chat_medico_basico"
      - "lookup_paciente_emergencia" 
      - "alertas_criticos_apenas"
    reduced_functionality: true
    user_notification: true
    estimated_recovery: "2-4_horas"
```

---

## ☁️ Arquitetura Fallback Emergencial GCP

### **🚨 Serviços Cloud Emergenciais**

```sh
┌─────────────────────────────────────────────────────────┐
│ ☁️ SERVIÇOS FALLBACK EMERGENCIAL GCP                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🎛️ CLUSTER EMERGENCIAL GKE AUTOPILOT                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🤖 SERVIÇOS IA REDUZIDOS                            │ │
│ │ • MedGemma 4B (Cloud TPU v4)                        │ │
│ │ • Whisper Large (Cloud GPU)                         │ │
│ │ • Chat médico básico apenas                         │ │
│ │ • Lookup paciente emergencial                       │ │
│ │ • Processamento alertas críticos                    │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 💾 CLOUD STORAGE                                        │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • Backup dados paciente emergencial                 │ │
│ │ • Documentos médicos críticos                       │ │
│ │ • Backups configuração sistema                      │ │
│ │ • Logs auditoria e dados compliance                 │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 🗄️ CLOUD SQL                                            │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • PostgreSQL 15 (Alta Disponibilidade)              │ │
│ │ • Backup tempo real dos Mac Studios                 │ │
│ │ • Operações read/write emergenciais                 │ │
│ │ • Processamento dados compatível LGPD               │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 🔐 SEGURANÇA & COMPLIANCE                               │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • Networking VPC privado                            │ │
│ │ • IAM & RBAC (permissões reduzidas)                 │ │
│ │ • Audit logging e monitoramento                     │ │
│ │ │ • Compliance residência dados brasileira           │ │
│ │ • Controles acesso emergencial apenas               │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### **Limitações Operacionais Emergenciais**

| **Serviço** | **Local (Mac Studio)** | **Fallback Emergencial GCP** |
|-------------|------------------------|-------------------------------|
| **Chat Médico** | MedGemma 4B completo | Respostas médicas básicas apenas |
| **Processamento Voz** | Whisper Large completo | Transcrição voz limitada |
| **Autenticação Facial** | FaceNet local | Username/password apenas |
| **Acesso Dados Paciente** | Database completo | Registros emergenciais apenas |
| **Performance IA** | Processamento local ótimo | Processamento cloud reduzido |
| **Tempo Resposta** | <100ms | 200-500ms |
| **Usuários Concorrentes** | 200 capacidade total | 50 usuários emergenciais |
| **Conjunto Funcionalidades** | 100% funcionalidade | 25% funções emergenciais |

---

## 📊 Monitoramento Alta Disponibilidade

### **Monitoramento Saúde Sistema**

```yaml
# Monitoramento Prometheus Alta Disponibilidade
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: medical-ha-alerts
  namespace: medical-monitoring
spec:
  groups:
  - name: alta_disponibilidade
    rules:
    
    # Saúde Mac Studio
    - alert: MacStudioDown
      expr: up{job="mac-studio"} == 0
      for: 30s
      labels:
        severity: critical
        service: plataforma-medica
      annotations:
        summary: "Mac Studio {{ $labels.instance }} está inativo"
        description: "Mac Studio está inativo há mais de 30 segundos"
        
    # Lag Replicação Database
    - alert: DatabaseReplicationLag
      expr: postgresql_replication_lag_seconds > 10
      for: 1m
      labels:
        severity: warning
        service: database
      annotations:
        summary: "Lag replicação PostgreSQL está alto"
        description: "Lag replicação é {{ $value }} segundos"
        
    # Ativação Fallback GCP
    - alert: GCPFallbackActivated
      expr: gcp_fallback_active == 1
      for: 0s
      labels:
        severity: critical
        service: fallback-emergencial
      annotations:
        summary: "Fallback Emergencial GCP Ativado"
        description: "Ambos Mac Studios estão inativos, fallback GCP está ativo"
        
    # Saúde Cluster Alta Disponibilidade
    - alert: HAClusterDegraded
      expr: (count(up{job="mac-studio"} == 1) / count(up{job="mac-studio"})) < 0.5
      for: 1m
      labels:
        severity: critical
        service: ha-cluster
      annotations:
        summary: "Cluster HA está degradado"
        description: "Menos de 50% dos nós Mac Studio estão disponíveis"
```

---

## 🔄 Testes Failover e Procedimentos

### **Cronograma Testes Recuperação Desastre**

| **Tipo Teste** | **Frequência** | **Duração** | **Downtime** |
|----------------|----------------|-------------|--------------|
| **Validação Health Check** | Diário | 5 minutos | 0 segundos |
| **Teste Failover Secundário** | Semanal | 30 minutos | <30 segundos |
| **Teste Fallback GCP** | Mensal | 2 horas | <5 minutos |
| **Simulação DR Completa** | Trimestral | 4 horas | Manutenção planejada |
| **Auditoria DR Anual** | Anual | 8 horas | Manutenção planejada |

### **Objetivos Tempo Recuperação (RTO)**

| **Cenário Falha** | **Tempo Recuperação** | **Perda Dados (RPO)** |
|-------------------|-----------------------|----------------------|
| **Falha Mac Studio primário** | <30 segundos | 0 segundos |
| **Falha Mac Studio secundário** | N/A (redundância) | 0 segundos |
| **Falha ambos Mac Studios** | <5 minutos | <60 segundos |
| **Desastre site completo** | <2 horas | <5 minutos |

---

**💡 Esta arquitetura de Alta Disponibilidade garante 99,9% de uptime para a plataforma Prontuário com failover automático entre sistemas dual Mac Studio M3 Ultra e fallback emergencial GCP para máxima continuidade do cuidado ao paciente!** 🏥⚡
