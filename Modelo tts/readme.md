

# Guía de instalación y uso de Piper TTS en Ubuntu

## 1. Instalar dependencias básicas

Abre una terminal y ejecuta:

```bash
sudo apt update
sudo apt install python3.10 python3.10-venv wget
```

## 2. Crear una carpeta de trabajo

```bash
mkdir ~/piper-tts
cd ~/piper-tts
```

## 3. Crear y activar un entorno virtual

```bash
python3.10 -m venv venv310
source venv310/bin/activate
```

## 4. Actualizar pip e instalar Piper

```bash
pip install --upgrade pip
pip install piper-tts wyoming-piper
```

## 5. Descargar un modelo de voz en español

Descarga los archivos del modelo desde Hugging Face ([https://huggingface.co/rhasspy/piper-voices](https://huggingface.co/rhasspy/piper-voices)) y cópialos a esta carpeta.

Ejemplo para la voz "daniela" (español argentino, calidad alta):

- es_AR-daniela-high.onnx
- es_AR-daniela-high.onnx.json

## 6. Probar la síntesis de voz

Ejecuta este comando para generar un audio:

```bash
echo "El diagnóstico provisional es espolón calcáneo." | venv310/bin/piper --model es_AR-daniela-high.onnx --output-file prueba.wav
```

Esto creará el archivo `prueba.wav` con la frase sintetizada.

## 7. (Opcional) Automatizar pruebas con un script


Puedes usar el siguiente script Python para generar varios audios automáticamente, con nombres únicos para cada archivo:

```python
import subprocess
import datetime

# Cambia el modelo si usas otro
MODEL = "es_AR-daniela-high.onnx"

# Frases para probar
frases = [
    "El diagnóstico provisional es asimilación nanomáquina alienígena.",
    "Paciente con antecedentes de exposición prolongada a la Nebulosa Cygnus X-1.",
    "Se recomienda cuarentena de clase Omega y pulsos de contención taquiónicos.",
    "¡Emergencia: singularidad de bolsillo inestable en reactor principal en curso!"
]

for i, texto in enumerate(frases, 1):
    # Nombre de archivo único con timestamp y número
    filename = f"prueba_{i}_{datetime.datetime.now().strftime('%Y%m%d_%H%M%S')}.wav"
    print(f"Generando: {filename} ...")
    # Ejecuta Piper desde el entorno virtual
    subprocess.run(
        [ "venv310/bin/piper", "--model", MODEL, "--output-file", filename ],
        input=texto.encode("utf-8")
    )
print("¡Listo! Audios generados.")
```

Guarda esto como `generate_audio.py` y ejecútalo con:

```bash
python generate_audio.py
```

---

¿Dudas o problemas? Revisa la documentación oficial de Piper: [https://github.com/rhasspy/piper](https://github.com/rhasspy/piper)

