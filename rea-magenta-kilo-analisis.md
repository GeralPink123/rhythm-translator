# REA de Magenta — hecho por Kilo Code + Nemotron 3 Ultra (23-nov-2026)

**Fuente:** `analisis-magenta.md` (345 líneas), guardado por Kilo en la Mac de Geral en `~/Downloads/analisis-magenta.md` el 23-nov-2026. Modelo: Nemotron 3 Ultra 550B A55B con la llave NIM de NVIDIA. Leído por fotos enviadas por Geral (11 fotos, líneas 1–345 completas).

**Veredicto:** el análisis es REAL y coincide con la verificación independiente hecha con el navegador el 23-nov (Melody Mixer, Beat Blender, Latent Loops; Interpolate exige Ableton; repo archivado 6-ene-2026; sin géneros latinos). **Límite honesto:** Kilo lo armó leyendo las páginas y la documentación, no haciendo clic en las demos en vivo con capturas — el archivo es solo texto, sin capturas. La parte útil para el Traductor (arquitectura, plantilla JSON, stack, roadmap) es real y usable.

## 1. La demo web de MusicVAE

La página es un artículo técnico largo con ejemplos de audio incrustados. **No hay una sola interfaz interactiva** — hay tres demos enlazadas que corren en el navegador:

| Demo | Qué hace | Enlace directo |
|---|---|---|
| **Melody Mixer** | Mezcla dos melodías de 2 compases y genera transiciones suaves | https://g.co/melodymixer |
| **Beat Blender** | Crea una "paleta" 2D de ritmos de batería; arrastras un camino y va morphing | https://experiments.withgoogle.com/ai/beat-blender/view/ |
| **Latent Loops** | Dibujas melodías en una matriz, exploras loops generados, armas secciones | https://goo.gl/magenta/latent-loops |

El artículo explica la teoría (espacios latentes, VAE, decoder jerárquico) pero **no hay un solo botón "Interpolate" central**. Interpolate es el concepto; las herramientas de arriba son sus encarnaciones web.

## 2. Melody Mixer (la más cercana a "Interpolate")

**Pantalla inicial:** cuadrícula de 16 pasos × 8 notas (piano roll simplificado). Dos patrones A y B uno al lado del otro. Slider horizontal grande en el centro: **Interpolate** (0 = solo A, 1 = solo B). Botón **Generate** / **Play**. Botón **Randomize** en cada patrón.

**Botón por botón:**
- **Play (▶):** reproduce el patrón actual (el que marca el slider). Loop de 2 compases en piano sintético.
- **Slider Interpolate:** arrastra — las notas en la cuadrícula cambian suavemente: notas de A se funden en notas de B. Morphing melódico: la melodía "camina" de A a B sin saltos bruscos.
- **Randomize (dado) en A:** patrón A se reemplaza por uno nuevo aleatorio (pero musical). Nueva melodía base.
- **Randomize en B:** igual para patrón B. Nueva melodía destino.
- **Generate:** recalcula la interpolación completa (vuelve a llenar los frames intermedios). Transición recalculada.
- **Tempo (BPM):** cambia velocidad de reproducción. Más rápido/lento, misma estructura.
- **Download / Export:** baja el MIDI del frame actual. Archivo .mid para tu DAW.

**Qué hace bien:** mezcla patrones concretos que tú pones (o generas aleatoriamente). El slider es control directo: ves y oyes cada punto intermedio. Los patrones suenan musicales (no ruido aleatorio). Corre 100% en el navegador (TensorFlow.js), sin instalar nada.

**Qué NO hace:**
- No hay botón "Haz esto sonar como Reggaetón / Dembow / Salsa".
- No te deja elegir un género destino con nombre.
- No separa "qué suena" (notas, ritmo) de "cómo suena" (timbre, swing, feel).
- No explica por qué cambió tal nota: la red neuronal es caja negra.

## 3. Beat Blender (ritmos — paleta 2D)

**Pantalla:** cuadrícula 4×4 = 16 "esquinas", cada una es un patrón de batería de 1 compás. 4 esquinas editables (A, B, C, D) — las demás se rellenan interpolando. Arrastras un punto rojo por la cuadrícula → suena el patrón correspondiente. Botones: **Randomize corners**, **Regenerate grid**, **Play/Stop**, **Tempo**, **Download MIDI**.

**Qué pasa al mover el punto:** esquinas cercanas → patrones similares. Centro → mezcla de las 4 esquinas. Movimiento suave → morphing rítmico continuo.

**Límites iguales que Melody Mixer:** no hay géneros latinos (dembow, salsa, plena, bomba, bachata, merengue, cumbia). No dices "quiero un dembow". Solo exploras el espacio latente a ciegas.

## 4. Latent Loops (melodías + secuenciador)

**Pantalla:** matriz de notas (piano roll) para dibujar tu semilla. Panel lateral: paleta de loops generados (cada uno = 2 compases). Arrastrar loops a una timeline abajo → armas secciones de 16 compases. Botones: **Generate palette**, **Clear**, **Play**, **Export MIDI**.

**Flujo típico:** 1. Dibujas 2 compases → Generate palette → aparecen 16 loops variados. 2. Escuchas cada uno (click) → arrastras los que te gustan a la timeline. 3. Play reproduce la canción armada. 4. Export → MIDI multi-pista.

**Qué aporta distinto:** tú das la semilla (dibujas) → la IA expande/varía. Ves muchas opciones a la vez (paleta), no una sola salida. Armas estructura larga (16 compases) con loops de 2 compases.

**Lo que sigue faltando:** géneros con nombre. Control de "swing latino", "clave", "tumbao", etc. Explicación de qué cambió entre tu dibujo y el loop generado.

## 5. Cómo funciona Interpolate realmente (documentación oficial)

**Fuente:** https://magenta.tensorflow.org/ + blog MusicVAE.

**La idea en una frase:** "Dos patrones entran → la red calcula un 'espacio latente' → salen mezclas intermedias suaves."

**Paso a paso (palabras simples):**
1. **Codificador (Encoder):** cada patrón (MIDI: notas, tiempos, velocidades) → vector de números ("latent code", ej. 256 dimensiones). "Es como comprimir un archivo ZIP: la esencia del patrón en pocos números."
2. **Espacio Latente:** todos los patrones posibles de entrenamiento viven en este espacio. Patrones parecidos → puntos cercanos.
3. **Interpolación:** se camina en línea recta entre los dos vectores y se sacan puntos intermedios.
4. **Decodificador (Decoder):** cada punto intermedio → patrón MIDI nuevo.

**Lo que NO explica la red (caja negra):**
- Por qué la nota Do en el compás 3 se convirtió en Mi bemol.
- Qué "rasgo musical" controla cada dimensión del vector latente.
- Cómo mapear "quiero más swing" → mover tal dimensión.
- No hay etiquetas semánticas (género, estilo, instrumentación).

## 6. Magenta Studio / Interpolate "de verdad" — requiere Ableton Live Suite + Max for Live

| Versión | Qué incluye | Requisitos | Corre en navegador gratis |
|---|---|---|---|
| Magenta Studio (Standalone) | 5 plugins: Continue, Groove, Generate, Drumify, **Interpolate** | App nativa (Electron), **no necesita Ableton** | ✅ Sí (descarga ~200 MB) |
| Magenta Studio (Ableton Live Pack) | Mismos 5 plugins como dispositivos Max for Live | **Ableton Live Suite** (no Standard) + **Max for Live** | ❌ No |
| Demos Web (Melody Mixer, etc.) | Versiones simplificadas JS | Solo navegador | ✅ Sí |

**El plugin Interpolate real (Standalone o Ableton):**
- Entrada: 2 clips MIDI (melodía o batería) en pistas distintas.
- Salida: N clips intermedios (tú eliges cuántos) en una pista nueva.
- Controles visibles: "Number of interpolations" (cuántos pasos), "Temperature" (aleatoriedad), "Model" (melody_2bar, drums_2bar, trio_16bar, etc.).
- Lo que NO tiene: selector de género ("Dembow", "Salsa"), explicación textual de cambios, patrones de entrenamiento latinos.
- **Conclusión práctica:** el Interpolate "pro" corre gratis en la app standalone (Electron), pero sigue siendo la misma caja negra sin géneros ni reglas visibles. La versión web es más accesible pero menos flexible (solo 2 compases, modelo fijo).

## 7. Lo que NO tiene Magenta (gaps críticos para nosotros)

- **Géneros latinos con nombre:** no hay modelos entrenados en dembow, salsa, plena, bomba, bachata, merengue, cumbia, reggaetón, timba, son, guaracha, candombe, milonga, zamba, chacarera, cueca, huayno, carnavalito, bambuco, pasillo, joropo, vals peruano, lando, festejo, marinera, tondero, etc.
- **Traductor "Patrón → Género Destino":** no existe "convierte este beat a Dembow". Solo interpolación entre dos patrones que tú provees.
- **Reglas visibles / explicables:** la red no dice "acentué el bombo en 1 y 3 y abrí el hi-hat en off-beats".
- **Separación "Qué suena" vs "Cómo suena":** el latent code mezcla notas, ritmo, densidad, contorno, todo junto. No hay controles separados para "swing", "clave", "densidad de notas", "rango melódico".
- **Repo principal archivado:** archivado el 6-ene-2026 (read-only). Nuevos proyectos en repos separados (Magenta RealTime, Lyria, DDSP-VST, Infinite Crate).
- **Entrenamiento propio accesible:** entrenar MusicVAE desde cero = GPU días, código TensorFlow 1.x legacy, datasets no públicos (Nottingham, MAESTRO, Groove, etc. si lo son, pero pipeline complejo).

## 8. Qué podemos aprender / reutilizar para nuestro proyecto

**Nuestro objetivo:** "Un traductor de ritmos que convierte tu patrón a un género destino con reglas claras y visibles, gratis y en el navegador."

**Ideas de Magenta que SIRVEN:**
- **Mezclar dos patrones (Interpolación):** nuestro "origen → destino" es una interpolación guiada: patrón usuario + plantilla género.
- **Control de Temperatura:** slider "Creatividad / Fidelidad": bajo = respeta tu patrón, alto = inyecta más sabor del género.
- **Espacio Latente / Paletas:** mostramos múltiples opciones (paleta) en vez de una sola salida. Usuario elige.
- **Separar Estructura de Contenido (Decoder Jerárquico):** Nivel 1: esqueleto rítmico (dónde caen golpes, clave, densidad). Nivel 2: ornamentación (ghost notes, fills, variaciones).
- **Semilla del Usuario (Latent Loops):** usuario dibuja / graba su patrón — ese es el "ancla". El género destino lo moldea.
- **Export MIDI / Web Audio:** salida estándar, usable en cualquier DAW.
- **Correr en Navegador (TF.js / ONNX Runtime Web / WebAssembly):** gratis, sin instalar, privacidad local.

**Qué NO sirve (descartar):**
- **Caja Negra Total:** usuario no entiende ni controla. Nosotros mostramos reglas: "Añadí bombo en 1 y 3", "Cambié hi-hat a off-beats".
- **Ableton Live Suite + Max for Live:** pago, propietario, excluye a la mayoría. Nuestro = 100% navegador, gratis.
- **Repo Archivado / TF 1.x Legacy:** código muerto. Usamos stack moderno (TypeScript, Vite, ONNX/TF.js, Web Audio API).
- **Modelos Genéricos (Melody / Drums / Trio):** no sirven para géneros latinos específicos. Entrenamos / curamos plantillas por género (JSON de reglas + loops de referencia).
- **Interpolación Libre (Sin Semántica):** nuestra interpolación es dirigida por reglas: cada paso tiene justificación textual.

## 9. Arquitectura propuesta para nuestro "Traductor de Ritmos"

**NAVEGADOR (Cliente)**

1. **ENTRADA:** Piano Roll / Grid 16 pasos (dibuja o graba MIDI). Archivo MIDI / Audio → onset detection → patrón.
2. **SELECCIÓN DE GÉNERO DESTINO:** lista con nombre: "Dembow", "Salsa", "Cumbia", ... Cada género = Plantilla JSON (ver abajo).
3. **MOTOR DE TRADUCCIÓN (Reglas Visibles):**
   - Análisis del patrón origen: densidad, syncopación, rango, instrumentos.
   - Reglas del género destino (plantilla): clave / patrón base de bombo/caja/hi-hat; swing %, micro-timing; densidad objetivo, fills cada N compases; instrumentos típicos (conga, timbal, güira...).
   - Aplicación paso a paso (con explicación): 1. Alinear downbeats → "Ajusté inicio a compás 1". 2. Imponer clave → "Añadí bombo en 2-3 de clave". 3. Redistribuir densidad → "Reducí notas 30%". 4. Añadir fills → "Inserté fill en compás 4/8". 5. Humanizar / Swing → "Apliqué swing 55%".
4. **SALIDA — PALETA DE OPCIONES (estilo Latent Loops):** slider "Fidelidad ↔ Creatividad" (= Temperatura). 4–6 variantes generadas (diferentes seeds). Cada variante: PLAY ▶ | EXPLICACIÓN 📋 | MIDI ⬇.
5. **EXPORTACIÓN:** MIDI multi-pista (bombo, caja, hi-hat, percusión...). JSON de reglas aplicadas (para auditoría / aprendizaje).

**Formato de Plantilla de Género (JSON legible y editable)** — ejemplo Dembow de Kilo (ver §11, corrección del snare):

```json
{
  "id": "dembow",
  "nombre": "Dembow Dominicano",
  "bpm": { "min": 100, "max": 110, "default": 104 },
  "compas": "4/4",
  "clave": "tresillo",
  "instrumentos": [
    { "id": "kick", "nombre": "Bombo", "midi": 36 },
    { "id": "snare", "nombre": "Caja", "midi": 38 },
    { "id": "hihat", "nombre": "Hi-Hat", "midi": 42 },
    { "id": "guira", "nombre": "Güira", "midi": 70 },
    { "id": "conga", "nombre": "Congas", "midi": 62 }
  ],
  "patronBase": {
    "kick": "X...X...X...X...",
    "snare": "....X.......X...",
    "hihat": "X.X.X.X.X.X.X.X.",
    "guira": "XXXXXXXXXXXXXXXX"
  },
  "swing": { "tipo": "shuffle", "valor": 55 },
  "densidadObjetivo": 0.65,
  "fills": {
    "cadaCompases": 4,
    "instrumentos": ["snare", "conga"],
    "patrones": ["...X.XXX", "X.X.XXX."]
  },
  "reglasTransformacion": [
    "Alinear downbeat del usuario al paso 0",
    "Imponer patrón de bombo de dembow (tresillo)",
    "Mantener density relativa del usuario ±20%",
    "Añadir güira continua en corcheas",
    "Insertar fill cada 4 compases"
  ]
}
```

**Ventaja:** cualquier músico / productor puede leer, entender, editar, añadir géneros. No hay caja negra.

## 10. Stack técnico mínimo (gratis, navegador, offline-first)

| Capa | Tecnología | Por qué |
|---|---|---|
| UI / Estado | React + TypeScript + Vite | Rápido, tipado, HMR |
| Piano Roll / Grid | @tonejs/midi + Canvas/WebGL propio o 'piano-roll' fork | Control total, sin deudas |
| Motor Reglas | TypeScript puro (funciones puras, testables) | Cero dependencias, corre en Web Worker |
| Síntesis / Preview | Tone.js / Web Audio API | Playback instantáneo en navegador |
| Export MIDI | @tonejs/midi | Estándar |
| Modelo ML Opcional (v2) | ONNX Runtime Web / Transformers.js | Si luego queremos "sugerir variaciones" con pequeño modelo, no para la traducción base |
| Deploy | Netlify / Vercel / GitHub Pages (static) | Gratis, HTTPS, CDN global |
| Offline | Service Worker (Workbox) | PWA instalable |

## 11. Roadmap mínimo viable (MVP)

| Sprint | Entregable |
|---|---|
| #1 | Piano Roll 16 pasos + carga MIDI + play Tone.js |
| #2 | Selector de género (3 plantillas hardcodeadas: Dembow, Salsa, Cumbia) + motor reglas básico (imponer patrón base) |
| #3 | Panel explicación paso a paso (texto generado desde reglas) |
| #4 | Slider Fidelidad/Creatividad + generación de 4 variantes (seeds) |
| #5 | Export MIDI multi-pista + PWA offline |
| #6 | Biblioteca de plantillas editable por usuario (localStorage → JSON import/export) |
| #7+ | Más géneros, detección de audio → patrón, modelo ML ligero para "variaciones creativas" |

## 12. Conclusión (tabla Magenta vs nuestro proyecto)

| Magenta | Nuestro Proyecto |
|---|---|
| Interpolación ciega entre 2 patrones | Traducción guiada: Patrón Usuario → Género Destino |
| Caja negra (latent space) | Reglas visibles, editables, explicadas en texto |
| Modelos genéricos (melody/drums/trio) | Plantillas por género con nombre (Dembow, Salsa, Plena...) |
| Requiere Ableton/Max para versión completa | 100% Navegador, Gratis, Offline-First |
| Repo archivado, TF 1.x legacy | Stack Moderno (TS, Vite, Web Audio, ONNX Web) |
| Temperatura = único control | Fidelidad/Creatividad + Reglas granulares + Paleta de opciones |

**Aprendizaje clave:** Magenta demostró que mezclar en espacio latente suena musical. Nosotros usamos esa intuición pero reemplazamos el espacio latente opaco por reglas transparentes por género. El usuario ve, entiende y controla cada transformación.

## ⚠️ CORRECCIÓN (revisión propia, 23-nov-2026)

El ejemplo JSON de Dembow de Kilo trae la **caja en [4, 12]** (`"....X.......X..."`). Eso NO es el dembow canónico verificado: el tresillo real es **caja en [3, 6, 11, 14]** (confirmado por 4 fuentes: Dembow Lab, ToMIDI directo, REA de ToMIDI y coqui-fm1). El bombo [0, 4, 8, 12] del JSON sí es correcto, y el hi-hat en corcheas también. **Antes de usar esa plantilla hay que corregir el snare a `"...X..X....X..X."` ([3, 6, 11, 14]).** El BPM default 104 también es alto para dembow (ToMIDI usa 95); el oído de Geral decide el final.
