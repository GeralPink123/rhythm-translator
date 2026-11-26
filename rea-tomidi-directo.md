# Desarme directo de ToMIDI (sin REA)

**Fecha:** 23 de noviembre de 2026
**Página:** https://tomidi.org/drum-pattern-generator
**Método:** lectura de la página viva y de sus archivos JavaScript públicos (los paquetes `drum-player-*.js`, `drum-pattern-generator-*.js` y el motor de sonido `sound-*.js`, más el manifiesto de muestras). REA no se usó: en la Mac de Geral se quedó sin cuota ("individual quota reached", resetea en ~14 horas).
**Regla cumplida:** esto es un resumen escrito con mis palabras a partir de lo leído. No se copió código de ToMIDI en ningún archivo nuestro. Las reglas musicales (qué pasos lleva cada género) son conocimiento musical público: se describen, no se copian.

---

## 1. Qué hace la página y cómo está organizada

ToMIDI convierte un género en un ritmo de batería que se puede tocar, editar y descargar, gratis en el navegador, sin cuenta y sin subir nada. Su frase: "No AI and no black box" (sin IA y sin caja negra): cada género es un conjunto de reglas escritas a mano, más una semilla aleatoria que elige entre variantes.

**La interfaz, de arriba a abajo:**

- **Selector de 12 géneros.** Al tocar uno, escribe el patrón y ajusta tempo, swing y kit de ese estilo.
- **La rejilla: 16 pasos, 10 carriles.** Un compás de 16 semicorcheas, con los carriles Kick (bombo), Snare (caja), Clap (palmas), Rim, Closed hat (charles cerrado), Open hat (charles abierto), High tom, Low tom, Ride y Crash. Las teclas son las de General MIDI (36 a 51). Se puede trabajar con 1, 2 o 4 compases.
- **Controles de cada paso.** Clic en un paso: apagado → normal → acento. Shift+clic: nota fantasma (ghost note, muy suave). Alt+clic: roll de 2, 3 o 4 golpes rápidos dentro de un paso (el sonido de los hi-hats de trap). Al tocar el nombre de un carril suena ese tambor solo; se puede silenciar un carril.
- **Generar, Variación, Fill, Lock, Clear, Undo/Redo.** Generar (o tecla R) escribe un patrón nuevo. Variación (o V) cambia ~12% de los golpes, solo en pasos que el género usa, y nunca toca las "anclas" (los golpes que definen el género, como el backbeat o el four on the floor). Lock congela los carriles que te gustan y Generar solo cambia los demás. Fill hace que el último compás termine con un redoble. Clear vacía los carriles no bloqueados. Ctrl+Z deshace.
- **Complejidad: Simple, Medium, Busy.** Busy añade notas fantasma, bombos extra y charles, y cambia un poco los compases siguientes.
- **Tempo, swing y humanize.** Tempo de 60 a 200 BPM (slider, botones +/- de 1 BPM, tap tempo). Swing de 0 a 60%: retrasa cada segunda semicorchea (33% da sensación de tresillo). Humanize añade pequeños cambios de tiempo (hasta ~15 ms) y de velocidad (hasta ~18%), con semilla, así lo que se exporta suena igual que lo que se oyó.
- **Siete kits.** Standard, Room, Power, Electronic, 808, Jazz y Brush. Son baterías muestreadas que se cargan en el navegador; mientras llegan, suena un sintetizador interno.
- **Exportación.** Download MIDI (una pista de batería en el canal 10, con tempo, 4/4, el programa del kit, y el swing, los rolls y el humanize escritos en las notas). Download WAV (44,1 kHz, estéreo, 16-bit, con 1,5 segundos de cola; Repeat 1×, 2×, 4× u 8×). Copy as text (el patrón como texto ASCII, un carril por línea). Los carriles silenciados no salen en ninguna de las tres.
- **Tarjeta de ejemplos.** En la página hay ejemplos rotativos (house, trap, reggaeton, drum and bass) con semillas fijas. Al abrir la página, el patrón por defecto es hip hop, Medium, 2 compases, con Fill.
- **Atajos.** Espacio: play/stop. R: generar. V: variación. 1 a 4: cambiar de compás. Ctrl+Z: deshacer.
- **Límites que la propia página admite.** No hay cuentas, ni presets guardados, ni enlaces para compartir: al recargar, se empieza de cero. No hay volumen ni panorama por carril, ni efectos, ni shaker ni cencerro, y no se pueden cargar muestras propias.

**El sitio.** Es una app React (paquetes JavaScript con hash), bilingüe inglés/chino (los textos vienen en `en` y `zh`), parte de tomidi.org, una suite de herramientas musicales (buscador de BPM, metrónomo, MIDI a WAV, editor MIDI, audio a MIDI, generador de progresiones de acordes, generador de música 8-bit, mp3 a MIDI). La página se publicó el 2026-10-28: tiene unas 3-4 semanas de vida.

**Velocidades de los golpes.** Nota fantasma ~45%, golpe normal 80%, acento 100% (antes del humanize).

---

## 2. Los 12 géneros y sus patrones

**Cómo se lee la tabla.** Pasos 1 a 16 (1 = primer golpe del compás; 1, 5, 9 y 13 son los 4 tiempos). `x` = golpe normal, `X` = acento, `g` = nota fantasma. Cuando un carril tiene una sola lista, ese patrón vale para Simple, Medium y Busy. Cuando hay variantes, se muestra el patrón Simple (el canónico) y se explica qué cambia en Medium y Busy. Las probabilidades "extra" son golpes opcionales que el generador añade con esa probabilidad según la complejidad.

**Constantes que se repiten en varios géneros:**

- `N` = caja en 2 y 4 con acento: pasos 5 y 13.
- `P` = bombo en los 4 tiempos con acento: pasos 1, 5, 9 y 13.
- `F` = charles abierto en los contratiempos: pasos 3, 7, 11 y 15.

### Rock — 112 BPM, swing 0%, kit Room (prog. 8), crash sí, fill de toms

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 9 (variantes: 1, 9, 11) |
| Caja | 5, 13 (acentos) |
| Charles cerrado | acentos 1, 5, 9, 13; normales 3, 7, 11, 15 |
| Charles abierto | vacío (extra: paso 14 con 25-35%, paso 6 con 10-20% en Medium/Busy) |

En Medium/Busy el bombo gana golpes en 3, 7, 8, 11 y 15; la caja gana fantasmas en 7, 9 y 15; el charles se llena de fantasmas o de 16 golpes. Anclas (nunca cambian en Variación): bombo paso 1; caja pasos 5 y 13. Probabilidad de ride extra: 0% / 15% / 25%.

### Pop — 100 BPM, swing 0%, kit Standard (prog. 0), crash sí, fill de toms

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 9 (variantes: 1, 9, 11) |
| Caja | 5, 13 (acentos) |
| Palmas | 5, 13 (probabilidad de aparecer: 25% / 45% / 55%) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (corcheas) |
| Charles abierto | vacío (extra: paso 14 con 30-40%, paso 6 con 10-15%) |

En Medium/Busy el bombo se sincopiza (pasos 4, 7, 10, 12, 14, 15) y la caja gana fantasmas en 7 y 15. Anclas: bombo 1; caja 5 y 13; palmas 5 y 13.

### Funk — 98 BPM, swing 12%, kit Standard (prog. 0), sin crash, fill de toms

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 3, 11 (variantes: 1, 11) |
| Caja | 5, 13 (acentos) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (corcheas) |
| Charles abierto | vacío (extra: paso 14 con 30-40%, paso 6 con 15-25%) |

El bombo es sincopado de fábrica. En Medium/Busy la caja se llena de fantasmas (pasos 7 y 9 con 35-60%; pasos 1, 3, 11, 13, 15 con 12-35%) y el charles se llena de fantasmas. Anclas: bombo 1; caja 5 y 13.

### Hip hop (boom bap) — 90 BPM, swing 22%, kit Room (prog. 8), sin crash, fill escaso

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 11 (variantes: 1, 9, 11) |
| Caja | 5, 13 (acentos) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (corcheas) |
| Charles abierto | vacío (extra: paso 14 con 25-35%, paso 6 con 10-20%) |

La propia página lo describe como ejemplo: el bombo cae en el 1, en la última semicorchea del tiempo 1 y en el "and" del 3, con charles abierto en el "and" del 4. En Medium el bombo se mueve al 3 y gana acentos; la caja gana fantasmas en 7, 9 y 15. Anclas: bombo 1; caja 5 y 13.

### Trap — 140 BPM, swing 0%, kit 808 (prog. 25), crash sí, fill de trap

| Carril | Pasos (Simple) |
|---|---|
| Bombo 808 | 1, 11 (variantes: 1, 7) |
| Palmas | 9 (acento, tiempo 3: sensación de medio tiempo) |
| Caja | 9 (probabilidad de aparecer: 50% / 60% / 70%) |
| Rim | vacío (extra: pasos 3, 5, 11, 13 con 6-15%) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (en Busy: las 16 semicorcheas, con rolls) |
| Charles abierto | vacío (extra: pasos 6, 14 con 6-12%) |

Rolls activados: un paso puede ser un roll de 2, 3 o 4 golpes (el charles de trap). Anclas: palmas y caja en el paso 9. El bombo se mantiene igual entre compases (keepKick).

### Lo-fi — 80 BPM, swing 28%, kit Jazz (prog. 32), sin crash, fill escaso

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 11 (variantes: 1, 9) |
| Caja | 5, 13 (suaves, sin acento) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (corcheas) |
| Charles abierto | vacío (extra: paso 14 con 15-25%) |

En Medium/Busy la caja gana fantasmas en 7, 9, 13 y 15 (hasta 30%) y el charles se llena de fantasmas. Anclas: bombo 1; caja 5 y 13. Probabilidad de ride extra: 0% / 15% / 20%.

### House — 124 BPM, swing 8%, kit 808 (prog. 25), crash sí, fill de redoble

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 5, 9, 13 (acentos: four on the floor) |
| Palmas | 5, 13 (acentos) |
| Caja | 5, 13 (probabilidad de aparecer: 0% / 25% / 35%) |
| Rim | vacío (extra: pasos 3, 11 con 6-18%) |
| Charles cerrado | vacío en Simple; en Medium/Busy: fantasmas en los contratiempos (2, 4, 6, 8, 10, 12, 14, 16) |
| Charles abierto | 3, 7, 11, 15 (contratiempos) |
| Ride | fantasmas 1, 3, 5, 7, 9, 11, 13, 15 (probabilidad: 0% / 10% / 20%) |

Anclas: bombo 1, 5, 9, 13; palmas 5 y 13; charles abierto 3, 7, 11, 15; caja 5 y 13. El bombo se mantiene igual entre compases.

### Techno — 130 BPM, swing 0%, kit Electronic (prog. 24), crash sí, fill de redoble

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 5, 9, 13 (acentos: four on the floor) |
| Palmas | 5, 13 (probabilidad de aparecer: 50% / 60% / 70%) |
| Rim | vacío en Simple; en Medium: 4, 11 (y variantes); en Busy: 4, 7, 11, 14 |
| Charles cerrado | 3, 7, 11, 15 (acentos en los contratiempos) |
| Charles abierto | 3, 7, 11, 15 (probabilidad: 40% / 60% / 70%) |
| Tom grave | fantasmas en 8, 16 (variantes: 4, 12; probabilidad 0% / 15% / 30%) |
| Ride | fantasmas 1, 3, 5, 7, 9, 11, 13, 15 (probabilidad: 0% / 20% / 30%) |

Anclas: bombo 1, 5, 9, 13; palmas 5 y 13; charles abierto 3, 7, 11, 15. El bombo se mantiene igual entre compases.

### Drum and bass (two-step) — 172 BPM, swing 0%, kit Standard (prog. 0), crash sí, fill de toms

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 11 (two-step: bombo en el 1 y en el "and" del 3) |
| Caja | 5, 13 (acentos) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (corcheas) |
| Charles abierto | vacío (extra: paso 14 con 15-25%) |

En Medium/Busy el bombo gana golpes en 3, 8, 9, 12 y 14; la caja gana fantasmas en 7, 9, 14 y 15. Anclas: bombo pasos 1 y 11; caja 5 y 13. Probabilidad de ride extra: 0% / 30% / 35%.

### Reggaeton (dembow) — 95 BPM, swing 0%, kit 808 (prog. 25), crash sí, fill de toms

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 5, 9, 13 (acentos: uno en cada tiempo) |
| Caja | 4, 7, 12, 15 (el tresillo 3-3-2) |
| Palmas | 4, 7, 12, 15 (igual que la caja; probabilidad de aparecer: 0% / 30% / 40%) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (corcheas; en Busy gana fantasmas o acentos) |
| Charles abierto | vacío (extra: paso 14 con 8-15%) |

Anclas: bombo 1, 5, 9, 13; caja y palmas 4, 7, 12, 15. La caja gana fantasmas en 7 y 15 en Medium/Busy (6-15%).

**Este es el dato clave del desarme:** el dembow de ToMIDI es bombo en los 4 tiempos (1, 5, 9, 13) y caja en 4, 7, 12, 15. Es exactamente el mismo patrón que usa la demo del Traductor de Ritmos (sacado de Dembow Lab): bombo 1, 5, 9, 13 y caja 4, 7, 12, 15. Dos fuentes independientes confirman el mismo dembow canónico.

### Disco — 118 BPM, swing 0%, kit Standard (prog. 0), crash sí, fill de toms

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 5, 9, 13 (acentos: four on the floor) |
| Caja | 5, 13 (acentos) |
| Palmas | 5, 13 (probabilidad de aparecer: 0% / 30% / 40%) |
| Charles cerrado | 1, 5, 9, 13 (en Busy gana fantasmas en los contratiempos) |
| Charles abierto | 3, 7, 11, 15 (contratiempos) |

Anclas: bombo 1, 5, 9, 13; caja 5 y 13; charles abierto 3, 7, 11, 15; palmas 5 y 13.

### Metal — 150 BPM, swing 0%, kit Power (prog. 16), crash sí, fill de toms

| Carril | Pasos (Simple) |
|---|---|
| Bombo | 1, 3, 5, 7, 9, 11, 13, 15 (doble bombo en corcheas; en Medium/Busy: las 16 semicorcheas) |
| Caja | 5, 13 (acentos) |
| Charles cerrado | 1, 3, 5, 7, 9, 11, 13, 15 (variantes: solo 1, 5, 9, 13) |

Anclas: bombo 1, 5, 9, 13; caja 5 y 13. Probabilidad de ride extra: 20% / 35% / 50% (la más alta de todos los géneros). El bombo se mantiene igual entre compases.

### Resumen de los 12 en una tabla

| Género | BPM | Swing | Kit | Sello del patrón |
|---|---|---|---|---|
| Rock | 112 | 0% | Room | Bombo 1 y 3, caja 2 y 4, charles en corcheas |
| Pop | 100 | 0% | Standard | Igual que rock, con palmas doblando la caja |
| Funk | 98 | 12% | Standard | Bombo sincopado, muchos fantasmas de caja |
| Hip hop | 90 | 22% | Room | Boom bap: bombo 1 y "and" del 3, caja 2 y 4 |
| Trap | 140 | 0% | 808 | Palmas en el tiempo 3 (medio tiempo), rolls de charles |
| Lo-fi | 80 | 28% | Jazz | Backbeat suave, fantasmas, ride de vez en cuando |
| House | 124 | 8% | 808 | Four on the floor, charles abierto en contratiempos |
| Techno | 130 | 0% | Electronic | Four on the floor, rim y charles en contratiempos |
| Drum and bass | 172 | 0% | Standard | Two-step: bombo 1 y "and" del 3, caja 2 y 4 |
| Reggaeton | 95 | 0% | 808 | Dembow: bombo en cada tiempo, caja 3-3-2 |
| Disco | 118 | 0% | Standard | Four on the floor, caja 2 y 4, abierto en contratiempos |
| Metal | 150 | 0% | Power | Doble bombo en corcheas o semicorcheas |

---

## 3. Cómo suena: el motor de audio

**Muestras reales (SoundFont).** Los sonidos son muestras del SoundFont "GeneralUser GS" versión 2.0.3, de S. Christian Collins, servidas desde `/soundfonts/gugs/v2.0.3/` en archivos `.sf2`, uno por kit (por ejemplo `k025.sf2` es el kit 808/909, de ~1,8 MB; el Standard, `k000.sf2`, de ~2,8 MB). La página descarga un `manifest.json` con la lista y carga solo el kit que hace falta. El kit se elige por el número de programa MIDI de General MIDI: 0 Standard, 8 Room, 16 Power, 24 Electronic, 25 808/909, 32 Jazz, 40 Brush. El manifiesto trae 13 kits, pero la interfaz ofrece 7.

**Sintetizador de respaldo.** Mientras las muestras cargan, suena un sintetizador interno hecho con Web Audio: el bombo es un seno que cae de 160 Hz a 45 Hz con ruido filtrado, y la caja y los charles son ruido con filtros. Así la página suena al instante, sin esperar la descarga.

**Reproducción.** Web Audio API con un programador de antelación (mira el tiempo y agenda los golpes por delante), un compresor (umbral -14 dB, ratio 4:1) y una ganancia maestra de 0,5. El swing y el humanize se calculan con semilla, así la exportación suena igual que la reproducción.

**Exportación.** El MIDI guarda el canal 10, las teclas General MIDI, el tempo, el 4/4 y el programa del kit, con el swing, los rolls y el humanize ya escritos en las notas. El WAV se renderiza con un contexto de audio sin conexión a 44,1 kHz, estéreo, 16-bit, con 1,5 segundos de cola.

**Los 10 sonidos.** Bombo 36, rim 37, caja 38, palmas 39, charles cerrado 42, tom grave 43, charles abierto 46, tom agudo 48, crash 49 y ride 51 (mapa General MIDI de batería).

---

## 4. Géneros latinos: lo que tiene y lo que falta

**Tiene uno solo: reggaeton.** Y su "reggaeton" es el dembow (95 BPM, kit 808, bombo en cada tiempo, caja 3-3-2). No hay más géneros latinos en la página: ni salsa, ni plena, ni bomba, ni bachata, ni merengue, ni cumbia. Tampoco hay shaker, cencerro ni güira entre los sonidos (los 10 carriles son los de la batería General MIDI estándar).

**Lo que falta, en concreto:**

- Salsa (clave 2-3 y 3-2, cencerro, conga, bongo).
- Plena (panderos, el patrón de mano).
- Bomba (sicá, yubá, cuembé).
- Bachata (bongo, güira, el "chucu-chucu").
- Merengue (tambora, güira).
- Cumbia (el patrón de la tambora y el guache).
- Ninguno de esos ritmos aparece como género, ni como patrón, ni como sonido.

**El hueco queda confirmado por el propio código:** el generador solo conoce 12 géneros y el único latino es el dembow. Todo lo demás latino no existe en la herramienta.

---

## 5. Qué aprendemos para el Traductor de Ritmos

**Ideas que vale copiar (como idea, nunca el código):**

1. **El modelo de datos por género.** Cada género es: BPM, swing, kit, carriles con variantes base por complejidad, golpes extra con probabilidad por complejidad, probabilidad de que un carril aparezca, y anclas. Esa es la forma correcta de guardar un género: no un patrón fijo, sino una familia de patrones con reglas. El Traductor puede usar exactamente esa estructura para sus géneros destino (dembow, salsa, plena, bachata): cada destino es un conjunto de reglas, no un preset.
2. **Las anclas.** La idea de que ciertos pasos "nunca cambian" es oro para el traductor: al traducir un patrón a dembow, lo que hay que fijar son las anclas del dembow (bombo 1, 5, 9, 13; caja 4, 7, 12, 15) y mover lo demás alrededor. La traducción es, en el fondo, "re-anclar" el patrón al género destino.
3. **La notación de 16 pasos con x/X/g/rolls.** Es simple, legible y exportable a MIDI y a texto. El Traductor puede usar la misma notación para mostrar el original y el traducido lado a lado.
4. **El posicionamiento "sin IA, sin caja negra".** ToMIDI vende que sus reglas son escritas a mano y explicables. El Traductor es eso mismo llevado más lejos: no solo reglas a mano, sino reglas que se ENSEÑAN ("esto se movió aquí por esto"). Esa es su ventaja contra cualquier generador con IA.
5. **La exportación triple (MIDI, WAV, texto).** Un patrón traducido que se puede bajar en MIDI y WAV es una herramienta, no un juguete. ToMIDI demuestra que el formato funciona.
6. **El sintetizador de respaldo.** Suena al instante sin descargar muestras. Nuestra demo ya hace eso (sonidos sintetizados con Web Audio): es el camino correcto para un solo archivo HTML sin descargas pesadas.

**Qué mejorar (donde ToMIDI se queda corto):**

1. **No convierte nada.** Genera desde cero por género; no acepta tu patrón. Esa es justo la función del Traductor: el hueco está intacto.
2. **No hay enlaces para compartir.** No se puede guardar ni compartir un patrón: al recargar se pierde todo. El Traductor debe tener enlace con el patrón (la URL lleva el patrón), porque un patrón traducido que se comparte es publicidad gratis.
3. **Solo un género latino.** El reggaeton es el único, y ni siquiera distingue dembow de perreo. El Traductor cubre salsa, plena, bomba, bachata, merengue y cumbia como destinos: ahí está la diferencia.
4. **No explica nada.** ToMIDI no te dice por qué un patrón es de un género. El Traductor explica cada cambio ("la caja se movió al tresillo 3-3-2"): eso lo convierte en herramienta para aprender, no solo para generar.
5. **Sin volumen por carril ni efectos, sin muestras propias.** El Traductor no necesita copiar eso, pero sí puede permitir que el patrón traducido se escuche con distintos kits (808 para dembow, acústico para salsa), que es algo que ToMIDI ya hace y funciona.

**El hueco, dicho en una frase:** ToMIDI valida el formato exacto (gratis, navegador, reglas a mano, MIDI/WAV) pero no tiene la función de conversión ni los géneros latinos. El Traductor es ToMIDI + conversión + latinos + explicación + enlace para compartir. Nada de eso existe en la página desarmada.

**Veredicto final:** se puede construir. El desarme no encontró nada que haga lo mismo, y el patrón de dembow de ToMIDI coincide con el de Dembow Lab y con el de nuestra demo, así que la base musical está verificada por tres fuentes.

---

## Fuentes verificadas (23-nov-2026)

- Página viva: https://tomidi.org/drum-pattern-generator (texto completo leído, 301 líneas).
- JavaScript público: `/assets/drum-player-CBPFAFSd.js` (reglas de los 12 géneros y generación), `/assets/drum-pattern-generator-CnzrXaxb.js` (interfaz y textos), `/assets/drum-pattern-generator-DZFh5Qtr.js` (guía de géneros y FAQ), `/assets/sound-TrSC4azS.js` (motor de audio y exportación WAV), `/soundfonts/gugs/v2.0.3/manifest.json` (kits y licencia GeneralUser GS v2.0.3).
- Patrones verificados paso a paso desde el objeto de géneros del código (los 12 géneros, BPM, swing, kit, carriles, variantes, extras, anclas).
