n8n
curl -X POST http://localhost:5678/webhook/voz \

# Guía de Uso: Generador de Voz Clínica con n8n + Piper TTS

Este flujo permite convertir texto en audio (.wav) usando Piper TTS, de forma local y automatizada con n8n.

---

## Requisitos

- Ubuntu (WSL2 o nativo)
- Python 3.10 con `piper-tts` instalado
- Modelo de voz descargado (ejemplo: `es_AR-daniela-high.onnx` y su `.json`)
- n8n instalado y corriendo

---

## Instalación rápida

1. **Instala Piper**
   - Ver la guía de instalación de Python y Piper en la documentación de piper-tts.

2. **Descarga el modelo de voz**
   - Descarga el archivo `.onnx` y su `.json` correspondiente para el modelo que desees usar (por ejemplo, `es_AR-daniela-high`).

3. **Inicia n8n**
   - En tu terminal, ejecuta:
     ```bash
     n8n
     ```
   - Accede a la interfaz web en [http://localhost:5678](http://localhost:5678).

---

## Flujo n8n: Texto a Voz

Este flujo utiliza el nodo Execute Command para llamar a Piper directamente.

### 1. Nodo Webhook

- **Método:** POST
- **Path:** `/voz`
- **Entrada esperada:** JSON
  ```json
  { "texto": "El usuario solicita el manual del equipo medico." }
  ```

### 2. Nodo Execute Command

- **Command:** bash
- **Arguments:**
  ```bash
  echo '{{ $json.body.texto }}' | [ruta-a-piper-venv]/bin/piper --model [ruta-a-modelo-onnx] --length-scale 1.1 --output-file [ruta-a-salida-wav]/output.wav
  ```
  - Reemplaza `[ruta-a-piper-venv]`, `[ruta-a-modelo-onnx]` y `[ruta-a-salida-wav]` por las rutas reales de tu sistema.
  - El parámetro `--length-scale 1.1` es opcional y sirve para ralentizar la voz.

### 3. Nodo Read/Write Files from Disk

- **Operation:** Read
- **File Path:** `[ruta-a-salida-wav]/output.wav`

### 4. Nodo Respond to Webhook

- Este nodo envía los datos binarios del archivo `.wav` como respuesta al cliente que hizo la llamada curl.

---

## Prueba rápida

Asegúrate de que el flujo de n8n esté activado (interruptor en la esquina superior derecha). En una terminal, ejecuta:

```bash
curl -X POST http://localhost:5678/webhook/voz \
  -H "Content-Type: application/json" \
  -d '{"texto": "El usuario solicita el manual del equipo medico."}' > audio_respuesta.wav
```

- El archivo `audio_respuesta.wav` es la respuesta binaria que envía n8n, guardada por tu terminal.
- El archivo original generado por n8n se llama `output.wav` y se encuentra en la ruta que especificaste.

---

## Notas y recomendaciones

- El flujo es independiente de otros sistemas (no requiere Ollama ni Qdrant).
- Podés cambiar el modelo de voz editando la ruta y el nombre en el nodo Execute Command.
- Si el audio no se genera, revisa que Piper esté correctamente instalado y que las rutas sean correctas.
- Para mayor calidad, probá diferentes frases médicas y ajustá el parámetro `--length-scale`.

---


---

## Exportar e importar el workflow n8n (.json)

Para compartir este flujo o usarlo en otra PC:

**Exportar:**
1. Abre el workflow en la interfaz web de n8n.
2. Haz clic en el menú de tres puntos (⋮) arriba a la derecha.
3. Selecciona “Export” y descarga el archivo `.json`.

**Importar:**
1. En otra instalación de n8n, abre la interfaz web.
2. Ve al menú de workflows y elige “Import”.
3. Selecciona el archivo `.json` exportado.
4. Ajusta las rutas y parámetros según tu sistema.

Esto permite que cualquier persona use el flujo sin tener que crearlo desde cero.

¿Dudas? Revisá los otros readmes del proyecto para instalación y pruebas avanzadas.
