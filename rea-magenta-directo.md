# Desarme de Magenta (Google) — directo, sin REA

**Fecha:** 22-nov-2026 (madrugada del 23-nov UTC)
**Método:** lectura directa de la página viva, su documentación y sus repos abiertos (sin REA, porque REA se quedó sin cuota en la Mac de Geral; resetea el lun 23-nov ~11:20 AM).
**Para:** el Traductor de Ritmos (proyecto GitHub de Geral Pink) — qué aprender de Magenta, qué no copiar, y qué hueco deja.
**Regla cumplida:** se aprendieron ideas y métodos; NO se copió código de Magenta en ningún archivo nuestro.

## Fuentes verificadas

- https://magenta.tensorflow.org/ (página principal)
- https://magentastudio.org/ (Magenta Studio: los 5 plugins)
- https://magenta.withgoogle.com/music-vae (MusicVAE, la base de Interpolate)
- https://magenta.withgoogle.com/groovae (GrooVAE)
- https://magenta.tensorflow.org/datasets/groove (Groove MIDI Dataset)
- https://github.com/magenta/magenta (repo principal Python)
- https://github.com/tensorflow/magenta-js (Magenta.js, navegador)
- https://github.com/magenta/magenta-studio (Magenta Studio)
- Precios de Ableton: tienda de Ableton en EE. UU., verificados nov 2026

---

## 1. Qué es Magenta

Magenta es un **proyecto de investigación abierto de Google** (nació en Google Brain en 2016) que explora si el machine learning puede generar música y arte. No es un producto comercial ni una empresa: es investigación con el código abierto.

Tiene tres patas:

| Pata | Qué es | Dónde vive |
| --- | --- | --- |
| Biblioteca Python | Los modelos originales, en TensorFlow | `github.com/magenta/magenta` |
| Magenta.js | Los mismos modelos, para correr en el navegador con TensorFlow.js | `github.com/tensorflow/magenta-js` |
| Magenta Studio | Los 5 modelos más útiles empaquetados como plugins | `github.com/magenta/magenta-studio` |

**Estado actual (verificado el 22-nov-2026): el repo principal está ARCHIVADO.** El README de `magenta/magenta` dice, textual: "This repository is currently inactive (archived, read only)". Datos del repo: 19.784 estrellas, 3.753 forks, 1.426 commits, creado el 2016-05-05, licencia Apache 2.0. Google dice que pasó a usar "repos individuales para los proyectos nuevos".

Lo nuevo de Magenta en 2026 ya no son estos modelos clásicos: la página principal destaca **Magenta RealTime 2 (MRT2)** — un modelo abierto para tocar instrumentos de IA en vivo —, la API **Lyria RealTime**, **The Infinite Crate** (plugin de DAW) y **DDSP-VST**. Los modelos que nos interesan (MusicVAE, GrooVAE, Melody RNN, Drums RNN) son de 2016–2019 y no se mantienen activamente.

**Licencias:** el código es Apache 2.0 (se puede estudiar, bifurcar y reentrenar). El Groove MIDI Dataset es CC BY 4.0 (se puede usar citando a Google).

**En una frase:** Magenta es el laboratorio abierto de Google para música con IA: publica el paper, publica el código, publica los datos, y deja que otros lo usen.

---

## 2. Los modelos y herramientas principales (en palabras simples)

| Modelo / herramienta | Qué hace | Año |
| --- | --- | --- |
| **Melody RNN** | Continúa una melodía: le das unos compases y te inventa lo que sigue, nota a nota. Red LSTM. | 2016 |
| **Drums RNN** | Genera patrones de batería nuevos (varios tambores a la vez). Red LSTM. | 2016 |
| **Polyphony RNN** | Genera varias voces a la vez (melodía + acompañamiento). | 2016 |
| **Performance RNN** | Toca piano con expresión: no solo qué notas, también con qué fuerza y qué tiempo. | 2017 |
| **MusicVAE** | Comprime una melodía, una batería o un trío (batería + bajo + melodía) en un código corto, y de ese código puede sacar variaciones, reconstruirla o mezclarla con otra. Modelos de 2 compases y de 16 compases. | 2018 |
| **GrooVAE** | MusicVAE + groove: entiende la velocidad (fuerza) y el microtiming (desvío del tiempo exacto) de cada golpe. Tres usos: Groove, Drumify y Groove Transfer. | 2019 |
| **NSynth / GANSynth** | Sintetizan sonido (timbre) con redes neuronales. No son MIDI, son audio. | 2017–2019 |
| **Magenta Studio** | Los 5 plugins para Ableton: Continue, Generate, Groove, Drumify, Interpolate. | 2018 |
| **Magenta.js** | `@magenta/music` corre MusicVAE, MelodyRNN, DrumsRNN, PerformanceRNN e ImprovRNN en el navegador. Demos: Melody Mixer, Beat Blender, Latent Loops. | 2018 |

**Entrenamiento:** Melody RNN, Drums RNN y MusicVAE se entrenaron con corpus grandes de archivos MIDI de la web (el Lakh MIDI Dataset, ~176.000 archivos; el paper de MusicVAE habla de ~1,5 millones de archivos recolectados). GrooVAE se entrenó con el Groove MIDI Dataset (ver §4).

**La idea que lo une todo:** Magenta no trabaja con audio, trabaja con **MIDI** (qué nota, cuándo, con qué fuerza). Por eso sus herramientas son generadores de ideas en MIDI, no generadores de canciones terminadas.

---

## 3. Interpolate — mezclar dos patrones

Es la herramienta de Magenta más cercana al Traductor de Ritmos, así que va con detalle.

### Qué le das y qué te devuelve

- **Le das:** dos clips MIDI. Pueden ser dos melodías o dos patrones de batería, de 2 compases cada uno.
- **Te devuelve:** hasta 16 variaciones híbridas entre los dos, como clips MIDI nuevos dentro de Ableton.
- **Parámetros:** "steps" (cuántas variaciones genera) y "temperature" (qué tan atrevido o aleatorio: 0,5 = predecible, 1,5 = sorprendente; la mayoría usa 0,9–1,1).

### Cómo lo hace (red neuronal, no reglas)

Interpolate usa **MusicVAE**. La idea, en palabras simples:

1. MusicVAE convierte cada patrón en un **punto de un mapa** (el "espacio latente"). En ese mapa, los patrones parecidos quedan cerca y los distintos quedan lejos.
2. Interpolate **traza una línea entre los dos puntos** y te devuelve los patrones que hay en el camino.
3. Los pasos intermedios son patrones válidos y musicales, no una mezcla a ciegas: la red aprendió de miles de MIDI qué suena bien y qué no.

Nadie le escribió reglas a Interpolate. No sabe qué es trap ni qué es house ni qué es dembow. Aprendió de los datos. Y **no explica nada**: no te dice qué cambió entre el patrón A y el híbrido, ni por qué. Caja negra.

### Precedente real: ya mezclaron géneros con Interpolate

Un productor documentó (kozmikkandi.com) haber usado Interpolate Beat con **4 patrones de entrada — trap, house, otra variación de house y drum and bass — y sacar 32 variaciones** combinándolos de a pares, para armar un loop de 8 compases. O sea: mezclar patrones de géneros distintos con Interpolate **ya se hizo**. Pero es una mezcla ciega entre dos patrones que tú aportas, no una traducción a un género destino con nombre.

### Límites (todos verificados)

| Límite | Detalle |
| --- | --- |
| **Requiere Ableton Live Suite + Max for Live** | Magenta Studio es un dispositivo Max for Live. Necesita Ableton Live 10.1 Suite o superior. Sin Ableton no hay Interpolate. |
| **La puerta cuesta $749** | El plugin es gratis (Apache 2.0), pero Ableton Live 12 Suite cuesta **$749** pago único en la tienda de EE. UU. (Intro $99, Standard $349). Solo Suite trae Max for Live. |
| **No es navegador** | No hay versión web de Interpolate. La versión standalone existe en lanzamientos viejos pero **no se mantiene activamente** (lo dice la propia página de Magenta Studio). |
| **Solo 2 compases** | El modelo de Interpolate trabaja con clips de 2 compases (el modelo largo de MusicVAE llega a 16, pero Interpolate en Studio es de 2). |
| **No hay género destino** | No le dices "hazlo dembow". Le das dos patrones y te devuelve algo entre los dos. El destino no tiene nombre. |
| **No explica** | No dice qué movió, qué quitó ni qué añadió. |
| **Proyecto a ritmo de investigación** | El repo `magenta/magenta-studio` tiene 1.277 estrellas y su README dice "This is not a Google product". Las versiones grandes del plugin salen con años de diferencia. |

**A favor, hay que decirlo:** todo corre **en local** (TensorFlow.js en la CPU, sin nube, sin cuotas, sin internet, y tu MIDI nunca sale de tu máquina). En eso Magenta Studio es honesto y gratis de usar, una vez que ya tienes Ableton.

---

## 4. GrooVAE — transferir el groove

### La idea central: un beat son dos cosas

El blog oficial de GrooVAE lo explica con una división simple:

- **La partitura (score):** qué tambores suenan y en qué pasos. Es lo que se escribe en la rejilla.
- **El groove:** cómo suenan: **con qué fuerza** se golpea cada tambor (**velocidad**) y **con qué desvío del tiempo exacto** (**microtiming**, el "laid back" o el "adelantado" que le da el sentir humano).

Un buen baterista, con una sola de las dos partes, sabe completar la otra. GrooVAE enseña a una red neuronal a hacer eso mismo.

### Los tres modelos

| Modelo | Le das | Te devuelve |
| --- | --- | --- |
| **Groove** (humanizar) | Una partitura cuantizada y rígida (la rejilla tal cual) | La misma partitura con velocidad y microtiming de baterista real. Es el "humanize" de un DAW, pero aprendido de bateristas de verdad. |
| **Drumify** | Un ritmo tocado en cualquier instrumento (o golpeando la mesa, o una línea de bajo) | La batería completa que encaje con ese ritmo. Funciona con cualquier entrada rítmica mientras el tempo sea constante. |
| **Groove Transfer** | El groove de un patrón A + la partitura de un patrón B | El patrón B tocado con el groove de A. El ejemplo del blog: tocar "Straight Ahead Jazz" con el groove de "Funky Drummer". |

### Cómo funciona (en palabras simples)

GrooVAE es una variante del autoencoder variacional (VAE) de MusicVAE. La diferencia clave: **codifica la partitura y el groove juntas en el mismo código**, y se entrena **tapando una parte** (la partitura o el groove) y obligando al modelo a predecir el beat real completo. Técnicamente cae en la categoría VIB (Variational Information Bottleneck) más que VAE, porque la entrada y la salida son distintas.

Para Groove Transfer, el truco es darle al decodificador acceso directo a la partitura (teacher forcing), así el codificador se ve obligado a guardar **solo el groove** en el código. Ese código de groove se puede aplicar después a cualquier otra partitura.

### Los datos: Groove MIDI Dataset

- **13,6 horas** de batería tocada por humanos, **1.150 archivos MIDI, 22.000+ compases**.
- **10 bateristas** (más del 80% del tiempo son profesionales contratados) tocando con metrónomo en una batería electrónica **Roland TD-11** en el estudio de Google.
- Cada interpretación está anotada con género, tempo y baterista. Casi todo en 4/4.
- Licencia **CC BY 4.0** (abierta, citando a Google). El ZIP completo pesa 4,76 GB; la versión solo-MIDI, 3,11 MB.
- Los modelos vienen en versiones de **2 y 4 compases**.

### En qué se parece a traducir de género — y en qué NO

**Se parece:**

- Groove Transfer ya separa **"qué se toca"** de **"cómo se toca"** y combina dos fuentes. Es la misma separación que el Traductor necesita: contenido (partitura) y estilo (groove).
- La humanización (velocidad + microtiming) es exactamente lo que hace que un dembow **suene** a dembow y no a rejilla de robot. Un dembow con todos los golpes a velocidad 100 y en el tiempo exacto suena a máquina.
- Drumify valida una idea de producto: "del ritmo sale la batería". El Traductor ya acepta un patrón dibujado; Drumify demuestra que la gente entiende ese flujo.

**NO es lo mismo:**

- GrooVAE transfiere el **sentir**, no el **género**: no mueve la caja al tresillo 3+3+2, no baja el tempo de 140 a 95, no quita el hi-hat abierto ni añade el bajo 808. **La partitura destino se queda igual; solo cambia cómo se toca.**
- Traducir de trap a dembow es cambiar la **partitura** (qué pasos llevan bombo y caja, el tempo, qué se quita y qué se añade). GrooVAE no hace eso.
- GrooVAE es **probabilístico y opaco**: el propio blog admite que a veces el modelo decide cambiar cosas que no le pediste (por ejemplo, añadir unas cajas al final). El Traductor es **determinista y explicable**: cada cambio tiene una regla y una explicación en palabras simples.
- GrooVAE necesita un modelo entrenado con horas de bateristas profesionales. El Traductor necesita las reglas escritas por el oído de Geral. Son dos caminos distintos al mismo problema.

---

## 5. Qué géneros cubre — y lo latino

### La lista oficial del Groove MIDI Dataset

La página del dataset publica la lista completa de géneros (18):

> afrobeat, afrocuban, blues, country, dance, funk, gospel, highlife, hiphop, jazz, latin, middleeastern, neworleans, pop, punk, reggae, rock, soul

**Lo que hay:**

- **"latin" existe, pero es una etiqueta genérica**, no un género con nombre. No se publica cuántas interpretaciones llevan esa etiqueta ni qué ritmos son.
- **"afrocuban"** también está en la lista.
- Los bateristas tenían formación en **jazz, música brasileña, rock y percusión orquestal**.

**Lo que NO hay:**

- No hay **dembow, reggaeton, salsa, plena, bomba, bachata, merengue ni cumbia** como estilos con nombre en ningún lado de Magenta.
- Un paper académico (NIME) que entrenó un modelo con el Groove MIDI Dataset dice que los patrones complejos como los latinos o afrocubanos **"no aparecen en grado medible"** en lo que el modelo genera. O sea: lo latino es marginal en los datos, y el modelo casi no lo aprendió.
- MusicVAE, Melody RNN y Drums RNN se entrenaron con corpus grandes de MIDI de la web (Lakh MIDI Dataset), sin etiquetas de género latino como destino.

**Conclusión: en Magenta no hay dembow ni salsa ni plena ni bachata como destino. El hueco latino sigue intacto.** Y no es casualidad: el dataset se grabó con bateristas de jazz, rock y música brasileña en un estudio de Google. Nadie les pidió que tocaran dembow.

---

## 6. Qué aprendemos para el Traductor de Ritmos

### Copiar como idea (nunca el código)

1. **Separar partitura y groove.** El Traductor traduce la partitura con reglas; pero el resultado además debe **sonar** con el groove del género destino: la caja del dembow no pega con la misma fuerza ni en el mismo microtiming que la del trap. GrooVAE nos enseña que velocidad + microtiming son parte del género, no un adorno. En una versión futura, cada género destino puede llevar su propio "perfil de groove" (qué golpes van acentuados, cuáles van fantasma, cuánto swing).
2. **Interpolate como inspiración de producto.** "Mezclar dos patrones" es una función que la gente entiende de inmediato, y ya hay un productor que mezcló trap + house + drum and bass con Interpolate. El Traductor puede ofrecer la mezcla como función extra más adelante — pero su función central es otra: **traducir a un destino con nombre**.
3. **Drumify como validación del flujo.** Si el usuario dibuja o toca un ritmo, de ahí sale una batería. El Traductor ya acepta el patrón dibujado; Drumify confirma que ese flujo es natural.
4. **El Groove MIDI Dataset como referencia de datos.** Si algún día el Traductor quiere humanizar con datos reales (no con reglas), ahí hay 13,6 horas de baterías reales con velocidad y microtiming, con licencia abierta (CC BY 4.0). No se copia ahora; solo saber que existe.
5. **Magenta.js como prueba de que el navegador aguanta.** Los modelos de Magenta corren en el navegador con TensorFlow.js. Nosotros no necesitamos red neuronal (las reglas a mano son más ligeras y explicables), pero Magenta.js demuestra que el navegador es un lugar legítimo para herramientas musicales serias.
6. **La "temperature" como control.** Magenta Studio tiene un deslizador de qué tan atrevido es el resultado. El Traductor puede tener su propio control: "qué tan estricto o qué tan libre" en la traducción (estricto = el patrón canónico del género; libre = variaciones con probabilidad, como los extras de ToMIDI).

### NO copiar

| No copiar | Por qué |
| --- | --- |
| **La caja negra** | Magenta no explica nada. El Traductor explica cada cambio en palabras simples. Esa es la diferencia que lo vende. |
| **Ableton de pago** | Interpolate exige Ableton Live Suite ($749). El Traductor es gratis en el navegador, sin instalar nada. |
| **Modelos pesados / pesos que descargar** | Magenta.js exige bajar los pesos del modelo al navegador. El Traductor es un solo HTML, cero dependencias. |
| **Construir sobre algo muerto** | El repo principal de Magenta está archivado y los modelos clásicos no se mantienen. No se construye encima de eso. |
| **Generar sin destino** | MusicVAE genera "algo parecido a los datos", sin que el usuario elija el género. El Traductor sí tiene destino con nombre: dembow, salsa, plena, bachata. |
| **El "a veces inventa" de GrooVAE** | GrooVAE a veces añade cosas que nadie le pidió. El Traductor no inventa: cada golpe tiene su regla y su explicación. |

### El hueco que deja Magenta

Magenta **mezcla** (Interpolate) y **humaniza** (GrooVAE), pero **no traduce**. Nadie en Magenta convierte tu patrón de trap en dembow con reglas que puedas leer, en el navegador, gratis, con géneros latinos como destino.

ToMIDI (desarmado el 22-nov) **genera** por género con reglas a mano, pero **no convierte** tu patrón. Magenta **mezcla** patrones con una red neuronal, pero **no convierte** a un destino con nombre.

**El hueco sigue siendo solo nuestro: pega tu patrón → reglas visibles → sale en dembow/salsa/plena/bachata, en un solo HTML, gratis, sin IA en la nube. Ni Magenta ni ToMIDI hacen eso.**

---

## 7. Veredicto

**Se puede construir.** Magenta valida dos ideas que el Traductor puede aprovechar (mezclar patrones y transferir groove), pero no hace lo que el Traductor hace, y no tiene nada latino como destino. La competencia más cercana sigue siendo un paper muerto de 2020 (Groove2Groove) y un generador que no convierte (ToMIDI).

**No se copió código de Magenta en ningún archivo nuestro.** Las ideas se describen con palabras propias.

---

## 8. Vista de afuera verificada en vivo (23-nov-2026, 12:15–12:22 PM AST)

La vista de afuera se hizo con el navegador de Muse, sin REA (cuota agotada otra vez tras ToMIDI; Opus y GPT-OSS 120B también dieron cuota agotada — la cuota es de la cuenta, no del modelo; vuelve lun 30-nov 11:29 AM AST). Solo lectura, sin inicio de sesión, descargas ni pagos. El audio no se pudo escuchar en ese navegador: se verificaron los cambios visuales (botón play que pasa a pausa, cabezal que avanza, rejilla que se llena), no el sonido.

### MusicVAE — la página sí carga, pero no es una demo interactiva

- URL: https://magenta.withgoogle.com/music-vae — viva. Es el artículo de blog "MusicVAE: Creating a palette for musical scores with machine learning", del 15-mar-2018, de Adam Roberts y otros.
- No tiene botones de generar ni control de temperatura: es texto con piano-rolls, 16 reproductores de audio y videos de YouTube. El play del primer audio ("Data Space Sample (not MusicVAE)", 0:07) cambió a pausa, así que el reproductor responde.
- Describe MusicVAE como una "paleta" para mezclar ideas musicales, con expresión, realismo y suavidad del espacio latente. Enlaza a las tres demos y al repo de GitHub.

### Las tres demos enlazadas: las tres vivas en 2026, con matices

- **Melody Mixer — VIVA, con carga frágil.** Enlace https://g.co/melodymixer → destino real https://experiments.withgoogle.com/ai/melody-mixer/view/. En la primera carga quedó atascada en "LOADING…" (botón gris deshabilitado) más de 3 minutos; tras recargarla una vez, apareció "PLAY". Dentro: dos melodías, MELODY 1 = Twinkle (morada, izquierda) y MELODY 2 = Sparse (naranja, derecha), con una franja central que muestra la mezcla de una a otra. El botón grande de reproducir/pausa cambió a pausa con el cabezal triangular al inicio. Cambiar MELODY 2 a "Generated" puso el menú en azul, con notas nuevas distintas a Sparse, y el cabezal avanzó por la mezcla. No tiene control de temperatura ni de aleatoriedad visible. En una línea: eliges dos melodías y escuchas cómo se transforma una en la otra.
- **Beat Blender — VIVA, pero con un defecto visible.** URL https://experiments.withgoogle.com/ai/beat-blender/view/. Cargó con "LOADING…" bajo el secuenciador y luego mostró la paleta 2D de colores (izquierda) y el secuenciador de 9 filas de percusión (derecha), tempo 120. El botón redondo blanco de reproducir hizo avanzar el cabezal de la columna 1 a la 4, así que la reproducción responde. Tiene controles UNDO, CLEAR, EDIT CORNERS, DRAG/DRAW, selector de salida MIDI, ABOUT y SAVE. Los presets (ROCK 1, ROCK 2, REGGAETON, BREAK, BASIC BACKBEAT, BOOTS & CATS, POP PUNK, HALF TIME, BOSSA HALF TIME, SAMBA FULL TIME) y "GENERATE NEW BEAT" existen en el código de la página, pero en esa vista (1919×998) no se veían en pantalla y no se pudieron pulsar (intento por programa: "covered"; por descripción visual: "not_visible"). No se probó arrastrar el punto blanco de la paleta. En una línea: arrastras un punto por una paleta de colores para mezclar ritmos de batería y dibujas o editas el patrón resultante.
- **Latent Loops — VIVA, pero lenta al generar.** Enlace https://goo.gl/magenta/latent-loops → destino real https://teampieshop.github.io/latent-loops/. Cargó con las instrucciones ("hover over loops to play them back", "fill the four corner cells with seed loops", "press ⚡ to generate a grid of new loops that interpolate between your seeds") y una rejilla con tres semillas en las esquinas. Tras pulsar "Click to enable audio" y el rayo ⚡ del centro, al principio no pasó nada visible, pero tras unos 20 segundos la rejilla se llenó con una cuadrícula de bucles intermedios que van del morado al naranja/amarillo entre las semillas. En una línea: eliges bucles semilla en las esquinas y genera una rejilla de bucles que interpolan entre ellos, que luego puedes arrastrar al portapapeles o a una línea de tiempo.

### Confirmado en magenta.tensorflow.org

- La página principal https://magenta.tensorflow.org/ carga y hoy destaca Magenta RealTime 2.
- En https://magenta.tensorflow.org/studio (Magenta Studio, plugin de Ableton Live, v2.0): **Interpolate** toma dos ritmos de batería o dos melodías como entradas y genera hasta 16 clips que combinan las cualidades de ambos; cada patrón es un punto en un mapa y el plugin dibuja una línea entre los dos puntos y devuelve clips a lo largo de esa línea. El número de clips lo fija el deslizador "steps". Requiere dos entradas en la misma pista, de la misma duración y de menos de 4 compases.
- **Temperature:** todas las herramientas tienen un deslizador de temperatura; controla la aleatoriedad al muestrear en la última capa de la red neuronal: valores altos dan más variación y a veces caos, valores bajos son más conservadores. Es un control del plugin de Ableton, no de las demos del navegador.
- **Ableton, no gratis en el navegador:** en Requisitos pide "Ableton Live 10.1 Suite o superior" (las versiones anteriores requieren una versión no incluida de Max 8) y la instalación es arrastrar el archivo .amxd a una pista MIDI dentro de Live. El Interpolate real no funciona gratis en el navegador; las demos web son versiones simplificadas y gratuitas de esa idea. La misma página advierte que la antigua versión independiente de Magenta Studio "no se mantiene activamente".
- **Repositorio principal archivado — sí, confirmado:** https://github.com/tensorflow/magenta redirige a https://github.com/magenta/magenta, donde se lee en un aviso amarillo: "This repository was archived by the owner on Jan 6, 2026. It is now read-only." (archivado el 6 de enero de 2026, ahora solo lectura). El README añade: "This repository is currently inactive (archived, read only)". Tenía 19.8k estrellas y 3.8k forks.

### Lecciones para el Traductor (de la vista de afuera)

1. **Copiar la metáfora de la paleta y los dos extremos, no la caja negra:** lo mejor de Magenta es "dos patrones entran, varios pasos intermedios salen" (Melody Mixer, Latent Loops, Interpolate con su deslizador "steps"). El Traductor debe mostrar cada paso intermedio y qué cambió en cada uno (por ejemplo, dónde cae el kick, el dembow "boom-ch-boom-chick", la clave de salsa), con reglas escritas y visibles, no un modelo que decide sin explicar.
2. **Presets con nombre de género como entrada, no como adorno:** Beat Blender ya trae un preset "REGGAETON" (además de BOSSA y SAMBA), pero en esa sesión esos botones ni siquiera se veían. El Traductor debe poner dembow, salsa, plena y bachata como botones grandes, visibles y pulsables, cada uno con su regla (tempo, patrón de bombo/caja, acentos) explicada en palabras simples junto al botón.
3. **Gratis en el navegador y con carga honesta:** no copiar el camino de pago (Ableton Live Suite + Max for Live) ni la fragilidad de 2018: Melody Mixer se quedó en "LOADING…" más de 3 minutos hasta recargar, y Latent Loops tardó ~20 segundos en generar sin indicar progreso. El Traductor debe cargar rápido, decir qué está pasando ("generando…") y funcionar sin instalar nada ni pagar nada.
4. **Un control de aleatoriedad con palabras de persona, no con jerga:** copiar la idea de Temperature ("qué tan atrevido/sorpresivo") pero con etiqueta clara ("fiel al género" ↔ "más atrevido") y con efecto visible en el patrón; y no depender de código archivado: no construir sobre el repositorio magenta/magenta (archivado el 6 de enero de 2026, solo lectura) ni sobre Magenta Studio v1.0, que la propia página dice que ya no se mantiene.

## 9. Lo no verificado (restante)

- **No probé Interpolate en vivo.** Requiere Ableton Live Suite ($749), que no tenemos. Todo lo de Interpolate viene de la documentación oficial de Magenta Studio y de reportes de productores que lo usaron.
- **No revisé el código de GrooVAE línea por línea.** El desarme se hizo desde la documentación, el blog oficial y el paper; no desde el código fuente del modelo.
- Los precios de Ableton son de su tienda en EE. UU. (verificados nov 2026): Intro $99, Standard $349, Suite $749. Pueden variar por país.
- **Nota:** lo que antes estaba en §8 como "no verifiqué si las demos siguen vivas" ya quedó verificado arriba (las tres vivas, 23-nov-2026).
