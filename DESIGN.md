# 🎨 Design da Interface - Prontuário MVP

## 📱 Conceito de Design Híbrido

A interface do Prontuário combina os melhores elementos de três paradigmas de design para criar uma experiência médica otimizada:

| **Inspiração** | **Elementos Adotados** | **Aplicação Médica** |
|----------------|------------------------|----------------------|
| **💬 WhatsApp** | **Chat bubbles, threads, áudio** | **Conversas com IA médica, histórico pacientes** |
| **🤖 Gemini** | **Respostas estruturadas, code blocks** | **Planos tratamento, protocolos médicos** |
| **🧠 memOS** | **Interface limpa, foco cognitivo** | **Redução sobrecarga, workflow eficiente** |

---

## 🎨 Paleta de Cores Médica

### **Cores Primárias**

```
🔵 Azul Médico Principal: #1565C0 (Confiança, profissionalismo)
⚪ Branco Limpo: #FFFFFF (Clareza, higiene)
🔷 Azul Claro: #E3F2FD (Calma, serenidade)
⚫ Cinza Texto: #212121 (Legibilidade)
```

### **Cores de Status Médico**

```
🟢 Verde Sucesso: #4CAF50 (Resultados normais, sucesso)
🟡 Amarelo Alerta: #FF9800 (Atenção, resultados borderline)
🔴 Vermelho Crítico: #F44336 (Urgente, valores anormais)
🟣 Roxo IA: #6A1B9A (Respostas da IA médica)
```

---

## 📱 Wireframes das Telas Principais

### **1. Tela de Chat Principal (Inspirada em WhatsApp)**

```mermaid
flowchart TD
    Header["📱 HEADER<br/>👨‍⚕️ Dr. Silva | 🔔 Alertas | ⚙️"]
    
    ChatArea["💬 ÁREA DE CHAT<br/>━━━━━━━━━━━━━━━━━━━━━━━━━━<br/>🤖 IA: Bom dia! Como posso ajudar?<br/>━━━━━━━━━━━━━━━━━━━━━━━━━━<br/>👨‍⚕️ Mostre dados Paciente 123<br/>━━━━━━━━━━━━━━━━━━━━━━━━━━<br/>🤖 [CARD ESTRUTURADO]<br/>📊 Maria Silva, 45 anos<br/>🩺 Diabetes Tipo 2<br/>💊 Metformina 500mg<br/>📈 HbA1c: 7.2% (⚠️ Alto)<br/>━━━━━━━━━━━━━━━━━━━━━━━━━━"]
    
    InputBar["📝 BARRA INPUT<br/>🎤 [Gravação] | Digite mensagem... | 📁 Upload | ➤"]
    
    QuickActions["⚡ AÇÕES RÁPIDAS<br/>👥 Pacientes | 🔬 Labs | 💊 Receitas | 📊 Relatórios"]

    Header --> ChatArea
    ChatArea --> InputBar
    InputBar --> QuickActions

    style Header fill:#1565C0,stroke:#0D47A1,stroke-width:2px,color:#fff
    style ChatArea fill:#F5F5F5,stroke:#E0E0E0,stroke-width:2px,color:#000
    style InputBar fill:#FFFFFF,stroke:#1565C0,stroke-width:2px,color:#000
    style QuickActions fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#000
```

### **2. Resposta Estruturada da IA (Inspirada em Gemini)**

```mermaid
flowchart TD
    UserQuery["👨‍⚕️ USUÁRIO<br/>Analise exames de Maria Silva"]
    
    AIResponse["🤖 RESPOSTA IA ESTRUTURADA<br/>┌─────────────────────────────────┐<br/>│ 📊 ANÁLISE LABORATORIAL        │<br/>├─────────────────────────────────┤<br/>│ 🔍 Glicemia: 180 mg/dL         │<br/>│ ⚠️  Status: ELEVADA            │<br/>│ 📈 Tendência: +15% (30 dias)   │<br/>├─────────────────────────────────┤<br/>│ 💡 RECOMENDAÇÕES               │<br/>│ • Ajustar Metformina para 850mg│<br/>│ • Reavaliar em 2 semanas       │<br/>│ • Orientar dieta restritiva     │<br/>├─────────────────────────────────┤<br/>│ 📋 PRÓXIMOS PASSOS             │<br/>│ [ ] Receita atualizada          │<br/>│ [ ] Agendamento retorno         │<br/>│ [ ] Orientação nutricional      │<br/>└─────────────────────────────────┘"]
    
    ActionButtons["🎯 BOTÕES AÇÃO<br/>[💊 Prescrever] [📅 Agendar] [📄 Imprimir]"]

    UserQuery --> AIResponse
    AIResponse --> ActionButtons

    style UserQuery fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#000
    style AIResponse fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#000
    style ActionButtons fill:#FFFFFF,stroke:#4CAF50,stroke-width:2px,color:#000
```

### **3. Interface Limpa - Foco Cognitivo (Inspirada em memOS)**

```mermaid
flowchart TD
    MinimalHeader["📱 HEADER MINIMALISTA<br/>Prontuário • 14:30"]
    
    FocusArea["🎯 ÁREA DE FOCO<br/>━━━━━━━━━━━━━━━━━━━━━━━━━━<br/>       PACIENTE ATIVO       <br/>━━━━━━━━━━━━━━━━━━━━━━━━━━<br/>    👨‍⚕️ João Santos, 67a      <br/>    🫀 Cardiologia           <br/>    ⏰ Consulta: 14:45       <br/>━━━━━━━━━━━━━━━━━━━━━━━━━━<br/>                              <br/>  🎤 Diga o que precisa...   <br/>                              <br/>━━━━━━━━━━━━━━━━━━━━━━━━━━"]
    
    ContextCards["📋 CARDS CONTEXTUAIS<br/>🩺 Últimos Sinais Vitais<br/>💊 Medicações Atuais<br/>📊 Exames Pendentes"]

    MinimalHeader --> FocusArea
    FocusArea --> ContextCards

    style MinimalHeader fill:#FAFAFA,stroke:#E0E0E0,stroke-width:1px,color:#666
    style FocusArea fill:#FFFFFF,stroke:#E0E0E0,stroke-width:2px,color:#000
    style ContextCards fill:#F8F9FA,stroke:#E0E0E0,stroke-width:1px,color:#000
```

---

## 🗨️ Componentes de Interface

### **Chat Bubble - Estilo WhatsApp Médico**

#### **Mensagem do Médico**
```
┌─────────────────────────────────────┐
│ 👨‍⚕️ Dr. Silva • 14:32                │
│ Prescreva Ibuprofeno 400mg para     │
│ Maria Silva                         │
│                              ✓✓ Lido │
└─────────────────────────────────────┘
```

#### **Resposta da IA Médica**
```
┌─────────────────────────────────────┐
│ 🤖 IA Médica • 14:32                │
│                                     │
│ 💊 PRESCRIÇÃO GERADA                │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│ Paciente: Maria Silva (45a)         │
│ Medicamento: Ibuprofeno 400mg       │
│ Posologia: 1cp 8/8h por 5 dias     │
│ ⚠️ Verificado: Sem interações       │
│                                     │
│ [📄 Imprimir] [📧 Enviar] [✏️ Editar] │
└─────────────────────────────────────┘
```

### **Cards de Dados Estruturados - Estilo Gemini**

#### **Card de Paciente**
```
╭─────────────────────────────────────╮
│ 👤 DADOS DO PACIENTE                │
├─────────────────────────────────────┤
│ Nome: Maria Silva                   │
│ Idade: 45 anos                      │
│ Sexo: Feminino                      │
│ Registro: #12345                    │
├─────────────────────────────────────┤
│ 🏥 DEPARTAMENTO                     │
│ Endocrinologia - Sala 203           │
│ Dr. Roberto Silva                   │
├─────────────────────────────────────┤
│ 📅 ÚLTIMA CONSULTA                  │
│ 15/01/2024 - Controle diabetes     │
│ Próxima: 29/01/2024                │
╰─────────────────────────────────────╯
```

#### **Card de Exames**
```
╭─────────────────────────────────────╮
│ 🔬 RESULTADOS LABORATORIAIS         │
├─────────────────────────────────────┤
│ Data: 20/01/2024                    │
│                                     │
│ 🩸 Glicemia em Jejum                │
│ 180 mg/dL     ⚠️ ALTO (REF: 70-100) │
│                                     │
│ 🧪 HbA1c                           │
│ 7.2%          ⚠️ ALTO (REF: <7.0)   │
│                                     │
│ 💉 Insulina                         │
│ 15 mU/L       ✅ NORMAL (REF: 2-20) │
├─────────────────────────────────────┤
│ 🎯 AÇÃO REQUERIDA                   │
│ Ajuste medicação + dieta restritiva │
╰─────────────────────────────────────╯
```

---

## 🎤 Interação por Voz - Interface de Áudio

### **Estado de Gravação Ativa**

```mermaid
flowchart TD
    RecordingState["🎤 GRAVANDO ÁUDIO<br/>┌─────────────────────────┐<br/>│   🔴 REC  00:15         │<br/>│  ████████░░░░░░░░░░░    │<br/>│                         │<br/>│ 'Prescreva Metformina   │<br/>│  500mg para diabete...' │<br/>│                         │<br/>│   [⏹️ Parar] [🗑️ Cancelar] │<br/>└─────────────────────────┘"]
    
    ProcessingState["🤖 PROCESSANDO<br/>┌─────────────────────────┐<br/>│   🔄 Analisando...      │<br/>│  ●●●●●●●●●●●●●●●●●●●●    │<br/>│                         │<br/>│ Reconhecendo termos     │<br/>│ médicos e processando   │<br/>│ sua solicitação...      │<br/>└─────────────────────────┘"]
    
    ResponseState["✅ RESPOSTA GERADA<br/>┌─────────────────────────┐<br/>│ 🤖 Entendi sua solicit. │<br/>│                         │<br/>│ Prescrição Metformina:  │<br/>│ • 500mg 2x/dia          │<br/>│ • Com refeições         │<br/>│ • Controle em 30 dias   │<br/>│                         │<br/>│ [📄 Confirmar] [🔄 Refazer]│<br/>└─────────────────────────┘"]

    RecordingState --> ProcessingState
    ProcessingState --> ResponseState

    style RecordingState fill:#FFEBEE,stroke:#F44336,stroke-width:2px,color:#000
    style ProcessingState fill:#FFF3E0,stroke:#FF9800,stroke-width:2px,color:#000
    style ResponseState fill:#E8F5E8,stroke:#4CAF50,stroke-width:2px,color:#000
```

---

## 📊 Visualização de Dados Médicos

### **Gráfico de Tendência - Interface Limpa**

```mermaid
flowchart TD
    ChartHeader["📈 GLICEMIA - ÚLTIMOS 30 DIAS"]
    
    ChartArea["GRÁFICO INTERATIVO<br/>┌─────────────────────────────────┐<br/>│ 200mg/dL ┼                     │<br/>│          │     ●               │<br/>│ 150mg/dL ┼   ●   ●             │<br/>│          │ ●       ●           │<br/>│ 100mg/dL ┼●         ● ●       │<br/>│          │             ●       │<br/>│  50mg/dL ┼                     │<br/>│          └─────────────────────│<br/>│           Jan 1  Jan 15  Jan 30│<br/>└─────────────────────────────────┘<br/>🎯 Toque nos pontos para detalhes"]
    
    ChartInsights["💡 INSIGHTS IA<br/>• Tendência: Melhora gradual<br/>• Picos: Relacionados a stress<br/>• Meta: Manter < 130mg/dL"]

    ChartHeader --> ChartArea
    ChartArea --> ChartInsights

    style ChartHeader fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#000
    style ChartArea fill:#FAFAFA,stroke:#E0E0E0,stroke-width:2px,color:#000
    style ChartInsights fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#000
```

---

## 🔧 Estados de Interface

### **Estado de Loading - memOS Inspired**

```
┌─────────────────────────┐
│                         │
│    ●●●●●●●●●●●●●●●●●●●   │
│                         │
│   Consultando IA...     │
│                         │
│   Analisando dados do   │
│   paciente Maria Silva  │
│                         │
└─────────────────────────┘
```

### **Estado de Erro - Amigável**

```
┌─────────────────────────┐
│   ⚠️ Ops! Algo deu errado  │
│                         │
│ Não consegui processar  │
│ sua solicitação.        │
│                         │
│ [🔄 Tentar Novamente]    │
│ [🎤 Usar Voz]           │
│ [💬 Chat Texto]         │
└─────────────────────────┘
```

---

## 📱 Responsive Design - Adaptação Mobile

### **Layout Portrait (Vertical)**
```
┌─────────────────┐
│ 📱 Header       │
├─────────────────┤
│                 │
│  💬 Chat Area   │
│                 │
│     (Scroll)    │
│                 │
├─────────────────┤
│ 📝 Input + 🎤   │
├─────────────────┤
│ ⚡ Quick Actions │
└─────────────────┘
```

### **Layout Landscape (Horizontal)**
```
┌─────────────┬─────────────┐
│ 📱 Header   │             │
├─────────────┤ 📋 Context  │
│             │    Panel    │
│ 💬 Chat     │             │
│   Area      │ 📊 Charts   │
│             │             │
│ 📝 Input+🎤 │ 📋 Actions  │
└─────────────┴─────────────┘
```

---

## 🎯 Microinterações

### **Feedback Tátil (Haptic)**
- **🎤 Início gravação**: Haptic leve
- **✅ Ação confirmada**: Haptic sucesso  
- **⚠️ Erro/Alerta**: Haptic erro
- **📱 Nova mensagem**: Haptic notificação

### **Animações Suaves**
- **💬 Chat bubbles**: Fade in bottom-up
- **📊 Cards**: Slide in left-right  
- **🎤 Recording**: Pulse animation
- **⚡ Loading**: Gentle breathing animation

---

## 🧪 Protótipo de Fluxo Completo

### **Cenário: Prescrição de Medicamento**

```mermaid
flowchart TD
    Start["👨‍⚕️ Dr. abre app"] 
    --> VoiceInput["🎤 'Prescreva Ibuprofeno para João'"]
    --> Processing["🤖 IA processa comando"]
    --> PatientSearch["🔍 Busca paciente João"]
    --> SafetyCheck["⚠️ Verifica interações medicamentosas"]
    --> PrescriptionCard["💊 Gera card prescrição estruturado"]
    --> Review["👨‍⚕️ Dr. revisa e confirma"]
    --> Generate["📄 Gera receita final"]
    --> Complete["✅ Prescrição completa"]

    style Start fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#000
    style VoiceInput fill:#FFEBEE,stroke:#F44336,stroke-width:2px,color:#000
    style Processing fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#000
    style PatientSearch fill:#FFF3E0,stroke:#FF9800,stroke-width:2px,color:#000
    style SafetyCheck fill:#FFEBEE,stroke:#F44336,stroke-width:2px,color:#000
    style PrescriptionCard fill:#E8F5E8,stroke:#4CAF50,stroke-width:2px,color:#000
    style Review fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#000
    style Generate fill:#F8F9FA,stroke:#6C757D,stroke-width:2px,color:#000
    style Complete fill:#E8F5E8,stroke:#4CAF50,stroke-width:2px,color:#000
```

---

## 📐 Especificações Técnicas

### **Tipografia**
- **Heading**: SF Pro Display Semibold 18-24pt
- **Body**: SF Pro Text Regular 16pt  
- **Caption**: SF Pro Text Regular 14pt
- **Monospace**: SF Mono Regular 14pt (dados médicos)

### **Espaçamento**
- **Padding**: 16px (padrão)
- **Margins**: 8px, 16px, 24px
- **Chat bubbles**: 12px padding interno
- **Card radius**: 12px border-radius

### **Acessibilidade**
- **Contraste**: WCAG AA compliant
- **Font scaling**: Suporte Dynamic Type
- **Voice Over**: Labels descritivos
- **Gesture navigation**: Suporte completo

---

## 🚀 Implementação SwiftUI

### **Componente Chat Bubble**
```swift
struct MedicalChatBubble: View {
    let message: MedicalMessage
    let isFromDoctor: Bool
    
    var body: some View {
        HStack {
            if isFromDoctor { Spacer() }
            
            VStack(alignment: .leading, spacing: 8) {
                HStack {
                    Image(systemName: isFromDoctor ? "stethoscope" : "brain.head.profile")
                    Text(isFromDoctor ? "Dr. Silva" : "IA Médica")
                        .font(.caption)
                        .foregroundColor(.secondary)
                }
                
                Text(message.content)
                    .font(.body)
                    .padding(.horizontal, 16)
                    .padding(.vertical, 12)
                    .background(
                        RoundedRectangle(cornerRadius: 18)
                            .fill(isFromDoctor ? Color.blue : Color.purple.opacity(0.1))
                    )
                    .foregroundColor(isFromDoctor ? .white : .primary)
            }
            
            if !isFromDoctor { Spacer() }
        }
        .padding(.horizontal)
    }
}
```

Esta especificação de design garante uma interface médica **familiar** (WhatsApp), **inteligente** (Gemini), e **focada** (memOS) para máxima eficiência no ambiente hospitalar. 