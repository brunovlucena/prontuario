# 🎨 Design Interface - Prontuário MVP

## 📱 Conceito Visual: WhatsApp + Gemini

**Main Screen** = Lista pacientes estilo WhatsApp  
**Chat Screen** = Interface limpa estilo Gemini  

---

## 🎨 Cores

```
🔵 Verde WhatsApp: #075E54 (header)
⚪ Verde Claro: #25D366 (ações)  
🤖 Azul Gemini: #1A73E8 (chat médico)
⚪ Branco: #FFFFFF (background)
🔘 Cinza: #F0F0F0 (inputs)
```

---

## 📱 Main Screen - Lista Pacientes (WhatsApp Style)

```
┌─────────────────────────────────────┐
│ Prontuário            🔍  📞  ⋮     │ ← Header verde WhatsApp
├─────────────────────────────────────┤
│ 🔍 Buscar pacientes...              │
├─────────────────────────────────────┤
│ Todos | Urgente 🔴 3 | Hoje 📅 12   │ ← Filtros
├─────────────────────────────────────┤
│                                     │
│ 👤  Maria Silva              23:45  │
│     🩺 Diabetes - Glicemia alta     │
│     ─────────────────────────────   │
│                                     │
│ 👤  João Santos              18:30  │
│     ❤️ Cardiologia - Pressão ok     │
│     ─────────────────────────────   │
│                                     │
│ 👤  Ana Costa                16:15  │
│     🤰 Pré-natal - Tudo normal      │
│     ─────────────────────────────   │
│                                     │
│ 👤  Pedro Lima               15:42  │
│     🧠 Neurologia - Exames ok       │
│     ─────────────────────────────   │
│                                     │
├─────────────────────────────────────┤
│  📞    👥    💬    ⚙️              │ ← Tab bar
└─────────────────────────────────────┘
```

---

## 💬 Chat Screen - Conversa IA (Gemini Style)

```
┌─────────────────────────────────────┐
│ ← Maria Silva, 45a           🩺     │ ← Header azul Gemini
├─────────────────────────────────────┤
│                                     │
│                                     │
│                                     │
│            Hello, Dr. Silva         │ ← Centro limpo tipo Gemini
│                                     │
│                                     │
│                                     │
├─────────────────────────────────────┤
│ Como posso ajudar com Maria Silva?  │
│                         🎤 📎 ↗️    │ ← Input minimalista
├─────────────────────────────────────┤
│    Examinar  |  Prescrever  |  Histórico   │ ← Sugestões
└─────────────────────────────────────┘
```

---

## 🗨️ Chat em Ação - Conversa Médica

```
┌─────────────────────────────────────┐
│ ← Maria Silva, 45a           🩺     │
├─────────────────────────────────────┤
│                                     │
│     ┌─────────────────────────────┐ │
│     │ Analise exames Maria Silva  │ │ ← Bubble azul médico
│     │                       ✓✓   │ │
│     └─────────────────────────────┘ │
│                               14:32 │
│                                     │
│ 🤖 IA Médica                        │
│ ┌─────────────────────────────────┐ │
│ │ 📊 ANÁLISE LABORATORIAL         │ │ ← Resposta estruturada IA
│ │                                 │ │
│ │ 🔍 Glicemia: 180 mg/dL ⚠️ ALTO  │ │
│ │ 📈 Tendência: +15% (30 dias)    │ │
│ │                                 │ │
│ │ 💡 RECOMENDAÇÃO                 │ │
│ │ • Ajustar Metformina 850mg      │ │
│ │ • Reavaliar em 2 semanas        │ │
│ │                                 │ │
│ │ [💊 Prescrever] [📅 Agendar]    │ │
│ └─────────────────────────────────┘ │
│ 14:33                               │
├─────────────────────────────────────┤
│ Digite ou grave sua próxima pergunta│
│                         🎤 📎 ↗️    │
└─────────────────────────────────────┘
```

---

## 🏥 Seleção de Departamento - Rounds Matinais

```
┌─────────────────────────────────────┐
│ 🏥 Hospital Real Português   07:00  │ ← Header hospitalar
├─────────────────────────────────────┤
│         Selecione Departamento      │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ ❤️  CARDIOLOGIA           12👥  │ │ ← 12 pacientes
│ │     2 médicos em rounds         │ │
│ └─────────────────────────────────┘ │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ 🚨  EMERGÊNCIA             3👥  │ │ ← 3 casos críticos
│ │     1 médico ativo              │ │
│ └─────────────────────────────────┘ │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ 🔪  CIRURGIA               5👥  │ │ ← 5 pré/pós-op
│ │     1 cirurgião em rounds       │ │
│ └─────────────────────────────────┘ │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ 🏥  UTI                    2👥  │ │ ← 2 cuidados críticos
│ │     1 intensivista ativo        │ │
│ └─────────────────────────────────┘ │
├─────────────────────────────────────┤
│ 📊 Admin | 🔔 Alertas | 👥 Equipes  │
└─────────────────────────────────────┘
```

---

## 🔔 Alertas Inter-Departamentais

```
┌─────────────────────────────────────┐
│ 🔔 Alertas Críticos          07:15  │
├─────────────────────────────────────┤
│ ⚠️  EMERGÊNCIA → CARDIOLOGIA        │
│                                     │
│ 👤 João Silva, 67a                  │
│ 🚨 STEMI confirmado                 │
│ 📍 Sala Trauma 3                   │
│                                     │
│ ❤️  Dr. Santos (Cardio) notificado  │
│ 🏥  UTI Leito 12 reservado          │
│ 🔪  Centro Cirúrgico 3 em alerta   │
│                                     │
│ [🚨 Ver Paciente] [📞 Ligar Equipe] │
├─────────────────────────────────────┤
│ ⚡  UTI → CIRURGIA                  │
│                                     │
│ 👤 Ana Costa, 32a                   │
│ 🩸 Hemorragia pós-operatória        │
│ 📍 UTI Leito 8                     │
│                                     │
│ [🔪 Cirurgia Urgente] [🩸 Protocolo]│
└─────────────────────────────────────┘
```

---

## 🔬 Revisão de Labs - Dr. Roberto Silva

```
┌─────────────────────────────────────┐
│ ← Carlos Mendoza, 52a        🔬     │
├─────────────────────────────────────┤
│ 📊 PAINEL TIREOIDIANO - 15/01/2024  │
│                                     │
│ 🧪 TSH: 12.5 mIU/L ⚠️ ALTO         │
│     Referência: 0.4-4.0             │
│                                     │
│ 🧪 T4: 0.8 ng/dL ⚠️ BAIXO          │
│     Referência: 0.9-1.7             │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ 📈 TENDÊNCIA 6 MESES            │ │
│ │                                 │ │
│ │ TSH │     ●                     │ │ ← Gráfico interativo
│ │  12 │   ●   ●                   │ │
│ │   8 │ ●       ●                 │ │
│ │   4 ├─────────────────────────  │ │ ← Linha referência
│ │     Sep  Nov  Jan  Mar  Mai     │ │
│ └─────────────────────────────────┘ │
│                                     │
│ 🤖 IA: Sugere hipotireoidismo       │
│ 💊 Levotiroxina 50mcg recomendada   │
│                                     │
│ [💊 Prescrever] [📈 Ver Mais] [📱 Notificar] │
└─────────────────────────────────────┘
```

---

## 🎤 Documentação por Voz - Dra. Patricia Lima

```
┌─────────────────────────────────────┐
│ ← Ana Silva, 28a             📝     │
├─────────────────────────────────────┤
│ 🎤 GRAVANDO CONSULTA...      01:23  │
│                                     │
│ ████████████░░░░░░░░░░░░░░░         │
│                                     │
│ 📝 TRANSCRIÇÃO TEMPO REAL:          │
│ ┌─────────────────────────────────┐ │
│ │ Paciente apresenta dor no peito │ │
│ │ há 2 dias, localizada região   │ │
│ │ precordial, sem irradiação.     │ │
│ │ Nega dispneia, palpitações...   │ │
│ └─────────────────────────────────┘ │
│                                     │
│ ⏹️ PARAR     🔄 PAUSAR     🗑️ APAGAR │
├─────────────────────────────────────┤
│ 🤖 IA organizará em:                │
│ • Queixa Principal                  │
│ • História Clínica                  │
│ • Exame Físico                      │
│ • Plano Diagnóstico                 │
└─────────────────────────────────────┘
```

---

## 📊 Dashboard Administrativo

```
┌─────────────────────────────────────┐
│ 📊 Admin Dashboard           07:30  │
├─────────────────────────────────────┤
│ 🏥 HOSPITAL REAL PORTUGUÊS          │
│                                     │
│ ┌─────┬─────┬─────┬─────┬─────────┐ │
│ │ Dept│Users│Pcts │Load │ Status  │ │
│ ├─────┼─────┼─────┼─────┼─────────┤ │
│ │ ❤️Card│  2  │ 12  │ 85% │ ✅ OK   │ │
│ │ 🚨Emrg│  1  │  3  │ 95% │ ⚠️ Alto│ │
│ │ 🔪Cirg│  1  │  5  │ 70% │ ✅ OK   │ │
│ │ 🏥UTI │  1  │  2  │100% │ 🔴 Max │ │
│ └─────┴─────┴─────┴─────┴─────────┘ │
│                                     │
│ 📈 MÉTRICAS TEMPO REAL:             │
│ • Consultas hoje: 47                │
│ • Prescrições: 23                   │
│ • Alertas críticos: 2               │
│ • Tempo médio atendimento: 12min    │
│                                     │
│ [📋 Relatórios] [⚙️ Config] [🔔 Alertas] │
└─────────────────────────────────────┘
```

---

## 📱 Mobile Layout

### iPhone (Portrait)
```
┌───────────────┐
│ 📱 Header     │
├───────────────┤
│               │
│   💬 Chat     │
│    Area       │
│               │
├───────────────┤
│ 📝 Input 🎤   │
├───────────────┤
│ ⚡ Actions    │
└───────────────┘
```

### iPad (Landscape) - Multi-Departamento
```
┌─────────────────┬───────────────┐
│                 │ 🏥 DASHBOARD  │
│   💬 Chat       │ ❤️ Card: 12   │
│    Principal    │ 🚨 Emrg: 3    │
│                 │ 🔪 Cirg: 5    │
│                 │ 🏥 UTI: 2     │
│                 │               │
│                 │ 🔔 ALERTAS    │
│                 │ ⚠️ 2 críticos │
└─────────────────┴───────────────┘
```

---

## 🎯 Fluxos Hospitalares Específicos

### Rounds Matinais (07:00)
```
🏥 Login → 🏢 Dept → 👥 Pacientes → 🎤 Rounds → 📝 Notas → 🔔 Alertas
```

### Emergência Crítica
```
🚨 Admissão → 📋 EMR Sync → 🔔 Auto-Alert → ❤️ Consulta → 🏥 UTI Prep → 📊 Protocolo
```

### Revisão Labs
```
🔬 Selecionar → 📄 Upload → 📈 Gráficos → 🎤 Consulta → 💊 Prescrição → 📱 Notificar
```

### Documentação
```
👩‍⚕️ Paciente → 🎤 Gravar → 🤖 Transcrever → ✏️ Revisar → 📝 Estruturar → 💾 Salvar
```

---

## ✨ Microinterações Hospitalares

- **🏥 Login departamental**: Animação departamento selecionado
- **🔔 Alertas críticos**: Vibração + som específico por urgência
- **📈 Gráficos labs**: Zoom touch com detalhes hover
- **🎤 Rounds por voz**: Pulse visual durante gravação
- **👥 Status equipe**: Indicadores tempo real online/offline
- **⚠️ Alertas interdepartamentais**: Notificação slide-in com ação

Agora o design está totalmente integrado com os casos de uso hospitalares reais! 🏥✨
