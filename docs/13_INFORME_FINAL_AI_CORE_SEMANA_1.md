# 🧠 INFORME FINAL Y DOCUMENTACIÓN: NÚCLEO DE INTELIGENCIA ARTIFICIAL
**Versión:** 2.0 (Cierre de Semana 1 / Inicio de Pruebas E2E)
**Componente:** `nuevamente-ai-core`
**Responsable:** Squad IA & Datos

---

## 1. Visión General de la Arquitectura (LangGraph)
El núcleo de IA no es un LLM llamando texto estático, sino un **Sistema Multi-Agente orquestado mediante un Grafo de Estados (`StateGraph`)**. Esto permite la ejecución condicional, auditorías de calidad y reintentos automáticos (ciclos).

### Los 4 Agentes del Grafo:
1. **Extractor (RAG / Segmentador):** Procesa archivos locales (.pdf, .txt, .md) mediante PyMuPDF. Segmenta en fragmentos jerárquicos (chunk: 800, overlap: 150) indexados en ChromaDB de forma asíncrona (optimizada de 56s a <0.05s usando lazy imports).
2. **Creador (Borrador):** Toma la petición del usuario, evalúa el Perfil (Junior/Senior/Ejecutivo) y el Formato (Flashcards/Quiz/Mapa) inyectando el contexto de RAG para redactar el material en crudo.
3. **Crítico (Evaluador de Fidelidad/Grounding):** Compara el borrador generado contra el texto original. Asigna un `anclaje_fuente_score`. Si la métrica es menor a **0.85**, obliga al Creador a reescribir (máximo 2 intentos). Si falla permanentemente, emite un código HTTP 422.
4. **Auditor (Hermes 3 Local):** Modelo local gratuito enfocado en escanear el JSON resultante en busca de vulnerabilidades lógicas, garantizando privacidad absoluta antes de la persistencia en base de datos.

---

## 2. Contratos de Datos y Frontera (Backend-IA)

Para resolver las discrepancias de tipos y proteger la estructura de los documentos originales, se definió la siguiente frontera de integración con Backend FastAPI:

* **Método de Ingesta:** `multipart/form-data`
* **Transmisión del Documento:** `document_raw` (Archivo binario directo, sin conversiones a strings JSON en el Request Body).
* **Campos de Metadatos:** Textos planos form-data (`perfil`, `formato`, `nicho_sector`, `nivel_detalle`).

### Catálogo de Errores Estandarizado (FastAPI Exceptions)
| HTTP Code | Nombre de Excepción | Causa Raíz en el Motor IA |
| :--- | :--- | :--- |
| **400** | `Bad Request` | Faltan campos en el form-data o formato de archivo no soportado. |
| **422** | `Unprocessable Entity` | **Contexto Insuficiente / Alucinación**. El documento no habla del tema y el Agente Crítico bloqueó la petición para evitar falsedades. |
| **500** | `Internal Error` | Fallo de orquestación en el StateGraph de LangGraph. |
| **503** | `Service Unavailable` | Límite de Cuotas y **Falla en el Failover**. Ningún modelo tiene disponibilidad. |

---

## 3. Resiliencia, Rendimiento y Seguridad (Hardening)

El motor fue diseñado para el rigor de un Hackathon en nivel Producción:

1. **Failover (Tolerancia a Fallos):** El sistema utiliza a Google Gemini como LLM primario. Si Gemini lanza un HTTP 429 por límite de peticiones (cuota gratuita), el sistema hace un _Failover_ automático y sin cortes hacia Llama-3 en **Groq**.
2. **Telemetría en Vivo:** El motor de IA soporta una inyección de dependencias `callback_telemetria`. Emite actualizaciones asíncronas con 5 pasos y barra de porcentaje para alimentar interfaces UI / Steppers usando Server-Sent Events (SSE).
3. **Protección Pydantic V2:** Todas las respuestas de salida están fuertemente tipadas en Pydantic V2 garantizando la limpieza del JSON final.
4. **Prevención SAST:**
   * **Inyección XSS:** Filtros regex incrustados.
   * **Prompt Injection / Jailbreaks:** Validadores semánticos que impiden que instrucciones como _"Ignore all previous prompts"_ se cuelen en el motor.

---

## 4. Herramientas de Desarrollo y Testing (DevEx)

El núcleo ahora incluye herramientas potentes para que los ingenieros prueben el sistema antes de subir a la nube:

* **CLI Interactiva (`cli_interactiva.py`):** Una consola en Python visual y guiada que permite ingresar archivos (ej. `prueba_vcn.txt`), seleccionar perfiles y ver cómo fluye la generación del grafo y la emisión de telemetría de forma secuencial y a colores.
* **Suite de Pruebas Pytest (12/12):** La IA cuenta con una suite E2E en `tests/test_ai_pipeline.py`. 
    * Prueba E2E Completa sincrónica y asincrónica.
    * Prueba de Filtros XSS y Prompts maliciosos.
    * **Prueba de Resiliencia T014:** Comprueba el failover correcto de Gemini a Groq.
    * **Prueba de Grounding 422 (T013):** Fuerzas una receta de cocina y exige un Quiz de DevOps. El test aprueba al demostrar que la IA aborta y devuelve un 422 en vez de alucinar la respuesta.
* **Reparador Mermaid Automático:** Función de parseo Regex local que repara árboles rotos de _Mermaid.js_ para mapas mentales usando un AST simple (0 costo de tokens).

---

## 5. Próximos Pasos (Semanas 2 y 3)
* **API Ingestion:** Conexión directa del Endpoint Mock en el Back hacia `ejecutar_pipeline_adaptacion_async`.
* **Oracle Cloud:** Creación del módulo persistente en Object Storage (VCN Bucket).
* **Renderizado UI:** Lectura de las tarjetas de JSON generadas en los visores dinámicos del Frontend.
