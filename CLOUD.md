# ☁️ Multi-Tenant Cloud Architecture - Medical Record Platform

## 🎯 Cloud Architecture Overview

The Medical Record platform implements a **multi-tenant cloud architecture** using **Kamaji** for tenant isolation, **Kubecost** for cost optimization, and **GCP** for scalable infrastructure. This enables **multiple hospitals** to use the platform with **complete isolation** and **cost transparency**.

---

## 🏗️ Multi-Tenant Architecture Overview

```sh
┌─────────────────────────────────────────────────────────┐
│ ☁️ GCP MULTI-TENANT MEDICAL RECORD PLATFORM             │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🏥 TENANT HOSPITALS                                     │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🏥 Real Portuguese Hospital (Tenant 1)              │ │
│ │ • 200 daily users, 4 departments                    │ │
│ │ • Dedicated control plane                           │ │
│ │ • Isolated resources                                │ │
│ │                                                     │ │
│ │ 🏥 Santa Maria Hospital (Tenant 2)                  │ │
│ │ • 500 daily users, 8 departments                    │ │
│ │ • Dedicated control plane                           │ │
│ │ • Scaled resources                                  │ │
│ │                                                     │ │
│ │ 🏥 São João Hospital (Tenant 3)                     │ │
│ │ • 150 daily users, 3 departments                    │ │
│ │ • Dedicated control plane                           │ │
│ │ • Right-sized resources                             │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ ⚙️ KAMAJI CONTROL PLANE MANAGEMENT                      │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • Multi-tenant Kubernetes control planes            │ │
│ │ • Tenant isolation & resource management            │ │
│ │ • Automated tenant provisioning                     │ │
│ │ • Cross-tenant security enforcement                 │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 💰 KUBECOST COST OPTIMIZATION                           │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • Per-tenant cost tracking                          │ │
│ │ • Resource optimization recommendations             │ │
│ │ • Cost allocation & chargeback                      │ │
│ │ • Budget alerts & governance                        │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 🌐 GCP INFRASTRUCTURE FOUNDATION                        │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • GKE Autopilot clusters                            │ │
│ │ • Cloud SQL for tenant databases                    │ │
│ │ • Cloud Storage for medical data                    │ │
│ │ • Cloud AI Platform for ML workloads                │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## ⚙️ Kamaji Multi-Tenant Control Planes

### **🎛️ Kamaji Architecture**

```sh
┌─────────────────────────────────────────────────────────┐
│ ⚙️ KAMAJI MULTI-TENANT ARCHITECTURE                     │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🎛️ MANAGEMENT CLUSTER (GKE Autopilot)                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🔧 Kamaji Control Plane Manager                     │ │
│ │ • TenantControlPlane CRDs                           │ │
│ │ • Tenant lifecycle management                       │ │
│ │ • Resource quota enforcement                        │ │
│ │ • Cross-tenant security policies                    │ │
│ │                                                     │ │
│ │ 📊 Kubecost Management                              │ │
│ │ • Cost aggregation across tenants                   │ │
│ │ • Resource optimization engine                      │ │
│ │ • Budget enforcement                                │ │
│ │ • Chargeback reporting                              │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼ Tenant Control Plane Provisioning           │
│ 🏥 TENANT CONTROL PLANES                                │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🏥 Real Portuguese Hospital Control Plane           │ │
│ │ ├─ API Server (dedicated)                           │ │
│ │ ├─ etcd (encrypted, isolated)                       │ │
│ │ ├─ Controller Manager                               │ │
│ │ ├─ Scheduler                                        │ │
│ │ └─ Addon Manager                                    │ │
│ │                                                     │ │
│ │ 🏥 Santa Maria Hospital Control Plane               │ │
│ │ ├─ API Server (dedicated)                           │ │
│ │ ├─ etcd (encrypted, isolated)                       │ │
│ │ ├─ Controller Manager                               │ │
│ │ ├─ Scheduler                                        │ │
│ │ └─ Addon Manager                                    │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼ Workload Scheduling                         │
│ ⚡ SHARED WORKER NODES (GKE Nodepool)                    │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🤖 AI Workloads Node Pool                           │ │
│ │ • Machine Type: n1-standard-16 + Tesla V100         │ │
│ │ • Auto-scaling: 1-10 nodes                          │ │
│ │ • Tenant workload isolation                         │ │
│ │                                                     │ │
│ │ 📡 API Services Node Pool                           │ │
│ │ • Machine Type: n1-standard-8                       │ │
│ │ • Auto-scaling: 2-20 nodes                          │ │
│ │ • High availability setup                           │ │
│ │                                                     │ │
│ │ 📊 Data Services Node Pool                          │ │
│ │ • Machine Type: n1-highmem-4                        │ │
│ │ • Auto-scaling: 1-5 nodes                           │ │
│ │ • Persistent storage attached                       │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### **🏥 Tenant Control Plane Configuration**

```yaml
# ============================================================================
# Real Portuguese Hospital - Tenant Control Plane
# ============================================================================
apiVersion: kamaji.clastix.io/v1alpha1
kind: TenantControlPlane
metadata:
  name: real-portuguese-hospital
  namespace: kamaji-system
spec:
  controlPlane:
    deployment:
      replicas: 2
      resources:
        apiServer:
          requests:
            cpu: "250m"
            memory: "512Mi"
          limits:
            cpu: "500m" 
            memory: "1Gi"
        controllerManager:
          requests:
            cpu: "125m"
            memory: "256Mi"
          limits:
            cpu: "250m"
            memory: "512Mi"
        scheduler:
          requests:
            cpu: "125m"
            memory: "256Mi"
          limits:
            cpu: "250m"
            memory: "512Mi"
    
    service:
      serviceType: LoadBalancer
      
  dataStore:
    driver: "etcd"
    endpoints:
      - "etcd-real-portuguese.kamaji-system.svc.cluster.local:2379"
    
  addons:
    coreDNS: {}
    kubeProxy: {}
    konnectivity: {}
    
  networkProfile:
    port: 6443
    certSANs:
      - "real-portuguese.medical-record.com"
      - "192.168.100.10"
    
  kubernetes:
    version: "v1.28.0"
    admissionControllers:
      - "NamespaceLifecycle"
      - "LimitRanger"
      - "ServiceAccount"
      - "ResourceQuota"
      - "PodSecurityPolicy"

---
# Resource Quotas for Real Portuguese Hospital
apiVersion: v1
kind: ResourceQuota
metadata:
  name: real-portuguese-quota
  namespace: real-portuguese-hospital
spec:
  hard:
    requests.cpu: "32"
    requests.memory: "128Gi"
    limits.cpu: "64"
    limits.memory: "256Gi"
    requests.nvidia.com/gpu: "4"
    persistentvolumeclaims: "20"
    pods: "100"
    services: "50"
    secrets: "100"
    configmaps: "100"

---
# Network Policy for Tenant Isolation
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: tenant-isolation
  namespace: real-portuguese-hospital
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          tenant: "real-portuguese-hospital"
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          tenant: "real-portuguese-hospital"
  - to: []
    ports:
    - protocol: TCP
      port: 53
    - protocol: UDP
      port: 53
```

---

## 💰 Kubecost Cost Management

### **📊 Cost Monitoring Architecture**

```sh
┌─────────────────────────────────────────────────────────┐
│ 💰 KUBECOST COST OPTIMIZATION PLATFORM                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 📊 COST COLLECTION & AGGREGATION                        │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🔍 Data Sources:                                    │ │
│ │ • GCP Billing API integration                       │ │
│ │ • Kubernetes resource metrics                       │ │
│ │ • Node utilization data                             │ │
│ │ • Storage usage analytics                           │ │
│ │ • Network traffic costs                             │ │
│ │                                                     │ │
│ │ 📈 Metrics Collection:                              │ │
│ │ • CPU/Memory/GPU utilization                        │ │
│ │ • Persistent volume costs                           │ │
│ │ • Load balancer expenses                            │ │
│ │ • External IP allocation                            │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 🏥 TENANT COST ALLOCATION                               │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Real Portuguese Hospital:                           │ │
│ │ • Total Monthly Cost: $2,450                        │ │
│ │ • AI Workloads: $1,200 (49%)                        │ │
│ │ • API Services: $800 (33%)                          │ │
│ │ • Data Storage: $300 (12%)                          │ │
│ │ • Network: $150 (6%)                                │ │
│ │                                                     │ │
│ │ Santa Maria Hospital:                               │ │
│ │ • Total Monthly Cost: $6,100                        │ │
│ │ • AI Workloads: $3,200 (52%)                        │ │
│ │ • API Services: $1,900 (31%)                        │ │
│ │ • Data Storage: $700 (11%)                          │ │
│ │ • Network: $300 (6%)                                │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 🎯 OPTIMIZATION RECOMMENDATIONS                         │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 💡 Cost Optimization Opportunities:                 │ │
│ │                                                     │ │
│ │ Right-sizing Recommendations:                       │ │
│ │ • Reduce API pod CPU requests (Save $200/month)     │ │
│ │ • Optimize GPU utilization (Save $400/month)        │ │
│ │ • Implement node auto-scaling (Save $300/month)     │ │
│ │                                                     │ │
│ │ Resource Optimization:                              │ │
│ │ • Use spot instances for batch jobs (Save 60%)      │ │
│ │ • Implement storage lifecycle (Save $100/month)     │ │
│ │ • Optimize data transfer (Save $80/month)           │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### **📈 Kubecost Implementation**

```yaml
# ============================================================================
# Kubecost Installation for Multi-Tenant Cost Management
# ============================================================================
apiVersion: v1
kind: Namespace
metadata:
  name: kubecost

---
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: kubecost
  namespace: kubecost
spec:
  chart: cost-analyzer
  repo: https://kubecost.github.io/cost-analyzer/
  targetNamespace: kubecost
  valuesContent: |-
    global:
      prometheus:
        enabled: true
        fqdn: http://prometheus-server.monitoring.svc.cluster.local:80
      grafana:
        enabled: true
        proxy: false
    
    kubecostProductConfigs:
      clusterName: "medical-record-production"
      productKey: "medical-record-enterprise-key"
      
    # Multi-tenant configuration
    kubecostModel:
      warmCache: true
      warmSavingsCache: true
      etl: true
      
    # Cost allocation configuration
    kubecostAggregator:
      enabled: true
      
    # Enterprise features for multi-tenancy
    saml:
      enabled: false
      
    oidc:
      enabled: true
      issuerURL: "https://accounts.google.com"
      clientID: "medical-record-kubecost"
      
    # Custom cost allocation
    kubecostDeployment:
      leaderFollower:
        enabled: true
      
    # GCP integration
    serviceKeySecret:
      enabled: true
      name: "kubecost-gcp-key"
      
    # Tenant-specific cost views
    kubecostFrontend:
      enabled: true
      image: "gcr.io/kubecost1/frontend"
      
    # Resource recommendations
    kubecostMetrics:
      emitPodAnnotations: true
      emitNamespaceAnnotations: true

---
# GCP Service Account for Billing API
apiVersion: v1
kind: Secret
metadata:
  name: kubecost-gcp-key
  namespace: kubecost
type: Opaque
data:
  service-key.json: |
    <base64-encoded-gcp-service-account-json>

---
# Custom Resource for Tenant Cost Allocation
apiVersion: v1
kind: ConfigMap
metadata:
  name: tenant-allocation-config
  namespace: kubecost
data:
  allocation.json: |
    {
      "allocations": [
        {
          "name": "real-portuguese-hospital",
          "filters": {
            "namespace": ["real-portuguese-.*"],
            "label": {
              "tenant": "real-portuguese-hospital"
            }
          },
          "shareIdle": false,
          "shareNamespaces": []
        },
        {
          "name": "santa-maria-hospital", 
          "filters": {
            "namespace": ["santa-maria-.*"],
            "label": {
              "tenant": "santa-maria-hospital"
            }
          },
          "shareIdle": false,
          "shareNamespaces": []
        }
      ]
    }
```

---

## 🌐 GCP Infrastructure Foundation

### **☁️ GCP Multi-Tenant Setup**

```sh
┌─────────────────────────────────────────────────────────┐
│ 🌐 GCP INFRASTRUCTURE ARCHITECTURE                      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🏢 ORGANIZATION LEVEL                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Medical Record Platform Organization                │ │
│ │ • Billing Account: medical-record-billing           │ │
│ │ • IAM Policies: centralized management              │ │
│ │ • Audit Logging: organization-wide                  │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 📁 PROJECT STRUCTURE                                    │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ medical-record-platform (Management)                │ │
│ │ • Kamaji control planes                             │ │
│ │ • Kubecost installation                             │ │
│ │ • Shared services (DNS, monitoring)                 │ │
│ │                                                     │ │
│ │ medical-record-tenant-rph (Real Portuguese)         │ │
│ │ • Tenant-specific resources                         │ │
│ │ • Dedicated Cloud SQL instances                     │ │
│ │ • Tenant data storage                               │ │
│ │                                                     │ │
│ │ medical-record-tenant-smh (Santa Maria)             │ │
│ │ • Tenant-specific resources                         │ │
│ │ • Dedicated Cloud SQL instances                     │ │
│ │ • Tenant data storage                               │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ ⚙️ SHARED INFRASTRUCTURE                                │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🎛️ GKE Autopilot Cluster                            │ │
│ │ • Region: us-central1                               │ │
│ │ • Node auto-provisioning                            │ │
│ │ • Workload Identity enabled                         │ │
│ │ • Network policies enforced                         │ │
│ │                                                     │ │
│ │ 🗄️ Cloud SQL (per tenant)                           │ │
│ │ • PostgreSQL 15 instances                           │ │
│ │ • High availability setup                           │ │
│ │ • Automated backups                                 │ │
│ │ • Point-in-time recovery                            │ │
│ │                                                     │ │
│ │ 💾 Cloud Storage                                    │ │
│ │ • Medical document storage                          │ │
│ │ • Tenant-isolated buckets                           │ │
│ │ • Lifecycle management                              │ │
│ │ • Regional replication                              │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### **🏗️ GCP Infrastructure as Code (Pulumi)**

```typescript
// ============================================================================
// Multi-Tenant GCP Infrastructure - Pulumi TypeScript
// ============================================================================

import * as gcp from "@pulumi/gcp";
import * as k8s from "@pulumi/kubernetes";
import * as pulumi from "@pulumi/pulumi";

// Organization and billing setup
const organization = new gcp.organizations.Project("medical-record-platform", {
    projectId: "medical-record-platform",
    name: "Medical Record Platform",
    billingAccount: "medical-record-billing-123456",
    orgId: "123456789012",
});

// Enable required APIs
const requiredAPIs = [
    "container.googleapis.com",
    "compute.googleapis.com", 
    "sqladmin.googleapis.com",
    "storage.googleapis.com",
    "cloudbilling.googleapis.com",
    "cloudresourcemanager.googleapis.com",
    "iam.googleapis.com",
    "monitoring.googleapis.com",
    "logging.googleapis.com"
];

requiredAPIs.forEach((api, index) => {
    new gcp.projects.Service(`api-${index}`, {
        project: organization.projectId,
        service: api,
    });
});

// GKE Autopilot Cluster for Multi-Tenant Workloads
const gkeCluster = new gcp.container.Cluster("medical-record-cluster", {
    project: organization.projectId,
    location: "us-central1",
    enableAutopilot: true,
    
    // Network configuration
    network: "default",
    subnetwork: "default",
    
    // Security configuration
    workloadIdentityConfig: {
        workloadPool: pulumi.interpolate`${organization.projectId}.svc.id.goog`,
    },
    
    // Cluster features
    addonsConfig: {
        networkPolicyConfig: {
            disabled: false,
        },
        kubernetesDashboard: {
            disabled: true, // Security best practice
        },
    },
    
    // Private cluster configuration
    privateClusterConfig: {
        enablePrivateNodes: true,
        enablePrivateEndpoint: false,
        masterIpv4CidrBlock: "10.100.0.0/28",
    },
    
    // IP allocation policy
    ipAllocationPolicy: {
        clusterSecondaryRangeName: "pods",
        servicesSecondaryRangeName: "services",
    },
    
    // Master authentication
    masterAuth: {
        clientCertificateConfig: {
            issueClientCertificate: false,
        },
    },
});

// Kubernetes provider
const k8sProvider = new k8s.Provider("k8s-provider", {
    kubeconfig: gkeCluster.endpoint.apply(endpoint => 
        gcp.container.getClusterOutput({
            name: gkeCluster.name,
            location: gkeCluster.location,
        }).then(cluster => {
            // Generate kubeconfig
            return `
apiVersion: v1
kind: Config
clusters:
- cluster:
    certificate-authority-data: ${cluster.masterAuth.clusterCaCertificate}
    server: https://${cluster.endpoint}
  name: gke-cluster
contexts:
- context:
    cluster: gke-cluster
    user: gke-user
  name: gke-context
current-context: gke-context
users:
- name: gke-user
  user:
    auth-provider:
      config:
        cmd-args: config config-helper --format=json
        cmd-path: gcloud
        expiry-key: '{.credential.token_expiry}'
        token-key: '{.credential.access_token}'
      name: gcp
`;
        })
    ),
});

// Tenant-specific Cloud SQL instances
const tenants = [
    { name: "real-portuguese", project: "medical-record-tenant-rph", users: 200 },
    { name: "santa-maria", project: "medical-record-tenant-smh", users: 500 },
    { name: "sao-joao", project: "medical-record-tenant-sjh", users: 150 },
];

tenants.forEach(tenant => {
    // Create tenant project
    const tenantProject = new gcp.organizations.Project(`${tenant.name}-project`, {
        projectId: tenant.project,
        name: `${tenant.name} Hospital`,
        billingAccount: "medical-record-billing-123456", 
        orgId: "123456789012",
    });
    
    // Enable Cloud SQL API for tenant
    new gcp.projects.Service(`${tenant.name}-sql-api`, {
        project: tenantProject.projectId,
        service: "sqladmin.googleapis.com",
    });
    
    // Cloud SQL instance for tenant
    const sqlInstance = new gcp.sql.DatabaseInstance(`${tenant.name}-db`, {
        project: tenantProject.projectId,
        name: `${tenant.name}-medical-db`,
        databaseVersion: "POSTGRES_15",
        region: "us-central1",
        
        settings: {
            tier: tenant.users > 300 ? "db-custom-4-16384" : "db-custom-2-8192",
            
            backupConfiguration: {
                enabled: true,
                startTime: "03:00",
                pointInTimeRecoveryEnabled: true,
                transactionLogRetentionDays: 7,
                backupRetentionSettings: {
                    retainedBackups: 30,
                },
            },
            
            ipConfiguration: {
                ipv4Enabled: false,
                privateNetwork: "default",
                enablePrivatePathForGoogleCloudServices: true,
            },
            
            maintenanceWindow: {
                day: 7, // Sunday
                hour: 4,
            },
            
            availabilityType: "REGIONAL", // High availability
            
            diskAutoresize: true,
            diskSize: tenant.users > 300 ? 200 : 100,
            diskType: "PD_SSD",
        },
    });
    
    // Database for medical records
    new gcp.sql.Database(`${tenant.name}-medical-db`, {
        project: tenantProject.projectId,
        name: "medical_records",
        instance: sqlInstance.name,
    });
    
    // Cloud Storage bucket for tenant documents
    new gcp.storage.Bucket(`${tenant.name}-storage`, {
        project: tenantProject.projectId,
        name: `${tenant.project}-medical-documents`,
        location: "US-CENTRAL1",
        
        versioning: {
            enabled: true,
        },
        
        lifecycleRules: [{
            condition: {
                age: 90,
            },
            action: {
                type: "SetStorageClass",
                storageClass: "COLDLINE",
            },
        }],
        
        encryption: {
            defaultKmsKeyName: `projects/${tenantProject.projectId}/locations/us-central1/keyRings/medical-encryption/cryptoKeys/medical-data`,
        },
    });
});

// Export cluster information
export const clusterName = gkeCluster.name;
export const clusterEndpoint = gkeCluster.endpoint;
export const clusterLocation = gkeCluster.location;
```

---

## 🔧 Deployment Configuration

### **🚀 Multi-Tenant Deployment Pipeline**

```yaml
# ============================================================================
# ArgoCD Application for Multi-Tenant Management
# ============================================================================
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: kamaji-multi-tenant
  namespace: argocd
spec:
  project: medical-record
  source:
    repoURL: https://github.com/medical-record/platform-config
    targetRevision: HEAD
    path: deployments/kamaji
  destination:
    server: https://kubernetes.default.svc
    namespace: kamaji-system
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
    - CreateNamespace=true

---
# Tenant onboarding automation
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: tenant-onboarding
  namespace: argocd
spec:
  generators:
  - list:
      elements:
      - tenant: real-portuguese-hospital
        project: medical-record-tenant-rph
        users: "200"
        departments: "4"
      - tenant: santa-maria-hospital
        project: medical-record-tenant-smh  
        users: "500"
        departments: "8"
      - tenant: sao-joao-hospital
        project: medical-record-tenant-sjh
        users: "150" 
        departments: "3"
        
  template:
    metadata:
      name: '{{tenant}}'
    spec:
      project: medical-record
      source:
        repoURL: https://github.com/medical-record/tenant-configs
        targetRevision: HEAD
        path: 'tenants/{{tenant}}'
        helm:
          parameters:
          - name: tenant.name
            value: '{{tenant}}'
          - name: tenant.project
            value: '{{project}}'
          - name: tenant.users
            value: '{{users}}'
          - name: tenant.departments
            value: '{{departments}}'
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{tenant}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
        syncOptions:
        - CreateNamespace=true
```

---

## 📊 Cost Optimization Strategy

### **💰 Multi-Tenant Cost Management**

```sh
┌─────────────────────────────────────────────────────────┐
│ 💰 COST OPTIMIZATION FRAMEWORK                          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 📊 COST ALLOCATION MODEL                                │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Shared Infrastructure Costs (30%):                  │ │
│ │ • Kamaji management cluster                         │ │
│ │ • Kubecost infrastructure                           │ │
│ │ • Monitoring & logging                              │ │
│ │ • Network & security                                │ │
│ │                                                     │ │
│ │ Tenant-Specific Costs (70%):                        │ │
│ │ • Compute resources (CPU/Memory/GPU)                │ │
│ │ • Storage (persistent volumes)                      │ │
│ │ • Database instances                                │ │
│ │ • Load balancers & networking                       │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 🎯 OPTIMIZATION STRATEGIES                              │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Right-Sizing:                                       │ │
│ │ • AI workloads: Use GPU scheduling for efficiency   │ │
│ │ • API services: Auto-scale based on demand          │ │
│ │ • Databases: Right-size based on usage patterns     │ │
│ │                                                     │ │
│ │ Resource Optimization:                              │ │
│ │ • Spot instances for batch processing (60% savings) │ │
│ │ • Preemptible VMs for development (80% savings)     │ │
│ │ • Storage lifecycle management                      │ │
│ │                                                     │ │
│ │ Multi-Tenant Efficiency:                            │ │
│ │ • Shared node pools with tenant isolation           │ │
│ │ • Resource pool optimization                        │ │
│ │ • Bin packing efficiency improvements               │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 📈 COST PROJECTION (Monthly)                            │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Real Portuguese Hospital (200 users):               │ │
│ │ • Base cost: $2,450/month                           │ │
│ │ • Optimized: $1,950/month (-20%)                    │ │
│ │                                                     │ │
│ │ Santa Maria Hospital (500 users):                   │ │
│ │ • Base cost: $6,100/month                           │ │
│ │ • Optimized: $4,650/month (-24%)                    │ │
│ │                                                     │ │
│ │ Platform Total:                                     │ │
│ │ • Base cost: $12,000/month                          │ │
│ │ • Optimized: $9,200/month (-23%)                    │ │
│ │ • Annual savings: $33,600                           │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## 🔒 Multi-Tenant Security

### **🛡️ Tenant Isolation & Security**

```sh
┌─────────────────────────────────────────────────────────┐
│ 🔒 MULTI-TENANT SECURITY ARCHITECTURE                   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🎛️ CONTROL PLANE ISOLATION                              │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • Dedicated API servers per tenant                  │ │
│ │ • Separate etcd encryption keys                     │ │
│ │ • Isolated control plane networks                   │ │
│ │ • Tenant-specific RBAC policies                     │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 🌐 NETWORK ISOLATION                                    │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • VPC-native networking                             │ │
│ │ • Network policies between tenants                  │ │
│ │ • Private Google Access                             │ │
│ │ • Firewall rules per tenant                         │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 💾 DATA ISOLATION                                       │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • Separate Cloud SQL instances                      │ │
│ │ • Tenant-specific storage buckets                   │ │
│ │ • KMS encryption per tenant                         │ │
│ │ • Database-level access controls                    │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 🔑 IDENTITY & ACCESS MANAGEMENT                         │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ • Workload Identity per tenant                      │ │
│ │ • Google Cloud IAM integration                      │ │
│ │ • Service account isolation                         │ │
│ │ • Cross-tenant access prevention                    │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## 📈 Monitoring & Observability

### **📊 Multi-Tenant Monitoring Stack**

```yaml
# ============================================================================
# Prometheus Configuration for Multi-Tenant Monitoring
# ============================================================================
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      evaluation_interval: 15s
      
    rule_files:
    - "/etc/prometheus/rules/*.yml"
    
    scrape_configs:
    # Kamaji control planes monitoring
    - job_name: 'kamaji-control-planes'
      kubernetes_sd_configs:
      - role: pod
        namespaces:
          names: ['kamaji-system']
      relabel_configs:
      - source_labels: [__meta_kubernetes_pod_label_app]
        action: keep
        regex: kamaji-.*
      - source_labels: [__meta_kubernetes_pod_annotation_tenant]
        target_label: tenant
        
    # Kubecost metrics
    - job_name: 'kubecost'
      static_configs:
      - targets: ['kubecost-cost-analyzer.kubecost:9003']
      
    # Tenant workload monitoring
    - job_name: 'tenant-workloads'
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace]
        action: keep
        regex: '(real-portuguese-.*|santa-maria-.*|sao-joao-.*)'
      - source_labels: [__meta_kubernetes_namespace]
        target_label: tenant
        regex: '([^-]+)-([^-]+)-.*'
        replacement: '${1}-${2}-hospital'

---
# Grafana Dashboard for Multi-Tenant Overview  
apiVersion: v1
kind: ConfigMap
metadata:
  name: multi-tenant-dashboard
  namespace: monitoring
data:
  dashboard.json: |
    {
      "dashboard": {
        "title": "Multi-Tenant Medical Record Platform",
        "panels": [
          {
            "title": "Tenant Resource Usage",
            "type": "stat",
            "targets": [
              {
                "expr": "sum by (tenant) (rate(container_cpu_usage_seconds_total[5m]))",
                "legendFormat": "{{ tenant }} CPU"
              }
            ]
          },
          {
            "title": "Cost by Tenant",
            "type": "piechart", 
            "targets": [
              {
                "expr": "sum by (tenant) (kubecost_node_cost_hourly * 24 * 30)",
                "legendFormat": "{{ tenant }}"
              }
            ]
          },
          {
            "title": "AI Workload Performance",
            "type": "graph",
            "targets": [
              {
                "expr": "rate(medgemma_inference_duration_seconds[5m])",
                "legendFormat": "{{ tenant }} - MedGemma Latency"
              }
            ]
          }
        ]
      }
    }
```

---

## 🎯 Implementation Roadmap

### **🗓️ Multi-Tenant Deployment Timeline**

```sh
┌─────────────────────────────────────────────────────────┐
│ 🗓️ IMPLEMENTATION PHASES                                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 📅 PHASE 1: FOUNDATION (Weeks 1-4)                      │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Week 1-2: GCP Infrastructure Setup                  │ │
│ │ • Create organization and projects                  │ │
│ │ • Set up billing and IAM                            │ │
│ │ • Deploy GKE Autopilot cluster                      │ │
│ │                                                     │ │
│ │ Week 3-4: Kamaji Installation                       │ │
│ │ • Install Kamaji operator                           │ │
│ │ • Configure tenant control planes                   │ │
│ │ • Test tenant isolation                             │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 📅 PHASE 2: COST MANAGEMENT (Weeks 5-6)                 │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Week 5: Kubecost Deployment                         │ │
│ │ • Install Kubecost with GCP billing                 │ │
│ │ • Configure tenant cost allocation                  │ │
│ │ • Set up cost optimization alerts                   │ │
│ │                                                     │ │
│ │ Week 6: Cost Optimization                           │ │
│ │ • Implement resource right-sizing                   │ │
│ │ • Configure auto-scaling policies                   │ │
│ │ • Test cost allocation accuracy                     │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 📅 PHASE 3: TENANT ONBOARDING (Weeks 7-10)              │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Week 7-8: Real Portuguese Hospital                  │ │
│ │ • Create tenant control plane                       │ │
│ │ • Deploy medical AI workloads                       │ │
│ │ • Migrate existing data                             │ │
│ │                                                     │ │
│ │ Week 9-10: Additional Tenants                       │ │
│ │ • Onboard Santa Maria Hospital                      │ │
│ │ • Onboard São João Hospital                         │ │
│ │ • Validate cross-tenant isolation                   │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 📅 PHASE 4: OPTIMIZATION (Weeks 11-12)                  │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Week 11: Performance Tuning                         │ │
│ │ • Optimize resource allocation                      │ │
│ │ • Fine-tune auto-scaling                            │ │
│ │ • Implement advanced monitoring                     │ │
│ │                                                     │ │
│ │ Week 12: Production Hardening                       │ │
│ │ • Security audit and hardening                      │ │
│ │ • Disaster recovery testing                         │ │
│ │ • Performance benchmarking                          │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

This comprehensive multi-tenant cloud architecture using **Kamaji**, **Kubecost**, and **GCP** enables **scalable, cost-effective** deployment of the Medical Record platform across **multiple hospitals** with **complete tenant isolation**, **transparent cost management**, and **enterprise-grade security**! ☁️🏥 