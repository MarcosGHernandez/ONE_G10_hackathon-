# Contratos de API para Backend (Integración IA)

**Propósito:** Este documento define de forma estricta los contratos (Endpoints, Inputs, Outputs y Telemetría) que el equipo de Backend (FastAPI) debe implementar para integrarse con el motor de Inteligencia Artificial desarrollado en la Semana 1.

---

## 1. Endpoints Principales

El Backend debe exponer los siguientes endpoints bajo el prefijo `/api/v1/`:

| Método | Endpoint | Descripción |
| :--- | :--- | :--- |
| `POST` | `/api/v1/adaptacion/generar` | Procesa un documento y genera el contenido educativo adaptado (Endpoint síncrono o base). |
| `GET` | `/api/v1/adaptacion/stream` | (Recomendado) Endpoint Server-Sent Events (SSE) o WebSocket para emitir el progreso de IA en tiempo real. |

---

## 2. Entradas (Input: Petición del Cliente)

El endpoint `POST /api/v1/adaptacion/generar` debe aceptar un cuerpo `application/json` que mapee exactamente al esquema `AdaptacionContenidoRequest` validado en la IA.

### Esquema JSON Esperado:
```json
{
  "documento_titulo": "Introducción a Redes VCN en OCI",
  "documento_contenido": "Texto completo o extracto del documento a procesar (Mínimo 20 caracteres, máximo 50,000)...",
  "perfil_destinatario": "Junior",
  "formato_salida": "Flashcards",
  "nicho_sector": "General",
  "nivel_detalle": "Didactico"
}
```

### Resumen de Campos y Validaciones (FastAPI / Pydantic V2):

| Campo | Tipo | Restricciones | Valores Permitidos / Enums |
| :--- | :--- | :--- | :--- |
| `documento_titulo` | String | `min_length=3`, `max_length=150` | Protegido con Regex Anti-Inyección. |
| `documento_contenido` | String | `min_length=20`, `max_length=50000` | Protegido contra XSS y Prompt Injection. |
| `perfil_destinatario` | Enum | Requerido | `"Junior"`, `"Senior"`, `"Ejecutivo"` |
| `formato_salida` | Enum | Requerido | `"Flashcards"`, `"Quiz Interactivo"`, `"Mapa Mental"`, `"Guia Paso a Paso"`, `"Resumen Ejecutivo"` |
| `nicho_sector` | Enum | Requerido | `"Fintech"`, `"Salud"`, `"E-commerce"`, `"General"` |
| `nivel_detalle` | Enum | Requerido | `"Didactico"`, `"Tecnico Intermedio"`, `"Exhaustivo"` |

---

## 3. Salidas (Output: Respuesta al Cliente)

El motor de IA devolverá un objeto estructurado. El Backend debe asegurar que la respuesta HTTP retorne este mismo esquema (`AdaptacionContenidoResponse`).

### Estructura de la Respuesta (Output)

| Objeto Principal | Atributos Clave | Propósito |
| :--- | :--- | :--- |
| `status` | String | Indicador de éxito (`"exito"`) o error (`"error"`). |
| `metadatos` | Objeto | Muestra qué configuraciones se aplicaron (tiempo, formato, nicho). |
| `contenido_adaptado` | Objeto | El contenido final estructurado (`Flashcards`, `Quiz`, `Mapa Mental`, etc.). |
| `evaluacion_calidad` | Objeto | Resultados de la auditoría de Hermes y agente crítico (score de anclaje). |
| `almacenamiento_oci` | Objeto | Referencias al Object Storage de Oracle (Bucket, Objeto ID). |
| `codigo_respuesta` | Entero | Código HTTP estándar asociado (e.g., 200, 400, 503). |

### Esquema JSON de Respuesta Exitoso (200 OK):
```json
{
  "status": "exito",
  "codigo_respuesta": 200,
  "metadatos": {
    "perfil_aplicado": "Junior",
    "formato_generado": "Flashcards",
    "tiempo_estimado_estudio_minutos": 15,
    "conceptos_clave": ["VCN", "Subredes", "Routing"],
    "nicho_contexto": "General"
  },
  "contenido_adaptado": {
    "titulo": "Domina las VCN en OCI",
    "introduccion_contextualizada": "Como desarrollador Junior, entender redes es el primer paso...",
    "items": [
      {
        "frente": "¿Qué es una VCN?",
        "dorso": "Virtual Cloud Network: Tu red privada en la nube de Oracle.",
        "pista_didactica": "Piensa en una VCN como el terreno cercado donde construyes tu casa.",
        "categoria_dificultad": "Básico"
      }
      // ... más items
    ]
  },
  "evaluacion_calidad": {
    "anclaje_fuente_score": 0.95,
    "claridad_pedagogica": "Alta",
    "observaciones": "Aprobado por el Agente Crítico",
    "reintentos_realizados": 0
  },
  "almacenamiento_oci": {
    "bucket": "nuevamente-contenidos-educativos",
    "objeto_id": "vcn-junior-flashcards-123",
    "status_upload": "pendiente",
    "ruta_publica_o_par": null
  }
}
```

---

## 4. Contrato de Telemetría (Server-Sent Events / Stream)

Dado que la IA puede tardar entre 5 y 15 segundos en generar contenidos complejos, es **obligatorio** que el Backend maneje la telemetría en tiempo real para no dejar la pantalla del frontend congelada.

La función del Core de IA `ejecutar_pipeline_adaptacion_async` recibe un `callback_telemetria`. El Backend debe inyectar ahí una función asíncrona que emita los eventos SSE hacia el Frontend.

### Tabla de Fases de Progreso (SSE)

| Fase | % Progreso Estimado | Acción Ejecutada |
| :--- | :--- | :--- |
| `EXTRACCION` | 20% | Se procesa y limpia el documento original. |
| `INDEXACION` | 40% | El Chunker y VectorStore fragmentan y calculan los embeddings en ChromaDB. |
| `GENERACION` | 60% | LangGraph orquesta a los agentes (Analizador, Creador, Crítico) para armar el material. |
| `AUDITORIA` | 80% | Nous Hermes 3 verifica vulnerabilidades e inyecciones localmente. |
| `COMPLETADO` | 100% | Retorna el payload JSON final estructurado. |

### Formato del Evento (Event Stream):
```text
event: progress
data: {"fase": "EXTRACCION", "paso": 1, "progreso_porcentaje": 20, "mensaje": "Extrayendo texto del documento..."}

event: progress
data: {"fase": "INDEXACION", "paso": 2, "progreso_porcentaje": 40, "mensaje": "Creando índices vectoriales en ChromaDB..."}

event: progress
data: {"fase": "GENERACION", "paso": 3, "progreso_porcentaje": 60, "mensaje": "LangGraph generando el contenido..."}

event: progress
data: {"fase": "AUDITORIA", "paso": 4, "progreso_porcentaje": 80, "mensaje": "Hermes 3 Auditando seguridad y lógica..."}

event: complete
data: {"status": "exito", "payload_final": { ... respuesta JSON completa ... }}
```

---

## 5. Manejo de Errores (Error Handling)

Si la IA o Pydantic detectan una inyección maliciosa (XSS, Prompt Injection) o los datos no cumplen con los Enums, el backend debe capturar las excepciones y retornar códigos HTTP apropiados.

### Ejemplo de Error 400 (Bad Request - Validación Pydantic o Seguridad):
```json
{
  "status": "error",
  "codigo_respuesta": 400,
  "mensaje": "Contenido bloqueado: posible intento de Prompt Injection detectado.",
  "detalles": [...]
}
```

### Ejemplo de Error 429 / 500 (API Limits Fallback Exhausted):
```json
{
  "status": "error",
  "codigo_respuesta": 503,
  "mensaje": "Los servicios de IA están saturados temporalmente. Por favor, intenta en unos minutos.",
  "detalles": "Rate limit exceeded on Groq failover."
}
```
