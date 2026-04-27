<div align="center">

# Prisma 💠

### **Hazlo tú, hazlo ya.**

Tu coworker IA local. Un input, múltiples especialistas.

[![Source](https://img.shields.io/badge/source-private-orange?style=flat-square)](#acceso-y-demo)
[![Status](https://img.shields.io/badge/status-active%20development-success?style=flat-square)](#)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![LangGraph](https://img.shields.io/badge/LangGraph-1.0-1c3c3c?style=flat-square)](https://langchain-ai.github.io/langgraph/)
[![React](https://img.shields.io/badge/React-18-61dafb?style=flat-square&logo=react&logoColor=white)](https://react.dev)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.115-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![OpenRouter](https://img.shields.io/badge/OpenRouter-300%2B%20modelos-ff7e29?style=flat-square)](https://openrouter.ai)

![Prisma — pantalla principal](docs/screenshots/01-empty.png)

</div>

---

## El problema

Los asistentes de IA generales son brillantes para conversar, pero cuando necesitas **trabajo terminado** — un Excel analizado, un informe Word entregado, un script ejecutado — terminas copiando código de una ventana, pegándolo en otra, ajustando rutas y rezando para que funcione.

Y cada mes la suscripción te cobra, uses 5 prompts o 5 000.

## La solución

Prisma vive en tu máquina y **ejecuta**. No describe, no sugiere, no te dice "podrías hacer esto". Lee tus archivos con pandas, genera Word/Excel/PowerPoint, ejecuta scripts en sandbox aislado y entrega artefactos verificados — todo orquestado bajo un patrón **planificar → ejecutar → verificar → iterar** con human-in-the-loop opcional.

> Como un prisma óptico que descompone la luz blanca en su espectro, **Prisma toma una sola petición y la reparte entre múltiples agentes especializados** que trabajan bajo un orquestador inteligente.

Y lo más importante: **pagas solo por lo que usas**. Tu propia cuenta de OpenRouter, tus propios modelos, sin suscripción mensual del software.

---

## ¿Por qué Prisma?

| | |
|---|---|
| 💸 **Sin suscripciones** | Una sola licencia. Tu cuenta de OpenRouter, tus modelos. Si no usas Prisma este mes, no pagas. Comparable: Copilot $30/mes/usuario, ChatGPT Plus $20/mes — Prisma cero. |
| 🔒 **100% local** | Tus archivos nunca salen de tu máquina. Solo las consultas al LLM viajan, encriptadas, vía OpenRouter. |
| 🛡️ **Modo ZDR** | Toggle Zero Data Retention en la UI: enruta solo a proveedores que no almacenan tus datos. Útil para información sensible. |
| 🛠️ **Ejecución real** | Sandbox aislado para correr código. Si pides análisis, genera y ejecuta `pandas`. Si pides un Word, te entrega el `.docx`. |
| 🔌 **300+ modelos** | Sin lock-in. Claude para razonar, Gemini para research, DeepSeek para escribir documentos — el modelo correcto para cada tarea, vía OpenRouter. |
| 🩺 **Auto-validación** | Prisma verifica que tus modelos estén disponibles. Si alguno se deprecia, te avisa con un banner y te sugiere el reemplazo de un click. |
| 🌊 **Streaming en tiempo real** | Ves cada token mientras se genera, status entre nodos, progreso del plan paso a paso. |
| 🧠 **Memoria persistente** | SQLite para historial conversacional, ChromaDB para RAG sobre tus documentos, watcher automático del workspace. |
| 📚 **Aprende de cada uso** | Después de cada tarea exitosa, propone mejoras a sus agentes que tú apruebas con un click. |

---

## Galería

### Estado en vivo: orquestador, plan, agente activo, modelos por rol

![Chat con tres agentes coordinándose](docs/screenshots/02-chat.png)

El panel derecho (Inspector) muestra qué agente está ejecutando, en qué fase del loop (planificar/ejecutar/verificar/completar), cuántas iteraciones lleva, y qué modelo se está usando para cada rol. La conversación muestra al agente Datos pasándole el control al agente Office tras analizar el Excel.

### Skills personalizables: cada agente con su propio prompt y modelo LLM

![Catálogo de skills disponibles](docs/screenshots/04-skills.png)

Cada agente es un *skill* editable: nombre, avatar, descripción corta, prompt completo y modelo. Activas/desactivas, ajustas la personalidad, y Prisma propone mejoras automáticamente después de cada ejecución exitosa.

### Tareas: contexto persistente en cada conversación

![Vista de tareas / backlog](docs/screenshots/03-tareas.png)

Las tareas activas se inyectan automáticamente en el contexto del orquestador. Cuando le pides algo a Prisma, ya sabe en qué proyectos estás trabajando, qué está en progreso y qué es crítico.

### Orquestador configurable: tu IA, tu personalidad

![Configuración del orquestador](docs/screenshots/05-orquestador.png)

Define el nombre, emoji, tono y modelo del orquestador (el agente que clasifica y delega). Cambia su personalidad sin tocar código.

---

## Arquitectura

```
                            Tu mensaje
                                 │
                                 ▼
            ┌────────────────────────────────────────┐
            │   Gateway FastAPI (REST + WebSocket)   │
            └─────────────────┬──────────────────────┘
                              ▼
                    ┌────────────────────┐
                    │  Router/Orquestador│  Clasifica intención + plan
                    └─┬──────┬──────┬────┘
                      ▼      ▼      ▼
                 ┌──────┐┌─────┐┌────┐┌─────┐
                 │Datos ││Off. ││Res.││Cód. │  Agentes especialistas
                 └───┬──┘└──┬──┘└──┬─┘└──┬──┘  (modelo potente por rol)
                     │      │      │     │
                     └──────┴──┬───┴─────┘
                               ▼
                       ┌──────────────┐
                       │  Sandbox     │  Ejecuta código real
                       └──────┬───────┘
                              ▼
                       ┌──────────────┐
                       │ Verificador  │  Plan → Execute → Verify → Iterate
                       └──┬─────┬─────┘  (max 3 iteraciones)
                       ¿OK?│     │ ¿iterar?
                           ▼     ▼
                     ┌────────┐┌─────────────┐
                     │ Output ││ Aprendizaje │  Propone mejoras
                     └────────┘└─────────────┘
```

**Cinco roles de modelo** — stack default optimizado por calidad/precio (abr-2026, configurable):

| Rol      | Modelo recomendado                | Por qué                                                           |
|----------|-----------------------------------|-------------------------------------------------------------------|
| Router   | `anthropic/claude-haiku-4.5`      | Rápido (TTFT ~600ms) y barato — clasifica intent en cada turno    |
| Datos    | `moonshotai/kimi-k2.6`            | Top en LiveCodeBench v6 — análisis numérico y pandas              |
| Office   | `deepseek/deepseek-v4-pro`        | Mejor relación costo/calidad para `python-docx` y `openpyxl`      |
| Research | `google/gemini-3-flash-preview`   | 1M de contexto — RAG sobre múltiples documentos                   |
| Código   | `openai/gpt-5.3-codex`            | #2 SWE-bench Pro — línea Codex entrenada para código              |

Cada rol tiene un suplente cross-provider: si el principal cae (404 / 503 / outage del proveedor), Prisma reintenta automáticamente sin que lo notes.

---

## Stack tecnológico

| Capa            | Tecnología                                        |
|-----------------|---------------------------------------------------|
| Orquestación    | LangGraph 1.0 + SqliteSaver (checkpoints)         |
| Modelos         | OpenRouter — 300+ modelos, BYO API key            |
| Backend         | FastAPI + WebSockets + asyncio                    |
| Datos           | pandas, openpyxl, sqlalchemy                      |
| Office          | python-docx, python-pptx, openpyxl                |
| RAG             | ChromaDB + watchdog (indexer automático)          |
| Frontend        | React 18 + Vite + Tailwind + Zustand              |
| Estilo          | macOS Sonoma/Sequoia design system                |
| Empaquetado     | Tauri (próximamente — `.exe` nativo de ~10 MB)    |

---

## Acceso y demo

> **Prisma es software propietario.** El código fuente vive en un repo privado — este repositorio contiene únicamente la documentación y los screenshots para presentar el proyecto.

Si te interesa:

- 🎬 **Ver una demo en vivo** — escríbeme por [LinkedIn](https://linkedin.com/in/jogomezm) o abre un [issue](https://github.com/ingjaviergomezm/prisma/issues) y agendamos.
- 🛒 **Comprar la licencia** — distribución vía Hotmart (próximamente). Mientras tanto, suscríbete por LinkedIn para recibir el lanzamiento.
- 🤝 **Colaborar / consultoría** — disponible para proyectos de IA agéntica aplicada a Oil & Gas, energía y productividad técnica.
- 🧪 **Acceso técnico bajo NDA** — para evaluación seria, contacto directo.

## Bajo el capó

Aunque el código es privado, la arquitectura es transparente.

**Pipeline típico de una petición:**

1. WebSocket recibe el mensaje del usuario
2. **Router** (Claude Haiku 4.5) clasifica intención y construye plan
3. **Agente especializado** (Kimi/DeepSeek/Gemini/GPT-5 Codex según el rol) genera código ejecutable
4. **Sandbox** (subprocess aislado, timeout 30-60s) ejecuta y captura stdout/stderr/artefactos
5. **Verificador** decide: entregar al usuario o iterar (max 3)
6. Resultado + artefactos se transmiten por WS al frontend
7. **Aprendizaje** propone mejoras al `SKILL.md` del agente para próximas veces

**Si un modelo cae**: el sistema dispara automáticamente un suplente cross-provider en el segundo intento — sin que el usuario lo note. Si un modelo deja de existir en OpenRouter, un banner ámbar avisa con la sugerencia de reemplazo.

---

## Roadmap

**Hecho** ✅
- [x] Grafo LangGraph + loop Plan→Execute→Verify→Iterate
- [x] Gateway FastAPI REST + WebSocket
- [x] Agentes datos / office / research / código / general con ejecución real
- [x] Sandbox compartido para código Python
- [x] Memoria SQLite + ChromaDB con watcher automático
- [x] UI Mission Control con diseño macOS (Sonoma/Sequoia)
- [x] Streaming de tokens en tiempo real
- [x] Aprendizaje autónomo (propuestas de skill)
- [x] Multi-turn con preservación de contexto
- [x] Pantalla "Ajustes": configura tu OpenRouter key + toggle ZDR sin tocar código
- [x] Validación automática de modelos: si alguno se deprecia, banner + sugerencia
- [x] Fallback cross-provider transparente: si un modelo cae, otro responde

**En camino** 🚧
- [ ] Módulo de **Tareas** con agente Configurador que te entrevista para configurar el contexto
- [ ] **Empaquetado nativo** con Tauri (`.exe` de ~10 MB)
- [ ] **Sistema de licencias** ligado a Machine ID + canal de updates
- [ ] **Panel de créditos OpenRouter** — saldo restante y consumo por modelo en tiempo real
- [ ] Integración con Hotmart para distribución comercial
- [ ] Integración MCP (Model Context Protocol) — GitHub, Drive, DBs
- [ ] Checkpoint humano opcional sobre `PLAN.md` antes de ejecutar

---

## Licencia

Software propietario · © 2026 Javier Gómez · Todos los derechos reservados.
Detalles en [LICENSE.md](LICENSE.md).

---

<div align="center">

**Construido por [Javier Gómez](https://github.com/ingjaviergomezm)** — Ingeniero Industrial · IA Agéntica · Oil & Gas

[GitHub](https://github.com/ingjaviergomezm) · [LinkedIn](https://linkedin.com/in/jogomezm)

</div>
