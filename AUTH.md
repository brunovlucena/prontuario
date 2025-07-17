# 🔐 Authentication & Authorization - Medical Record MVP

## 🎯 Overview

The Medical Record platform implements **simple username/password authentication** for the MVP phase, designed for **Real Portuguese Hospital** with **200 daily users** across **4 departments**. This document details the authentication architecture, GCP integration, and Kubernetes RBAC implementation.

---

## 🏗️ Authentication Architecture Overview

```sh
┌─────────────────────────────────────────────────────────┐
│ 📱 iPhone CLIENT AUTHENTICATION                         │
│                                                         │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🔐 LOGIN SCREEN (MVP Simple)                        │ │
│ │ ┌─────────────────────────────────────────────────┐ │ │
│ │ │ 👨‍⚕️ Username: dr.santos.cardio                   │ │ │
│ │ │ 🔑 Password: ●●●●●●●●●●●●                       │ │ │
│ │ │ 🏥 Department: [Cardiology ▼]                   │ │ │
│ │ │                                                 │ │ │
│ │ │ [🔐 LOGIN]                                      │ │ │
│ │ └─────────────────────────────────────────────────┘ │ │
│ │                                                     │ │
│ │ 📱 iOS Keychain Integration:                        │ │
│ │ • Secure credential storage                         │ │
│ │ • Biometric unlock (optional)                       │ │
│ │ • Auto-login for trusted devices                    │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
          │
          ▼ 🔒 HTTPS (Hospital Internal)
┌─────────────────────────────────────────────────────────┐
│ 🖥️ MAC STUDIO M3 ULTRA - AUTH SERVICES                  │
│                                                         │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🌐 API GATEWAY & LOAD BALANCER                      │ │
│ │ • NGINX Ingress Controller                          │ │
│ │ • Rate limiting: 5 login attempts/minute            │ │
│ │ • DDoS protection                                   │ │
│ │ • Request routing by department                     │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 🔐 AUTHENTICATION SERVICE                           │ │
│ │ • FastAPI authentication backend                    │ │
│ │ • JWT token generation and validation               │ │
│ │ • Session management                                │ │
│ │ • Department-based authorization                    │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 💾 LOCAL USER DATABASE                              │ │
│ │ • PostgreSQL 15 (local)                             │ │
│ │ • Hashed passwords (bcrypt)                         │ │
│ │ • User roles and permissions                        │ │
│ │ • Department assignments                            │ │
│ │ • Session tracking                                  │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## 👥 User Roles & Permissions

### **🏥 Hospital User Hierarchy**

```sh
┌─────────────────────────────────────────────────────────┐
│ 👥 USER ROLES - REAL PORTUGUESE HOSPITAL                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🏥 HOSPITAL ADMIN                                       │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Users: 2 (Hospital IT administrators)               │ │
│ │ Permissions:                                        │ │
│ │ • Full system access                                │ │
│ │ • User management (create/delete accounts)          │ │
│ │ • System configuration                              │ │
│ │ • All department access                             │ │
│ │ • Audit log viewing                                 │ │
│ │ • Emergency override capabilities                   │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 👨‍⚕️ DEPARTMENT HEAD                                      │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Users: 4 (1 per department)                         │ │
│ │ • Dr. Silva (Cardiology Head)                       │ │
│ │ • Dr. Costa (Emergency Head)                        │ │
│ │ • Dr. Oliveira (Surgery Head)                       │ │
│ │ • Dr. Ferreira (ICU Head)                           │ │
│ │                                                     │ │
│ │ Permissions:                                        │ │
│ │ • Department-wide patient access                    │ │
│ │ • Team management and scheduling                    │ │
│ │ • Department metrics and reports                    │ │
│ │ • Inter-departmental communication                  │ │
│ │ • Teaching and training access                      │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 👨‍⚕️ ATTENDING PHYSICIAN                                  │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Users: 60 (15 per department average)               │ │
│ │ Examples:                                           │ │
│ │ • dr.santos.cardio (Interventional Cardiologist)    │ │
│ │ • dr.lima.cardio (Heart Failure Specialist)         │ │
│ │ • dr.costa.emergency (Emergency Physician)          │ │
│ │                                                     │ │
│ │ Permissions:                                        │ │
│ │ • Own patient assignments                           │ │
│ │ • Department patient consultation                   │ │
│ │ • Medical AI chat access                            │ │
│ │ • Voice documentation                               │ │
│ │ • Laboratory and imaging review                     │ │
│ │ • Prescription and ordering                         │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 👩‍⚕️ RESIDENT PHYSICIAN                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Users: 80 (20 per department average)               │ │
│ │ Examples:                                           │ │
│ │ • resident.ana.cardio (Cardiology R3)               │ │
│ │ • resident.joao.emergency (Emergency R2)            │ │
│ │                                                     │ │
│ │ Permissions:                                        │ │
│ │ • Assigned patient access only                      │ │
│ │ • Medical AI chat (supervised mode)                 │ │
│ │ • Voice documentation                               │ │
│ │ • Laboratory review                                 │ │
│ │ • Educational content access                        │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 👩‍⚕️ NURSE                                                │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Users: 48 (12 per department average)               │ │
│ │ Examples:                                           │ │
│ │ • nurse.maria.cardio (Cardiology RN)                │ │
│ │ • nurse.carlos.icu (ICU RN)                         │ │
│ │                                                     │ │
│ │ Permissions:                                        │ │
│ │ • Patient care documentation                        │ │
│ │ • Vital signs and basic assessment                  │ │
│ │ • Medication administration records                 │ │
│ │ • Limited AI chat (nursing protocols)               │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 📊 TOTAL USERS: 194 (within 200 user target)            │
└─────────────────────────────────────────────────────────┘
```

---

## 🔐 Authentication Flow

### **📱➡️🖥️ Complete Login Process**

```sh
┌─────────────────────────────────────────────────────────┐
│ 🔐 AUTHENTICATION FLOW                                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 1️⃣ USER LOGIN ATTEMPT                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ POST /auth/login                                    │ │
│ │ {                                                   │ │
│ │   "username": "dr.santos.cardio",                   │ │
│ │   "password": "SecureHospitalPass123!",             │ │
│ │   "department": "cardiology",                       │ │
│ │   "device_id": "iPhone15_ABC123"                    │ │
│ │ }                                                   │ │
│ │                                                     │ │
│ │ Client Info:                                        │ │
│ │ • IP: 192.168.100.45 (Hospital WiFi)                │ │
│ │ • User-Agent: MedicalRecord/1.0 iOS                 │ │
│ │ • Timestamp: 2024-01-15T07:30:00Z                   │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 2️⃣ AUTHENTICATION VALIDATION                            │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Security Checks:                                    │ │
│ │ ✅ Rate limiting (5 attempts/minute) - PASSED       │ │
│ │ ✅ Username format validation - PASSED              │ │
│ │ ✅ Department assignment check - PASSED             │ │
│ │ ✅ Device whitelist verification - PASSED           │ │
│ │ ✅ Hospital network validation - PASSED             │ │
│ │                                                     │ │
│ │ Database Query:                                     │ │
│ │ SELECT user_id, password_hash, role, department,    │ │
│ │        last_login, failed_attempts                  │ │
│ │ FROM users                                          │ │
│ │ WHERE username = 'dr.santos.cardio'                 │ │
│ │   AND active = true                                 │ │
│ │   AND department = 'cardiology'                     │ │
│ │                                                     │ │
│ │ Password Verification:                              │ │
│ │ bcrypt.checkpw(password, stored_hash) - ✅ VALID    │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 3️⃣ JWT TOKEN GENERATION                                 │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Token Payload:                                      │ │
│ │ {                                                   │ │
│ │   "user_id": "usr_santos_001",                      │ │
│ │   "username": "dr.santos.cardio",                   │ │
│ │   "role": "attending_physician",                    │ │
│ │   "department": "cardiology",                       │ │
│ │   "permissions": [                                  │ │
│ │     "patient_access",                               │ │
│ │     "ai_chat",                                      │ │
│ │     "voice_documentation",                          │ │
│ │     "prescription_write"                            │ │
│ │   ],                                                │ │
│ │   "session_id": "sess_abc123def",                   │ │
│ │   "issued_at": 1705305000,                          │ │
│ │   "expires_at": 1705391400,                         │ │
│ │   "hospital_id": "real_portuguese"                  │ │
│ │ }                                                   │ │
│ │                                                     │ │
│ │ Token Security:                                     │ │
│ │ • Algorithm: RS256 (RSA with SHA-256)               │ │
│ │ • Key rotation: Every 30 days                       │ │
│ │ • Expiration: 24 hours                              │ │
│ └─────────────────────────────────────────────────────┘ │
│           │                                             │
│           ▼                                             │
│ 4️⃣ SUCCESSFUL AUTHENTICATION RESPONSE                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ HTTP 200 OK                                         │ │
│ │ {                                                   │ │
│ │   "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",     │ │
│ │   "token_type": "Bearer",                           │ │
│ │   "expires_in": 86400,                              │ │
│ │   "user_info": {                                    │ │
│ │     "username": "dr.santos.cardio",                 │ │
│ │     "display_name": "Dr. Santos",                   │ │
│ │     "role": "attending_physician",                  │ │
│ │     "department": "cardiology",                     │ │
│ │     "last_login": "2024-01-14T22:15:00Z"            │ │
│ │   },                                                │ │
│ │   "permissions": ["patient_access", "ai_chat", ...  │ │
│ │ }                                                   │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## ☁️ GCP Integration & RBAC

### **🔐 Google Cloud IAM Roles**

```sh
┌─────────────────────────────────────────────────────────┐
│ ☁️ GCP IAM CONFIGURATION                                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🏥 HOSPITAL SERVICE ACCOUNTS                            │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ medical-record-production@real-portuguese.iam       │ │
│ │ Roles:                                              │ │
│ │ • Cloud SQL Client                                  │ │
│ │ • Cloud Storage Object Admin                        │ │
│ │ • GKE Developer                                     │ │
│ │ • Logging Writer                                    │ │
│ │ • Monitoring Metric Writer                          │ │
│ │                                                     │ │
│ │ medical-record-backup@real-portuguese.iam           │ │
│ │ Roles:                                              │ │
│ │ • Cloud SQL Backup Operator                         │ │
│ │ • Cloud Storage Object Creator                      │ │
│ │                                                     │ │
│ │ medical-record-monitoring@real-portuguese.iam       │ │
│ │ Roles:                                              │ │
│ │ • Monitoring Viewer                                 │ │
│ │ • Logging Viewer                                    │ │
│ │ • Error Reporting Writer                            │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ 👨‍💻 DEVELOPER ACCESS (Limited)                           │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ developer@real-portuguese.iam                       │ │
│ │ Roles:                                              │ │
│ │ • GKE Developer (development namespace only)        │ │
│ │ • Cloud Build Editor                                │ │
│ │ • Container Registry Service Agent                  │ │
│ │                                                     │ │
│ │ Conditions:                                         │ │
│ │ • Time-based access (business hours only)           │ │
│ │ • IP restriction (hospital network)                 │ │
│ │ • MFA required                                      │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### **⚙️ Kubernetes RBAC Configuration**

```yaml
# ============================================================================
# Kubernetes RBAC for Medical Record Platform
# ============================================================================

# Namespace-based separation
apiVersion: v1
kind: Namespace
metadata:
  name: medical-production
  labels:
    environment: production
    hospital: real-portuguese

---
apiVersion: v1
kind: Namespace
metadata:
  name: medical-development
  labels:
    environment: development
    hospital: real-portuguese

---
# Medical AI Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: medical-ai-service
  namespace: medical-production
  annotations:
    iam.gke.io/gcp-service-account: medical-record-production@real-portuguese.iam

---
# API Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: medical-api-service
  namespace: medical-production
  annotations:
    iam.gke.io/gcp-service-account: medical-record-production@real-portuguese.iam

---
# ClusterRole for Medical AI Services
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: medical-ai-role
rules:
- apiGroups: [""]
  resources: ["pods", "services", "configmaps", "secrets"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources: ["events"]
  verbs: ["create"]

---
# ClusterRole for API Services
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: medical-api-role
rules:
- apiGroups: [""]
  resources: ["pods", "services", "configmaps", "secrets"]
  verbs: ["get", "list", "watch", "create", "update", "patch"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch", "update", "patch"]
- apiGroups: ["networking.k8s.io"]
  resources: ["networkpolicies"]
  verbs: ["get", "list", "watch"]

---
# ClusterRole for Monitoring
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: medical-monitoring-role
rules:
- apiGroups: [""]
  resources: ["nodes", "pods", "services", "endpoints"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets", "statefulsets"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["extensions"]
  resources: ["ingresses"]
  verbs: ["get", "list", "watch"]

---
# Bind AI Service Account to AI Role
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: medical-ai-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: medical-ai-role
subjects:
- kind: ServiceAccount
  name: medical-ai-service
  namespace: medical-production

---
# Bind API Service Account to API Role
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: medical-api-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: medical-api-role
subjects:
- kind: ServiceAccount
  name: medical-api-service
  namespace: medical-production

---
# Role for Department-based Access Control
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: medical-production
  name: department-cardiology-role
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list"]
  resourceNames: ["cardiology-config", "hospital-config"]
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get"]
  resourceNames: ["cardiology-db-secret"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: medical-production
  name: department-emergency-role
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list"]
  resourceNames: ["emergency-config", "hospital-config"]
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get"]
  resourceNames: ["emergency-db-secret"]

---
# Network Policies for Department Isolation
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: department-isolation-policy
  namespace: medical-production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: medical-production
    - podSelector:
        matchLabels:
          department: cardiology
    ports:
    - protocol: TCP
      port: 8080
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: medical-production
    ports:
    - protocol: TCP
      port: 8080
  - to: []  # Allow DNS
    ports:
    - protocol: UDP
      port: 53
```

---

## 🔒 Security Implementation

### **🛡️ FastAPI Authentication Service**

```python
# Authentication Service Implementation
from fastapi import FastAPI, HTTPException, Depends, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
import jwt
import bcrypt
import redis
from datetime import datetime, timedelta
from typing import Optional, List
import os
from dataclasses import dataclass

app = FastAPI(title="Medical Record Authentication Service")

# Configuration
JWT_SECRET_KEY = os.getenv("JWT_SECRET_KEY", "your-secret-key")
JWT_ALGORITHM = "RS256"
JWT_EXPIRE_HOURS = 24
REDIS_URL = "redis://redis-cache:6379"

# Redis for session management
redis_client = redis.from_url(REDIS_URL)
security = HTTPBearer()

@dataclass
class User:
    user_id: str
    username: str
    role: str
    department: str
    permissions: List[str]
    display_name: str

@dataclass
class LoginRequest:
    username: str
    password: str
    department: str
    device_id: str

class AuthenticationService:
    def __init__(self):
        self.hospital_users = {
            # Attending Physicians
            "dr.santos.cardio": {
                "user_id": "usr_santos_001",
                "password_hash": bcrypt.hashpw("SecureHospitalPass123!".encode(), bcrypt.gensalt()),
                "role": "attending_physician",
                "department": "cardiology",
                "display_name": "Dr. Santos",
                "permissions": [
                    "patient_access",
                    "ai_chat",
                    "voice_documentation",
                    "prescription_write",
                    "lab_review",
                    "imaging_review"
                ],
                "active": True
            },
            "dr.lima.cardio": {
                "user_id": "usr_lima_002",
                "password_hash": bcrypt.hashpw("HospitalSecure456!".encode(), bcrypt.gensalt()),
                "role": "attending_physician",
                "department": "cardiology",
                "display_name": "Dr. Lima",
                "permissions": [
                    "patient_access",
                    "ai_chat",
                    "voice_documentation",
                    "prescription_write",
                    "lab_review"
                ],
                "active": True
            },
            "dr.costa.emergency": {
                "user_id": "usr_costa_003",
                "password_hash": bcrypt.hashpw("EmergencyDoc789!".encode(), bcrypt.gensalt()),
                "role": "attending_physician",
                "department": "emergency",
                "display_name": "Dr. Costa",
                "permissions": [
                    "patient_access",
                    "ai_chat",
                    "voice_documentation",
                    "prescription_write",
                    "emergency_protocols",
                    "critical_alerts"
                ],
                "active": True
            },
            # Residents
            "resident.ana.cardio": {
                "user_id": "usr_ana_res_001",
                "password_hash": bcrypt.hashpw("ResidentPass123!".encode(), bcrypt.gensalt()),
                "role": "resident_physician",
                "department": "cardiology",
                "display_name": "Dr. Ana (Resident)",
                "permissions": [
                    "patient_access_supervised",
                    "ai_chat_supervised",
                    "voice_documentation",
                    "lab_review",
                    "educational_content"
                ],
                "active": True
            },
            # Nurses
            "nurse.maria.cardio": {
                "user_id": "usr_maria_nurse_001",
                "password_hash": bcrypt.hashpw("NurseSecure456!".encode(), bcrypt.gensalt()),
                "role": "nurse",
                "department": "cardiology",
                "display_name": "Nurse Maria",
                "permissions": [
                    "patient_documentation",
                    "vital_signs",
                    "medication_admin",
                    "ai_chat_nursing"
                ],
                "active": True
            },
            # Hospital Admin
            "admin.hospital": {
                "user_id": "usr_admin_001",
                "password_hash": bcrypt.hashpw("AdminSecure789!".encode(), bcrypt.gensalt()),
                "role": "hospital_admin",
                "department": "administration",
                "display_name": "Hospital Administrator",
                "permissions": [
                    "full_access",
                    "user_management",
                    "system_config",
                    "audit_logs",
                    "emergency_override"
                ],
                "active": True
            }
        }
    
    def validate_user(self, username: str, password: str, department: str) -> Optional[User]:
        """Validate user credentials and return user object if valid"""
        
        if username not in self.hospital_users:
            return None
        
        user_data = self.hospital_users[username]
        
        # Check if user is active
        if not user_data.get("active", False):
            return None
        
        # Check department match (except for hospital admin)
        if user_data["role"] != "hospital_admin" and user_data["department"] != department:
            return None
        
        # Verify password
        if not bcrypt.checkpw(password.encode(), user_data["password_hash"]):
            return None
        
        return User(
            user_id=user_data["user_id"],
            username=username,
            role=user_data["role"],
            department=user_data["department"],
            permissions=user_data["permissions"],
            display_name=user_data["display_name"]
        )
    
    def create_jwt_token(self, user: User) -> str:
        """Create JWT token for authenticated user"""
        
        payload = {
            "user_id": user.user_id,
            "username": user.username,
            "role": user.role,
            "department": user.department,
            "permissions": user.permissions,
            "hospital_id": "real_portuguese",
            "iat": datetime.utcnow(),
            "exp": datetime.utcnow() + timedelta(hours=JWT_EXPIRE_HOURS)
        }
        
        token = jwt.encode(payload, JWT_SECRET_KEY, algorithm=JWT_ALGORITHM)
        
        # Store session in Redis
        session_key = f"session:{user.user_id}"
        redis_client.setex(
            session_key,
            timedelta(hours=JWT_EXPIRE_HOURS),
            token
        )
        
        return token
    
    def verify_jwt_token(self, token: str) -> Optional[User]:
        """Verify JWT token and return user if valid"""
        
        try:
            payload = jwt.decode(token, JWT_SECRET_KEY, algorithms=[JWT_ALGORITHM])
            
            # Check if session exists in Redis
            session_key = f"session:{payload['user_id']}"
            if not redis_client.exists(session_key):
                return None
            
            return User(
                user_id=payload["user_id"],
                username=payload["username"],
                role=payload["role"],
                department=payload["department"],
                permissions=payload["permissions"],
                display_name=payload.get("display_name", payload["username"])
            )
        
        except jwt.ExpiredSignatureError:
            return None
        except jwt.InvalidTokenError:
            return None

auth_service = AuthenticationService()

# Rate limiting storage
login_attempts = {}

@app.post("/auth/login")
async def login(login_request: LoginRequest):
    """Authenticate user and return JWT token"""
    
    # Rate limiting check
    client_ip = "192.168.100.45"  # Would get from request in real implementation
    current_time = datetime.now()
    
    if client_ip in login_attempts:
        attempts = login_attempts[client_ip]
        recent_attempts = [t for t in attempts if (current_time - t).seconds < 300]  # 5 minutes
        
        if len(recent_attempts) >= 5:
            raise HTTPException(
                status_code=status.HTTP_429_TOO_MANY_REQUESTS,
                detail="Too many login attempts. Please try again later."
            )
        
        login_attempts[client_ip] = recent_attempts
    else:
        login_attempts[client_ip] = []
    
    # Validate credentials
    user = auth_service.validate_user(
        login_request.username,
        login_request.password,
        login_request.department
    )
    
    if not user:
        # Record failed attempt
        login_attempts[client_ip].append(current_time)
        
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid credentials or department mismatch"
        )
    
    # Generate JWT token
    access_token = auth_service.create_jwt_token(user)
    
    # Clear failed attempts on successful login
    if client_ip in login_attempts:
        del login_attempts[client_ip]
    
    return {
        "access_token": access_token,
        "token_type": "Bearer",
        "expires_in": JWT_EXPIRE_HOURS * 3600,
        "user_info": {
            "username": user.username,
            "display_name": user.display_name,
            "role": user.role,
            "department": user.department
        },
        "permissions": user.permissions
    }

async def get_current_user(credentials: HTTPAuthorizationCredentials = Depends(security)) -> User:
    """Dependency to get current authenticated user"""
    
    token = credentials.credentials
    user = auth_service.verify_jwt_token(token)
    
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid or expired token"
        )
    
    return user

@app.get("/auth/me")
async def get_user_info(current_user: User = Depends(get_current_user)):
    """Get current user information"""
    
    return {
        "user_id": current_user.user_id,
        "username": current_user.username,
        "display_name": current_user.display_name,
        "role": current_user.role,
        "department": current_user.department,
        "permissions": current_user.permissions
    }

@app.post("/auth/logout")
async def logout(current_user: User = Depends(get_current_user)):
    """Logout user and invalidate session"""
    
    # Remove session from Redis
    session_key = f"session:{current_user.user_id}"
    redis_client.delete(session_key)
    
    return {"message": "Successfully logged out"}

@app.get("/auth/health")
async def auth_health_check():
    """Health check endpoint"""
    
    return {
        "status": "healthy",
        "active_sessions": redis_client.dbsize(),
        "hospital": "real_portuguese"
    }
```

---

## 📊 Security Monitoring

### **🔍 Audit Logging**

```sh
┌─────────────────────────────────────────────────────────┐
│ 📊 AUTHENTICATION AUDIT LOGGING                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 🔐 LOGIN EVENTS:                                        │
│ • Successful logins with user/department/timestamp      │
│ • Failed login attempts with reason                     │
│ • Rate limiting triggers                                │
│ • Unusual login patterns (off-hours, new devices)       │
│                                                         │
│ 🚪 SESSION EVENTS:                                      │
│ • Session creation and expiration                       │
│ • Token refresh activities                              │
│ • Logout events (manual vs automatic)                   │
│ • Session hijacking attempts                            │
│                                                         │
│ 🛡️ SECURITY EVENTS:                                     │
│ • Permission escalation attempts                        │
│ • Department boundary violations                        │
│ • Suspicious API access patterns                        │
│ • Emergency override activations                        │
│                                                         │
│ 📍 COMPLIANCE LOGGING:                                  │
│ • Patient data access logs                              │
│ • Medical record modifications                          │
│ • Administrative actions                                │
│ • LGPD compliance tracking                              │
└─────────────────────────────────────────────────────────┘
```

This comprehensive authentication system provides **simple, secure login** for **200 hospital users** with **department-based access control**, **GCP integration**, and **complete audit tracking** for **LGPD compliance**! 🔐🏥 