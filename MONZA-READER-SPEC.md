# MONZA READER — Especificación Técnica Completa

> Blueprint para replicar un Speed Reader RSVP con tus colores y marca.
> > Basado en el análisis de FlowReader (leer.azteclab.co).
> > > Para desarrollar con Claude Code + VSCode.
> > >
> > > ---
> > >
> > > ## 1. RESUMEN
> > >
> > > Monza Reader es un **lector RSVP (Rapid Serial Visual Presentation)** que muestra palabras una a una (o en grupos de 2-3) a velocidad controlable, con un punto de reconocimiento óptimo (ORP) resaltado. No tiene backend — todo corre en el navegador.
> > >
> > > ---
> > >
> > > ## 2. STACK TECNOLÓGICO
> > >
> > > | Tecnología | Uso |
> > > |---|---|
> > > | **React 18+** | Framework UI (SPA, client-side rendering) |
> > > | **TypeScript** | Tipado estático |
> > > | **Vite** | Bundler y dev server |
> > > | **Tailwind CSS** | Estilos utility-first con variables CSS HSL |
> > > | **Framer Motion** | Animaciones (transiciones, hover, enter/exit) |
> > > | **Lucide React** | Iconos SVG |
> > > | **React Router** | Navegación client-side |
> > > | **pdf.js 3.11.174** | Extracción de texto de PDFs (carga dinámica desde CDN) |
> > > | **localStorage** | Persistencia de preferencias (WPM, chunkSize) |
> > >
> > > ---
> > >
> > > ## 3. ESTRUCTURA DE ARCHIVOS
> > >
> > > ```
> > > src/
> > > ├── App.tsx                     # Router principal (2 rutas: home + reader)
> > > ├── main.tsx                    # Entry point
> > > ├── index.css                   # Variables CSS + Tailwind + estilos ORP
> > > ├── hooks/
> > > │   └── useSpeedReader.ts       # Hook principal: estado, timer, controles
> > > ├── lib/
> > > │   ├── orp.ts                  # Algoritmo ORP
> > > │   ├── wordParser.ts           # Preparación de palabras, conteo, timing
> > > │   └── pdfExtractor.ts         # Carga dinámica pdf.js y extracción
> > > ├── components/
> > > │   ├── InputPage.tsx            # Pantalla inicio (textarea + PDF drop)
> > > │   ├── ReaderView.tsx           # Vista lectura completa
> > > │   ├── WordDisplay.tsx          # Componente ORP (grid 3 columnas)
> > > │   ├── ProgressBar.tsx          # Barra progreso con glow
> > > │   ├── SpeedControls.tsx        # Botones -/play/+ y WPM display
> > > │   └── ChunkSelector.tsx        # Selector 1w/2w/3w
> > > └── styles/
> > >     └── theme.ts                 # Colores de Monza
> > > ```
> > >
> > > ---
> > >
> > > ## 4. ALGORITMOS CLAVE
> > >
> > > ### 4.1 Cálculo del ORP (Optimal Recognition Point)
> > >
> > > ```typescript
> > > function calcORPPosition(word: string): number {
> > >   const letterCount = word.replace(/[^a-zA-Z]/g, '').length;
> > >   if (letterCount <= 1) return 0;
> > >   if (letterCount <= 3) return 0;
> > >   if (letterCount <= 5) return 1;
> > >   if (letterCount <= 9) return Math.floor(letterCount * 0.35);
> > >   return Math.floor(letterCount * 0.4);
> > > }
> > >
> > > function findORPIndex(word: string): number {
> > >   const targetPos = calcORPPosition(word);
> > >   let letterCount = 0;
> > >   for (let i = 0; i < word.length; i++) {
> > >     if (/[a-zA-Z]/.test(word[i])) {
> > >       if (letterCount === targetPos) return i;
> > >       letterCount++;
> > >     }
> > >   }
> > >   return 0;
> > > }
> > > ```
> > >
> > > ### 4.2 Temporización inteligente de palabras
> > >
> > > ```typescript
> > > function getWordDelay(wpm: number, wordObj: WordObject): number {
> > >   const baseDelay = 60000 / wpm; // milisegundos por palabra
> > >   if (wordObj.isEndOfSentence) return baseDelay * 2;   // .!? → pausa doble
> > >   if (wordObj.isPunctuation) return baseDelay * 1.5;    // ,;:- → pausa 1.5x
> > >   return baseDelay;
> > > }
> > > ```
> > >
> > > ### 4.3 Preparación de palabras (word parser)
> > >
> > > ```typescript
> > > interface WordObject {
> > >   text: string;
> > >   isEndOfSentence: boolean;
> > >   isPunctuation: boolean;
> > >   index: number;
> > >   orpIndex: number;
> > > }
> > >
> > > function prepareWords(text: string, chunkSize: number = 1): WordObject[] {
> > >   const cleaned = text.replace(/\s+/g, ' ').replace(/[\r\n]+/g, ' ').trim();
> > >   if (!cleaned) return [];
> > >   const rawWords = cleaned.split(' ').filter(w => w.length > 0);
> > >   const result: WordObject[] = [];
> > >
> > >   if (chunkSize === 1) {
> > >     rawWords.forEach((word, index) => {
> > >       const lastChar = word.slice(-1);
> > >       result.push({
> > >         text: word,
> > >         isEndOfSentence: /[.!?]/.test(lastChar),
> > >         isPunctuation: /[,;:\-]/.test(lastChar),
> > >         index,
> > >         orpIndex: findORPIndex(word)
> > >       });
> > >     });
> > >   } else {
> > >     for (let i = 0; i < rawWords.length; i += chunkSize) {
> > >       const chunk = rawWords.slice(i, i + chunkSize).join(' ');
> > >       const lastChar = chunk.slice(-1);
> > >       const firstWord = rawWords[i];
> > >       result.push({
> > >         text: chunk,
> > >         isEndOfSentence: /[.!?]/.test(lastChar),
> > >         isPunctuation: /[,;:\-]/.test(lastChar),
> > >         index: result.length,
> > >         orpIndex: findORPIndex(firstWord)
> > >       });
> > >     }
> > >   }
> > >   return result;
> > > }
> > >
> > > function countWords(text: string): number {
> > >   return text.trim().split(/\s+/).filter(w => w.length > 0).length;
> > > }
> > > ```
> > >
> > > ### 4.4 Loop de animación (requestAnimationFrame)
> > >
> > > ```typescript
> > > // Dentro del hook useSpeedReader
> > > // Usa requestAnimationFrame en lugar de setInterval para más fluidez
> > > // Acumula tiempo delta entre frames y avanza cuando supera el delay
> > >
> > > const animationRef = useRef<number | null>(null);
> > > const lastTimeRef = useRef(0);
> > > const accumulatorRef = useRef(0);
> > >
> > > const animate = useCallback((timestamp: number) => {
> > >   if (!lastTimeRef.current) lastTimeRef.current = timestamp;
> > >   const delta = timestamp - lastTimeRef.current;
> > >   lastTimeRef.current = timestamp;
> > >
> > >   setState(prev => {
> > >     if (!prev.isPlaying || prev.currentIndex >= prev.words.length) return prev;
> > >     const currentWord = prev.words[prev.currentIndex];
> > >     const delay = getWordDelay(wpm, currentWord);
> > >
> > >     accumulatorRef.current += delta;
> > >     if (accumulatorRef.current >= delay) {
> > >       accumulatorRef.current = 0;
> > >       const nextIndex = prev.currentIndex + 1;
> > >       if (nextIndex >= prev.words.length) {
> > >         return { ...prev, isPlaying: false, progress: 100 };
> > >       }
> > >       return {
> > >         ...prev,
> > >         currentIndex: nextIndex,
> > >         currentWord: prev.words[nextIndex],
> > >         progress: (nextIndex / prev.words.length) * 100
> > >       };
> > >     }
> > >     return prev;
> > >   });
> > >
> > >   animationRef.current = requestAnimationFrame(animate);
> > > }, [wpm]);
> > > ```
> > >
> > > ---
> > >
> > > ## 5. EXTRACCIÓN DE PDF
> > >
> > > ```typescript
> > > let pdfLoaded = false;
> > > let pdfPromise: Promise<void> | null = null;
> > >
> > > async function loadPdfJs(): Promise<void> {
> > >   if (pdfLoaded) return;
> > >   if (pdfPromise) return pdfPromise;
> > >
> > >   pdfPromise = new Promise((resolve, reject) => {
> > >     if (window.pdfjsLib) { pdfLoaded = true; resolve(); return; }
> > >     const script = document.createElement('script');
> > >     script.src = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js';
> > >     script.async = true;
> > >     script.onload = () => {
> > >       window.pdfjsLib.GlobalWorkerOptions.workerSrc =
> > >         'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
> > >       pdfLoaded = true;
> > >       resolve();
> > >     };
> > >     script.onerror = () => reject(new Error('Failed to load PDF.js'));
> > >     document.head.appendChild(script);
> > >   });
> > >   return pdfPromise;
> > > }
> > >
> > > async function extractTextFromPdf(file: File) {
> > >   try {
> > >     await loadPdfJs();
> > >     const buffer = await file.arrayBuffer();
> > >     const pdf = await window.pdfjsLib.getDocument({ data: buffer }).promise;
> > >     const pages: string[] = [];
> > >     for (let i = 1; i <= pdf.numPages; i++) {
> > >       const page = await pdf.getPage(i);
> > >       const content = await page.getTextContent();
> > >       pages.push(content.items.map((item: any) => item.str).join(' '));
> > >     }
> > >     const text = pages.join('\n').trim();
> > >     if (!text) return { success: false, error: 'No readable text found' };
> > >     return { success: true, text, pageCount: pdf.numPages };
> > >   } catch (err) {
> > >     return { success: false, error: 'Failed to parse PDF file' };
> > >   }
> > > }
> > >
> > > function isValidPdf(file: File): boolean {
> > >   return file.type === 'application/pdf' || file.name.toLowerCase().endsWith('.pdf');
> > > }
> > > ```
> > >
> > > ---
> > >
> > > ## 6. FUNCIONALIDADES COMPLETAS
> > >
> > > | Funcionalidad | Detalle |
> > > |---|---|
> > > | Velocidad WPM | Rango 300-1200, default 400, incrementos de +/-50 |
> > > | Modos de chunk | 1 palabra, 2 palabras, 3 palabras |
> > > | Pausa inteligente | 2x en fin de oración (.!?), 1.5x en puntuación (,;:-) |
> > > | ORP | Letra roja con text-shadow glow en posición óptima |
> > > | Pantalla completa | API Fullscreen nativa del navegador |
> > > | Barra de progreso | Porcentaje + conteo "X / Y" palabras |
> > > | Atajos de teclado | Ver sección 7 |
> > > | PDF support | pdf.js carga dinámica, extrae texto de todas las páginas |
> > > | Persistencia | WPM y chunkSize guardados en localStorage |
> > > | Pausa por visibilidad | Se pausa al cambiar de pestaña (visibilitychange API) |
> > > | Animaciones | Framer Motion para transiciones, keyframe wordIn |
> > > | Responsive | Texto escala de 3xl a 8xl según viewport |
> > > | Estado "Ready" | Muestra "Ready" antes de empezar, clic para iniciar |
> > > | Contador de palabras | Muestra total de palabras y tiempo estimado de lectura |
> > >
> > > ---
> > >
> > > ## 7. ATAJOS DE TECLADO
> > >
> > > | Tecla | Acción |
> > > |---|---|
> > > | `Space` | Play / Pause |
> > > | `ArrowUp` | Aumentar velocidad +50 WPM |
> > > | `ArrowDown` | Disminuir velocidad -50 WPM |
> > > | `ArrowLeft` | Retroceder 10 palabras |
> > > | `ArrowRight` | Avanzar 10 palabras |
> > > | `Escape` | Salir del lector (volver a home) |
> > > | `F` | Toggle pantalla completa |
> > >
> > > ---
> > >
> > > ## 8. ICONOS (Lucide React)
> > >
> > > ```
> > > ArrowLeft       → Botón Exit (volver)
> > > Maximize        → Entrar fullscreen
> > > Minimize        → Salir fullscreen
> > > Play            → Reproducir
> > > Pause           → Pausar (icono con 2 barras)
> > > Minus           → Decrementar velocidad
> > > Plus            → Incrementar velocidad
> > > ChevronLeft     → Saltar -10 palabras
> > > ChevronRight    → Saltar +10 palabras
> > > RotateCcw       → Restart (reiniciar lectura)
> > > Upload          → Icono zona drag & drop PDF
> > > BookOpen        → Icono botón "Start Reading"
> > > Loader          → Spinner mientras extrae PDF
> > > FileText        → PDF cargado exitosamente
> > > AlertCircle     → Error en carga de PDF
> > > ```
> > >
> > > ---
> > >
> > > ## 9. SISTEMA DE DISEÑO (Variables CSS)
> > >
> > > ### Variables base (PERSONALIZAR CON COLORES MONZA):
> > >
> > > ```css
> > > :root {
> > >   /* === COLORES PRINCIPALES === */
> > >   /* CAMBIAR ESTOS POR TUS COLORES DE MONZA */
> > >   --background: 250 15% 8%;
> > >   --foreground: 45 20% 92%;
> > >   --primary: 270 40% 55%;              /* << TU COLOR PRINCIPAL */
> > >   --primary-foreground: 45 20% 98%;
> > >   --card: 250 12% 12%;
> > >   --card-foreground: 45 20% 92%;
> > >   --border: 250 12% 18%;
> > >   --muted: 250 10% 20%;
> > >   --muted-foreground: 250 8% 55%;
> > >   --accent: 270 35% 45%;               /* << TU COLOR ACENTO */
> > >   --accent-foreground: 45 20% 98%;
> > >   --destructive: 0 65% 55%;
> > >   --destructive-foreground: 45 20% 98%;
> > >   --secondary: 250 10% 18%;
> > >   --secondary-foreground: 45 15% 85%;
> > >   --popover: 250 12% 12%;
> > >   --popover-foreground: 45 20% 92%;
> > >   --input: 250 12% 15%;
> > >   --ring: 270 40% 55%;
> > >   --radius: .75rem;
> > >
> > >   /* === READER ESPECÍFICO === */
> > >   --reader-bg: 250 20% 6%;
> > >   --reader-bg-gradient: linear-gradient(135deg,
> > >     hsl(250 20% 6%) 0%,
> > >     hsl(270 15% 8%) 50%,
> > >     hsl(250 18% 7%) 100%);
> > >   --reader-text: 45 25% 94%;
> > >   --reader-text-glow: 0 0 60px hsla(45, 25%, 94%, .15);
> > >   --reader-control-bg: 250 15% 12%;
> > >   --reader-control-border: 250 12% 20%;
> > >   --reader-progress: 270 45% 50%;      /* << COLOR BARRA PROGRESO */
> > >   --reader-progress-track: 250 12% 15%;
> > >
> > >   /* === TRANSICIONES === */
> > >   --transition-smooth: cubic-bezier(.4, 0, .2, 1);
> > >   --transition-bounce: cubic-bezier(.34, 1.56, .64, 1);
> > > }
> > > ```
> > >
> > > ### Estilos CSS personalizados obligatorios:
> > >
> > > ```css
> > > /* ORP Word Display - Grid de 3 columnas */
> > > .orp-word-container {
> > >   display: grid;
> > >   grid-template-columns: 1fr auto 1fr;
> > >   align-items: center;
> > >   width: 100%;
> > >   max-width: 100%;
> > >   overflow: hidden;
> > >   font-feature-settings: "kern" 0;
> > >   letter-spacing: 0px;
> > > }
> > >
> > > .orp-before, .orp-after {
> > >   overflow: hidden;
> > >   text-overflow: clip;
> > > }
> > >
> > > .orp-before {
> > >   text-align: right;
> > >   color: hsl(var(--reader-text));
> > > }
> > >
> > > .orp-letter {
> > >   color: red;
> > >   font-weight: 700;
> > >   text-shadow: rgba(255, 0, 0, 0.6) 0 0 15px,
> > >                rgba(255, 0, 0, 0.3) 0 0 30px;
> > >   text-align: center;
> > >   padding: 0 1px;
> > > }
> > >
> > > .orp-after {
> > >   text-align: left;
> > >   color: hsl(var(--reader-text));
> > > }
> > >
> > > .reader-word {
> > >   font-feature-settings: "kern" 0;
> > >   letter-spacing: 0px;
> > >   white-space: nowrap;
> > > }
> > >
> > > /* Glass Panel (controles) */
> > > .glass-panel {
> > >   background: rgba(28, 26, 35, 0.8);
> > >   backdrop-filter: blur(20px);
> > >   border: 1px solid hsl(var(--reader-control-border));
> > > }
> > >
> > > /* Progress Bar Glow */
> > > .progress-glow {
> > >   box-shadow: rgba(127, 70, 185, 0.4) 0 0 20px;
> > > }
> > >
> > > .bg-reader-progress {
> > >   background-color: hsl(var(--reader-progress));
> > > }
> > >
> > > .bg-reader-track {
> > >   background-color: hsl(var(--reader-progress-track));
> > > }
> > >
> > > /* Animación entrada de palabra */
> > > @keyframes wordIn {
> > >   0% { opacity: 0; transform: translateY(4px) scale(0.98); }
> > >   100% { opacity: 1; transform: translateY(0) scale(1); }
> > > }
> > > ```
> > >
> > > ---
> > >
> > > ## 10. COMPONENTE WORD DISPLAY (ORP)
> > >
> > > ```tsx
> > > interface WordDisplayProps {
> > >   word: WordObject | null;
> > > }
> > >
> > > function WordDisplay({ word }: WordDisplayProps) {
> > >   if (!word) return null;
> > >   const { text, orpIndex } = word;
> > >   const before = text.slice(0, orpIndex);
> > >   const letter = text[orpIndex];
> > >   const after = text.slice(orpIndex + 1);
> > >
> > >   return (
> > >     <div className="orp-word-container">
> > >       <span className="orp-before">{before}</span>
> > >       <span className="orp-letter">{letter}</span>
> > >       <span className="orp-after">{after}</span>
> > >     </div>
> > >   );
> > > }
> > > ```
> > >
> > > ---
> > >
> > > ## 11. PERSISTENCIA (localStorage)
> > >
> > > ```typescript
> > > const STORAGE_KEY = 'monza-reader-settings';
> > >
> > > interface Settings {
> > >   wpm: number;
> > >   chunkSize: number;
> > > }
> > >
> > > function loadSettings(): Settings {
> > >   try {
> > >     const stored = localStorage.getItem(STORAGE_KEY);
> > >     if (stored) {
> > >       const parsed = JSON.parse(stored);
> > >       return {
> > >         wpm: Math.min(1200, Math.max(300, parsed.wpm || 400)),
> > >         chunkSize: [1, 2, 3].includes(parsed.chunkSize) ? parsed.chunkSize : 1
> > >       };
> > >     }
> > >   } catch (e) {
> > >     console.error('Failed to load settings:', e);
> > >   }
> > >   return { wpm: 400, chunkSize: 1 };
> > > }
> > >
> > > function saveSettings(settings: Settings): void {
> > >   try {
> > >     localStorage.setItem(STORAGE_KEY, JSON.stringify(settings));
> > >   } catch (e) {
> > >     console.error('Failed to save settings:', e);
> > >   }
> > > }
> > > ```
> > >
> > > ---
> > >
> > > ## 12. LAYOUT DE CONTROLES DEL READER
> > >
> > > ```
> > > ┌─────────────────────────────────────────────────┐
> > > │ [← Exit]                    [1w][2w][3w]  [⛶]  │  ← Top bar (glass-panel)
> > > │                                                   │
> > > │                                                   │
> > > │                                                   │
> > > │              te[c]nica                            │  ← Palabra centrada, ORP en rojo
> > > │                                                   │
> > > │                                                   │
> > > │                                                   │
> > > │  ════════════════════════════════                  │  ← Barra progreso (glow)
> > > │  6 / 118                                    4%    │
> > > │      [◄] [−]  [ ▶ ]  [+] [►]  [↺]              │  ← Controles
> > > │              400 WPM                              │
> > > └─────────────────────────────────────────────────┘
> > > ```
> > >
> > > Controles bottom bar:
> > > - `◄` ChevronLeft: saltar -10 palabras (oculto en mobile)
> > > - - `−` Minus: -50 WPM
> > >   - - `▶` Play/Pause: botón circular grande, bg-primary
> > >     - - `+` Plus: +50 WPM
> > >       - - `►` ChevronRight: saltar +10 palabras (oculto en mobile)
> > >         - - `↺` RotateCcw: restart (oculto en mobile)
> > >          
> > >           - ---
> > >
> > > ## 13. TAMAÑOS RESPONSIVOS DEL TEXTO
> > >
> > > ```
> > > text-3xl   → mobile pequeño
> > > sm:text-5xl → mobile
> > > md:text-6xl → tablet
> > > lg:text-7xl → desktop
> > > xl:text-8xl → desktop grande
> > > ```
> > >
> > > ---
> > >
> > > ## 14. PROMPT PARA CLAUDE CODE
> > >
> > > Copia y pega este prompt en Claude Code para empezar:
> > >
> > > ```
> > > Lee el archivo MONZA-READER-SPEC.md de este repositorio.
> > > Quiero que construyas paso a paso un speed reader RSVP
> > > basándote en esa especificación completa.
> > >
> > > Empieza creando el proyecto con:
> > > - Vite + React + TypeScript
> > > - Tailwind CSS
> > > - Framer Motion
> > > - Lucide React
> > > - React Router
> > >
> > > Sigue la estructura de archivos del spec.
> > > Implementa primero los algoritmos core (ORP, word parser, timing),
> > > luego los componentes UI, y al final las animaciones.
> > >
> > > Mis colores de marca son: [PONER TUS COLORES AQUÍ]
> > > Mi fuente es: Inter (o la que prefieras)
> > > El nombre de la app es: Monza Reader
> > > ```
> > >
> > > ---
> > >
> > > ## 15. CHECKLIST DE DESARROLLO
> > >
> > > - [ ] Proyecto Vite + React + TS creado
> > > - [ ] - [ ] Tailwind CSS configurado con variables CSS
> > > - [ ] - [ ] Algoritmo ORP implementado y testeado
> > > - [ ] - [ ] Word parser con detección de puntuación
> > > - [ ] - [ ] Hook useSpeedReader con requestAnimationFrame
> > > - [ ] - [ ] Componente InputPage (textarea + word count)
> > > - [ ] - [ ] Extractor PDF con pdf.js dinámico
> > > - [ ] - [ ] Zona drag & drop para PDF
> > > - [ ] - [ ] Componente WordDisplay con grid ORP
> > > - [ ] - [ ] ReaderView con layout completo
> > > - [ ] - [ ] Controles de velocidad (+/- 50 WPM)
> > > - [ ] - [ ] Selector de chunks (1w/2w/3w)
> > > - [ ] - [ ] Barra de progreso con glow
> > > - [ ] - [ ] Atajos de teclado completos
> > > - [ ] - [ ] Fullscreen API
> > > - [ ] - [ ] localStorage para persistir settings
> > > - [ ] - [ ] Pausa en visibilitychange
> > > - [ ] - [ ] Animaciones Framer Motion
> > > - [ ] - [ ] Responsive (mobile → desktop)
> > > - [ ] - [ ] Colores personalizados de Monza aplicados
> > > - [ ] - [ ] Deploy (Vercel/Netlify)
