# 🧠 NuevaMente - G10 Hackathon (Data & IA)

**Proyecto Oficial para el Hackathon Oracle ONE / Alura (G10 - Marcos Hernández)**

Este repositorio almacena **exclusivamente toda la documentación arquitectónica, técnica y de gestión** del componente de Inteligencia Artificial y Datos (DataIA Pipeline) desarrollado para el proyecto NuevaMente.

El propósito de este repositorio es brindar una visión completamente transparente de las decisiones de diseño, la mitigación de brechas pedagógicas y los contratos de integración para el desarrollo del MVP (Mínimo Producto Viable).

---

## 📑 Índice de Documentación Oficial

A continuación se listan todos los documentos expandidos y especificados del proyecto:

### 1. Arquitectura y Decisiones Core
*   [**Alineación de Arquitectura MVP**](docs/ALINEACION_ARQUITECTURA_MVP.md): Documento maestro que detalla el uso del orquestador Multi-Agente (LangGraph), el *Query Builder* pedagógico, la regla de *Grounding* del Agente Crítico, y el descarte de GraphRAG para el MVP en favor de ChromaDB.
*   [**Plan de Optimización de Rendimiento**](docs/PLAN_OPTIMIZACION_RENDIMIENTO.md): Diagnóstico de tiempos de respuesta (*Cold Start* vs *Warm Start*) y estrategias técnicas (Caché Semántico y Lifespan de FastAPI) para garantizar una latencia menor a 3 segundos en producción.

### 2. Integración y Contratos Inter-Equipos
*   [**Contratos Backend API**](docs/CONTRATOS_BACKEND_API.md): Especificación estricta de las entradas y salidas (JSON) esperadas. Incluye el esquema de metadatos pedagógicos obligatorios (`tiempo_estimado_estudio_minutos`, `conceptos_clave`) exigidos por el jurado.
*   [**Checklist Maestro de Avances y Hitos**](docs/CHECKLIST_MAESTRO_AVANCES_Y_HITOS.md): Tabla de seguimiento general para coordinar a los 3 Squads (IA, Backend/Frontend, Cloud).

### 3. Auditoría y Control de Calidad (Issues)
*   [**Feedback Arquitectónico (Fernando)**](issues/fernando-obs.md): Observaciones críticas levantadas sobre el diseño pedagógico inicial (Gaps de metadatos y estrategias de RAG).
*   [**Resolución de Observaciones**](issues/fernando-obs-respuesta.md): Documento de respuesta técnica donde se aprobaron e integraron las correcciones (F1 a F5) en el código base (Inyección de Nichos, Cálculo de tiempos y LLM-as-a-judge).

---

## 🛠️ Stack Tecnológico Documentado
*   **Orquestación:** LangGraph (StateGraph, Multi-Agente asíncrono).
*   **LLM Engine:** Gemini 2.5 Flash (Base) / Groq Llama 3.3 (Failover).
*   **Vector Store:** ChromaDB (con Embeddings Sentence-Transformers).
*   **Validación:** Pydantic V2 (Esquemas estructurados y prevención de inyecciones prompt/XSS).
*   **Despliegue Objetivo:** OCI Always Free (Oracle Cloud Infrastructure).

> *Este repositorio es de solo-lectura para fines de documentación. El código fuente de ejecución reside en el repositorio principal.*
