# MONZA READER + VOICE — Especificación Técnica Completa

> Blueprint para construir Monza Reader con integración de voz clonada (ElevenLabs), backend serverless, y colores oficiales Monza Lab.
> > Para desarrollar con Claude Code + VSCode.
> >
> > ---
> >
> > ## 1. RESUMEN
> >
> > Monza Reader es un lector RSVP (Rapid Serial Visual Presentation) que muestra palabras una a una (o en grupos de 2-3) a velocidad controlable, con un punto de reconocimiento óptimo (ORP) resaltado. Incluye:
> >
> > - **Lectura visual RSVP** con ORP
> > - - **Narración con voz clonada** vía ElevenLabs TTS API
> >   - - **Backend serverless** para proteger API keys
> >     - - **Colores oficiales Monza Lab**
> >       - - **Control de velocidad** tanto visual como de audio
> >        
> >         - ---
> >
> > ## 2. STACK TECNOLÓGICO
> >
> > ### Frontend
> > | Tecnología | Uso |
> > |---|---|
> > | React 18+ | Framework UI (SPA, client-side rendering) |
> > | TypeScript | Tipado estático |
> > | Vite | Bundler y dev server |
> > | Tailwind CSS | Estilos utility-first con variables CSS HSL |
> > | Framer Motion | Animaciones (transiciones, hover, enter/exit) |
> > | Lucide React | Iconos SVG |
> > | React Router | Navegación client-side |
> > | pdf.js 3.11.174 | Extracción de texto de PDFs |
> > | localStorage | Persistencia de preferencias |
> > | Web Audio API | Control de reproducción de audio |
> >
> > ### Backend (Serverless)
> > | Tecnología | Uso |
> > |---|---|
> > | Vercel Serverless Functions | API proxy para ElevenLabs |
> > | Node.js 18+ | Runtime |
> > | ElevenLabs API v1 | Text-to-Speech con voz clonada |
> >
> > ---
> >
> > ## 3. ESTRUCTURA DE ARCHIVOS
> >
> > ```
> > monza-reader/
> > ├── api/                          # Vercel serverless functions
> > │   └── tts.ts                    # Proxy ElevenLabs TTS
> > ├── src/
> > │   ├── App.tsx                   # Router principal (home + reader)
> > │   ├── main.tsx                  # Entry point
> > │   ├── index.css                 # Variables CSS + Tailwind + estilos ORP
> > │   ├── hooks/
> > │   │   ├── useSpeedReader.ts     # Hook principal: estado, timer, controles
> > │   │   └── useVoicePlayer.ts     # Hook de audio: ElevenLabs streaming
> > │   ├── lib/
> > │   │   ├── orp.ts                # Algoritmo ORP
> > │   │   ├── wordParser.ts         # Preparación de palabras, conteo, timing
> > │   │   ├── pdfExtractor.ts       # Carga dinámica pdf.js y extracción
> > │   │   └── audioSync.ts          # Sincronización audio ↔ palabras
> > │   ├── components/
> > │   │   ├── InputPage.tsx          # Pantalla inicio (textarea + PDF drop)
> > │   │   ├── ReaderView.tsx         # Vista lectura completa
> > │   │   ├── WordDisplay.tsx        # Componente ORP (grid 3 columnas)
> > │   │   ├── ProgressBar.tsx        # Barra progreso con glow
> > │   │   ├── SpeedControls.tsx      # Botones -/play/+ y WPM display
> > │   │   ├── ChunkSelector.tsx      # Selector 1w/2w/3w
> > │   │   ├── VoiceToggle.tsx        # Toggle voz on/off
> > │   │   ├── VoiceSpeedControl.tsx  # Slider velocidad de voz
> > │   │   └── SettingsPanel.tsx      # Panel config (API key, voice ID)
> > │   └── styles/
> > │       └── theme.ts              # Colores de Monza Lab
> > ├── .env.local                    # Variables de entorno (API keys)
> > ├── vercel.json                   # Config Vercel
> > └── package.json
> > ```
> >
> > ---
> >
> > ## 4. COLORES OFICIALES MONZA LAB
> >
> > Extraídos del sitio oficial monzalab.com:
> >
> > ### Paleta Principal
> > | Nombre | Hex | HSL | Uso |
> > |---|---|---|---|
> > | Monza Pink | `#F8B4D9` | `330 88% 84%` | Color principal / acentos / CTAs |
> > | Monza Black | `#0B0B10` | `240 19% 5%` | Fondo principal |
> > | Monza Cream | `#FFFCF7` | `39 100% 98%` | Texto principal |
> > | Monza Gray Light | `#C9CCD3` | `220 10% 80%` | Texto secundario nav |
> > | Pink Hover | `#F4CBDE` | `330 65% 87%` | Hover del rosa |
> > | Card BG | `rgba(15, 14, 22, 0.9)` | — | Fondo de tarjetas |
> > | Border Subtle | `rgba(248, 180, 217, 0.1)` | — | Bordes sutiles |
> > | Border Hover | `rgba(248, 180, 217, 0.28)` | — | Bordes hover |
> > | Tag BG | `rgba(248, 180, 217, 0.08)` | — | Fondo de tags |
> > | Tag Border | `rgba(248, 180, 217, 0.2)` | — | Borde de tags |
> > | Glow Shadow | `rgba(248, 180, 217, 0.3)` | — | Sombra glow botones |
> > | Footer BG Start | `#0B0A0F` | — | Fondo footer degradado |
> > | Footer BG End | `#12101A` | — | Fondo footer degradado |
> >
> > ### Tipografías Monza
> > | Fuente | Uso |
> > |---|---|
> > | Telegraf (600-700) | Headings / Display (font-display) |
> > | Public Sans (400-600) | Body text |
> > | Inter | Monospace / UI alternativa |
> >
> > ### Variables CSS para Monza Reader
> >
> > ```css
> > :root {
> >   /* === COLORES PRINCIPALES MONZA LAB === */
> >   --background: 240 19% 5%;             /* #0B0B10 - Monza Black */
> >   --foreground: 39 100% 98%;            /* #FFFCF7 - Monza Cream */
> >
> >   --primary: 330 88% 84%;               /* #F8B4D9 - Monza Pink */
> >   --primary-foreground: 240 19% 5%;     /* Negro sobre rosa */
> >
> >   --card: 255 22% 7%;                   /* rgba(15,14,22) - Card BG */
> >   --card-foreground: 39 100% 98%;       /* Crema */
> >
> >   --border: 330 88% 84% / 0.1;         /* Rosa sutil */
> >   --muted: 260 15% 12%;                /* Gris oscuro muted */
> >   --muted-foreground: 220 10% 80%;     /* #C9CCD3 */
> >
> >   --accent: 330 65% 87%;               /* #F4CBDE - Pink Hover */
> >   --accent-foreground: 240 19% 5%;     /* Negro */
> >
> >   --destructive: 0 65% 55%;
> >   --destructive-foreground: 39 100% 98%;
> >
> >   --secondary: 260 15% 10%;
> >   --secondary-foreground: 39 80% 90%;
> >
> >   --popover: 255 22% 7%;
> >   --popover-foreground: 39 100% 98%;
> >
> >   --input: 260 15% 10%;
> >   --ring: 330 88% 84%;                  /* Monza Pink */
> >   --radius: .75rem;
> >
> >   /* === READER ESPECÍFICO === */
> >   --reader-bg: 240 19% 5%;             /* Monza Black */
> >   --reader-bg-gradient: linear-gradient(
> >     135deg,
> >     hsl(240 19% 5%) 0%,               /* #0B0B10 */
> >     hsl(255 22% 7%) 50%,              /* Card oscuro */
> >     hsl(240 19% 5%) 100%              /* #0B0B10 */
> >   );
> >   --reader-text: 39 100% 98%;          /* Monza Cream */
> >   --reader-text-glow: 0 0 60px hsla(39, 100%, 98%, .15);
> >
> >   --reader-control-bg: 255 22% 7%;     /* Card BG */
> >   --reader-control-border: 330 88% 84% / 0.15;  /* Rosa sutil */
> >
> >   --reader-progress: 330 88% 84%;      /* Monza Pink - barra progreso */
> >   --reader-progress-track: 260 15% 10%;
> >
> >   /* === ORP LETTER === */
> >   --orp-color: #F8B4D9;               /* Monza Pink para la letra ORP */
> >   --orp-glow: rgba(248, 180, 217, 0.6);
> >   --orp-glow-spread: rgba(248, 180, 217, 0.3);
> >
> >   /* === TRANSICIONES === */
> >   --transition-smooth: cubic-bezier(.4, 0, .2, 1);
> >   --transition-bounce: cubic-bezier(.34, 1.56, .64, 1);
> > }
> > ```
> >
> > ### CSS ORP actualizado con colores Monza
> >
> > ```css
> > .orp-letter {
> >   color: var(--orp-color);              /* Monza Pink en vez de rojo */
> >   font-weight: 700;
> >   text-shadow:
> >     var(--orp-glow) 0 0 15px,
> >     var(--orp-glow-spread) 0 0 30px;
> >   text-align: center;
> >   padding: 0 1px;
> > }
> >
> > .progress-glow {
> >   box-shadow: rgba(248, 180, 217, 0.4) 0 0 20px;  /* Glow rosa Monza */
> > }
> >
> > .glass-panel {
> >   background: rgba(15, 14, 22, 0.8);   /* Card BG con transparencia */
> >   backdrop-filter: blur(20px);
> >   border: 1px solid hsl(330 88% 84% / 0.15);  /* Borde rosa sutil */
> > }
> > ```
> >
> > ---
> >
> > ## 5. INTEGRACIÓN ELEVENLABS — VOZ CLONADA
> >
> > ### 5.1 Arquitectura de Audio
> >
> > ```
> > [Usuario escribe texto]
> >     → [Click "Leer con voz"]
> >     → [Frontend envía texto al backend proxy]
> >     → [Backend llama ElevenLabs API con Voice ID]
> >     → [ElevenLabs devuelve audio stream]
> >     → [Backend retorna audio al frontend]
> >     → [Frontend reproduce audio + sincroniza palabras RSVP]
> > ```
> >
> > ### 5.2 Backend Serverless — API Proxy (`api/tts.ts`)
> >
> > ```typescript
> > // api/tts.ts — Vercel Serverless Function
> > import type { VercelRequest, VercelResponse } from '@vercel/node';
> >
> > const ELEVENLABS_API_KEY = process.env.ELEVENLABS_API_KEY;
> > const DEFAULT_VOICE_ID = process.env.ELEVENLABS_VOICE_ID;
> >
> > export default async function handler(req: VercelRequest, res: VercelResponse) {
> >   // Solo POST
> >   if (req.method !== 'POST') {
> >     return res.status(405).json({ error: 'Method not allowed' });
> >   }
> >
> >   const { text, voiceId, speed = 1.0, stability = 0.5, similarityBoost = 0.75 } = req.body;
> >
> >   if (!text || text.length === 0) {
> >     return res.status(400).json({ error: 'Text is required' });
> >   }
> >
> >   // Limitar texto a 5000 caracteres por request
> >   if (text.length > 5000) {
> >     return res.status(400).json({ error: 'Text too long. Max 5000 characters.' });
> >   }
> >
> >   const selectedVoiceId = voiceId || DEFAULT_VOICE_ID;
> >
> >   try {
> >     const response = await fetch(
> >       `https://api.elevenlabs.io/v1/text-to-speech/${selectedVoiceId}/stream`,
> >       {
> >         method: 'POST',
> >         headers: {
> >           'Accept': 'audio/mpeg',
> >           'Content-Type': 'application/json',
> >           'xi-api-key': ELEVENLABS_API_KEY!,
> >         },
> >         body: JSON.stringify({
> >           text,
> >           model_id: 'eleven_multilingual_v2',
> >           voice_settings: {
> >             stability: Math.min(1, Math.max(0, stability)),
> >             similarity_boost: Math.min(1, Math.max(0, similarityBoost)),
> >             speed: Math.min(4.0, Math.max(0.25, speed)),
> >           },
> >         }),
> >       }
> >     );
> >
> >     if (!response.ok) {
> >       const errorText = await response.text();
> >       return res.status(response.status).json({
> >         error: 'ElevenLabs API error',
> >         details: errorText
> >       });
> >     }
> >
> >     // Stream de audio
> >     res.setHeader('Content-Type', 'audio/mpeg');
> >     res.setHeader('Transfer-Encoding', 'chunked');
> >     res.setHeader('Cache-Control', 'no-cache');
> >
> >     const reader = response.body?.getReader();
> >     if (!reader) {
> >       return res.status(500).json({ error: 'No response body' });
> >     }
> >
> >     while (true) {
> >       const { done, value } = await reader.read();
> >       if (done) break;
> >       res.write(value);
> >     }
> >
> >     res.end();
> >   } catch (error) {
> >     console.error('TTS error:', error);
> >     return res.status(500).json({ error: 'Internal server error' });
> >   }
> > }
> > ```
> >
> > ### 5.3 Variables de entorno (`.env.local`)
> >
> > ```env
> > # ElevenLabs
> > ELEVENLABS_API_KEY=tu_api_key_aqui
> > ELEVENLABS_VOICE_ID=tu_voice_id_de_voz_clonada
> >
> > # Nota: estos valores NUNCA se exponen al frontend.
> > # Solo el backend serverless los usa.
> > ```
> >
> > ### 5.4 Config Vercel (`vercel.json`)
> >
> > ```json
> > {
> >   "rewrites": [
> >     { "source": "/api/:path*", "destination": "/api/:path*" }
> >   ],
> >   "headers": [
> >     {
> >       "source": "/api/(.*)",
> >       "headers": [
> >         { "key": "Access-Control-Allow-Origin", "value": "*" },
> >         { "key": "Access-Control-Allow-Methods", "value": "POST, OPTIONS" },
> >         { "key": "Access-Control-Allow-Headers", "value": "Content-Type" }
> >       ]
> >     }
> >   ]
> > }
> > ```
> >
> > ---
> >
> > ## 6. HOOK DE VOZ — `useVoicePlayer.ts`
> >
> > ```typescript
> > import { useState, useRef, useCallback } from 'react';
> >
> > interface VoicePlayerState {
> >   isLoading: boolean;
> >   isPlaying: boolean;
> >   error: string | null;
> >   speed: number;
> >   voiceEnabled: boolean;
> > }
> >
> > interface VoicePlayerControls {
> >   generateAndPlay: (text: string) => Promise<void>;
> >   pause: () => void;
> >   resume: () => void;
> >   stop: () => void;
> >   setSpeed: (speed: number) => void;
> >   toggleVoice: () => void;
> > }
> >
> > export function useVoicePlayer(): [VoicePlayerState, VoicePlayerControls] {
> >   const [state, setState] = useState<VoicePlayerState>({
> >     isLoading: false,
> >     isPlaying: false,
> >     error: null,
> >     speed: 1.0,
> >     voiceEnabled: false,
> >   });
> >
> >   const audioRef = useRef<HTMLAudioElement | null>(null);
> >   const blobUrlRef = useRef<string | null>(null);
> >
> >   const cleanup = useCallback(() => {
> >     if (audioRef.current) {
> >       audioRef.current.pause();
> >       audioRef.current.src = '';
> >       audioRef.current = null;
> >     }
> >     if (blobUrlRef.current) {
> >       URL.revokeObjectURL(blobUrlRef.current);
> >       blobUrlRef.current = null;
> >     }
> >   }, []);
> >
> >   const generateAndPlay = useCallback(async (text: string) => {
> >     cleanup();
> >     setState(prev => ({ ...prev, isLoading: true, error: null }));
> >
> >     try {
> >       const response = await fetch('/api/tts', {
> >         method: 'POST',
> >         headers: { 'Content-Type': 'application/json' },
> >         body: JSON.stringify({
> >           text,
> >           speed: state.speed,
> >           stability: 0.5,
> >           similarityBoost: 0.75,
> >         }),
> >       });
> >
> >       if (!response.ok) {
> >         throw new Error('Failed to generate audio');
> >       }
> >
> >       const audioBlob = await response.blob();
> >       const audioUrl = URL.createObjectURL(audioBlob);
> >       blobUrlRef.current = audioUrl;
> >
> >       const audio = new Audio(audioUrl);
> >       audioRef.current = audio;
> >
> >       audio.onended = () => {
> >         setState(prev => ({ ...prev, isPlaying: false }));
> >       };
> >
> >       audio.onerror = () => {
> >         setState(prev => ({
> >           ...prev,
> >           isPlaying: false,
> >           error: 'Audio playback error'
> >         }));
> >       };
> >
> >       await audio.play();
> >       setState(prev => ({ ...prev, isLoading: false, isPlaying: true }));
> >     } catch (error) {
> >       setState(prev => ({
> >         ...prev,
> >         isLoading: false,
> >         error: error instanceof Error ? error.message : 'Unknown error',
> >       }));
> >     }
> >   }, [cleanup, state.speed]);
> >
> >   const pause = useCallback(() => {
> >     audioRef.current?.pause();
> >     setState(prev => ({ ...prev, isPlaying: false }));
> >   }, []);
> >
> >   const resume = useCallback(() => {
> >     audioRef.current?.play();
> >     setState(prev => ({ ...prev, isPlaying: true }));
> >   }, []);
> >
> >   const stop = useCallback(() => {
> >     cleanup();
> >     setState(prev => ({ ...prev, isPlaying: false }));
> >   }, [cleanup]);
> >
> >   const setSpeed = useCallback((speed: number) => {
> >     const clampedSpeed = Math.min(4.0, Math.max(0.25, speed));
> >     setState(prev => ({ ...prev, speed: clampedSpeed }));
> >     // Nota: el cambio de speed requiere regenerar el audio
> >     // ElevenLabs no permite cambiar speed en tiempo real
> >   }, []);
> >
> >   const toggleVoice = useCallback(() => {
> >     setState(prev => ({ ...prev, voiceEnabled: !prev.voiceEnabled }));
> >     if (state.voiceEnabled) {
> >       cleanup();
> >     }
> >   }, [state.voiceEnabled, cleanup]);
> >
> >   return [
> >     state,
> >     { generateAndPlay, pause, resume, stop, setSpeed, toggleVoice },
> >   ];
> > }
> > ```
> >
> > ---
> >
> > ## 7. SINCRONIZACIÓN AUDIO ↔ RSVP (`audioSync.ts`)
> >
> > ```typescript
> > // Estrategia de sincronización:
> > // El audio manda el ritmo. Estimamos la posición del audio basado en
> > // el tiempo transcurrido y la velocidad estimada de habla.
> >
> > interface SyncConfig {
> >   totalWords: number;
> >   audioDuration: number;     // segundos
> >   wordsPerMinute: number;    // estimado del speech
> > }
> >
> > export function estimateWordIndex(
> >   currentTime: number,       // segundos actuales del audio
> >   config: SyncConfig
> > ): number {
> >   const progress = currentTime / config.audioDuration;
> >   const estimatedIndex = Math.floor(progress * config.totalWords);
> >   return Math.min(estimatedIndex, config.totalWords - 1);
> > }
> >
> > // Modo alternativo: audio y visual independientes
> > // El usuario controla WPM visual y speed de audio por separado
> > export type SyncMode = 'audio-driven' | 'independent';
> >
> > export interface SyncState {
> >   mode: SyncMode;
> >   audioSpeed: number;        // 0.25 - 4.0
> >   visualWPM: number;         // 300 - 1200
> > }
> >
> > export const DEFAULT_SYNC_STATE: SyncState = {
> >   mode: 'independent',      // Por defecto, cada uno corre por su lado
> >   audioSpeed: 1.0,
> >   visualWPM: 400,
> > };
> > ```
> >
> > ---
> >
> > ## 8. COMPONENTES DE VOZ
> >
> > ### 8.1 VoiceToggle.tsx
> >
> > ```typescript
> > import { Volume2, VolumeX } from 'lucide-react';
> > import { motion } from 'framer-motion';
> >
> > interface VoiceToggleProps {
> >   enabled: boolean;
> >   onToggle: () => void;
> >   isLoading?: boolean;
> > }
> >
> > export function VoiceToggle({ enabled, onToggle, isLoading }: VoiceToggleProps) {
> >   return (
> >     <motion.button
> >       whileHover={{ scale: 1.05 }}
> >       whileTap={{ scale: 0.95 }}
> >       onClick={onToggle}
> >       disabled={isLoading}
> >       className={`
> >         flex items-center gap-2 px-4 py-2 rounded-full text-sm font-medium
> >         transition-all duration-200
> >         ${enabled
> >           ? 'bg-[#F8B4D9]/20 text-[#F8B4D9] border border-[#F8B4D9]/40'
> >           : 'bg-white/5 text-white/50 border border-white/10'
> >         }
> >         ${isLoading ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer'}
> >       `}
> >       aria-label={enabled ? 'Desactivar voz' : 'Activar voz'}
> >     >
> >       {enabled ? <Volume2 size={16} /> : <VolumeX size={16} />}
> >       <span>{enabled ? 'Voz ON' : 'Voz OFF'}</span>
> >     </motion.button>
> >   );
> > }
> > ```
> >
> > ### 8.2 VoiceSpeedControl.tsx
> >
> > ```typescript
> > interface VoiceSpeedControlProps {
> >   speed: number;
> >   onSpeedChange: (speed: number) => void;
> > }
> >
> > const SPEED_PRESETS = [0.5, 0.75, 1.0, 1.25, 1.5, 2.0];
> >
> > export function VoiceSpeedControl({ speed, onSpeedChange }: VoiceSpeedControlProps) {
> >   return (
> >     <div className="flex items-center gap-3">
> >       <span className="text-xs text-white/50 uppercase tracking-wider">
> >         Voz
> >       </span>
> >       <div className="flex items-center gap-1">
> >         {SPEED_PRESETS.map((preset) => (
> >           <button
> >             key={preset}
> >             onClick={() => onSpeedChange(preset)}
> >             className={`
> >               px-2 py-1 text-xs rounded-md transition-all duration-200
> >               ${Math.abs(speed - preset) < 0.01
> >                 ? 'bg-[#F8B4D9]/20 text-[#F8B4D9] font-semibold'
> >                 : 'text-white/40 hover:text-white/70'
> >               }
> >             `}
> >           >
> >             {preset}x
> >           </button>
> >         ))}
> >       </div>
> >     </div>
> >   );
> > }
> > ```
> >
> > ---
> >
> > ## 9. LAYOUT ACTUALIZADO DEL READER (CON VOZ)
> >
> > ```
> > ┌──────────────────────────────────────────────────────┐
> > │ [← Exit]  [1w][2w][3w]  [🔊 Voz ON]  [⛶]          │ ← Top bar
> > │                                                      │
> > │                                                      │
> > │                                                      │
> > │                  te[c]nica                            │ ← ORP en rosa Monza
> > │                                                      │
> > │                                                      │
> > │                                                      │
> > │ ═══════════════════════════════════════               │ ← Barra rosa Monza
> > │ 6 / 118        4%                                    │
> > │                                                      │
> > │ Voz: [0.5x] [0.75x] [1x] [1.25x] [1.5x] [2x]     │ ← Speed de voz
> > │ [◄] [−]  [ ▶ ]  [+] [►] [↺]                        │ ← Controles
> > │                    400 WPM                            │
> > └──────────────────────────────────────────────────────┘
> > ```
> >
> > ---
> >
> > ## 10. FUNCIONALIDADES COMPLETAS (ACTUALIZADO)
> >
> > | Funcionalidad | Detalle |
> > |---|---|
> > | Velocidad WPM | Rango 300-1200, default 400, incrementos de +/-50 |
> > | Modos de chunk | 1 palabra, 2 palabras, 3 palabras |
> > | Pausa inteligente | 2x en fin de oración (.!?), 1.5x en puntuación (,;:-) |
> > | ORP | Letra **rosa Monza (#F8B4D9)** con text-shadow glow |
> > | Pantalla completa | API Fullscreen nativa del navegador |
> > | Barra de progreso | Rosa Monza con glow |
> > | Atajos de teclado | Space, Arrows, Escape, F, V (toggle voz) |
> > | PDF support | pdf.js carga dinámica |
> > | Persistencia | WPM, chunkSize, voiceEnabled, voiceSpeed en localStorage |
> > | Pausa por visibilidad | Se pausa al cambiar de pestaña |
> > | Animaciones | Framer Motion |
> > | Responsive | 3xl a 8xl según viewport |
> > | **Voz clonada** | **ElevenLabs TTS con tu voz** |
> > | **Velocidad de voz** | **0.25x a 4.0x (presets: 0.5, 0.75, 1.0, 1.25, 1.5, 2.0)** |
> > | **Backend seguro** | **Vercel serverless proxy para API keys** |
> > | **Modos de sync** | **Audio-driven o independiente** |
> >
> > ---
> >
> > ## 11. ATAJOS DE TECLADO (ACTUALIZADO)
> >
> > | Tecla | Acción |
> > |---|---|
> > | Space | Play / Pause |
> > | ArrowUp | Aumentar velocidad +50 WPM |
> > | ArrowDown | Disminuir velocidad -50 WPM |
> > | ArrowLeft | Retroceder 10 palabras |
> > | ArrowRight | Avanzar 10 palabras |
> > | Escape | Salir del lector (volver a home) |
> > | F | Toggle pantalla completa |
> > | **V** | **Toggle voz on/off** |
> > | **[ (bracket izq)** | **Reducir velocidad de voz** |
> > | **] (bracket der)** | **Aumentar velocidad de voz** |
> >
> > ---
> >
> > ## 12. PERSISTENCIA (ACTUALIZADO)
> >
> > ```typescript
> > const STORAGE_KEY = 'monza-reader-settings';
> >
> > interface Settings {
> >   wpm: number;
> >   chunkSize: number;
> >   voiceEnabled: boolean;
> >   voiceSpeed: number;
> >   syncMode: 'audio-driven' | 'independent';
> > }
> >
> > function loadSettings(): Settings {
> >   try {
> >     const stored = localStorage.getItem(STORAGE_KEY);
> >     if (stored) {
> >       const parsed = JSON.parse(stored);
> >       return {
> >         wpm: Math.min(1200, Math.max(300, parsed.wpm || 400)),
> >         chunkSize: [1, 2, 3].includes(parsed.chunkSize) ? parsed.chunkSize : 1,
> >         voiceEnabled: parsed.voiceEnabled ?? false,
> >         voiceSpeed: Math.min(4.0, Math.max(0.25, parsed.voiceSpeed || 1.0)),
> >         syncMode: parsed.syncMode || 'independent',
> >       };
> >     }
> >   } catch (e) {
> >     console.error('Failed to load settings:', e);
> >   }
> >   return { wpm: 400, chunkSize: 1, voiceEnabled: false, voiceSpeed: 1.0, syncMode: 'independent' };
> > }
> > ```
> >
> > ---
> >
> > ## 13. PROMPT PARA CLAUDE CODE
> >
> > ```
> > Lee los archivos MONZA-READER-SPEC.md y MONZA-VOICE-SPEC.md de este repositorio.
> >
> > Quiero que construyas paso a paso un speed reader RSVP con voz clonada, basándote en ambas especificaciones.
> >
> > Empieza creando el proyecto con:
> > - Vite + React + TypeScript
> > - Tailwind CSS
> > - Framer Motion
> > - Lucide React
> > - React Router
> >
> > Incluye:
> > 1. El reader RSVP visual completo
> > 2. La integración de voz con ElevenLabs
> > 3. El backend serverless (Vercel functions) como proxy
> > 4. Los colores oficiales de Monza Lab
> >
> > Mis colores de marca son:
> > - Principal: #F8B4D9 (Monza Pink)
> > - Fondo: #0B0B10 (Monza Black)
> > - Texto: #FFFCF7 (Monza Cream)
> > - Hover: #F4CBDE
> > - Fuente display: Telegraf
> > - Fuente body: Public Sans
> >
> > Mi fuente display es: Telegraf
> > Mi fuente body es: Public Sans
> >
> > El nombre de la app es: Monza Reader
> >
> > Para la voz:
> > - El Voice ID y API key se configuran en .env.local
> > - El backend proxy va en api/tts.ts
> > - El modelo de voz es: eleven_multilingual_v2
> > ```
> >
> > ---
> >
> > ## 14. CHECKLIST DE DESARROLLO
> >
> > ### Fase 1: Reader Visual
> > - [ ] Proyecto Vite + React + TS creado
> > - [ ] - [ ] Tailwind CSS configurado con colores Monza Lab
> > - [ ] - [ ] Fuentes Telegraf + Public Sans cargadas
> > - [ ] - [ ] Algoritmo ORP implementado (letra rosa Monza)
> > - [ ] - [ ] Word parser con detección de puntuación
> > - [ ] - [ ] Hook useSpeedReader con requestAnimationFrame
> > - [ ] - [ ] Componente InputPage (textarea + word count)
> > - [ ] - [ ] Extractor PDF con pdf.js dinámico
> > - [ ] - [ ] Zona drag & drop para PDF
> > - [ ] - [ ] Componente WordDisplay con grid ORP
> > - [ ] - [ ] ReaderView con layout completo
> > - [ ] - [ ] Controles de velocidad (+/- 50 WPM)
> > - [ ] - [ ] Selector de chunks (1w/2w/3w)
> > - [ ] - [ ] Barra de progreso con glow rosa
> > - [ ] - [ ] Atajos de teclado completos
> > - [ ] - [ ] Fullscreen API
> > - [ ] - [ ] localStorage para persistir settings
> > - [ ] - [ ] Pausa en visibilitychange
> > - [ ] - [ ] Animaciones Framer Motion
> > - [ ] - [ ] Responsive (mobile a desktop)
> > - [ ] - [ ] Colores Monza Lab aplicados
> >
> > - [ ] ### Fase 2: Voz Clonada
> > - [ ] - [ ] Backend serverless api/tts.ts
> > - [ ] - [ ] Variables de entorno configuradas
> > - [ ] - [ ] Hook useVoicePlayer
> > - [ ] - [ ] VoiceToggle component
> > - [ ] - [ ] VoiceSpeedControl component
> > - [ ] - [ ] Integración voz en ReaderView
> > - [ ] - [ ] Modo sincronización audio-driven
> > - [ ] - [ ] Modo sincronización independiente
> > - [ ] - [ ] Atajos V, [, ] funcionando
> > - [ ] - [ ] Persistencia de settings de voz
> > - [ ] - [ ] Manejo de errores de API
> > - [ ] - [ ] Loading state mientras genera audio
> >
> > - [ ] ### Fase 3: Deploy
> > - [ ] - [ ] vercel.json configurado
> > - [ ] - [ ] Variables de entorno en Vercel dashboard
> > - [ ] - [ ] Deploy a Vercel
> > - [ ] - [ ] Dominio custom (opcional)
> > - [ ] - [ ] Testing completo
> >
> > - [ ] ---
> >
> > - [ ] ## 15. NOTAS DE COSTOS ELEVENLABS
> >
> > - [ ] | Plan | Caracteres/mes | Precio |
> > - [ ] |---|---|---|
> > - [ ] | Free | 10,000 | $0 |
> > - [ ] | Starter | 30,000 | $5/mes |
> > - [ ] | Creator | 100,000 | $22/mes |
> > - [ ] | Pro | 500,000 | $99/mes |
> >
> > - [ ] Estimación: un libro de 50,000 palabras son aproximadamente 300,000 caracteres. El plan Creator cubre una lectura completa de un libro por mes.
> >
> > - [ ] ---
> >
> > - [ ] ## 16. SEGURIDAD
> >
> > - [ ] - La API key de ElevenLabs **NUNCA** se expone al frontend
> > - [ ] - El backend serverless actua como proxy
> > - [ ] - Rate limiting recomendado: 10 requests/minuto por IP
> > - [ ] - Texto limitado a 5,000 caracteres por request
> > - [ ] - Variables de entorno solo en Vercel dashboard y .env.local (gitignored)
