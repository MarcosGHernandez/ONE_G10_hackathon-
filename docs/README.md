# DOSSIER DE DOCUMENTACIÓN OFICIAL DE INGENIERÍA
## Proyecto: NuevaMente — Sistema Inteligente de Adaptación y Generación de Contenido Educativo
**Hackathon:** ONE (Oracle Next Education) & Alura Latam — Cohorte G-10  
**Carácter:** Documentación Técnica de Referencia y Plan Maestro de Ejecución

---

## 1. Estructura del Dossier Documental

La carpeta `docs/` contiene el conjunto exhaustivo de artefactos de ingeniería para arrancar el proyecto y respaldar la presentación ante el jurado calificador de Oracle y Alura:

```text
docs/
├── README.md                                  # Índice general del dossier documental (Este archivo)
├── 01_PRD_PRODUCT_REQUIREMENTS_DOCUMENT.md    # Requisitos de producto, visión, user personas e ISO 25010
├── 02_SISTEMA_DE_DISENO_Y_UI_UX.md           # Tokens, componentes didácticos (Flashcards, Quizzes, Mapas) y Wireframes
├── 03_WBS_Y_DEFINICION_DE_TAREAS.md          # Desglose formal de tareas por squad e integrante con dependencias
├── 04_TIMELINE_EXTENDIDO_Y_HITOS.md           # Cronograma extendido día a día, diagrama de Gantt e hitos de control
├── 05_CONTRATOS_DE_DATOS_Y_SCHEMAS.md         # Modelos Pydantic V2 canónicos para Backend, Frontend y LangGraph
├── 06_ARQUITECTURA_DE_IA_Y_SISTEMA_MULTIAGENTE.md # Pipeline RAG, Chunking, Embeddings y LangGraph Multi-Agente
├── 07_DIAGRAMAS_DE_ARQUITECTURA_Y_FLUJOS.md       # Diagramas C4, Handshake entre Squads, Secuencia E2E y Git
├── 08_BUENAS_PRACTICAS_SKILLS_Y_RESILIENCIA.md   # Auditoría Hermes 3, catálogo de tools, manejo de fallos y rate limit
├── 09_PLAN_MAESTRO_INTEGRADO_IA_BACKEND_CLOUD_FRONTEND.md # Plan maestro integrado E2E y tareas atómicas por squad
├── 10_PLAN_OPERATIVO_SQUAD_IA_Y_DATOS.md          # Plan de ejecución autónomo para el Squad de IA y Datos
├── 11_PLAN_OPERATIVO_SQUAD_BACKEND_Y_CLOUD.md     # Plan de ejecución autónomo para el Squad de Backend y OCI
├── 12_PLAN_OPERATIVO_SQUAD_FRONTEND_Y_UX.md       # Plan de ejecución autónomo para el Squad de Frontend y UX
├── CHECKLIST_MAESTRO_AVANCES_Y_HITOS.md       # Tablero maestro de seguimiento, fechas, tareas y checklist por área
└── adr/                                       # Architectural Decision Records (Decisiones de Ingeniería)
    ├── 001-seleccion-langgraph-vs-cadenas-monoliticas.md
    ├── 002-persistencia-obligatoria-oci-object-storage-always-free.md
    └── 003-protocolo-hibrido-rest-y-telemetria-sse.md
```

---

## 2. Propósito y Audiencia de Cada Documento

| Documento | Audiencia Primaria | Contenido y Utilidad |
|---|---|---|
| **`01_PRD_PRODUCT_REQUIREMENTS_DOCUMENT.md`** | Todo el Equipo / Evaluadores | Especificación funcional formal (FRs), requerimientos de calidad ISO/IEC 25010 (NFRs) y casos de uso del producto. |
| **`02_SISTEMA_DE_DISENO_Y_UI_UX.md`** | Karen Gonzalez & Cristian Cortes | Guía de estilo "Minimalismo Clásico Tecnológico", diseño de tarjetas 3D, quizzes con feedback, mapas mentales Mermaid y Stepper de telemetría en tiempo real. |
| **`03_WBS_Y_DEFINICION_DE_TAREAS.md`** | Jacqueline Rioja & Líderes de Squad | Desglose de actividades código por código (GEST, COMMS, IA, QA, OCI, BACK, FRONT), responsables directos y criterios de entrega. |
| **`04_TIMELINE_EXTENDIDO_Y_HITOS.md`** | Jacqueline Rioja & Cristian Maida | Plan temporal detallado desde la Semana 0 hasta la Semana 5, fechas de convergencia, Code Freeze y guion del Video Pitch. |
| **`05_CONTRATOS_DE_DATOS_Y_SCHEMAS.md`** | Marcos Gael, Joaquin, Diego, Cristian C. | Código Python con esquemas Pydantic V2 de entrada, salida, telemetría y estructuras específicas de cada formato didáctico. |
| **`06_ARQUITECTURA_DE_IA_Y_SISTEMA_MULTIAGENTE.md`** | Fernando Falla, Marcos Gael, Andy Mijail | Especificación de PyMuPDF, chunking jerárquico (800/150), Google Gemini Embeddings / FastEmbed y los 5 agentes de LangGraph. |
| **`07_DIAGRAMAS_DE_ARQUITECTURA_Y_FLUJOS.md`** | Todo el Equipo / Pitch | Representación gráfica formal: C4 Containers, Secuencia E2E, Handshake entre Squads, Máquina de Estados LangGraph y Git. |
| **`08_BUENAS_PRACTICAS_SKILLS_Y_RESILIENCIA.md`** | Todo el Equipo (Especialmente IA y Backend) | Dictamen Hermes 3, catálogo de 5 Skills agénticas, tolerancia a fallos en LLMs/OCI, rate limits y prompt engineering. |
| **`09_PLAN_MAESTRO_INTEGRADO_IA_BACKEND_CLOUD_FRONTEND.md`** | Todos los Squads e Integrantes | **Plan de Integración E2E ("Ir de la Mano"):** Handshake entre squads, sincronización por hitos semanales, política zero-blockers y pruebas de los 3 casos oficiales. |
| **`10_PLAN_OPERATIVO_SQUAD_IA_Y_DATOS.md`** | Marcos Gael, Fernando, Andy, Jacqueline | **Guía operativa autónoma de IA:** Ingestión PyMuPDF, ChromaDB, LangGraph (5 nodos, loop crítico), fábrica LLMs, prompts por perfil y tests unitarios. |
| **`11_PLAN_OPERATIVO_SQUAD_BACKEND_Y_CLOUD.md`** | Diego, Cristian C., Alexis, Joaquín | **Guía operativa autónoma de Backend & Cloud:** FastAPI, Mock endpoint inmediato, streaming SSE, OCI SDK Always Free, OCI Budgets ($0.00) y Docker Compose. |
| **`12_PLAN_OPERATIVO_SQUAD_FRONTEND_Y_UX.md`** | Karen Gonzalez, Cristian Cortes | **Guía operativa autónoma de Frontend & UX:** Tokens Dark Mode, Drag & Drop, Flashcards 3D, Quizzes interactivos con feedback, visor Mermaid y Stepper reactivo. |

---

## 3. Guía Rápida para la Presentación en el Hackathon

Para presentar el proyecto ante los evaluadores, la narrativa debe alinearse con los tres activos clave documentados:
1. **Rigor Técnico e Innovación:** Orquestación Multi-Agente con LangGraph, agente crítico de anclaje semántico (`anclaje_fuente_score` $\ge 0.85$) y generación multimodal (Flashcards, Quizzes con justificación técnica y Mapas Mentales navegables).
2. **Infraestructura de Nube Gratuita:** Demostración en vivo de persistencia en **OCI Object Storage Always Free** con coste $0.00 USD garantizado mediante OCI Budgets.
3. **Experiencia de Usuario:** Interfaz minimalista clásica con telemetría en vivo y componentes interactivos diseñados para el aprendizaje activo.
