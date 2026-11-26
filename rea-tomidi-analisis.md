# Desarme REA de ToMIDI — Análisis Completo

**Fuente:** REA (Antigravity) corrido por Geral en la Mac, lun 23-nov-2026, 11:29–11:48 AST.
**Modelo:** Claude Sonnet 4.6 (Thinking) — Gemini 3.5 Flash High no tenía cuota; el análisis terminó y se guardó como artifact "Tomidi Análisis Completo" antes de que la cuota se acabara otra vez.
**Entregado por fotos:** 36 fotos en 4 paquetes (11:58–12:03 AST). Este archivo es la consolidación fiel de lo que dicen las fotos.
**Página:** https://tomidi.org/drum-pattern-generator

## Verificación cruzada con el desarme directo

El desarme directo del 22-nov (`rea-tomidi-directo.md`) ya tenía casi todo esto. REA lo confirma por fuera, como una persona usándolo, y añade las fórmulas exactas. Coincidencias verificadas:

- **Dembow:** REA confirma bombo en [0,4,8,12] y caja en [3,6,11,14] (base 0) = bombo 1,5,9,13 y caja 4,7,12,15 (base 1). Es el mismo patrón de Dembow Lab y del desarme directo. **Tres fuentes confirman el mismo dembow canónico.**
- **SoundFont:** GeneralUser GS v2.0.3, de S. Christian Collins, en `/soundfonts/gugs/v2.0.3/`. Idéntico al desarme directo.
- **Los 12 géneros con BPM y swing:** coinciden exactos con el desarme directo (ver tabla §3).

Lo NUEVO que aporta REA: las cadenas de patrón de 16 caracteres por género y complejidad, la tabla de anchors, las fórmulas exactas de swing y humanize, el mapa MIDI de notas, las velocidades, la estructura de exportación y el stack técnico completo.

---

## 1. ¿Qué es y qué hace?

ToMIDI Drum Pattern Generator: app web gratis que genera patrones de batería con reglas escritas a mano + semilla aleatoria. Sin cuenta, nada se sube.

- 12 géneros, rejilla de 16 pasos, 1/2/4 compases de 4/4.
- 10 carriles de batería: Kick, Snare, Clap, Rim, Closed Hat, Open Hat, Hi Tom, Lo Tom, Ride, Crash.
- 7 kits: Standard, Room, Power, Electronic, 808, Jazz, Brush.
- Controles: BPM (60–200), Swing (0–60%), Humanize, Complexity (Simple/Medium/Busy), bloqueo de carril (track lock), Fill, Variation.
- Edición por paso: clic cicla vacío → normal → acento → ghost note → roll.
- Exporta MIDI (General MIDI Ch.10, canal 9, mapa GM), WAV (motor de samples real, 44.1 kHz/16 bits) y Tab (texto ASCII con notación de batería).
- Reproducción en tiempo real (Web Audio API con programación de baja latencia).

## 2. Arquitectura de la interfaz (UI)

### 2.1 Estructura de la página

- Sidebar: logo ToMIDI, navegación principal, botón "Sign in".
- Main: breadcrumb, H1, párrafo descriptivo + 4 funciones destacadas, sección EXAMPLES — 12 botones de género con BPM: Rock 112, Pop 100, Funk 98, Hip hop 90, Trap 140, Lo-fi 80, House 124, Techno 130, Drum and bass 172, Reggaeton 95, Disco 118, Metal 150.
- MAIN STUDIO (sección `#transcriber`): selector de género; controles BPM (slider/±), Swing, Humanize, selector Complexity (Simple/Medium/Busy), selector Bars (1/2/4), selector Kit (7 kits GM), botones Generate (R), Variation (V), Fill, Clear, Play/Stop (Space); rejilla de 16 pasos con filas Kick/Snare/Clap/Rim/Closed Hat/Open Hat/Hi Tom/Lo Tom/Ride/Crash; EXPORT (Download MIDI, Download WAV con barra de progreso, Copy as Tab ASCII); secciones SEO/FAQ debajo del estudio.

### 2.2 IDs de sección en el DOM (observados en el HTML)

Atributos `data-*` del bundle JS: `dp-steps` → rejilla de pasos, `dp-genres` → selector de género, `dp-lock` → estado de carriles bloqueados, `dp-swing` → control de swing, `dp-fill` → toggle de fill, `dp-export` → sección de exportación, `data-genre` → atributo en los botones de género.

## 3. Los 12 géneros: BPM, swing, kit y reglas

Datos extraídos directamente del JS `drum-player-CBPFAFSd.js` (observación estática, no ejecución).

| # | Género | Slug | BPM | Swing | Kit |
|---|--------|------|-----|-------|-----|
| 1 | Rock | rock | 112 | 0% | 8 |
| 2 | Pop | pop | 100 | 0% | 0 |
| 3 | Funk | funk | 98 | 12% | 0 |
| 4 | Hip hop | hip-hop | 90 | 22% | 8 |
| 5 | Trap | trap | 140 | 0% | 25 |
| 6 | Lo-fi | lo-fi | 80 | 28% | 32 |
| 7 | House | house | 124 | 8% | 25 |
| 8 | Techno | techno | 130 | 0% | 24 |
| 9 | Drum and bass | drum-and-bass | 172 | 0% | 0 |
| 10 | Reggaeton | reggaeton | 95 | 0% | 25 |
| 11 | Disco | disco | 118 | 0% | 0 |
| 12 | Metal | metal | 150 | 0% | 16 |

### 3.1 Cómo funciona el sistema de reglas (ingeniería del motor)

Notación de patrones en el JS: cadenas de 16 caracteres.

| Carácter | Significado |
|----------|-------------|
| `-` | sin golpe |
| `x` | golpe normal ~80% vel |
| `X` | acento 100% vel |
| `g` | ghost note ~45% vel |
| `2,3,4` | roll N veces 80% vel |

Ejemplos:

- Rock Kick (Simple): `x.......x.......` (bombo en 1 y 9, o sea 1 y 3 en tiempos); variante `x.......x.x.....`
- Rock Snare: `...X.......X...` (caja en 5 y 13 = tiempos 2 y 4, con acento)
- Rock Closed Hat (Simple): `X.x.X.x.X.x.X.x.` (corcheas alternando acento y normal)
- Reggaeton Snare (el dembow): `...x..x...x..x..` (patrón sincopado típico del dembow)
- House Kick: `X...X...X...X...` (four on the floor)
- Trap Closed Hat (Busy): `xxxxxxxxxxxxxxxx` (16 golpes)

Algoritmo de generación (pseudocódigo del `drumPlayer.js`, funciones Mn/Tn):

```
generatePattern(genre, complexity, seed)
  seededRandom (hash estilo Mulberry32)
  emptyGrid 10 carriles × (bars×16) pasos
  por instrumento:
    1) elegir figura base por complejidad [0=Simple, 1=Medium, 2=Busy],
       randomChoice(lane.base[complexity], rng)
    2) parsear cadena de 16 caracteres → steps[] + rolls[]
    3) añadir golpes opcionales por probabilidad de complejidad
    4) si complexity >= 2: añadir rolls en ride
    5) si Fill: applyFill en el último compás
```

Anchors (posiciones fijas protegidas): cada género define pasos que nunca cambian, p. ej. Rock kick:[0], snare:[4,12] — garantiza que el patrón siempre suene al género.

## 4. Patrones detallados por género

(Pasos en base 0. `x` = normal, `X` = acento, `g` = ghost.)

### 4.1 Rock (112 BPM, Swing 0%, Room Kit)

- Kick: `x.......x.......` (simple — tiempos 1 y 3), `x.......x.x.....` (medium — síncopa en 10)
- Snare: `....X.......X...` (anclada: tiempos 2 y 4, acento fuerte)
- CH: `X.x.X.x.X.x.X.x.` (simple — corcheas con acento/sin acento), `XgxgXgxgXgxgXgxg` (busy — ghost notes entre corcheas)
- Crash: `X` (inicio, vel 100%)
- Fill: toms — 4 u 8 pasos de Hi Tom / Lo Tom / Snare
- Extras: kick ghost en 6,14 (0–25% prob), snare ghost en 7,9,15 (0–30% prob)

### 4.2 Pop (100 BPM, Swing 0%, Standard Kit)

- Kick: `x.......x.......` (similar a Rock)
- Snare: `....X.......X...`
- Clap: `....X.......X...` (doble con la snare a 25–55% probabilidad)
- CH: `x.x.x.x.x.x.x.x.` (simple, corcheas parejas), `xgxgxgxgxgxgxgxg` (busy, con ghost notes)
- Extras: kick extra en 10 (0–25% prob), snare ghost en 15,7 (0–20% prob)

### 4.3 Funk (98 BPM, Swing 12%, Standard Kit)

- Kick: `x.x.......x.....` (simple — sincopado en posición 2), `x.x.......x..x..` (medium — más variación)
- Snare: `....X.......X...`
- Snare ghost: pos 7,9 (0–60%), pos 1,3,11,13,15 (0–35%), pos 14 (0–20%)
- CH: `xgxgxgxgxgxgxgxg` (busy — todos ghost-groove tipo Funkadelic), `XgxgXgxgXgxgXgxg` (alternativa busy)

### 4.4 Hip hop (90 BPM, Swing 22%, Room Kit)

- Kick: `x.......x.......` (simple — suelto, boom bap), `x.......x..x....` (medium — desplazado)
- Snare: `....X.......X...`
- CH: `x.x.x.x.x.x.x.x.` → `x.g.x.g.x.g.x.g.` (ghost-groove), `x.xgx.x.x.xgx.x.` (busy — muy sincopado)
- Ghost extra: kick en 7 (0–25%), snare en 15,7,9 (0–25%)

### 4.5 Trap (140 BPM, Swing 0%, 808 Kit)

- Kick: `x.......x.......` (suelto, keepKick=true — no se toca en fill), `x.......x...x...` (medium)
- Clap: `........X.......` (SOLO en paso 9, acento fuerte — identidad del trap)
- Snare: `........x.......` (a 50–70% probabilidad, suave debajo del clap)
- CH: `x.x.x.x.x.x.x.x.` → `xxxxxxxxxxxxxxxx` (16 golpes), `XxxxXxxxxXxxxXxxxx` (con acentos en tiempos fuertes)
- Rim: extra en 3,5,11,13 (sutil, 0–15% prob)
- Fill: rolls de hi-hat (tipo trap roll — escalada de velocidad)

### 4.6 Lo-fi (80 BPM, Swing 28%, Jazz Kit)

- Kick: `x.......x.......` (simple)
- Snare: `....x.......x...` (soft, sin acento — relajado)
- Snare ghost: pos 7,15 (0–30%), pos 9,13 (0–15%)
- CH: `x.g.x.g.x.g.x.g.` (ghost-groove — identidad lo-fi), `xgxgxgxgxgxgxgxg` (busy)
- Ride: chance 15–20% de sustituir CH por ride (feel de jazz suave)

### 4.7 House (124 BPM, Swing 8%, 808 Kit)

- Kick: `X...X...X...X...` (four on the floor, keepKick)
- Clap: `....X.......X...` (anclada en 2 y 4)
- Open Hat: `..x...x...x...x.` (en "and" corcheas = upbeats — ESENCIAL)
- CH: vacío (simple) → `.g.g.g.g.g.g.g.g` (medium) → `xgxgxg...` (busy)
- Ride: `g.g.g.g.g.g.g.g.` (ghost rides, 10–20% prob)
- Rim: extra en 3,11 (0–18%) y 13 (0–12% ghost)
- Fill: roll creciente de snare (crescendo de intensidad)

### 4.8 Techno (130 BPM, Swing 0%, Electronic Kit)

- Kick: `X...X...X...X...` (four on the floor, keepKick)
- Clap: `....x.......x...` (a 50–70% prob)
- CH: `..X...X...X...X.` (simple — solo en "and" fuertes), `xgxgxgxgxgxgxgxg` (busy), `xgXgxgXgxgXgxgXg` (con acentos en 3,7,11,15 — muy techno)
- Open Hat: `..x...x...x...x.` (a 40–70% prob)
- Rim: sincopado `...x.......x.....` (medium) → `...x..x...x..x...` (busy)
- Lo Tom: ghost en pos 7,15 (0–30% prob) — añade peso
- Fill: roll de snare (igual que House)

### 4.9 Drum and Bass (172 BPM, Swing 0%, Standard Kit)

- Kick: `x.......x.......` (simple), `x.x.......x.....` (medium)
- Snare: `....X..g....X...` (anclada 2 y 4)
- Snare ghost: pos 7,9 (0–40%), pos 15 (0–30%), pos 14 (0–15%)
- CH: `x.x.x.x.x.x.x.x.` → `xgxgxgxgxgxgxgxg` (busy — Amen-like feel)
- Ride: chance 30–35% (alternativa a CH)

### 4.10 Reggaeton (95 BPM, Swing 0%, 808 Kit)

El único género latinoamericano presente. Implementa el dembow auténtico.

- Kick: `X...X...X...X...` (four on the floor, anchors en 0,4,8,12)
- Snare: `...x..x...x..x.` ← DEMBOW (pasos 3, 6, 11, 14 en base 0)
- Clap: `...x..x...x..x.` (con la snare, a 30–40% prob)
- CH: `x.x.x.x.x.x.x.x.` → `XgxgXgxgXgxgXgxg` (busy)
- Extras: open hat en 14 (0–15% prob)
- "El patrón dembow `...x..x...x..x.` (pasos 3, 6, 11, 14) define todo el género. Traducido a golpes: corchea-y del 1, corchea del 2, corchea-y del 3, corchea del 4."

### 4.11 Disco (118 BPM, Swing 0%, Standard Kit)

- Kick: `X...X...X...X...` (four on the floor)
- Snare: `....X.......X...` (en 2 y 4)
- Clap: `....x.......x...` (a 30–40% prob)
- CH: `x...x...x...x...` (simple = cuartos), `xg.gxg.gxg.gxg.g` (medium)
- Open Hat: `..x...x...x...x.` (en upbeats — el "Charleston" de disco)

### 4.12 Metal (150 BPM, Swing 0%, Power Kit)

- Kick: `x.x.x.x.x.x.x.x.` (dobles = double kick, keepKick), `xxxxxxxxxxxxxxxx` (blastbeat), `x.xxx.xxx.xxx.xx` (variación — gallop feel)
- Snare: `....X.......X...` (en 2 y 4, ancla el groove)
- CH: `x.x.x.x.x.x.x.x.` → `x...x...x...x...` (alternancia)
- Ride: 20–50% prob (alternativa a CH)

## 5. Motor de audio: Web Audio API + SpessaSynth + Muestras

### 5.1 Cadena de audio (observada en `sound-TzSC4azS.js` + `samples-B5v4MgfX.js`)

**MODO SAMPLED — AudioWorklet + SpessaSynth**

- `spessasynth_processor.min-BDvAPZH5.js`
- Soundfont: GeneralUser GS v2.0.3 (C. Collins)
- URL: `tomidi.org/soundfonts/gugs/v2.0.3/` (`p000.sf2`...)
- Formato: SF2 partido por programa
- Canal MIDI: Ch.9 (drums GM standard)
- Decoder OGG/Vorbis: WASM en `samples-B5v4MgfX.js`

**MODO SYNTH (fallback) — Web Audio API nativo (si SpessaSynth falla)**

- Osciladores: triangle, sine, square, sawtooth
- Envolventes ADSR por instrumento
- DynamicsCompressor + BiquadFilter lowpass
- Reverb (artificial, IR sintético)

**SCHEDULER**

- `window.setInterval(25ms)` + `requestAnimationFrame`
- Lookahead: 200ms (`scheduleAheadTime`)
- `AudioContext.currentTime` para timing preciso
- Latencia de salida compensada (`outputLatency`)

**RENDER WAV**

- OfflineAudioContext — offline render
- 44100 Hz, estéreo, 16-bit
- `encode.worker-02OuoetY.js` (Worker en hilo aparte)
- Tail: 1.5 segundos de reverb/release
- Normalize + fadeOut

### 5.2 Mapa de notas MIDI (General MIDI drum map, Ch.9)

| Instrumento | MIDI note | Tab | ¿Cymbal? | Duración beats |
|-------------|-----------|-----|----------|----------------|
| Kick | 36 | BD | No | 0.5 |
| Rim | 37 | RS | No | 0.25 |
| Snare | 38 | SD | No | 0.5 |
| Clap | 39 | CP | No | 0.5 |
| Closed Hat | 42 | HH | Sí | 0.25 |
| Lo Tom | 43 | LT | No | 0.5 |
| Open Hat | 46 | OH | Sí | 1.0 |
| Hi Tom | 48 | HT | No | 0.5 |
| Crash | 49 | CC | Sí | 2.0 |
| Ride | 51 | RD | Sí | 1.0 |

### 5.3 Velocidades MIDI

| Tipo de hit | Factor (0–1) | MIDI vel aprox |
|-------------|--------------|----------------|
| Ghost note | 0.45 | ~57 |
| Normal | 0.80 | ~102 |
| Acento | 1.00 | 127 |

### 5.4 Implementación del Swing

Fórmula exacta extraída del JS (función `$`). Swing push: cada 2da semicorchea se empuja hacia adelante.

```javascript
timing_offset(beat, swing_pct) {
  s = swing_pct / 100  // 0.0 a 0.6
  si es_posicion_impar(beat):
    offset = s * 0.25   // push adelante
  sino:
    offset = -(s * 0.25)  // pull atrás
}
// Swing 33% = triplet feel. Swing 60% = máximo.
```

### 5.5 Implementación del Humanize

```javascript
// Timing jitter (seeded — mismo seed = mismo resultado)
jitter = (hash(seed,note) - 0.5) * 15 * humanize * bpm / 60000
// Velocity jitter (±10%)
velJitter = (hash(seed,note) - 0.5) * 0.10 * humanize
// Determinista: NO es verdaderamente aleatorio
```

## 6. Sistema de exportación

### 6.1 MIDI Export

Estructura del archivo MIDI (función Zn):

```javascript
{
  format: 1, tracks: 1, ppq: 480,
  timeSig: [4, 4],
  tempos: [{t: 0, bpm: X}],
  events: [
    {t: 0, type: 'program', ch: 9, program: kitNumber},
    {t: beat*60/bpm, type: 'note', ch: 9,
     pitch: GM_note, vel: velocity*127, dur: duration*60/bpm}
  ]
}
// Nombre de archivo: "{género-slug}-{bpm}bpm.mid"
// Ejemplos: "rock-112bpm.mid", "hip-hop-90bpm.mid"
```

### 6.2 WAV Export

- Motor SpessaSynth modo sampled, con fallback al sintetizador nativo
- OfflineAudioContext para render no-realtime
- Worker separado `encode.worker-02OuoetY.js` para codificación WAV
- Muestras OGG/Vorbis con decoder WASM embebido en `samples-B5v4MgfX.js`
- Tail de 1.5 segundos + normalize

### 6.3 Tab Text Export (Yn())

```
BD|x...x...x...x...|
SD|....o.......o...|
HH|x.x.x.x.x.x.x.x.|
```

## 7. Los géneros que faltan — análisis de brechas

### 7.1 Géneros presentes vs géneros del mundo latino

| Género | ¿En ToMIDI? | Notas |
|--------|-------------|-------|
| Reggaeton | ✅ | Tiene el patrón dembow correcto |
| Dembow puro | ⚠️ | Dentro de Reggaeton, sin nombre propio |
| Salsa | ❌ | Ausente — clave 2-3 o 3-2, conga, timbales |
| Plena | ❌ | Ausente — pandero, ritmo 3+3+2 |
| Bachata | ❌ | Ausente — maracas en 16ths, bongo, güira |
| Cumbia | ❌ | Ausente — caja, guacharaca, tambora |
| Merengue | ❌ | Ausente — tambora, güira, ~130–160bpm |
| Bomba | ❌ | Ausente — tambores africanos afropuertorriqueños |
| Dancehall | ❌ | Ausente — similar al reggaeton pero diferente feel |
| Afrobeat | ❌ | Ausente — clave africana, ritmos polirrítmicos |
| Bossa nova | ❌ | Ausente — clave brasileña, surdo |
| Samba | ❌ | Ausente — caixa, surdo, agogô |

### 7.2 Géneros globales que faltan

| Género | ¿En ToMIDI? | BPM aprox |
|--------|-------------|-----------|
| Country | ❌ | 120 |
| R&B / Neo Soul | ❌ | 80–100 |
| Reggae | ❌ | 70–90 |
| Jazz (swing) | ❌ | 120–200 |
| Blues | ❌ | 70–120 |
| Afrobeats (Ghana) | ❌ | 100–120 |
| Amapiano | ❌ | 112–115 |
| Drill | ❌ | 140–145 |

> WARNING — Brecha cultural importante: de 12 géneros, solo 1 es latinoamericano (Reggaeton). No hay representación de música afrocaribeña (salsa, plena, bomba), tropical (bachata, merengue, cumbia) ni afrobrasileña (samba, bossa).

## 8. Lecciones para el Traductor de Ritmos

### 8.1 Lo que el código de ToMIDI nos enseña

"La representación interna de un patrón es extremadamente limpia:"

```typescript
// Estructura real del patrón (observada en JS)
interface Pattern {
  bars: 1 | 2 | 4;
  steps: number[][];  // steps[instrumento][paso] = 0|1|2|3
  rolls: number[][];  // rolls[instrumento][paso] = 1|2|3|4
}
// Valores de steps: 0=mute, 1=ghost, 2=normal, 3=accent
// Valores de rolls: 1=single, 2=duplet, 3=triplet, 4=quadruplet
```

"Esta es exactamente la representación intermedia ideal para un Traductor de Ritmos."

### 8.2 Anchors por género (posiciones rítmicas fijas)

Extraído directamente del código de ToMIDI:

```javascript
rock:      { kick: [0], snare: [4,12] }
pop:       { kick: [0], snare: [4,12], clap: [4,12] }
funk:      { kick: [0], snare: [4,12] }
hiphop:    { kick: [0], snare: [4,12] }
trap:      { clap: [8], snare: [8] }
lofi:      { kick: [0], snare: [4,12] }
house:     { kick: [0,4,8,12], clap: [4,12], ohh: [2,6,10,14], snare: [4,12] }
techno:    { kick: [0,4,8,12], clap: [4,12], ohh: [2,6,10,14] }
dnb:       { kick: [0,10], snare: [4,12] }
reggaeton: { kick: [0,4,8,12], snare: [3,6,11,14], clap: [3,6,11,14] }
disco:     { kick: [0,4,8,12], snare: [4,12], ohh: [2,6,10,14], clap: [4,12] }
metal:     { kick: [0,4,8,12], snare: [4,12] }

// PROPUESTOS para géneros latinos (Traductor de Ritmos)
salsa:     { kick: [0,8], clave: [0,3,7,8,12] }   // clave 3-2
bachata:   { kick: [0], martillo: [3,7,11,15] }   // marimba bass + bongo
plena:     { pandero_low: [0,6,12], pandero_hi: [3,9] }  // 3+3+2
cumbia:    { caja: [4,8,12], guacharaca: "16ths" }
merengue:  { tambora: [0,8], guira: "16ths", bpm: 160 }
```

**CORRECCIÓN (23-nov-2026, por coqui-fm1):** los anchors latinos propuestos arriba están MAL en 4 puntos y parciales en 2. Lo correcto, sacado del repo coqui-fm1 (`firmware/src/groove.c`, tabla `GR_STYLE`), está en `reglas-latinas-coqui-fm1.md`, que es la autoridad desde ahora: salsa clave 3-2 es [0,6,12] + [20,24] (dos compases), no [0,3,7,8,12]; el bombo de salsa es [6,14] por compás, no [0,8]; el martillo de bachata son corcheas derechas [0,2,4,6,8,10,12,14] con grave en [12] y slap en [14], no [3,7,11,15]; la tambora de merengue es [4,12,14] y el BPM de la demo es 128, no 160. Plena: el 3+3+2 solo aparece en el requinto [0,3,6] (son tres tambores, no un solo pandero). Cumbia: llamador [4,12], sin el 8; guacharaca con patrón X.xx, no 16avos parejos. El dembow canónico (bombo [0,4,8,12], caja [3,6,11,14]) queda confirmado por cuarta fuente. BOMBA YUBA es 6/8 (24 pasos) y no cabe en la rejilla de 16: decisión de diseño pendiente.

### 8.3 Tabla de mapeo instrumental entre géneros

| Función rítmica | Rock | Hip hop | Trap | House |
|-----------------|------|---------|------|-------|
| Tiempo fuerte grave | Kick | Kick | 808 Kick | Kick 4/4 |
| Backbeat | Snare | Snare | Clap | Clap |
| Pulso/Groove | Closed Hat | CH ghost | CH 16ths | Open Hat |
| Acento | Crash | — | — | Crash |
| Relleno | Toms 4u8 | Sparse | Trap roll | Roll snare |

### 8.4 Arquitectura propuesta: Traductor de Ritmos

```
TRADUCTOR DE RITMOS

ENTRADA
├─ Patrón ToMIDI (formato steps/rolls descrito arriba)
├─ MIDI importado (channel 9, GM drum map)
└─ Patrón manual (clic en rejilla)

ANÁLISIS: Extracción de estructura rítmica
├─ Detectar anchors (kick, snare, clap — tiempos fuertes)
├─ Calcular densidad (hits / compás por función)
├─ Detectar feel: groove, swing, syncopation index
└─ Mapear a "RhythmCore" (representación agnóstica de género)

REGLAS DE TRADUCCIÓN: Género A → Género B
├─ Mantener anchors del género destino (preservar identidad)
├─ Mapear instrumento origen → función → instrumento destino
├─ Adaptar densidad de hats (ej. 8ths Rock → 16ths Trap)
├─ Ajustar swing (0% Rock → 22% Hip hop)
├─ Cambiar BPM al default del género destino (o mantener)
└─ Preservar variación/fills del patrón original

SALIDA
├─ Rejilla 16 pasos en nuevo género
├─ Comparación visual antes/después
└─ Exportar MIDI / WAV
```

### 8.5 Limitaciones de ToMIDI que el Traductor debe superar

| Limitación ToMIDI | Solución en Traductor |
|-------------------|----------------------|
| Sin instrumentos de percusión latina | Ampliar mapa: clave, conga, timbales, güira, pandero |
| Solo compases de 4/4 | Añadir 6/8, 3/4 para vals, cumbia ternaria |
| Sin patrones polirrítmicos | Capas con métricas distintas para Afrobeat, Samba |
| Sin persistencia | URL con estado hash o exportación de preset JSON |
| Swing solo en 16ths | Añadir swing en 8ths para jazz, tripleta para blues |

## 9. Stack técnico completo

| Capa | Tecnología identificada |
|------|------------------------|
| Framework UI | React + TanStack Router |
| Bundler | Vite (chunks con hash en `/assets/`) |
| Renderizado | SSR + React client hydration |
| Motor de audio | Web Audio API (AudioContext, AudioWorklet) |
| Síntesis sampled | SpessaSynth (`spessasynth_processor.min-BDvAPZH5.js`) |
| Soundfont | GeneralUser GS v2.0.3 (S. Christian Collins) — SF2 partido por programa |
| Síntesis fallback | Osciladores Web Audio nativos (triangle/sine/square/sawtooth + ADSR) |
| Exportación WAV | OfflineAudioContext + encode.worker (hilo separado) |
| Exportación MIDI | Librería propia ligera (`midi-file-DmZWGErg.js`) |
| Decodificador OGG | WebAssembly (Vorbis decoder en `samples-B5v4MgfX.js`) |
| RNG de patrones | Hash determinista estilo Mulberry32, basado en semilla numérica |
| Localización | i18n en/zh-CN |
| Analytics | Plausible (self-hosted) |

---

*Análisis generado con REA (Reverse Engineer Anything). Consolidado de 36 fotos enviadas por Geral el 23-nov-2026. No se copió código de ToMIDI: las reglas y fórmulas se registraron como referencia para el Traductor de Ritmos, como el desarme directo del 22-nov.*
