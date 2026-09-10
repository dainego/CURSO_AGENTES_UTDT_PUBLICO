# Tutorial 0 — Introducción a Google ADK

Seis módulos curados y probados en vivo para dar los primeros pasos con **Google ADK** (Agent Development Kit), el framework de Google para construir agentes basados en LLM: agentes con tools, agentes que se encadenan entre sí, agentes que fuerzan su salida a un formato estructurado, agentes con estado de sesión, orquestación multi-modelo vía LiteLLM, y callbacks del ciclo de vida del agente.

Adaptado desde [github.com/AhsanAyaz/ai-agents-google-adk](https://github.com/AhsanAyaz/ai-agents-google-adk) (tiene más ejemplos de los que se incluyen acá — este tutorial se queda con los más simples e intuitivos para arrancar; el resto vive en `../Tutorial1/`).

## Los seis módulos, en orden

| # | Carpeta | Qué aprendés | Nivel |
|---|---|---|---|
| 1 | [`tools_agent/`](tools_agent/) | Qué es una tool y cómo el agente decide usarla solo | Introductorio |
| 2 | [`structured_output/`](structured_output/) | Forzar salida con schema (Pydantic) + encadenar 2 agentes vía estado | Intermedio |
| 3 | [`sessions_and_agents/`](sessions_and_agents/) | Inyectar estado de sesión (`InMemorySessionService`) en el prompt del agente | Intermedio |
| 4 | [`multi_model/`](multi_model/) | Orquestar agentes en paralelo sobre distintos modelos vía **LiteLLM** | Intermedio-avanzado |
| 5 | [`agents_and_callbacks/`](agents_and_callbacks/) | 5 callbacks del ciclo de vida (antes/después de agente, modelo y tool) | Intermedio-avanzado |
| 6 | [`marketing_campaign_agent/`](marketing_campaign_agent/) | Orquestar un pipeline de 5 agentes con `SequentialAgent` | Cierre / integrador |

Cada carpeta tiene su propio `README.md` con el objetivo puntual del ejemplo, una explicación de cómo está implementado (con fragmentos de código comentados) y consultas de prueba sugeridas. Empezá por el 1 y segui el orden — cada uno suma un concepto sobre el anterior.

## Setup (una sola vez)

```bash
cd ADK/Tutorial0
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

Esto instala, entre otras cosas, `google-cloud-aiplatform[adk,agent_engines]` y **`google-adk>=2.8.0`**, que trae el **CLI `adk`** que vas a usar para correr los ejemplos.

⚠️ **La versión de `google-adk` importa.** Con `2.1.0` (la que se instalaba antes de fijar la versión en `requirements.txt`), `adk web` corrido desde acá no muestra el panel de grafo/README de `multi_model` (tira un error 500 al serializar los agentes que usan `LiteLlm`) y directamente **no detecta** los 5 sub-ejemplos de `agents_and_callbacks/` — esa versión solo escanea un nivel de carpetas bajo `adk web`, así que `agents_and_callbacks/example_01_...` queda invisible. Ambos problemas están arreglados en `2.8.0`. Si ya tenías el venv creado con una versión vieja: `pip install -U google-adk`.

## Autenticación (elegí UNA de las dos)

Google ADK necesita acceso a un modelo Gemini. Cada ejemplo tiene su propio `.env.example` — copialo a `.env` en esa misma carpeta (`cp .env.example .env`) y completá **una** de estas dos opciones.

⚠️ **El `.env.example` ya viene con la Opción A activa** (lista para completar) **y la Opción B comentada**. Si querés usar la B, comentá las líneas de la A y descomentá las de la B — dejar las dos activas al mismo tiempo hace que gane la B y te tire un error confuso mencionando `tu-project-id`.

### Opción A — Google AI Studio (recomendada para arrancar)

La más simple: no necesitás una cuenta de Google Cloud ni `gcloud` instalado.

1. Conseguí una API key gratis en [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
2. En el `.env` (ya están activas, solo completá la key):
   ```
   GOOGLE_API_KEY=tu-api-key
   GOOGLE_GENAI_USE_VERTEXAI=FALSE
   ```

### Opción B — Vertex AI con tu cuenta de Google Cloud

Si ya tenés un proyecto de GCP con la API de Vertex AI habilitada:

1. `gcloud auth application-default login`
2. `gcloud config set project tu-project-id`
3. En el `.env`, comentá las 2 líneas de la Opción A y descomentá/completá:
   ```
   GOOGLE_GENAI_USE_VERTEXAI=TRUE
   GOOGLE_CLOUD_PROJECT=tu-project-id
   GOOGLE_CLOUD_LOCATION=us-central1
   ```

En ambos casos, el modelo usado es `gemini-2.5-flash` (rápido y con buena relación costo/calidad para estos ejemplos).

⚠️ **Excepción — `multi_model/`:** este ejemplo enruta dos de sus tres agentes vía LiteLLM (`gemini/` provider), que autentica con `GOOGLE_API_KEY` y no con las credenciales de Vertex AI. Por eso **requiere la Opción A** aunque el resto del tutorial use la Opción B. Ver su propio `README.md`.

## Cómo correr un ejemplo

Desde `ADK/Tutorial0/` (así `adk` detecta los tres a la vez):

```bash
# una sola consulta por consola:
adk run tools_agent "¿qué hora es?"

# o la interfaz web, con selector de ejemplo, historial y grafo de agentes:
adk web
```

`sessions_and_agents/` es la excepción: no se corre con `adk run`/`adk web`, sino con su propio script (`python run_agent_with_session.py`), porque arma la sesión a mano antes de invocar al agente — ver su `README.md`.

`agents_and_callbacks/` es una carpeta con 5 sub-ejemplos independientes (`example_01_...` a `example_05_...`), cada uno con su propio `agent.py` — ver su `README.md` para el detalle de cada uno. En el selector de `adk web` van a aparecer con el nombre `agents_and_callbacks.example_0N_...` (con punto, no como una carpeta agrupadora) — cada uno es una app independiente, con su propio grafo y README.

`adk web` levanta un servidor local (por defecto en `http://127.0.0.1:8000`) con una UI donde podés:
- Elegir qué ejemplo correr, arriba a la izquierda.
- Chatear con el agente y ver, evento por evento, qué tool o sub-agente se ejecutó.
- Ver el **grafo de la estructura del agente** (ícono junto al selector) — muy útil en `marketing_campaign_agent`, donde se ven los 5 sub-agentes encadenados, resaltados en verde mientras corren.

## Qué está probado

Los tres primeros ejemplos se corrieron de punta a punta contra la API real (vía Vertex AI) sin errores, el **2026-09-03/04** — incluidos los 5 pasos completos de `marketing_campaign_agent` (confirmado por log: 5 llamadas al modelo y cierre limpio del runner) y el tool que pega contra una API externa en `tools_agent` (`get_randomuser_from_ramdomuserme`).

`sessions_and_agents`, `multi_model` y `agents_and_callbacks` se sumaron y probaron el **2026-09-08**:
- `sessions_and_agents` corrió de punta a punta vía Vertex AI (Opción B), inyectando y leyendo correctamente el estado de sesión.
- `multi_model` corrió de punta a punta con una API key real de AI Studio (Opción A): `ResearchAgent` (Gemini nativo, con `google_search`) → `LinkedInPostsAgent` + `InstagramReelScriptAgent` en paralelo (ambos vía LiteLLM) → `PostsMergerAgent`. Nota: `gemini-2.5-flash-lite` dejó de estar disponible para cuentas nuevas de AI Studio, así que el modelo barato usado vía LiteLLM quedó en `gemini-3.5-flash-lite` — si en el futuro este también deja de estar disponible, hay que actualizar `LITELLM_GEMINI_MODEL` en el `.env`.
- `agents_and_callbacks`: los 5 sub-ejemplos importan sin error; se corrió de punta a punta contra Vertex AI (Opción B) el `example_01_agent_lifecycle_logging`, confirmando los logs `[BEFORE AGENT]`/`[AFTER AGENT]` con `session_id` y duración. Los otros 4 (sanitización de input, enhancement de respuesta, validación de args de tool, y caching de tool) no se probaron interacción por interacción — están listos para probarlos en clase con `adk web`, siguiendo los escenarios sugeridos en cada `README.md`.

La vía A (Google AI Studio) se dejó bien configurada en el código y los `.env.example` (y se verificó que, tal como quedan los archivos, no cae en la trampa de la sección de arriba), pero no se probó con una API key real en este entorno — antes de una clase en vivo, conviene correr al menos uno con esa opción para confirmar.

**2026-09-09 — fix de `google-adk`:** el venv tenía instalado `google-adk==2.1.0` (nunca quedó pineado en `requirements.txt`), que rompía el panel de grafo/README de `multi_model` (500 al serializar agentes `LiteLlm`) y no descubría los 5 sub-ejemplos de `agents_and_callbacks/` en `adk web` (esa versión no escanea subcarpetas anidadas). Se subió a `2.8.0` y se fijó en `requirements.txt`; se volvió a verificar que los 10 agentes (los 5 top-level + los 5 de `agents_and_callbacks`, más `multi_model` y `marketing_campaign_agent`) cargan bien en `adk web`, con grafo y README funcionando en los 10.

**Nota de idioma:** las *instructions* de los agentes (los prompts que definen su comportamiento, en `agent.py`/`instructions.py`) quedaron en inglés — son el código ya probado del repo original, y traducirlas implicaba re-testear todo. En la práctica, el modelo suele responder en el idioma de tu pregunta, pero en las pruebas `marketing_campaign_agent` devolvió el brief mayormente en inglés incluso con una consulta en español. Si para el curso preferís que respondan siempre en español, hay que traducir esas instructions y volver a probar — es una decisión de contenido, no algo que se resuelve solo con documentación.
