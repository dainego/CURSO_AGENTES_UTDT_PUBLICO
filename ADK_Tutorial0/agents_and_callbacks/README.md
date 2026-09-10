# agents_and_callbacks — Callbacks del ciclo de vida del agente

## Objetivo

Mostrar los 4 puntos de enganche (*callbacks*) que expone ADK para interceptar y modificar el comportamiento de un agente en distintas etapas: antes/después de que corre el agente, antes/después de que se llama al modelo, y antes/después de que se ejecuta una tool. Son 5 sub-ejemplos independientes, cada uno con su propio `agent.py` autocontenido.

| # | Carpeta | Callback mostrado | Qué hace |
|---|---|---|---|
| 1 | [`example_01_agent_lifecycle_logging/`](example_01_agent_lifecycle_logging/) | `before_agent_callback` / `after_agent_callback` | Loggea inicio/fin de cada interacción, con `session_id` y duración |
| 2 | [`example_02_model_input_sanitization/`](example_02_model_input_sanitization/) | `before_model_callback` | Redacta PII (tarjetas, SSN) del input del usuario antes de que llegue al modelo |
| 3 | [`example_03_model_response_enchancement/`](example_03_model_response_enchancement/) | `after_model_callback` | Extrae datos estructurados de la respuesta del modelo y le agrega links según el contenido |
| 4 | [`example_04_tool_arg_validation_modification/`](example_04_tool_arg_validation_modification/) | `before_tool_callback` | Valida/normaliza los argumentos que el modelo decide pasarle a una tool antes de ejecutarla |
| 5 | [`example_05_tool_response_transformation_caching/`](example_05_tool_response_transformation_caching/) | `before_tool_callback` + `after_tool_callback` | Cachea resultados de tool y transforma la respuesta cruda en un formato legible |

Cada carpeta tiene su propio `README.md` (en inglés, del repo original) con el detalle de qué observar en la consola al probarlo — quedaron sin traducir porque son explicaciones ya probadas y traducirlas implicaría re-verificar cada una.

## Setup y autenticación

Mismo procedimiento que el resto del tutorial — ver `../README.md`. Cada sub-ejemplo trae su propio `.env.example` — copialo a `.env` (`cp .env.example .env`) y completá la Opción A o B.

## Cómo correr un ejemplo

Estos ejemplos están pensados para probarse con **múltiples turnos** (algunos muestran su efecto recién en el segundo mensaje, como el caching de `example_05`), así que conviene `adk web` en vez de `adk run` con una sola consulta:

```bash
cd agents_and_callbacks/example_01_agent_lifecycle_logging   # o el que quieras probar
adk web
```

También se puede correr `adk web` desde `ADK/Tutorial0/` (la raíz) y elegir el ejemplo en el selector — todos los `agent.py` de esta carpeta quedan expuestos ahí también.
