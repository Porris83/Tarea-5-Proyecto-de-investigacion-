# Asistente Clínico RAG con Síntesis de Voz

Sistema completo de consultas médicas que combina búsqueda semántica (RAG) con síntesis de voz usando n8n como orquestador.

## 🚀 Funcionalidades

- **Consulta por texto**: Envía preguntas médicas vía HTTP POST
- **Búsqueda semántica**: Encuentra respuestas relevantes using embeddings y QDrant  
- **Síntesis de voz**: Convierte las respuestas a audio con Piper TTS
- **Respuesta en audio**: Recibe archivos WAV con la respuesta hablada

## 📋 Prerrequisitos

Debes tener instalado y configurado:

- **n8n** (puerto 5678)
- **Ollama** con modelo `qllama/multilingual-e5-large`
- **QDrant** (puerto 6333) con colección `escenarios_clinicos`
- **Piper TTS** con modelo `es_AR-daniela-high.onnx` en `/home/tu-usuario/piper-tts/`

## ⚡ Configuración rápida

### 1. Importar el workflow en n8n

1. Abre n8n en `http://localhost:5678`
2. Ve a **Import** 
3. Copia y pega el contenido del archivo `Consulta_Clinica_con_Voz.json`
4. Haz clic en **Import**

### 2. Activar el workflow

1. Una vez importado, cambia el estado de **Inactive** a **Active**
2. El webhook estará disponible en: `http://localhost:5678/webhook/consulta_audio`

### 3. Verificar servicios activos

Antes de probar, asegúrate que estén corriendo:

```bash
# Terminal 1: Ollama
ollama serve

# Terminal 2: QDrant (si no está como servicio)
qdrant

# Terminal 3: n8n
n8n start
```

## 🧪 Probar el sistema

### Opción 1: Con el HTML de prueba

1. Abre `test_consulta_clinica.html` en tu navegador
2. Ingresa tu pregunta médica
3. Haz clic en **🎤 Enviar Consulta**
4. Descarga y reproduce el archivo de audio generado

### Opción 2: Con cURL

```bash
curl -X POST http://localhost:5678/webhook/consulta_audio \
  -H "Content-Type: application/json" \
  -d '{"pregunta": "¿Cuáles son los síntomas de la diabetes?"}' \
  --output respuesta.wav
```

### Opción 3: Con código Python

```python
import requests

url = "http://localhost:5678/webhook/consulta_audio"
data = {"pregunta": "¿Qué es la hipertensión arterial?"}

response = requests.post(url, json=data)

if response.ok:
    with open("respuesta.wav", "wb") as f:
        f.write(response.content)
    print("Audio guardado como respuesta.wav")
```

## 🔄 Flujo del sistema

1. **Webhook** → Recibe consulta POST con `{"pregunta": "texto"}`
2. **Ollama Embeddings** → Convierte pregunta a vector numérico
3. **Buscar QDrant** → Encuentra contenido médico similar
4. **Code** → Extrae y formatea la respuesta
5. **Execute Command** → Genera audio con Piper TTS
6. **Read/Write Files** → Lee el archivo WAV generado
7. **Respond to Webhook** → Devuelve audio al cliente

## 🐛 Troubleshooting

**Error: "connection refused"**
- Verifica que todos los servicios estén corriendo
- Ollama: `http://localhost:11434`
- QDrant: `http://localhost:6333`
- n8n: `http://localhost:5678`

**Error: "model not found"**
- Ejecuta: `ollama pull qllama/multilingual-e5-large`

**Error: "collection not found"**  
- Asegúrate que QDrant tenga la colección `escenarios_clinicos` creada

**Sin respuesta de audio**
- Verifica que Piper esté en `/home/tu-usuario/piper-tts/venv310/bin/piper`
- Verifica que el modelo esté en `/home/tu-usuario/piper-tts/es_AR-daniela-high.onnx`

## 📁 Archivos incluidos

- `Consulta_Clinica_con_Voz_UNIFICADO.json` → Workflow de n8n
- `test_consulta_clinica.html` → Interfaz web de prueba
- `README.md` → Esta documentación

¡Listo! Tu asistente clínico RAG con voz debería estar funcionando. 
