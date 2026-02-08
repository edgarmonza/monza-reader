# Monza Reader

> Speed Reader RSVP con voz clonada, colores Monza Lab, y controles avanzados.
>
> ---
>
> ## Que es Monza Reader
>
> Monza Reader es un lector de velocidad RSVP (Rapid Serial Visual Presentation) que muestra palabras una a una con un punto de reconocimiento optimo (ORP) resaltado. Incluye narracion con tu voz clonada via ElevenLabs.
>
> ## Features
>
> - **Lectura RSVP** con ORP (Optimal Recognition Point) resaltado en rosa Monza
> - - **Voz clonada** via ElevenLabs TTS API con tu voz
>   - - **Control de velocidad** visual (300-1200 WPM) y de audio (0.25x-4.0x)
>     - - **PDF support** con extraccion automatica de texto
>       - - **Chunks** de 1, 2, o 3 palabras
>         - - **Pausa inteligente** en puntuacion (2x en fin de oracion, 1.5x en comas)
>           - - **Fullscreen** nativo
>             - - **Atajos de teclado** completos
>               - - **Responsive** (mobile a desktop)
>                 - - **Backend seguro** (Vercel serverless) para proteger API keys
>                  
>                   - ## Stack
>                  
>                   - **Frontend:** React 18 + TypeScript + Vite + Tailwind CSS + Framer Motion + Lucide React
>                  
>                   - **Backend:** Vercel Serverless Functions (proxy ElevenLabs API)
>
> **Voz:** ElevenLabs API v1 (eleven_multilingual_v2)
>
> ## Colores Monza Lab
>
> | Color | Hex | Uso |
> |---|---|---|
> | Monza Pink | `#F8B4D9` | Principal / ORP / CTAs |
> | Monza Black | `#0B0B10` | Fondo |
> | Monza Cream | `#FFFCF7` | Texto |
> | Pink Hover | `#F4CBDE` | Hover states |
>
> **Fuentes:** Telegraf (display) + Public Sans (body)
>
> ## Especificaciones
>
> - [`MONZA-READER-SPEC.md`](./MONZA-READER-SPEC.md) — Spec visual del reader RSVP
> - - [`MONZA-VOICE-SPEC.md`](./MONZA-VOICE-SPEC.md) — Spec de voz, backend, colores, y deploy
>  
>   - ## Como construir
>  
>   - ```
>     Lee los archivos MONZA-READER-SPEC.md y MONZA-VOICE-SPEC.md de este repositorio.
>     Construye paso a paso un speed reader RSVP con voz clonada basandote en ambas specs.
>     ```
>
> Ver el prompt completo en la seccion 13 de MONZA-VOICE-SPEC.md.
>
> ## Setup
>
> ```bash
> # Clonar
> git clone https://github.com/edgarmonza/monza-reader.git
> cd monza-reader
>
> # Instalar
> npm install
>
> # Variables de entorno
> cp .env.example .env.local
> # Editar .env.local con tu API key y Voice ID de ElevenLabs
>
> # Dev
> npm run dev
>
> # Deploy
> vercel
> ```
>
> ## Atajos de teclado
>
> | Tecla | Accion |
> |---|---|
> | Space | Play / Pause |
> | Arrow Up/Down | +/- 50 WPM |
> | Arrow Left/Right | Saltar +/- 10 palabras |
> | Escape | Salir del reader |
> | F | Fullscreen |
> | V | Toggle voz |
> | [ / ] | Velocidad de voz |
>
> ---
>
> Built with Monza Lab aesthetics. By Edgar Navarro.
