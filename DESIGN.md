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

## 🎤 Gravação de Voz

```
┌─────────────────────────────────────┐
│            🔴 Gravando...           │
│               00:15                 │
│                                     │
│         ████████░░░░░░░░            │ ← Barra progresso
│                                     │
│        "Prescreva Metformina        │
│         500mg para diabete..."      │ ← Transcrição tempo real
│                                     │
│     ⏹️ Parar     🗑️ Cancelar        │
└─────────────────────────────────────┘
```

---

## 📊 Card de Paciente Expandido

```
┌─────────────────────────────────────┐
│ 👤 Maria Silva, 45 anos             │
│ 📍 Endocrinologia - Leito 203       │
│ 📅 Última: 15/01 | Próxima: 29/01  │
├─────────────────────────────────────┤
│ 🩸 ÚLTIMOS EXAMES                   │
│                                     │
│ Glicemia: 180 mg/dL ⚠️ Alto         │
│ HbA1c: 7.2% ⚠️ Alto                 │
│ Pressão: 130/80 ✅ Normal           │
├─────────────────────────────────────┤
│ [📋 Histórico] [💊 Receitas] [📅 Agendar] │
└─────────────────────────────────────┘
```

---
