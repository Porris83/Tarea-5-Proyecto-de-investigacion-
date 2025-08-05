
# Agente Clínico RAG Local con n8n, Qdrant y Ollama

Este proyecto implementa un flujo completo de Retrieval-Augmented Generation (RAG) en entorno local. Permite recibir preguntas clínicas y devolver respuestas relevantes desde una base vectorial cargada en Qdrant, usando embeddings generados por multilingual-e5-large vía Ollama.

---

## Índice

1. [Requisitos](#requisitos)
2. [Instalación](#instalación)
3. [Workflow paso a paso](#workflow-paso-a-paso)
4. [Prueba con curl](#prueba-con-curl)
5. [Posibles errores](#posibles-errores)
6. [Notas técnicas](#notas-técnicas)

---

## Requisitos

- Docker instalado y corriendo
- WSL2 con Ubuntu, o Linux nativo
- Node.js >= 18
- npm >= 9
- Python 3 (para la carga de datos en Qdrant)
- Qdrant (puerto por defecto: 6333)
- Ollama con el modelo multilingual-e5-large descargado
- n8n instalado globalmente

---

## Instalación

### 1. Instalar n8n

```bash
sudo npm install -g n8n
```

### 2. Iniciar n8n

```bash
n8n
```

Accedé desde tu navegador a: [http://localhost:5678](http://localhost:5678)

---

## Workflow paso a paso

### Paso 1 – Nodo Webhook

- **Método:** POST
- **Path:** consulta
- **Respond:** Using Respond to Webhook Node

Espera una entrada en formato:

```json
{
  "pregunta": "Qué hacer en caso de hipoglucemia severa?"
}
```

### Paso 2 – Nodo HTTP Request → Ollama

Envía la pregunta al modelo de embeddings.

**Configuración:**

- **Method:** POST
- **URL:** `http://localhost:11434/api/embeddings`
- **Body Content Type:** application/json

```json
{
  "model": "multilingual-e5-large",
  "prompt": "{{ $json.pregunta }}"
}
```

### Paso 3 – Nodo HTTP Request → Qdrant

Busca los vectores más similares en la colección.

**Configuración:**

- **Method:** POST
- **URL:** `http://localhost:6333/collections/escenarios_clinicos/points/search`
- **Body Content Type:** application/json

```json
{
  "vector": {{ $json.embedding }},
  "top": 5,
  "with_payload": true
}
```

Podés ajustar `top: 1` para respuestas más directas.

### Paso 4 – Nodo Code

Extrae los campos relevantes del resultado:

```javascript
const puntos = $json.result || [];
return [
  {
    json: {
      respuestas: puntos.map(p => ({
        texto: p.texto || "[sin texto]",
        especialidad: p.jerarquia?.especialidad || "[sin especialidad]"
      }))
    }
  }
];
```

### Paso 5 – Nodo Respond to Webhook

Opción: First Incoming Item

Esto envía la respuesta al usuario final:

```json
{
  "respuestas": [
   "Escenario N° 2: Cuidados del postoperatorio inmediato Caso: la Sra Gonzalez, David de 72 años de edad, quien fue sometido a una cirugía exploratoria de abdomen, por imagen en tomografía compatible con Cáncer de páncreas. Se empleó anestesia general balanceada. El usuario es dejado por el camillero en la habitación. La Sra Gonzales ingresa orientado en tiempo y espacio, con ligera somnolencia. Presenta AVP en MSI, sonda vesical, sonda nasogástrica y drenaje en cavidad abdominal de tipo aspirativo. El usuario refiere tener náuseas y vómitos."
  ]
}
{
  "especialidad": [
    "cirugia"
  ]
}
```

---

## Prueba con curl

```bash
curl -X POST http://localhost:5678/webhook-test/consulta \
  -H 'Content-Type: application/json' \
  -d '{"pregunta": "sonda vesical"}'
```

---

## Posibles errores

| Mensaje                  | Causa                                         | Solución                                              |
|--------------------------|-----------------------------------------------|-------------------------------------------------------|
| 404 - Model not found    | Ollama no tiene el modelo descargado          | Ejecutar: `ollama pull multilingual-e5-large`         |
| Webhook not registered   | No ejecutaste el workflow antes de enviar curl| Ejecutá el workflow en n8n                            |
| JSON inválido            | Error en la construcción del cuerpo JSON       | Asegurá que `vector` sea una lista numérica válida    |

---

## Notas técnicas

- **Modelo de embeddings:** Usá siempre `multilingual-e5-large` (sin prefijos ni variantes). Verificá que esté correctamente descargado en Ollama.
- **Consistencia de embeddings:** Los embeddings cargados en Qdrant deben generarse con el mismo modelo que usás para las consultas. Si cambias el modelo, deberás regenerar los embeddings y recargar la colección.
- **Ajuste de resultados:** Comenzá probando con `top: 5` para obtener contexto amplio y luego ajustá a `top: 1` si buscás precisión en la respuesta.
- **Extracción de campos:** El campo extraído desde `payload` puede ser `texto`, `nombre`, `tag` u otro, según tu dataset. Adaptá el código del nodo Function para reflejar la estructura real de tus datos.
- **Seguridad y acceso:** Asegurá que los puertos de Qdrant (`6333`) y Ollama (`11434`) estén accesibles desde tu entorno n8n. Considerá el uso de redes Docker o reglas de firewall si trabajás en producción.
- **Validación de datos:** Antes de enviar los datos a Qdrant, validá que el embedding sea un array numérico y que la colección esté correctamente poblada.

---