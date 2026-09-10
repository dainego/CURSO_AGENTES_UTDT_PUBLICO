# multi_model

Pipeline que combina un agente de research en Gemini (nativo, con la tool `google_search`) con dos agentes en paralelo que escriben en Gemini también, pero vía **LiteLLM** (LinkedIn e Instagram), y un agente final que fusiona ambos posts.

En el repo original, los agentes de LinkedIn e Instagram usaban OpenAI y Claude respectivamente vía LiteLLM — acá se reemplazaron por Gemini (el modelo más barato disponible, `gemini-3.5-flash-lite` — `gemini-2.5-flash-lite` dejó de estar disponible para cuentas nuevas de AI Studio) ruteado por LiteLLM, para conservar el patrón de orquestación multi-modelo sin necesitar API keys de otros proveedores.

⚠️ Este ejemplo requiere la **Opción A (Google AI Studio)** — la ruta `gemini/` de LiteLLM autentica con `GOOGLE_API_KEY`, no con las credenciales de Vertex AI de la Opción B. Ver `.env.example` para el detalle.

```bash
cp .env.example .env   # completá GOOGLE_API_KEY (Opción A)
adk run multi_model "el futuro de los agentes de IA"
# o: adk web
```
