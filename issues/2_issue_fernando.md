# Propuesta de Frontera de Integración DataIA ↔ Backend



**NuevaMente — G10 LATAM Equipo 15**  
**Área:** Data / IA  
**Fecha:** 26 de septiembre de 2026  
**Estado:** Propuesta técnica para avanzar Semana 2  
**Autores:** Squad DataIA

\---

## 1\. Objetivo de este documento

Consolidar una dinamica clara, pragmática y respetuosa del squad DataIA para:

1. Definir de forma concreta **qué recibe DataIA** desde Backend.
2. Proteger la fidelidad del documento original.
3. Convertir el pipeline de DataIA en una **función limpia** que Backend invoca.
4. Desbloquear la implementación de IA-02, IA-03 e IA-04 (y por extensión el resto del pipeline) sin generar deuda técnica ni rework en la Semana 2.

Esta propuesta se presenta con espíritu de colaboración y cortesía profesional. Busca acelerar el trabajo conjunto, no imponer.

\---

## 2\. Principio técnico fundamental

> \\\*\\\*Backend entrega el documento original tal cual lo subió el usuario (sin extracción, limpieza ni transformación) + los parámetros de adaptación.\\\*\\\*  
> \\\*\\\*DataIA es responsable de la extracción, normalización, chunking, embeddings y todo el procesamiento pedagógico.\\\*\\\*

### ¿Por qué este enfoque?

* La fidelidad estructural del documento (títulos, listas, tablas, bloques de código, jerarquía) es crítica para un pipeline pedagógico.
* Cualquier tratamiento previo por Backend puede perder información irrecuperable.
* DataIA tiene el conocimiento técnico y la responsabilidad de adaptar el contenido a perfiles y formatos educativos.
* Mantiene el pipeline de DataIA como una función limpia y desacoplada.

\---

## 3\. Propuesta de Request (Backend → DataIA)

Backend invoca el pipeline de DataIA enviando:

|Campo|Tipo|Obligatorio|Descripción|
|-|-|:-:|-|
|`document\\\_id`|string|Sí|Identificador único del documento|
|`document\\\_title`|string|Sí|Título del documento|
|`document\\\_raw`|binary / bytes|Sí|Contenido original del archivo tal cual lo subió el usuario (PDF, DOCX, MD, TXT, etc.) sin ninguna modificación|
|`document\\\_mime\\\_type`|string|Sí|MIME type original (ej. `application/pdf`)|
|`document\\\_filename`|string|Sí|Nombre original del archivo|
|`profile`|enum|Sí|`Junior` \| `Senior` \| `Ejecutivo`|
|`format`|enum|Sí|`Flashcards` \| `Quiz Interactivo` \| `Resumen Ejecutivo` \| `Mapa Mental`|
|`nicho\\\_sector`|enum|Sí|`Fintech` \| `Salud` \| `E-commerce` \| `General`|
|`nivel\\\_detalle`|enum|Sí (o default)|`Didactico` \| `Tecnico Intermedio` \| `Exhaustivo`|
|`request\\\_id`|string|Sí|Identificador de la solicitud (para trazabilidad e idempotencia)|
|`contract\\\_version`|string|Sí|`"2.0"`|

### 

### Notas de implementación

* El mecanismo de transporte recomendado para el MVP es **multipart/form-data** (archivo + JSON de parámetros) o el equivalente más simple que Backend prefiera.
* DataIA **no** espera texto ya extraído ni normalizado.
* DataIA realiza internamente la extracción y preservación de estructura.

\---

## 4\. Propuesta de Response (DataIA → Backend)

La respuesta sigue la estructura ya alineada en el squad:

```json
{
  "contract\\\_version": "2.0",
  "request\\\_id": "req\\\_123",
  "document\\\_id": "doc\\\_456",
  "status": "success",
  "codigo\\\_respuesta": "OK",
  "metadatos": {
    "perfil\\\_aplicado": "Junior",
    "formato\\\_generado": "Flashcards",
    "tiempo\\\_estimado\\\_estudio\\\_minutos": 15,
    "conceptos\\\_clave": \\\["VCN", "Subredes", "Routing"],
    "nicho\\\_contexto": "General"
  },
  "contenido\\\_adaptado": {
    "titulo": "...",
    "introduccion\\\_contextualizada": "...",
    "items": \\\[]
  },
  "evaluacion\\\_calidad": {
    "anclaje\\\_fuente\\\_score": 0.92,
    "claridad\\\_pedagogica": "Alta",
    "observaciones": "...",
    "clasificacion\\\_fidelidad": "Alta"
  }
}
```

Los schemas detallados por formato (Flashcards, Quiz, Resumen Ejecutivo, Mapa Mental) se mantienen según la alineación ya realizada por el squad.

\---

## 5\. Base operativa que adoptamos

Tomamos como referencia la **Alineación de Arquitectura MVP** publicada por Marcos (26-09-2026), que ya incorpora:

* Orquestación con LangGraph
* Query Builder orientado a formato
* Extracción de `conceptos\\\_clave`
* Cálculo de `tiempo\\\_estimado\\\_estudio\\\_minutos`
* Uso estricto de `nicho\\\_sector` en los prompts
* Grounding con umbral 0.85 + reintentos (LLM-as-a-judge)
* Descarte de GraphRAG para el MVP

Esta base ya está impactada en código y cubre las observaciones técnicas planteadas anteriormente.

\---

## 6\. Puntos que todavía requieren decisión conjunta (no bloquean el inicio)

Estos puntos se pueden cerrar en la próxima reunión o por mensaje corto. **No detienen** el arranque de IA-02 / IA-03 / IA-04:

|#|Punto|Opciones principales|Prioridad|
|-|-|-|-|
|1|Comportamiento ante contexto insuficiente|`error` controlado vs `partial`|Alta|
|2|Evidencia exacta de grounding|score + observaciones (+ IDs de chunks opcionales)|Alta|
|3|Responsabilidad de persistencia OCI|Backend / DataIA / mixta|Media|
|4|Catálogo mínimo de códigos de error|Lista corta acordada|Media|
|5|Default de `nivel\\\_detalle`|Obligatorio vs default = `Didactico`|Baja|

Todo lo demás (streaming, metadata avanzada, especialización de Query Builder, etc.) queda como evolución posterior al MVP.

\---

## 
