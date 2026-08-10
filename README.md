# Simulador de Negociación FUDO

Ring de negociación gamificado para entrenar al equipo comercial de FUDO (Venta, Retención, Recupero, Up-selling) con casos reales anonimizados, un comprador simulado por IA, instructor en vivo y evaluación automática.

## Login

Usa la **misma cuenta del Toolkit CS** (Cotizador, Guru, etc.): mismo proyecto de Supabase Auth, misma tabla `user_profiles`/`roles`. Cualquiera que ya tenga cuenta en el Hub entra directo acá sin crear nada nuevo. El panel de admin (cola de casos de Intercom) queda visible solo para el rol `admin`, igual que en el Hub.

Nota: es un login independiente por sitio (no sesión única real entre Hub y este repo, salvo que se hosteen bajo el mismo dominio) — pero al ser el mismo backend de Auth, las credenciales son las mismas.

## Cómo funciona

- `index.html` es un archivo estático único (HTML + CSS + JS). No requiere build ni servidor propio.
- Usa la API de Claude (`api.anthropic.com`) para simular al cliente, dar coaching en vivo y evaluar la negociación al cerrar.
- Usa dos proyectos de [Supabase](https://supabase.com):
  - **Identidad** (compartido con el Hub): login, nombre, rol.
  - **Entrenamiento Comercial** (propio): progreso, liga mensual, historial de sesiones, cola de casos de Intercom.

## Deploy

Pensado para hostearse como sitio estático (GitHub Pages, Netlify, Vercel, etc.), independiente del repo del Hub — así se puede actualizar el entrenador sin tocar ni redeployar el Hub, y viceversa.

1. Subir este repo a GitHub.
2. Activar GitHub Pages (Settings → Pages → Deploy from branch → `main` → `/root`).
3. Agregar un `tool-card` en el `index.html` del Hub que linkee a la URL resultante, para que aparezca como una herramienta más del Toolkit CS.

## ⚠️ Pendiente: la API key de Claude

Este archivo llama directamente a `https://api.anthropic.com/v1/messages` desde el navegador. Dentro de Claude.ai esa llamada la resuelve Anthropic del lado del servidor sin exponer ninguna key. **Fuera de Claude.ai (acá, en GitHub Pages), esa llamada no va a funcionar hasta resolver esto**:

- **Recomendado**: una Edge Function de Supabase (podemos usar el mismo proyecto "Entrenamiento Comercial") que reciba el pedido del HTML, le agregue la key de Anthropic desde una variable de entorno del servidor, y llame a la API. El HTML se actualiza para pegarle a esa function en vez de a `api.anthropic.com` directamente.
- **No recomendado para producción**: dejar la key en el HTML — quedaría expuesta a cualquiera que abra "ver código fuente".

## Supabase — proyecto "Entrenamiento Comercial"

Esquema ya aplicado: `reps` (espejo de usuarios del Hub, mismo id de Auth), `progress`, `monthly_scores`, `sessions`, `case_queue`. RLS activado con políticas abiertas (aceptable para herramienta interna de bajo riesgo).
