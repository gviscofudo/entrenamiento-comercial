# Simulador de Negociación FUDO

Ring de negociación gamificado para entrenar al equipo comercial de FUDO (Venta, Retención, Recupero, Up-selling) con casos reales anonimizados, un comprador simulado por IA, instructor en vivo y evaluación automática.

**En vivo**: https://gviscofudo.github.io/entrenamiento-comercial/

## Login

Usa la misma cuenta del Toolkit CS (Cotizador, Guru, etc.): mismo proyecto de Supabase Auth, misma tabla `user_profiles`/`roles`. El panel de admin (cola de casos de Intercom) queda visible solo para el rol `admin`.

## Cómo funciona

- `index.html` es un archivo estático único (HTML + CSS + JS), sin build.
- El comprador IA, el instructor en vivo y el evaluador pasan por una **Edge Function de Supabase** (`claude-proxy`, proyecto "Entrenamiento Comercial") que agrega la API key de Anthropic del lado del servidor. La key nunca vive en este HTML.
- Dos proyectos de Supabase:
  - **Identidad** (compartido con el Hub, `xkqopmcfwxpajbvjgclg`): login, nombre, rol.
  - **Entrenamiento Comercial** (`nsgvyjbagqrapcofqfkl`, propio): progreso, liga mensual, historial de sesiones, cola de casos de Intercom, y la Edge Function `claude-proxy`.

## ⚠️ Único paso manual pendiente: activar la API key de Claude

La Edge Function `claude-proxy` ya está desplegada, pero le falta el secret con la API key real de Anthropic (esto **no se puede hacer vía MCP/API automatizada** — hay que cargarlo a mano en el dashboard, por seguridad):

1. Entrar a https://supabase.com/dashboard/project/nsgvyjbagqrapcofqfkl/settings/functions
2. Agregar un secret: `ANTHROPIC_API_KEY` = (tu API key de Anthropic, se consigue en https://console.anthropic.com/settings/keys)
3. Guardar. La función ya queda activa sin necesidad de redeploy.

Hasta que se cargue ese secret, el resto del simulador (login, categorías, progreso, liga, cola de admin) funciona normal — solo el chat con el cliente simulado va a devolver un error indicando que falta la key.

## Deploy

Repo independiente del Hub — se puede actualizar sin tocar ni redeployar el Hub, y viceversa. Cualquier cambio a `index.html` + push a `main` se refleja solo en GitHub Pages (puede tardar 1-2 min).

Para que aparezca como una herramienta más del Toolkit CS, agregar un `tool-card` en el `index.html` del Hub apuntando a la URL de arriba.

## Supabase — proyecto "Entrenamiento Comercial"

Esquema aplicado: `reps` (espejo de usuarios del Hub, mismo id de Auth), `progress`, `monthly_scores`, `sessions`, `case_queue`. RLS activado con políticas abiertas (aceptable para herramienta interna de bajo riesgo). Edge Function `claude-proxy` para las llamadas a Claude.
