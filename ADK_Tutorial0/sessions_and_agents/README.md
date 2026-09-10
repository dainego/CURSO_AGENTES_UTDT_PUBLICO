# sessions_and_agents

Muestra cómo inyectar estado de sesión (nombre de usuario, preferencias) en el prompt del agente vía `InMemorySessionService`, en vez de pasarlo como parte del mensaje del usuario.

```bash
cp .env.example .env   # completá la Opción A o B — ver ../README.md
python run_agent_with_session.py
```

Este ejemplo se corre con el script Python directo (no con `adk run`), porque arma la sesión a mano antes de invocar al agente.
