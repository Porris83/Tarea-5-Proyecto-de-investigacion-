# Tarea 5: Piper TTS para Voz Médica Natural

## Descripción del Proyecto

Este repositorio contiene la implementación de un sistema de síntesis de voz (TTS) usando Piper TTS, enfocado en aplicaciones médicas con voces en español. El proyecto incluye integración con n8n para automatización de workflows.

## Contenido de la Carpeta Entregables

###  `Modelo tts/`
- **`readme.md`**: Guía completa de instalación y configuración de Piper TTS en Ubuntu
- Instrucciones paso a paso para configurar el entorno virtual
- Ejemplo de síntesis con terminología médica ("espolón calcáneo")

###  `n8n/`
- **`Readme-n8n.md`**: Documentación del Agente Clínico RAG con n8n, Qdrant y Ollama
- **`Readme-piper-n8n.md`**: Guía específica para la integración de Piper TTS con n8n
- **`Configuracion n8n/`**:
  - `Agente Clínico RAG – Paso 1.json`: Workflow RAG completo
  - `Texto--voz.json`: Workflow específico para conversión texto a voz

###  `output.wav`
Muestra de audio generada con voz en español argentino (modelo daniela-high) usando terminología médica.

## Características Implementadas

✅ **Configuración completa de Piper TTS**
- Modelo de voz en español argentino (es_AR-daniela-high)
- Entorno virtual Python 3.10
- Generación de audio .wav

✅ **Integración con n8n**
- API REST via webhook (`/voz`)
- Workflow automatizado para conversión texto a voz
- Respuesta con archivo de audio generado

✅ **Documentación completa**
- Guías de instalación paso a paso
- Instrucciones de uso y configuración
- Ejemplos prácticos con terminología médica

## Estado del Proyecto

Este es un entregable funcional que cumple con los objetivos básicos de la Tarea 5. El sistema está configurado para uso local y puede expandirse con múltiples voces y pruebas de calidad adicionales según la evolución del proyecto.

## Tecnologías Utilizadas

- **Piper TTS**: Motor de síntesis de voz
- **n8n**: Automatización de workflows
- **Python 3.10**: Entorno de ejecución
- **Ubuntu/WSL2**: Sistema operativo
- **Wyoming Protocol**: API de comunicación

## Próximos Pasos

El proyecto puede expandirse con:
- Comparación de múltiples voces médicas
- Pruebas de calidad con terminología compleja
- Integración con sistemas médicos existentes
- Optimización de parámetros de síntesis

---

*Proyecto en desarrollo*
