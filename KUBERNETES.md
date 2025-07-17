# Medical Record - Kubernetes Manifests

This document contains all Kubernetes configurations for the Real Portuguese Hospital medical AI platform, optimized for Mac Studio M3 Ultra.

---

## 🎯 Configuration Overview

```
┌─────────────────────────────────────────────┐
│ 🏥 REAL PORTUGUESE HOSPITAL - K8S CLUSTER   │
├─────────────────────────────────────────────┤
│                                             │
│ 🖥️ Hardware: Mac Studio M3 Ultra            │
│ • CPU: 32-core (24P+8E)                     │
│ • GPU: 80-core unified                      │
│ • Memory: 512GB                             │
│ • Storage: 8TB SSD                          │
│                                             │
│ ⚙️ Virtual Nodes Configuration:             │
│ • AI Node: 300GB, 50 GPU cores              │
│ • API Node: 100GB, 15 GPU cores             │
│ • Data Node: 100GB, 15 GPU cores            │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 📋 Configuration Index

1. [🖥️ Node Configurations](#-node-configurations)
2. [🤖 AI Service Manifests](#-ai-service-manifests)
3. [📡 API Service Manifests](#-api-service-manifests)
4. [📊 Data Service Manifests](#-data-service-manifests)
5. [🔧 Infrastructure Manifests](#-infrastructure-manifests)
6. [📊 Monitoring & Observability](#-monitoring--observability)
7. [🔐 Security & RBAC](#-security--rbac)

---

## 🖥️ Node Configurations

### **🔄 UPDATED: Corrected Resource Allocation**

```yaml
# ============================================================================
# M3 Ultra Virtual Nodes Configuration - CORRECTED ALLOCATION
# ============================================================================

# AI Inference Node - Primary Workload (60% memory, 62% GPU)
apiVersion: v1
kind: Node
metadata:
  name: m3-ultra-ai-node
  labels:
    kubernetes.io/arch: arm64
    node-role.kubernetes.io/ai-worker: ""
    hardware.type: m3-ultra
    workload: medgemma-inference
    zone: hospital-local
  annotations:
    node.alpha.kubernetes.io/ttl: "0"
    node.medgemma.ai/specialization: "medical-inference"
spec:
  capacity:
    cpu: "16"
    memory: "300Gi"  # 300GB for MedGemma 4B + KV cache
    nvidia.com/gpu: "50"  # 50 GPU cores for AI inference
    neural-engine.apple.com/cores: "20"
    ephemeral-storage: "2Ti"
  allocatable:
    cpu: "15"
    memory: "285Gi"
    nvidia.com/gpu: "48"
    neural-engine.apple.com/cores: "20"
    ephemeral-storage: "1.8Ti"
  taints:
  - key: workload
    value: ai-inference
    effect: NoSchedule

---
# API Backend Node - Web Services (20% memory, 19% GPU)
apiVersion: v1
kind: Node
metadata:
  name: m3-ultra-api-node
  labels:
    kubernetes.io/arch: arm64
    node-role.kubernetes.io/api-worker: ""
    hardware.type: m3-ultra
    workload: api-services
    zone: hospital-local
  annotations:
    node.alpha.kubernetes.io/ttl: "0"
    node.medgemma.ai/specialization: "api-backend"
spec:
  capacity:
    cpu: "8"
    memory: "100Gi"  # Sufficient for API services
    nvidia.com/gpu: "15"  # Light GPU for preprocessing
    neural-engine.apple.com/cores: "6"
    ephemeral-storage: "2Ti"
  allocatable:
    cpu: "7"
    memory: "90Gi"
    nvidia.com/gpu: "14"
    neural-engine.apple.com/cores: "6"
    ephemeral-storage: "1.8Ti"
  taints:
  - key: workload
    value: api-backend
    effect: NoSchedule

---
# Data Processing Node - Analytics & Storage (20% memory, 19% GPU)
apiVersion: v1
kind: Node
metadata:
  name: m3-ultra-data-node
  labels:
    kubernetes.io/arch: arm64
    node-role.kubernetes.io/data-worker: ""
    hardware.type: m3-ultra
    workload: data-processing
    zone: hospital-local
  annotations:
    node.alpha.kubernetes.io/ttl: "0"
    node.medgemma.ai/specialization: "data-analytics"
spec:
  capacity:
    cpu: "8"
    memory: "100Gi"  # Data processing & analytics
    nvidia.com/gpu: "15"  # Light GPU for data processing
    neural-engine.apple.com/cores: "6"
    storage: "8Ti"
    ephemeral-storage: "2Ti"
  allocatable:
    cpu: "7"
    memory: "90Gi"
    nvidia.com/gpu: "14"
    neural-engine.apple.com/cores: "6"
    storage: "7Ti"
    ephemeral-storage: "1.8Ti"
  taints:
  - key: workload
    value: data-processing
    effect: NoSchedule
```

### **📋 REFERENCE: Original Node Configuration (Deprecated)**

<details>
<summary>Click to view original incorrect allocation</summary>

```yaml
# ============================================================================
# DEPRECATED: Original Equal Resource Allocation (Incorrect)
# ============================================================================

# Original AI Node - Equal allocation (WRONG)
apiVersion: v1
kind: Node
metadata:
  name: m3-ultra-ai-node-old
  labels:
    kubernetes.io/arch: arm64
    node-role.kubernetes.io/ai-worker: ""
    hardware.type: m3-ultra
    status: deprecated
spec:
  capacity:
    cpu: "10"
    memory: "170Gi"  # Too little for MedGemma 4B
    nvidia.com/gpu: "25"  # Insufficient GPU allocation
    neural-engine.apple.com/cores: "10"
  allocatable:
    cpu: "9"
    memory: "160Gi"
    nvidia.com/gpu: "24"
    neural-engine.apple.com/cores: "10"

# Original API Node - Over-allocated (WRONG)
apiVersion: v1
kind: Node
metadata:
  name: m3-ultra-api-node-old
  labels:
    kubernetes.io/arch: arm64
    node-role.kubernetes.io/api-worker: ""
    hardware.type: m3-ultra
    status: deprecated
spec:
  capacity:
    cpu: "10"
    memory: "170Gi"  # Too much for API services
    nvidia.com/gpu: "25"  # Wasted GPU allocation
  allocatable:
    cpu: "9"
    memory: "160Gi"
    nvidia.com/gpu: "24"

# Original Data Node - Over-allocated (WRONG)
apiVersion: v1
kind: Node
metadata:
  name: m3-ultra-data-node-old
  labels:
    kubernetes.io/arch: arm64
    node-role.kubernetes.io/data-worker: ""
    hardware.type: m3-ultra
    status: deprecated
spec:
  capacity:
    cpu: "12"
    memory: "170Gi"  # Too much for data processing
    nvidia.com/gpu: "30"  # Wasted GPU allocation
    storage: "8Ti"
  allocatable:
    cpu: "11"
    memory: "160Gi"
    nvidia.com/gpu: "29"
    storage: "7Ti"
```

</details>

---

## 🤖 AI Service Manifests

### **MedGemma 4B Deployment**

```yaml
# ============================================================================
# MedGemma 4B Medical AI Inference Service
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: medgemma-inference
  namespace: medical-ai
  labels:
    app: medgemma
    component: inference
    version: "4b-multimodal"
    hospital: real-portuguese
spec:
  replicas: 1  # Single high-memory pod
  strategy:
    type: Recreate  # Ensure clean restart
  selector:
    matchLabels:
      app: medgemma
      component: inference
  template:
    metadata:
      labels:
        app: medgemma
        component: inference
        version: "4b-multimodal"
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
        prometheus.io/path: "/metrics"
    spec:
      nodeSelector:
        workload: medgemma-inference
      tolerations:
      - key: workload
        value: ai-inference
        effect: NoSchedule
      containers:
      - name: medgemma-server
        image: gcr.io/medical-record-ai/medgemma:4b-latest
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        - containerPort: 8081
          name: grpc
        - containerPort: 9090
          name: metrics
        env:
        - name: MODEL_NAME
          value: "medgemma-4b-multimodal"
        - name: MODEL_PATH
          value: "/models/medgemma-4b"
        - name: MAX_CONCURRENT_REQUESTS
          value: "200"
        - name: GPU_MEMORY_FRACTION
          value: "0.9"
        - name: HOSPITAL_ID
          value: "real-portuguese"
        - name: LOG_LEVEL
          value: "INFO"
        resources:
          requests:
            memory: "250Gi"  # MedGemma 4B + KV cache
            cpu: "12"
            nvidia.com/gpu: "40"
            neural-engine.apple.com/cores: "16"
          limits:
            memory: "280Gi"
            cpu: "15"
            nvidia.com/gpu: "48"
            neural-engine.apple.com/cores: "20"
        volumeMounts:
        - name: model-storage
          mountPath: /models
        - name: cache-storage
          mountPath: /cache
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 30
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
      volumes:
      - name: model-storage
        persistentVolumeClaim:
          claimName: medgemma-models-pvc
      - name: cache-storage
        emptyDir:
          sizeLimit: 50Gi

---
# MedGemma Service
apiVersion: v1
kind: Service
metadata:
  name: medgemma-service
  namespace: medical-ai
  labels:
    app: medgemma
    component: inference
spec:
  type: ClusterIP
  ports:
  - port: 8080
    targetPort: 8080
    protocol: TCP
    name: http
  - port: 8081
    targetPort: 8081
    protocol: TCP
    name: grpc
  selector:
    app: medgemma
    component: inference

---
# Model Storage PVC
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: medgemma-models-pvc
  namespace: medical-ai
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Gi
  storageClassName: local-ssd
```

### **Whisper Speech-to-Text Service**

```yaml
# ============================================================================
# Whisper Large Speech-to-Text Service
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: whisper-speech
  namespace: medical-ai
  labels:
    app: whisper
    component: speech-to-text
    version: "large"
spec:
  replicas: 2  # Load balancing for 200 users
  selector:
    matchLabels:
      app: whisper
      component: speech-to-text
  template:
    metadata:
      labels:
        app: whisper
        component: speech-to-text
    spec:
      nodeSelector:
        workload: medgemma-inference
      tolerations:
      - key: workload
        value: ai-inference
        effect: NoSchedule
      containers:
      - name: whisper-server
        image: gcr.io/medical-record-ai/whisper:large-coreml
        ports:
        - containerPort: 8082
          name: http
        env:
        - name: MODEL_SIZE
          value: "large"
        - name: LANGUAGE
          value: "en"
        - name: CORE_ML_ENABLED
          value: "true"
        resources:
          requests:
            memory: "8Gi"
            cpu: "2"
            nvidia.com/gpu: "4"
          limits:
            memory: "12Gi"
            cpu: "4"
            nvidia.com/gpu: "8"

---
apiVersion: v1
kind: Service
metadata:
  name: whisper-service
  namespace: medical-ai
spec:
  ports:
  - port: 8082
    targetPort: 8082
  selector:
    app: whisper
    component: speech-to-text
```

---

## 📡 API Service Manifests

### **FastAPI Backend Service**

```yaml
# ============================================================================
# FastAPI Medical Backend Service
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: medical-api
  namespace: medical-backend
  labels:
    app: medical-api
    component: backend
    version: "v1.0"
spec:
  replicas: 3  # High availability for 200 users
  selector:
    matchLabels:
      app: medical-api
      component: backend
  template:
    metadata:
      labels:
        app: medical-api
        component: backend
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8000"
    spec:
      nodeSelector:
        workload: api-services
      tolerations:
      - key: workload
        value: api-backend
        effect: NoSchedule
      containers:
      - name: fastapi-server
        image: gcr.io/medical-record-ai/medical-api:latest
        ports:
        - containerPort: 8000
          name: http
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: url
        - name: MEDGEMMA_SERVICE_URL
          value: "http://medgemma-service.medical-ai:8080"
        - name: WHISPER_SERVICE_URL
          value: "http://whisper-service.medical-ai:8082"
        - name: HOSPITAL_ID
          value: "real-portuguese"
        resources:
          requests:
            memory: "4Gi"
            cpu: "2"
            nvidia.com/gpu: "2"
          limits:
            memory: "8Gi"
            cpu: "4"
            nvidia.com/gpu: "4"
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
        readinessProbe:
          httpGet:
            path: /ready
            port: 8000
          initialDelaySeconds: 15

---
apiVersion: v1
kind: Service
metadata:
  name: medical-api-service
  namespace: medical-backend
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8000
    protocol: TCP
  selector:
    app: medical-api
    component: backend
```

### **Authentication Service**

```yaml
# ============================================================================
# Medical Authentication & Authorization Service
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth-service
  namespace: medical-backend
  labels:
    app: auth-service
    component: security
spec:
  replicas: 2
  selector:
    matchLabels:
      app: auth-service
      component: security
  template:
    metadata:
      labels:
        app: auth-service
        component: security
    spec:
      nodeSelector:
        workload: api-services
      containers:
      - name: auth-server
        image: gcr.io/medical-record-ai/auth:latest
        ports:
        - containerPort: 8001
        env:
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: auth-secrets
              key: jwt-secret
        - name: HOSPITAL_EHR_API
          valueFrom:
            configMapKeyRef:
              name: hospital-config
              key: ehr-api-url
        resources:
          requests:
            memory: "2Gi"
            cpu: "1"
          limits:
            memory: "4Gi"
            cpu: "2"

---
apiVersion: v1
kind: Service
metadata:
  name: auth-service
  namespace: medical-backend
spec:
  ports:
  - port: 8001
    targetPort: 8001
  selector:
    app: auth-service
    component: security
```

---

## 📊 Data Service Manifests

### **Analytics & Vector Search Service**

```yaml
# ============================================================================
# Medical Analytics & Vector Search Service
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: analytics-service
  namespace: medical-data
  labels:
    app: analytics
    component: data-processing
spec:
  replicas: 2
  selector:
    matchLabels:
      app: analytics
      component: data-processing
  template:
    metadata:
      labels:
        app: analytics
        component: data-processing
    spec:
      nodeSelector:
        workload: data-processing
      tolerations:
      - key: workload
        value: data-processing
        effect: NoSchedule
      containers:
      - name: analytics-server
        image: gcr.io/medical-record-ai/analytics:latest
        ports:
        - containerPort: 8003
        env:
        - name: VECTOR_DB_URL
          value: "http://vector-db:6333"
        - name: ELASTICSEARCH_URL
          valueFrom:
            configMapKeyRef:
              name: data-config
              key: elasticsearch-url
        resources:
          requests:
            memory: "8Gi"
            cpu: "3"
            nvidia.com/gpu: "6"
          limits:
            memory: "16Gi"
            cpu: "6"
            nvidia.com/gpu: "12"

---
# Vector Database (Qdrant)
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: vector-db
  namespace: medical-data
spec:
  serviceName: vector-db
  replicas: 1
  selector:
    matchLabels:
      app: vector-db
  template:
    metadata:
      labels:
        app: vector-db
    spec:
      nodeSelector:
        workload: data-processing
      containers:
      - name: qdrant
        image: qdrant/qdrant:latest
        ports:
        - containerPort: 6333
        - containerPort: 6334
        volumeMounts:
        - name: vector-storage
          mountPath: /qdrant/storage
        resources:
          requests:
            memory: "4Gi"
            cpu: "2"
          limits:
            memory: "8Gi"
            cpu: "4"
  volumeClaimTemplates:
  - metadata:
      name: vector-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 500Gi

---
apiVersion: v1
kind: Service
metadata:
  name: vector-db
  namespace: medical-data
spec:
  ports:
  - port: 6333
    name: http
  - port: 6334
    name: grpc
  selector:
    app: vector-db
```

### **Redis Cache Service**

```yaml
# ============================================================================
# Redis Cache for Medical Data
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-cache
  namespace: medical-data
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-cache
  template:
    metadata:
      labels:
        app: redis-cache
    spec:
      nodeSelector:
        workload: data-processing
      containers:
      - name: redis
        image: redis:7-alpine
        ports:
        - containerPort: 6379
        command:
        - redis-server
        - --maxmemory
        - "8gb"
        - --maxmemory-policy
        - "allkeys-lru"
        resources:
          requests:
            memory: "4Gi"
            cpu: "1"
          limits:
            memory: "8Gi"
            cpu: "2"

---
apiVersion: v1
kind: Service
metadata:
  name: redis-cache
  namespace: medical-data
spec:
  ports:
  - port: 6379
  selector:
    app: redis-cache
```

---

## 🔧 Infrastructure Manifests

### **Namespaces**

```yaml
# ============================================================================
# Kubernetes Namespaces
# ============================================================================
apiVersion: v1
kind: Namespace
metadata:
  name: medical-ai
  labels:
    purpose: ai-inference
    hospital: real-portuguese

---
apiVersion: v1
kind: Namespace
metadata:
  name: medical-backend
  labels:
    purpose: api-services
    hospital: real-portuguese

---
apiVersion: v1
kind: Namespace
metadata:
  name: medical-data
  labels:
    purpose: data-processing
    hospital: real-portuguese

---
apiVersion: v1
kind: Namespace
metadata:
  name: monitoring
  labels:
    purpose: observability
```

### **ConfigMaps**

```yaml
# ============================================================================
# Hospital Configuration
# ============================================================================
apiVersion: v1
kind: ConfigMap
metadata:
  name: hospital-config
  namespace: medical-backend
data:
  hospital-name: "Real Portuguese Hospital"
  hospital-id: "real-portuguese"
  ehr-api-url: "https://api.realportuguese.com"
  max-concurrent-users: "200"
  departments: |
    - cardiology
    - emergency
    - surgery
    - icu
    - radiology

---
# Data Processing Configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: data-config
  namespace: medical-data
data:
  elasticsearch-url: "http://elasticsearch:9200"
  vector-dimensions: "768"
  cache-ttl: "3600"
  analytics-batch-size: "100"

---
# AI Models Configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: ai-config
  namespace: medical-ai
data:
  medgemma-model-path: "/models/medgemma-4b"
  whisper-model-path: "/models/whisper-large"
  max-tokens: "8192"
  temperature: "0.1"
  medical-specialties: |
    - cardiology
    - endocrinology
    - emergency
    - radiology
    - pathology
```

### **Storage Classes**

```yaml
# ============================================================================
# Storage Classes for M3 Ultra
# ============================================================================
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-ssd
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
reclaimPolicy: Retain

---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: Immediate
reclaimPolicy: Delete
allowVolumeExpansion: true
```

---

## 📊 Monitoring & Observability

### **Prometheus Configuration**

```yaml
# ============================================================================
# Prometheus for Medical AI Monitoring
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
      - name: prometheus
        image: prom/prometheus:latest
        ports:
        - containerPort: 9090
        volumeMounts:
        - name: prometheus-config
          mountPath: /etc/prometheus
        - name: prometheus-storage
          mountPath: /prometheus
        resources:
          requests:
            memory: "4Gi"
            cpu: "1"
          limits:
            memory: "8Gi"
            cpu: "2"
      volumes:
      - name: prometheus-config
        configMap:
          name: prometheus-config
      - name: prometheus-storage
        emptyDir:
          sizeLimit: 100Gi

---
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
    scrape_configs:
    - job_name: 'medgemma-inference'
      static_configs:
      - targets: ['medgemma-service.medical-ai:8080']
    - job_name: 'medical-api'
      static_configs:
      - targets: ['medical-api-service.medical-backend:8000']
    - job_name: 'kubernetes-nodes'
      kubernetes_sd_configs:
      - role: node
    - job_name: 'kubernetes-pods'
      kubernetes_sd_configs:
      - role: pod

---
apiVersion: v1
kind: Service
metadata:
  name: prometheus
  namespace: monitoring
spec:
  ports:
  - port: 9090
  selector:
    app: prometheus
```

### **Grafana Dashboard**

```yaml
# ============================================================================
# Grafana for Medical AI Dashboards
# ============================================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana:latest
        ports:
        - containerPort: 3000
        env:
        - name: GF_SECURITY_ADMIN_PASSWORD
          valueFrom:
            secretKeyRef:
              name: grafana-secrets
              key: admin-password
        volumeMounts:
        - name: grafana-dashboards
          mountPath: /var/lib/grafana/dashboards
        resources:
          requests:
            memory: "2Gi"
            cpu: "1"
          limits:
            memory: "4Gi"
            cpu: "2"
      volumes:
      - name: grafana-dashboards
        configMap:
          name: grafana-dashboards

---
apiVersion: v1
kind: Service
metadata:
  name: grafana
  namespace: monitoring
spec:
  type: LoadBalancer
  ports:
  - port: 3000
  selector:
    app: grafana
```

---

## 🔐 Security & RBAC

### **Service Accounts**

```yaml
# ============================================================================
# Service Accounts for Medical AI
# ============================================================================
apiVersion: v1
kind: ServiceAccount
metadata:
  name: medgemma-sa
  namespace: medical-ai
automountServiceAccountToken: true

---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: medical-api-sa
  namespace: medical-backend
automountServiceAccountToken: true

---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: analytics-sa
  namespace: medical-data
automountServiceAccountToken: true
```

### **RBAC Configuration**

```yaml
# ============================================================================
# RBAC for Medical AI Services
# ============================================================================
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: medical-ai-reader
rules:
- apiGroups: [""]
  resources: ["pods", "services", "configmaps"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: medical-ai-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: medical-ai-reader
subjects:
- kind: ServiceAccount
  name: medgemma-sa
  namespace: medical-ai
- kind: ServiceAccount
  name: medical-api-sa
  namespace: medical-backend
```

### **Network Policies**

```yaml
# ============================================================================
# Network Policies for Medical Data Security
# ============================================================================
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: medical-ai-network-policy
  namespace: medical-ai
spec:
  podSelector:
    matchLabels:
      app: medgemma
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: api-services
    ports:
    - protocol: TCP
      port: 8080
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          purpose: data-processing
    ports:
    - protocol: TCP
      port: 6333

---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: medical-data-network-policy
  namespace: medical-data
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: ai-inference
    - namespaceSelector:
        matchLabels:
          purpose: api-services
```

---

## 🚀 Deployment Commands

### **Quick Deploy Script**

```bash
#!/bin/bash
# ============================================================================
# Quick Deployment Script for Medical AI Platform
# ============================================================================

echo "🏥 Deploying Real Portuguese Hospital Medical AI Platform..."

# Create namespaces
kubectl apply -f namespaces.yaml

# Deploy infrastructure
kubectl apply -f storage-classes.yaml
kubectl apply -f configmaps.yaml
kubectl apply -f secrets.yaml

# Deploy AI services
kubectl apply -f ai-services/
kubectl wait --for=condition=ready pod -l app=medgemma -n medical-ai --timeout=300s

# Deploy API services
kubectl apply -f api-services/
kubectl wait --for=condition=ready pod -l app=medical-api -n medical-backend --timeout=180s

# Deploy data services
kubectl apply -f data-services/
kubectl wait --for=condition=ready pod -l app=analytics -n medical-data --timeout=120s

# Deploy monitoring
kubectl apply -f monitoring/

echo "✅ Medical AI Platform deployed successfully!"
echo "📊 Grafana: http://localhost:3000"
echo "🤖 MedGemma API: http://localhost:8080"
echo "📡 Medical API: http://localhost:80"
```

### **Health Check Commands**

```bash
# Check node resources
kubectl top nodes

# Check AI service status
kubectl get pods -n medical-ai -o wide

# Check resource allocation
kubectl describe node m3-ultra-ai-node

# Test MedGemma inference
curl -X POST http://medgemma-service.medical-ai:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"messages": [{"role": "user", "content": "Analyze this ECG..."}]}'
```

---

## 📋 Summary

This comprehensive Kubernetes configuration provides:

✅ **Corrected Resource Allocation**: AI Node gets 300GB + 50 GPU cores
✅ **Complete Application Stack**: MedGemma, Whisper, FastAPI, Analytics  
✅ **Production-Ready**: Health checks, monitoring, RBAC, network policies
✅ **Scalable Architecture**: Designed for 200 concurrent medical users
✅ **Observability**: Prometheus + Grafana monitoring
✅ **Security**: Network policies, RBAC, secrets management

**Total Resources Used:**
- **Memory**: 500GB / 512GB (98% utilization)
- **GPU**: 80 cores / 80 cores (100% utilization)  
- **CPU**: 32 cores / 32 cores (100% utilization)
- **Storage**: 8TB local + persistent volumes

The platform is optimized for **Real Portuguese Hospital's 200 daily medical users** with **MedGemma 4B medical AI** running on **Mac Studio M3 Ultra**. 