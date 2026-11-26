# La bomba yubá en 6/8 — cómo resolverla en el Traductor de Ritmos

**Fecha:** 23 de noviembre de 2026
**Problema:** la bomba yubá es 6/8 y en coqui-fm1 tiene 24 pasos (12 por compás), pero la rejilla del Traductor es de 16 pasos (un compás de 4/4). ¿Cómo cabe la yubá sin romper el Traductor?
**Fuentes:** `firmware/src/groove.c` y `tools/gen_hub.py` del repo coqui-fm1 (leídos en la web, sin clonar ni ejecutar nada); `reglas-latinas-coqui-fm1.md`; las páginas de ToMIDI, ohminod, Stem Shovel, PATTERN-MACHINE, RockBlocks, Max Online Tools y El Hipnótico.

## El problema en una frase

Un compás de dembow se divide en 16 partes iguales (semicorcheas) y un compás de yubá se divide en 12 partes iguales (tresillos de corchea). Son dos maneras distintas de partir el mismo compás, y un golpe que cae justo en una división casi nunca cae justo en la otra.

## Lo que confirmó coqui-fm1 (verificado en el código)

- **BOMBA YUBA: 24 pasos, 6/8.** En `groove.c`, la tabla `GR_STYLE` dice: `{"BOMBA YUBA", 24, 32, 18, ...}` — 24 pasos, 32 ticks por paso, feel 18. El comentario del código lo dice claro: *"bomba yubá in 6/8 (a step: an eighth-note triplet; two bars of 6)"* — un paso es un tresillo de corchea, dos compases de 6. El dibujo del compás en el código es `|1 . . 2 . . |1 . . 2 . . |`: cada compás tiene 2 pulsos grandes, cada uno partido en 3.
- **BOMBA SICA: 32 pasos, 4/4.** En la misma tabla: `{"BOMBA SICA", 32, 24, 22, ...}` — 32 pasos, 24 ticks por paso, feel 22. **La sica NO es 6/8: es 4/4 como el dembow.** Su BPM es 108. La sica no tiene este problema: cabe en la rejilla de 16 tal cual.
- **YUBÁ: 100 BPM.** En `tools/gen_hub.py`, tabla `STYLE_PLAY`: `"BOMBA YUBA": (100, 39)`. Son tempos de demostración del autor, no tempos oficiales.
- **El compás dura LO MISMO en 4/4 y en 6/8.** Este es el dato clave. En `groove.c` el reloj va a 96 ticks por negra. Un paso de 4/4 vale 24 ticks (16 pasos × 24 = 384 ticks por compás). Un paso de 6/8 vale 32 ticks (12 pasos × 32 = 384 ticks por compás). **384 ticks en los dos casos: el compás dura exactamente lo mismo; solo cambia en cuántas partes se divide (16 o 12).** Y en `gen_hub.py` la demo graba cada estilo con la misma fórmula, `bar_ms = 4 * 60000 // bpm` — 4 negras por compás, también para la yubá. O sea: en coqui, cambiar de 4/4 a 6/8 no cambia la duración ni el tempo. Solo cambia la rejilla.

### El patrón real de la yubá (de coqui, verificado contra las cadenas del código)

Dos compases, 24 pasos en total (base 0). `X` = acento, `x` = golpe, `g` = fantasma.

| Carril | Compás 1 (pasos 0–11) | Compás 2 (pasos 12–23) |
|---|---|---|
| Cuá (madera) | 0X, 2x, 3X, 5x, 6X, 8x, 9X, 11x | 12X, 14x, 15X, 17x, 18X, 20x, 21X, 23x |
| Tumba (buleador) | 0X, 3x, 6X, 9x | 12X, 15x, 18X, 21x |
| Conga apagada (fantasmas) | 1g, 5g, 7g, 11g | 13g, 17g, 19g, 23g |
| Maracas | 0x, 2x, 3x, 5x, 6x, 8x, 9x, 11x | 12x, 14x, 15x, 17x, 18x, 20x, 21x, 23x |
| Quinto (subidor) | 6X, 9x | 18X, 20X |

**Fill (12 pasos):** quinto 0X, 2x, 3X, 5x, 6X, 8x, 9X, 10X, 11X; tumba 0X, 6X.
**Patrón clave:** la tumba marca cada 3 tresillos [0, 3, 6, 9] — ese es el pulso del 6/8.

### El patrón de dembow (el origen del ejemplo)

De Dembow Lab y ToMIDI, rejilla de 16, un compás (el compás 2 repite igual):

| Carril | Pasos (0–15) |
|---|---|
| Bombo | 0, 4, 8, 12 |
| Caja (tresillo 3+3+2) | 3, 6, 11, 14 |
| Hi-hat (corcheas) | 0, 2, 4, 6, 8, 10, 12, 14 |

## Cómo lo resuelven otros (verificado en sus páginas)

Nadie mete un 6/8 en una rejilla fija de 16. Todos cambian la rejilla cuando cambia el compás:

| Herramienta | Cómo resuelve el 6/8 |
|---|---|
| **ToMIDI** (https://tomidi.org/drum-pattern-generator) | **No lo resuelve:** rejilla fija de 16 pasos, 1, 2 o 4 compases de 4/4. Su propio análisis lo pone como limitación: "Solo compases de 4/4 — Añadir 6/8, 3/4". |
| **ohminod Sequencer** (https://audio.ohminod.net/en/documentation/sequencer/time-signature) | Menú de compás: 4/4, 3/4, 6/8, 7/8. Una página nueva tiene **16 pasos en 4/4, 12 pasos en 3/4 o 6/8, 14 pasos en 7/8**. La rejilla cambia con el compás. |
| **Stem Shovel Drum Machine** (https://www.stemshovel.com/docs/drum-machine) | Botones **4/4, 3/4 y 6/8** para el compás, más botones de largo. Habla de patrones de **12 o 24 pasos**. |
| **PATTERN-MACHINE** (https://patternmachine.tongatron.org/) | **8–36 pasos, 4/4 o 6/8**, rejillas derechas o de tresillo. Ojo: pide cuenta para abrir la caja de ritmos. |
| **RockBlocks** (https://rockblocks.app/) | **Cualquier compás** (3/4, 5/4, 7/8 y más), con bloques de ritmo por tiempo. Tiene una vista clásica de 16 pasos y una vista de bloques; se cambia entre las dos cuando se quiere. |
| **Max Online Tools** (https://www.maxonlinetools.com/step-sequencer) | El usuario elige el número de pasos: 8 para medio compás, 32 para dos compases, y **12 o 24 pasos "para música en tres, como un vals"**. |
| **El Hipnótico** (https://www.elhipnotico.com/musictools/caja-de-ritmos/) | Secuenciador de 8–32 pasos con un estilo **6/8** entre sus géneros. |
| **Coqui mismo** | El compás dura lo mismo (384 ticks); la rejilla es de 32 pasos en 4/4 y de 24 en 6/8. El aparato no aplasta el 6/8 en 16: cambia la división. |

**Conclusión de esta parte:** la solución normal en el mundo es cambiar el número de pasos con el compás. Nadie que se tome en serio el 6/8 lo mete a la fuerza en 16 pasos.

## La cuenta, en palabras simples

Para pasar un golpe de la rejilla de 16 a la de 12: se mira en qué parte del compás cae (paso ÷ 16), se multiplica por 12 y se redondea al paso más cercano. Si cae justo en la mitad entre dos pasos, se redondea hacia arriba ("la mitad hacia arriba").

Solo 4 posiciones coinciden exactas en las dos rejillas: los 4 tiempos del compás. En la de 16 son los pasos 0, 4, 8, 12; en la de 12 son los pasos 0, 3, 6, 9. Todo lo demás cae entre dos pasos y hay que redondearlo. Eso no es un error: es la naturaleza del cambio de 4/4 a 6/8. La regla se escribe y se muestra, no se esconde.

---

## Alternativa A — La rejilla cambia con el género (RECOMENDADA)

**Cómo se ve:** el Traductor tiene dos rejillas. En 4/4, la de siempre: 16 casillas por compás, agrupadas de 4 en 4 (1 . . . 2 . . . 3 . . . 4 . . .). En 6/8, la rejilla tiene **12 casillas por compás, agrupadas de 3 en 3** (1 . . 2 . . | 1 . . 2 . .), con los 2 pulsos grandes marcados. Dos compases de yubá = 24 casillas. Cuando el usuario elige yubá como destino (o como origen), la rejilla cambia sola y se ve el cambio. El tempo no cambia: el compás dura lo mismo.

**Cómo cae exacto el patrón de yubá:** cae 1 a 1, sin tocar nada. Los 24 pasos de coqui son los 24 pasos de la rejilla. Tumba [0, 3, 6, 9, 12, 15, 18, 21], cuá [0, 2, 3, 5, 6, 8, 9, 11, 12, 14, 15, 17, 18, 20, 21, 23], maracas igual que el cuá, quinto [6, 9, 18, 20], fantasmas [1, 5, 7, 11, 13, 17, 19, 23]. El fill de 12 pasos cabe en un compás de la rejilla.

### Ejemplo trabajado: dembow → yubá (compás 1)

Regla de traducción: cada golpe se pasa por su posición en el tiempo (paso ÷ 16 × 12, redondeo a la mitad hacia arriba). Por función: bombo → tumba (el grave que marca el pulso), caja → cuá (la voz que responde), hi-hat → maracas (la que lleva el tiempo).

| Origen (dembow, 16 pasos) | Cuenta | Destino (yubá, 12 pasos) | ¿Es el patrón real? |
|---|---|---|---|
| Bombo 0 | 0 ÷ 16 × 12 = 0 | Tumba 0 | Sí, exacto |
| Bombo 4 | 4 ÷ 16 × 12 = 3 | Tumba 3 | Sí, exacto |
| Bombo 8 | 8 ÷ 16 × 12 = 6 | Tumba 6 | Sí, exacto |
| Bombo 12 | 12 ÷ 16 × 12 = 9 | Tumba 9 | Sí, exacto |
| Caja 3 | 3 ÷ 16 × 12 = 2,25 → 2 | Cuá 2 | Sí: el cuá real tiene el 2 |
| Caja 6 | 6 ÷ 16 × 12 = 4,5 → 5 | Cuá 5 | Sí: el cuá real tiene el 5 |
| Caja 11 | 11 ÷ 16 × 12 = 8,25 → 8 | Cuá 8 | Sí: el cuá real tiene el 8 |
| Caja 14 | 14 ÷ 16 × 12 = 10,5 → 11 | Cuá 11 | Sí: el cuá real tiene el 11 |
| Hi-hat 0, 2, 4, 6, 8, 10, 12, 14 | 0, 1,5, 3, 4,5, 6, 7,5, 9, 10,5 → 0, 2, 3, 5, 6, 8, 9, 11 | Maracas 0, 2, 3, 5, 6, 8, 9, 11 | **Sí: son EXACTAMENTE las maracas reales** |

**Resultado de la ida:** el bombo del dembow cae exacto sobre la tumba de la yubá [0, 3, 6, 9]; la caja cae sobre el cuá [2, 5, 8, 11]; el hi-hat cae exacto sobre las maracas [0, 2, 3, 5, 6, 8, 9, 11]. **La traducción no se acerca al patrón real: CAE DENTRO de él.** El esqueleto del dembow ya vive dentro de la yubá. El compás 2 sale igual, corrido 12 pasos. El quinto y los fantasmas son color propio de la yubá y no vienen del dembow: el Traductor los muestra como "de la yubá", no como traducidos. El bajo 808 de la demo del dembow ([0, 6, 8, 14]) no tiene casa limpia en la yubá (es grave como la tumba y chocaría con ella): en la v1 se pierde y se avisa; lo decide el oído de Geral.

### Ejemplo trabajado: yubá → dembow (la vuelta, compás 1)

Regla: se pasa al revés (paso ÷ 12 × 16, redondeo a la mitad hacia arriba). Tumba → bombo, cuá → caja, maracas → hi-hat. Y una regla más, visible: **los anclajes del género destino siempre suenan** (son lo que define al género); los golpes de origen que caen encima los refuerzan; los que no caen encima se mueven al paso más cercano y se dice cuáles se movieron.

| Origen (yubá, 12 pasos) | Cuenta | Destino (dembow, 16 pasos) |
|---|---|---|
| Tumba 0, 3, 6, 9 | 0, 4, 8, 12 | Bombo 0, 4, 8, 12 — exacto, el bombo del dembow |
| Cuá 0, 2, 3, 5, 6, 8, 9, 11 | 0, 2,67, 4, 6,67, 8, 10,67, 12, 14,67 → 0, 3, 4, 7, 8, 11, 12, 15 | Caja: 8 golpes no caben en una caja de dembow (lleva 4). Los 4 más cercanos a los anclajes [3, 6, 11, 14] son 3, 7, 11, 15 |
| Maracas 0, 2, 3, 5, 6, 8, 9, 11 | igual que el cuá → 0, 3, 4, 7, 8, 11, 12, 15 | Hi-hat: el dembow lleva corcheas [0, 2, 4, 6, 8, 10, 12, 14]; los golpes caen 1 paso corridos en 4 de 8 |

**Resultado honesto de la vuelta:** el bombo sale perfecto [0, 4, 8, 12]. La caja sale [3, 7, 11, 15]: pareja, un golpe en el "y" de cada tiempo, **no es el tresillo 3+3+2 del dembow** (el cuá reparte sus golpes parejo y el tresillo es disparejo: no hay manera de sacarlo de ahí). El hi-hat sale corrido 1 paso en la mitad de los golpes. Por eso la regla de los anclajes: en la vuelta, la caja pone el tresillo canónico [3, 6, 11, 14] y el Traductor muestra "2 golpes de la cuá cayeron un paso tarde y se movieron al tresillo". **La vuelta es la dirección floja y se dice así.** La ida es la dirección fuerte.

**Ventajas:**
- El patrón real de la yubá cabe exacto, sin redondeos escondidos. Lo que se ve es lo que suena.
- Es lo que hacen las herramientas que ya resolvieron esto (ohminod, Stem Shovel, PATTERN-MACHINE): la rejilla cambia con el compás.
- El compás dura lo mismo: no cambia el tempo ni el BPM al traducir.
- La regla de traducción es una sola frase ("por la posición en el tiempo, redondeo a la mitad hacia arriba") y se puede mostrar golpe por golpe, como en la tabla de arriba.
- Sirve para el futuro: si un día entra un 3/4 (vals) o un 7/8, es la misma idea (la rejilla cambia al número de pasos del compás).
- La exportación a MIDI es directa: en MIDI cada paso es un número de ticks exacto (a 480 ppq, un paso de 6/8 son 160 ticks).

**Pegas:**
- Hay que programar dos tamaños de rejilla en vez de uno, y el dibujo cambia cuando cambia el género.
- El usuario tiene que aprender una segunda rejilla (12 casillas en grupos de 3). Es un cambio visible, no escondido.
- La vuelta (yubá → dembow) es aproximada y hay que decirlo: la caja no sale con el tresillo exacto sin la regla de los anclajes.

---

## Alternativa B — Aplastar el 6/8 en 16 pasos

**Cómo se ve:** una sola rejilla de 16 para todo, siempre. La yubá se dibuja en la misma rejilla de siempre, con una nota que diga "yubá aproximada a 16 pasos".

**Cómo caería el patrón de yubá:** se pasa cada golpe de 12 a 16 (paso ÷ 12 × 16, redondeo). Tumba [0, 3, 6, 9] → [0, 4, 8, 12] (exacto, es el bombo). Cuá [0, 2, 3, 5, 6, 8, 9, 11] → [0, 3, 4, 7, 8, 11, 12, 15]. Maracas igual. Quinto [6, 9] → [8, 12]. Fantasmas [1, 5, 7, 11] → [1, 7, 9, 15].

**Ejemplo trabajado, dembow → yubá:** no hay traducción de verdad. Como todo vive en 16 pasos, "traducir a yubá" sería reemplazar el patrón por la yubá aplastada de arriba: bombo [0, 4, 8, 12], caja [0, 3, 4, 7, 8, 11, 12, 15], hi-hat igual. La vuelta es trivial porque nunca se salió de la rejilla de 16.

**Ventajas:**
- Cero cambios: la misma rejilla, el mismo código, la misma exportación, el mismo modelo mental. Lo más rápido de construir.
- La ida y la vuelta son triviales (no hay cambio de rejilla).

**Pegas:**
- **Destruye el ritmo.** El cuá aplastado [0, 3, 4, 7, 8, 11, 12, 15] no es la yubá: el tresillo se pierde, los golpes caen en semicorcheas derechas y suena a un 4/4 recto, no a un 6/8. El redondeo mueve golpes hasta casi un paso entero (medio paso de tresillo = 2/3 de semicorchea).
- **Miente.** El Traductor promete reglas visibles y verdaderas; aquí la regla visible sería falsa ("esto es yubá" cuando no suena a yubá). El oído de Geral lo rechazaría al primer play.
- La yubá dejaría de ser un género real y pasaría a ser un preset disfrazado. El hueco dentro del hueco (la bomba solo existe en coqui) seguiría sin cubrirse de verdad.

**Veredicto de esta alternativa:** descartada. Es el resuelve, no la solución.

---

## Alternativa C — Una rejilla universal de 48 micropasos

**Cómo se ve:** una sola rejilla, pero de **48 micropasos por compás**. 48 es el mínimo común múltiplo de 16 y 12: un paso de 4/4 son 3 micropasos y un paso de 6/8 son 4 micropasos. La rejilla muestra las 48 casillitas, con las posiciones de la rejilla activa marcadas (en 4/4 se encienden las casillas 0, 3, 6, 9...; en 6/8 las 0, 4, 8, 12...). Es como trabajan los DAW por dentro (ticks).

**Cómo cae exacto el patrón de yubá:** exacto, como en la A. Tumba [0, 3, 6, 9] en 12 pasos = micropasos [0, 12, 24, 36]. Cuá [0, 2, 3, 5, 6, 8, 9, 11] = micropasos [0, 8, 12, 20, 24, 32, 36, 44]. El fill de 12 pasos cabe exacto.

**Ejemplo trabajado, dembow → yubá:** bombo [0, 4, 8, 12] en 16 pasos = micropasos [0, 12, 24, 36] — **idénticos a la tumba**. Caja [3, 6, 11, 14] = micropasos [9, 18, 33, 42]; los micropasos del cuá más cercanos son 8, 20, 32, 44 (a 1 o 2 micropasos de distancia). Hi-hat [0, 2, 4, 6, 8, 10, 12, 14] = micropasos [0, 6, 12, 18, 24, 30, 36, 42]; las maracas están en [0, 8, 12, 20, 24, 32, 36, 44] (a 0, 2, 0, 2, 0, 2, 0, 2 micropasos). La vuelta es igual que en la A.

**Ventajas:**
- Cero error de redondeo en la rejilla: todo golpe cae en un micropaso exacto, en las dos direcciones.
- Una sola rejilla para todo, y a prueba de futuro: cualquier compás que divida 48 (3/4, 7/8, 5/4) cabe sin cambiar nada.
- La exportación a MIDI es trivial (a 480 ppq, un micropaso son 40 ticks exactos: el compás son 1920 ticks entre 48 micropasos).

**Pegas:**
- **48 casillas en el teléfono no se pueden tocar bien.** La rejilla se vuelve chiquita y el usuario tiene que entender qué es un micropaso. La fórmula de Geral es simple (un toque, sin complicaciones): esta rejilla es lo contrario.
- La "exactitud" es solo de la rejilla, no de la música: el tresillo del dembow y el tresillo de la yubá no coinciden ni en 48 pasos (la caja cae a 1–2 micropasos del cuá más cercano). El redondeo no desaparece: solo se esconde en números más chicos. El problema musical es el mismo que en la A.
- Más código que la A (hay que marcar qué micropasos pertenecen a cada división) para ganar casi nada en el resultado que se oye.

**Veredicto de esta alternativa:** correcta por dentro, mala por fuera. Es la solución de ingeniero, no la del usuario.

---

## La recomendación

**La rejilla cambia con el género: 16 pasos en 4/4 y 12 pasos en 6/8 (Alternativa A).**

Porque es la única que pone el patrón real de la yubá exacto, sin redondeos escondidos, y es la que usan las herramientas que ya resolvieron esto. La ida (dembow → yubá) cae dentro del patrón real de coqui, golpe por golpe. La vuelta se hace con la regla visible de los anclajes y se dice que es aproximada. Y en el teléfono se ve simple: 12 casillas en grupos de 3, dos pulsos grandes marcados.

La B se descarta porque destruye el ritmo y miente. La C se descarta porque la exactitud extra no se oye y la rejilla de 48 no se puede tocar en el teléfono.

## Lo que queda por decidir (con el oído de Geral)

1. **La vuelta (yubá → dembow):** ¿vale la caja [3, 7, 11, 15] que sale de la cuenta, o se usa el tresillo canónico [3, 6, 11, 14] con la nota de los 2 golpes movidos? La recomendación es el tresillo canónico con la nota visible.
2. **El quinto y los fantasmas:** en la ida no vienen del dembow. ¿Se añaden como color propio de la yubá, o la traducción sale solo con tumba, cuá y maracas? La recomendación es que salgan solo con tumba, cuá y maracas, y el quinto se pueda añadir con un toque.
3. **El bajo 808:** en la ida no tiene casa limpia (choca con la tumba). ¿Se pierde con aviso, o se funde con la tumba? Lo decide el oído.
4. **El BPM final de la yubá:** coqui usa 100 como tempo de demo. El BPM real de la bomba yubá tocada lo decide el oído de Geral, no coqui.
5. **El fill de 12 pasos:** cabe exacto en un compás de la rejilla de 12. ¿El Traductor lo ofrece como fill del último compás, como hace coqui? Queda para la v1.

## Nota de licencia

Coqui-fm1 es GPL-3.0. Las reglas de este archivo se sacaron mirando los patrones (las cadenas de pasos y los números de `groove.c` y `gen_hub.py`), no copiando su código. Si el Traductor usara código de coqui, heredaría la licencia GPL: no se copia código.

---

*Investigación hecha solo leyendo páginas públicas, sin cuentas ni pagos, sin clonar ni ejecutar nada. 23-nov-2026.*


## Decisiones de Geral — 23-nov-2026 (resuelven las 5 pendientes)

Geral decidió la yubá con su oído. BPM **98** — sweet spot con suficiente aire para el balanceo de la yubá sin perder pegada urbana.

**La vuelta (8 compases):**
- Compases 1–4: vuelta hipnótica y constante para asentar el cuerpo y el swing.
- Compases 5–8: misma base rítmica, pero con pequeña variación de acento en el hi-hat / shaker y espacio abierto para que el quinto responda.

**Patrón por capa (en semicorcheas — 16 pasos por compás, pasos 1-based como él los dio):**

| Capa | Pasos | Rol |
|---|---|---|
| B/C | 1,9 · 5,13 | Base |
| Tambores | 16avos | Motor |
| Quinto | 14–15 / 7–8 | Respuesta |
| 808 | 1,9 (8→9) | Peso |
| Fill | C8: 13–16 | Giro |

En 0-based (como usa el paper): B/C [0,4,8,12]; Quinto [13,14] / [6,7]; 808 [0,8] (con el desplazamiento 8→9); Fill en el compás 8 [12,13,14,15].

Nota: Geral da el patrón en 16 pasos por compás, no en los 12 de la rejilla 6/8 propuesta en este documento. Eso queda por resolver al construir: o la yubá vive en la rejilla de 16 con estos pasos, o se traduce a 12.
