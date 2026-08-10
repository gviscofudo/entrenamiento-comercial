# Simulador de Negociación FUDO

Ring de negociación gamificado para entrenar al equipo comercial de FUDO (Venta, Retención, Recupero, Up-selling) con casos reales anonimizados, un comprador simulado por IA, instructor en vivo y evaluación automática.

## Cómo funciona

- `index.html` es un archivo estático único (HTML + CSS + JS). No requiere build ni servidor propio.
- Usa la API de Claude (`api.anthropic.com`) para simular al cliente, dar coaching en vivo y evaluar la negociación al cerrar.
- Usa [Supabase](https://supabase.com) como backend para progreso, liga mensual y la cola de casos de Intercom.

## Deploy

Este archivo está pensado para hostearse como sitio estático (GitHub Pages, Netlify, Vercel, etc.). Al ser un único `index.html`, alcanza con:

1. Subir este repo a GitHub.
2. Activar GitHub Pages (Settings → Pages → Deploy from branch → `main` → `/root`).
3. Listo, queda disponible en `https://<usuario>.github.io/<repo>/`.

## ⚠️ Importante: la API key de Claude

Este archivo llama directamente a `https://api.anthropic.com/v1/messages` desde el navegador. **Dentro de Claude.ai (como artifact) esa llamada funciona sin exponer ninguna key**, porque Anthropic la resuelve del lado del servidor.

Fuera de Claude.ai (acá, en GitHub Pages), **esa llamada no va a funcionar así nomás** — necesita que se le pase una API key real de Anthropic, y ponerla directamente en el HTML la dejaría expuesta a cualquiera que abra el archivo o inspeccione el código fuente. Antes de este deploy hay que resolver esto con uno de estos caminos:

- **Recomendado**: un pequeño backend/proxy (una function serverless en Vercel/Netlify/Supabase Edge Functions) que reciba el pedido del HTML, le agregue la key desde una variable de entorno del servidor, y llame a Anthropic. El HTML se actualiza para pegarle a esa function en vez de a `api.anthropic.com` directamente.
- **No recomendado para producción**: dejar la key en el HTML. Sirve solo para una prueba interna muy acotada y de corta duración, nunca para algo que vean 30-80 personas.

## Supabase

El proyecto real ("Entrenamiento Comercial") ya está creado y con el esquema aplicado (`reps`, `progress`, `monthly_scores`, `sessions`, `case_queue`). La URL y la publishable key ya están en `index.html`. Las políticas RLS están abiertas (cualquiera con la publishable key puede leer/escribir) — aceptable para una herramienta interna de bajo riesgo; si se quiere blindar más adelante, agregar Supabase Auth real.
