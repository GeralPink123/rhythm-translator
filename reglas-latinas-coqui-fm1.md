# Reglas rítmicas latinas — sacadas de coqui-fm1

**Fecha:** 23 de noviembre de 2026
**Repo:** https://github.com/saulofilho/coqui-fm1 — firmware de groovebox latina/caribeña para el aparato M-VAVE FM-1, con emulador en el navegador. Licencia GPL-3.0. Creado 5-nov-2026, 0 estrellas, último cambio 17-nov-2026.
**Para qué:** el proyecto Traductor de Ritmos de Geral Pink (convertir tu patrón a un género destino con reglas claras, gratis, en el navegador).

## Cómo se sacaron las reglas

Se leyó el código y la documentación del repo, sin ejecutar nada y sin copiar código:

- **Los pasos de cada género** salen de `firmware/src/groove.c`, tabla `GR_STYLE` (los 14 estilos, cada uno con sus carriles y su fill). El propio archivo dice que los ritmos son "las claves, el tumbao, el martillo, el dembow, la bomba y la plena", escritos para COQUÍ y simplificados a lo que cabe en un loop de dos compases.
- **El BPM de cada género** sale de `tools/gen_hub.py`, tabla `STYLE_PLAY` (el tempo que el autor eligió para grabar la demo de cada estilo). Ojo: son tempos de demostración del autor, no tempos oficiales de cada género.
- **Las descripciones en palabras** salen de `MANUAL.md`, sección 2 ("GROOVE: the styles").
- **Los nombres de los 27 tambores** salen de `hub/coqui-data.js` (generado desde `groove.c` y `perc.c`): KICK, HAT, SNARE, OPEN HAT, CLAP, MARACAS, CASCARA, COWBELL, BELL, CLAVE, WOOD, GUIRO, TUMBA, SLAP, CONGA, MUTE, QUINTO, GUIRO LONG, BONGO LO, BONGO HI, BONGO SLAP, TIMBAL LO, PAILA, TIMBAL HI, BOOM, RIM, TAMBORA.

## Cómo leer las tablas

- **Rejilla de 16 pasos, base 0:** el paso 0 es el tiempo 1, el 4 es el tiempo 2, el 8 es el tiempo 3, el 12 es el tiempo 4. Cada género tiene DOS compases (32 pasos en el repo); aquí se da el compás 1 (pasos 0–15, el formato del Traductor) y el compás 2 (pasos 16–31) cuando cambia.
- **Fuerza del golpe:** `X` = acento (fuerte), `x` = golpe normal, `o` = suave, `g` = fantasma (muy suave). En el repo las velocidades son X=170, x=130, o=90, g=50.
- **Fill:** un compás de 16 pasos que suena en el último compás de cada 2, 4 u 8 compases. No es obligatorio para el Traductor, pero está aquí por si hace falta.
- **FEEL:** el swing propio del estilo (0 = derecho; más alto = más suelto). Solo 5 estilos tienen feel distinto de 0: son 14, bomba sica 22, bomba yubá 18, plena 16, cumbia 12.
- **BOMBA YUBÁ es 6/8:** no tiene 32 pasos sino 24 (tresillos de corchea, dos compases de 6). No cabe en una rejilla de 16.

---

## 1. DEMBOW — 95 BPM, feel 0

Reggaetón: el bombo en cada tiempo, la caja con el tresillo 3+3+2.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Bombo | 0X, 4x, 8X, 12x | 16X, 20x, 24X, 28x |
| Caja | 3x, 6X, 11x, 14X | 19x, 22X, 27x, 30X |
| Hi-hat cerrado | corcheas: 0o, 2x, 4o, 6x, 8o, 10x, 12o, 14x | igual: 16o, 18x, 20o, 22x, 24o, 26x, 28o, 30x |
| Palmas | 6x | 20x, 30x |
| Timbal agudo | — | 31X |

**Fill (1 compás):** caja 3x, 6x, 11x, 14x; palmas 8x, 10x, 12x, 13x, 14X, 15X; timbal grave 12X, 14X.

**Patrón clave:** bombo en los 4 tiempos [0, 4, 8, 12] + caja en [3, 6, 11, 14] (tresillo 3+3+2). El compás 2 es igual al 1, salvo las palmas y el timbal del final.

**Cruce con lo anotado:** ✅ **CONFIRMA EXACTO** el dembow canónico que ya teníamos (bombo [0,4,8,12], caja [3,6,11,14]). Cuarta fuente que dice lo mismo: Dembow Lab, ToMIDI, el desarme directo y ahora coqui-fm1.

---

## 2. PERREO — 92 BPM, feel 0

El reggaetón viejo (old-school): el boom 808 grave, el rim con el dembow, un hi-hat abierto.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Boom 808 | 0X, 8x | 16X, 24x |
| Bombo | 4x, 12x | 20x, 28x |
| Rim | 3x, 6X, 11x, 14X | 19x, 22X, 27x, 30X |
| Hi-hat cerrado | corcheas: 0x–14x (todas) | igual: 16x–30x (todas) |
| Hi-hat abierto | 14x | 30x |
| Caja | 6X | 22X |

**Fill (1 compás):** rim 0x, 2x, 4x, 6x, 8x, 10x, 11x, 12x, 13x, 14X, 15X; boom 0X.

**Patrón clave:** el RIM lleva el dembow [3, 6, 11, 14]; el boom grave marca 1 y 3 [0, 8]; el bombo marca 2 y 4 [4, 12]. No es lo mismo que el dembow: aquí el bombo no va en los 4 tiempos.

**Cruce con lo anotado:** ➕ **AÑADE** un género nuevo. El perreo es la variante vieja del dembow; el Traductor lo puede ofrecer como destino aparte (o como variante del dembow).

---

## 3. SALSA 2-3 — 100 BPM, feel 0

Clave de son 2-3, cencerro, güiro, tumbao de conga, martillo de bongó.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Clave | 4X, 8X | 16X, 22X, 28X |
| Cencerro | 0X, 4x, 8X, 12x | 16X, 20x, 24X, 28x |
| Güiro | 0X, 2x, 3x, 4X, 6x, 7x, 8X, 10x, 11x, 12X, 14x, 15x | igual: 16X, 18x, 19x, 20X, 22x, 23x, 24X, 26x, 27x, 28X, 30x, 31x |
| Conga apagada (fantasmas) | 0g, 2g, 6g, 8g, 10g, 14g | 16g, 18g, 22g, 24g, 26g, 30g |
| Slap (conga) | 4X | 20X |
| Conga abierta | 12x, 14x | 28x, 30x |
| Tumba | 14X | 30X |
| Bongó agudo (martillo) | 0x, 2o, 4x, 6o, 8x, 10o, 12x, 14o | 16x, 18o, 20x, 22o, 24x, 26o, 28x, 30o |
| Bongó grave | 14x | 30x |
| Bombo | 6o, 14x | 22o, 30x |

**Fill (1 compás):** timbal agudo 0x, 2x, 4x, 6x, 7x, 8x, 10x, 12X; timbal grave 10x, 12X, 14X; paila 0x, 2x, 4x, 6x; bongó agudo — (vacío).

**Patrón clave:** clave 2-3 = [4, 8] + [16, 22, 28] (dos compases). Tumbao: conga abierta [12, 14] + tumba [14]. Martillo de bongó en corcheas (golpe en los tiempos, suave en los contratiempos).

---

## 4. SALSA 3-2 — 100 BPM, feel 0

Clave 3-2 y la cáscara del timbal (el verso).

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Clave | 0X, 6X, 12X | 20X, 24X |
| Cáscara (timbal) | 0X, 2x, 4x, 5X, 7x, 9X, 11x, 12X, 14x | 16X, 18x, 20x, 21X, 23x, 25X, 27x, 28X, 30x |
| Güiro | 0X, 2x, 3x, 4X, 6x, 7x, 8X, 10x, 11x, 12X, 14x, 15x | igual que en 2-3 |
| Conga apagada (fantasmas) | 0g, 2g, 6g, 8g, 10g, 14g | 16g, 18g, 22g, 24g, 26g, 30g |
| Slap (conga) | 4X | 20X |
| Conga abierta | 12x, 14x | 28x, 30x |
| Tumba | 14X | 30X |
| Bongó agudo (martillo) | 0x, 2o, 4x, 6o, 8x, 10o, 12x, 14o | igual que en 2-3 |
| Bombo | 6o, 14x | 22o, 30x |

**Fill (1 compás):** timbal agudo 0x, 2x, 4x, 6x, 8x, 9x, 10x, 11x, 12X; timbal grave 12X, 14X; cáscara — (vacía).

**Patrón clave:** clave 3-2 = [0, 6, 12] + [20, 24] (dos compases). Todo lo demás es igual que en salsa 2-3, salvo que el cencerro se cambia por la cáscara.

**Cruce con lo anotado:** ❌ **CONTRADICE** el anchor propuesto en `rea-tomidi-analisis.md` (salsa clave [0,3,7,8,12]). La clave de son 3-2 correcta, según coqui, es [0, 6, 12] en el compás 1 (tiempos 1, "y" del 2, y 4) y [20, 24] en el compás 2 (tiempos 2 y 3). La propuesta [0,3,7,8,12] no es la clave de son: hay que corregirla.

---

## 5. SON — 90 BPM, feel 14

Son montuno: más lento, martillo de bongó, maracas, clave, tumbao ligero. Sin campana ni cencerro.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Clave | 4X, 8X | 16X, 22X, 28X |
| Maracas | 0x, 2o, 4x, 6o, 8x, 10o, 12x, 14o | igual |
| Bongó agudo (martillo) | 0X, 2g, 4x, 6g, 8X, 10g, 12x, 14g | 16X, 18g, 20x, 22g, 24X, 26g, 28x, 30g |
| Bongó grave | 10x, 14X | 26x, 30X |
| Bongó slap | 6x | 22x |
| Conga apagada (fantasmas) | 0g, 6g, 8g, 14g | 16g, 22g, 24g, 30g |
| Conga abierta | 12x, 14x | 28x, 30x |
| Güiro | 0X, 4x, 6x, 8X, 12x, 14x | 16X, 20x, 22x, 24X, 28x, 30x |

**Fill (1 compás):** bongó agudo 0x, 2x, 4x, 6x, 8x, 9x, 10x, 12X; bongó grave 10x, 12X, 14X.

**Patrón clave:** clave 2-3 [4, 8] + [16, 22, 28]; martillo de bongó en corcheas con acentos en 1 y 3 y fantasmas en los contratiempos; el güiro es más ralo que en la salsa. El más lento de todos (90 BPM) y con feel 14 (un poco suelto).

**Cruce con lo anotado:** ➕ **AÑADE** un género nuevo. El son es la raíz de la salsa; el Traductor lo puede ofrecer como destino aparte.

---

## 6. MAMBO — 106 BPM, feel 0

La campana de mambo (2-3), el cencerro, el tumbao completo, un bombo caliente.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Clave | 4X, 8X | 16X, 22X, 28X |
| Campana (mambo bell) | 0X, 2x, 4X, 6x, 7x, 8X, 10x, 12x, 13X, 15x | 16X, 18x, 20X, 22x, 23x, 24X, 26x, 28x, 29X, 31x |
| Cencerro | 0X, 8X | 16X, 24X |
| Güiro | 0X, 2x, 3x, 4X, 6x, 7x, 8X, 10x, 11x, 12X, 14x, 15x | igual que en salsa |
| Conga apagada (fantasmas) | 0g, 2g, 6g, 8g, 10g, 14g | 16g, 18g, 22g, 24g, 26g, 30g |
| Slap (conga) | 4X | 20X |
| Conga abierta | 12x, 14x | 28x, 30x |
| Tumba | 14X | 30X |
| Timbal grave | 15X | 31X |
| Bombo | 6x, 14X | 22x, 30X |

**Fill (1 compás):** timbal agudo 0x, 2x, 4x, 5x, 6x, 8x, 10x, 12X; timbal grave 6x, 8x, 9x, 11x, 12X, 14X; campana — (vacía).

**Patrón clave:** la campana de mambo lleva el ritmo (10 golpes por compás); el cencerro marca 1 y 3 [0, 8]; el timbal grave remata en el "y" del 4 [15, 31]; el bombo es más fuerte que en la salsa (acento en 14 y 30).

**Cruce con lo anotado:** ➕ **AÑADE** un género nuevo. El mambo comparte el tumbao con la salsa pero la campana lo hace otro género.

---

## 7. CHACHA — 118 BPM, feel 0

Cha-cha-chá: uno, dos, cha-cha-chá en el güiro y la campana.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Güiro | 0X, 4X, 8X, 12X, 14x | 16X, 20X, 24X, 28X, 30x |
| Cencerro | 0X, 4x, 8X, 12x | 16X, 20x, 24X, 28x |
| Clave | 4X, 8X | 16X, 22X, 28X |
| Conga apagada (fantasmas) | 0g, 4g, 8g | 16g, 20g, 24g |
| Slap (conga) | 4X | 20X |
| Conga abierta | 12x, 14x | 28x, 30x |
| Timbal grave | 12x, 14x | 28x, 30x |

**Fill (1 compás):** timbal agudo 0x, 2x, 4x, 6x, 8x, 10x, 12X; timbal grave 12x, 14X.

**Patrón clave:** el "cha-cha-chá" es el güiro en [12, 14] (tiempo 4 y su "y"). El resto es el mismo esqueleto que la salsa (clave 2-3, tumbao [12, 14]).

**Cruce con lo anotado:** ➕ **AÑADE** un género nuevo. Fácil de hacer en el Traductor: es la salsa con el güiro cambiado al cha-cha-chá.

---

## 8. BOMBA SICA — 108 BPM, feel 22

Bomba sicá: los palitos del cuá, el buleador grave, el subidor.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Cuá (madera, palitos) | 0X, 2x, 4X, 6x, 7x, 8X, 10x, 12X, 14x, 15x | 16X, 18x, 20X, 22x, 23x, 24X, 26x, 28X, 30x, 31x |
| Tumba (buleador) | 0X, 6x, 8X, 14x | 16X, 22x, 24X, 30x |
| Conga apagada (fantasmas) | 3g, 5g, 11g, 13g | 19g, 21g, 27g, 29g |
| Maracas | 0x, 2o, 4x, 6o, 8x, 10o, 12x, 14o | igual |
| Quinto (subidor) | 10X, 14X | 20x, 26X, 28X |
| Slap (conga) | 12X | 28X |

**Fill (1 compás):** quinto 0X, 2x, 4X, 6x, 8X, 10x, 11x, 12X, 14X; tumba 0X, 8X.

**Patrón clave:** el cuá lleva el ritmo (10 golpes por compás, parecido a la campana de mambo pero con otro acento); el buleador grave marca [0, 6, 8, 14]; el quinto (subidor) improvisa en los huecos. Feel 22: el más suelto de todos.

**Cruce con lo anotado:** ➕ **AÑADE** un género que no existe en ninguna otra herramienta. La bomba SOLO existe en coqui-fm1: es el hueco dentro del hueco.

---

## 9. BOMBA YUBA — 100 BPM, feel 18 — ⚠️ EN 6/8, NO EN 4/4

Bomba yubá en 6/8. Un paso es un tresillo de corchea; dos compases de 6. **24 pasos en total, no 32.**

| Carril (instrumento) | Compás 1 (pasos 0–11) | Compás 2 (pasos 12–23) |
|---|---|---|
| Cuá (madera, palitos) | 0X, 2x, 3X, 5x, 6X, 8x, 9X, 11x | 12X, 14x, 15X, 17x, 18X, 20x, 21X, 23x |
| Tumba (buleador) | 0X, 3x, 6X, 9x | 12X, 15x, 18X, 21x |
| Conga apagada (fantasmas) | 1g, 5g, 7g, 11g | 13g, 17g, 19g, 23g |
| Maracas | 0x, 2x, 3x, 5x, 6x, 8x, 9x, 11x | 12x, 14x, 15x, 17x, 18x, 20x, 21x, 23x |
| Quinto (subidor) | 6X, 9x | 18X, 20X |

**Fill (12 pasos):** quinto 0X, 2x, 3X, 5x, 6X, 8x, 9X, 10X, 11X; tumba 0X, 6X.

**Patrón clave:** la tumba marca cada 3 tresillos [0, 3, 6, 9] (el pulso del 6/8); el cuá marca el patrón en tresillos. No se puede meter en una rejilla de 16 pasos sin cambiar la rejilla.

**Cruce con lo anotado:** ➕ **AÑADE** un género que no existe en ninguna otra herramienta, PERO exige una decisión: el Traductor trabaja en 16 pasos (4/4). La yubá necesita una rejilla de 24 pasos en 6/8, o no se puede traducir a ella. Queda por decidir.

---

## 10. PLENA — 104 BPM, feel 16

Plena: el seguidor, el punteador en los contratiempos, el requinto.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Tumba (seguidor, grave) | 0X, 6x, 8X, 14x | 16X, 22x, 24X, 30x |
| Conga apagada (punteador) | 2x, 6x, 10x, 14x | 18x, 22x, 26x, 30x |
| Conga abierta | 6x, 14x | 22x, 30x |
| Quinto (requinto) | 0x, 3x, 6x, 12x, 14x | 20x, 23x, 26X |
| Güiro | 0X, 2x, 4X, 6x, 8X, 10x, 12X, 14x | igual |

**Fill (1 compás):** quinto 0x, 2x, 4x, 5x, 6X, 8x, 10x, 12X, 14X; tumba 0X, 8X, 12X.

**Patrón clave:** el seguidor grave [0, 6, 8, 14]; el punteador en los contratiempos [2, 6, 10, 14]; el tresillo 3+3+2 aparece en el requinto del compás 1 [0, 3, 6].

**Cruce con lo anotado:** ⚠️ **PARCIAL**. La propuesta en `rea-tomidi-analisis.md` era pandero grave [0,6,12] y pandero agudo [3,9]. Coqui no tiene un solo pandero sino tres tambores: el seguidor es [0, 6, 8, 14] (el 0 y el 6 coinciden, el 12 no), el punteador es [2, 6, 10, 14] (no [3, 9]), y el tresillo 3+3+2 solo aparece en el requinto [0, 3, 6]. La idea del 3+3+2 va por buen camino, pero los pasos exactos hay que corregirlos y oírlos.

---

## 11. MERENGUE — 128 BPM, feel 0

Merengue: el raspado de la güira, el palo y la mano de la tambora.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Hi-hat cerrado (güira) | 0X, 2x, 3x, 4X, 6x, 7x, 8X, 10x, 11x, 12X, 14x, 15x | igual |
| Cáscara | 0x, 2x, 4x, 6x, 7x, 8x, 10x, 12x, 14x, 15x | 16x, 18x, 20x, 22x, 23x, 24x, 26x, 28x, 30x, 31x |
| Tambora | 4x, 12X, 14X | 20x, 28X, 30X |
| Bombo | 0X, 8x | 16X, 24x |
| Cencerro | 0X, 4x, 8X, 12x | 16X, 20x, 24X, 28x |

**Fill (1 compás):** tambora 0x, 2x, 4x, 6x, 8x, 10x, 11x, 12X, 14X; cáscara 0x–11x (16avos corridos, 12 golpes).

**Patrón clave:** la güira con el patrón X.xx [0, 2, 3, 4, 6, 7, 8, 10, 11, 12, 14, 15]; la tambora en [4, 12, 14] (tiempos 2 y 4, más el "y" del 4); el bombo en 1 y 3 [0, 8].

**Cruce con lo anotado:** ❌ **CONTRADICE** la propuesta en `rea-tomidi-analisis.md` (tambora [0,8], güira en 16avos, 160 BPM). Según coqui la tambora NO va en [0, 8] sino en [4, 12, 14]; la güira no son 16avos parejos sino el patrón X.xx; y el BPM es 128, no 160. El merengue rápido de verdad sí llega a 150–160, así que el 128 es el tempo de la demo de coqui, no una regla: el BPM final lo decide el oído de Geral.

---

## 12. BACHATA — 124 BPM, feel 0

Bachata: el martillo del bongó con su cuarto tiempo, la güira.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Bongó agudo (martillo) | 0X, 2x, 4x, 6x, 8X, 10x, 12x, 14x | 16X, 18x, 20x, 22x, 24X, 26x, 28x, 30x |
| Bongó grave | 12X | 28X |
| Bongó slap | 14x | 30x |
| Hi-hat cerrado (güira) | 0x, 2x, 3x, 4x, 6x, 7x, 8x, 10x, 11x, 12x, 14x, 15x | igual |
| Bombo | 0X, 8X | 16X, 24X |

**Fill (1 compás):** bongó agudo 0x, 2x, 4x, 6x, 8x, 10x, 11x, 12X; bongó grave 12x, 14X.

**Patrón clave:** el martillo es el bongó agudo en corcheas [0, 2, 4, 6, 8, 10, 12, 14], con acento en 1 y 3; el bongó grave marca el 4º tiempo [12]; el slap va en el "y" del 4 [14]; el bombo marca 1 y 3 [0, 8].

**Cruce con lo anotado:** ❌ **CONTRADICE** la propuesta en `rea-tomidi-analisis.md` (martillo [3,7,11,15]). Según coqui el martillo NO va en los contratiempos [3, 7, 11, 15] sino en corcheas derechas [0, 2, 4, 6, 8, 10, 12, 14]. Hay que corregir la propuesta.

---

## 13. CUMBIA — 96 BPM, feel 12

Cumbia: la guacharaca, el llamador en los contratiempos, la tambora.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Güiro (guacharaca) | 0X, 2x, 3x, 4X, 6x, 7x, 8X, 10x, 11x, 12X, 14x, 15x | igual |
| Conga (llamador) | 4X, 12X | 20X, 28X |
| Conga apagada (fantasmas) | 2g, 6g, 10g, 14g | 18g, 22g, 26g, 30g |
| Tumba (tambora) | 0X, 8x | 16X, 24x |
| Maracas | 0x, 2x, 4x, 6x, 8x, 10x, 12x, 14x | igual |

**Fill (1 compás):** tumba 0X, 4X, 8X, 10x, 12X, 14X; conga 4x, 12x, 14x, 15x.

**Patrón clave:** la guacharaca con el patrón X.xx (acento en 0, 4, 8, 12); el llamador en 2 y 4 [4, 12]; la tambora en 1 y 3 [0, 8]; las maracas en corcheas.

**Cruce con lo anotado:** ⚠️ **PARCIAL**. La propuesta era caja [4,8,12] y guacharaca en 16avos. Coqui dice: el llamador es [4, 12] (el 8 no está) y la guacharaca no son 16avos parejos sino el patrón X.xx [0, 2, 3, 4, 6, 7, 8, 10, 11, 12, 14, 15]. La idea va bien, los pasos exactos hay que corregirlos.

---

## 14. SOCA — 126 BPM, feel 0

Soca: bombo en los 4 tiempos, el hierro (cencerro) llevando el ritmo, la caja en 2 y 4.

| Carril (instrumento) | Compás 1 (pasos 0–15) | Compás 2 (pasos 16–31) |
|---|---|---|
| Bombo | 0X, 4X, 8X, 12X | 16X, 20X, 24X, 28X |
| Caja | 4x, 12x | 20x, 28x |
| Cencerro (hierro) | 0x, 2x, 3x, 4x, 6x, 7x, 8x, 10x, 11x, 12x, 14x, 15x | igual |
| Hi-hat abierto | 2x, 6x, 10x, 14x | 18x, 22x, 26x, 30x |
| Conga abierta | 6x, 14x | 16x, 22x, 30x |

**Fill (1 compás):** caja 0x, 2x, 4x, 6x, 8x, 9x, 10x, 11x, 12X, 13X, 14X, 15X; timbal agudo 12X.

**Patrón clave:** bombo en los 4 tiempos (four on the floor); caja en 2 y 4 [4, 12]; el hierro con el patrón X.xx; el hi-hat abierto en los contratiempos [2, 6, 10, 14].

**Cruce con lo anotado:** ➕ **AÑADE** un género nuevo. La soca es caribeña, no latina en sentido estricto, pero el repo la trae y sirve como destino.

---

## Resumen: los 14 géneros de un vistazo

| Género | BPM | Compases | Carriles | Patrón clave (compás 1) |
|---|---|---|---|---|
| DEMBOW | 95 | 2 × 16 | 5 | bombo [0,4,8,12] + caja [3,6,11,14] |
| PERREO | 92 | 2 × 16 | 6 | rim dembow [3,6,11,14] + boom [0,8] |
| SALSA 2-3 | 100 | 2 × 16 | 10 | clave [4,8] + tumbao [12,14] |
| SALSA 3-2 | 100 | 2 × 16 | 9 | clave [0,6,12] + cáscara |
| SON | 90 | 2 × 16 | 8 | clave 2-3 + martillo con fantasmas |
| MAMBO | 106 | 2 × 16 | 10 | campana [0,2,4,6,7,8,10,12,13,15] |
| CHACHA | 118 | 2 × 16 | 7 | güiro cha-cha-chá [12,14] |
| BOMBA SICA | 108 | 2 × 16 | 6 | cuá + buleador [0,6,8,14] |
| BOMBA YUBA | 100 | 2 × 6/8 | 5 | 24 pasos, tumba [0,3,6,9] |
| PLENA | 104 | 2 × 16 | 5 | seguidor [0,6,8,14] + requinto [0,3,6] |
| MERENGUE | 128 | 2 × 16 | 5 | güira X.xx + tambora [4,12,14] |
| BACHATA | 124 | 2 × 16 | 5 | martillo corcheas + grave en [12] |
| CUMBIA | 96 | 2 × 16 | 5 | guacharaca X.xx + llamador [4,12] |
| SOCA | 126 | 2 × 16 | 5 | bombo 4/4 + caja [4,12] + hierro |

**Patrones que se repiten en varios géneros (atajos para el Traductor):**
- El güiro/guacharaca X.xx [0, 2, 3, 4, 6, 7, 8, 10, 11, 12, 14, 15] es el mismo en salsa 2-3, salsa 3-2, mambo, cumbia, merengue y bachata (en bachata y merengue va en el carril del hi-hat cerrado).
- El tumbao de conga [12, 14] + tumba [14] es el mismo en salsa 2-3, salsa 3-2, mambo, son y chacha.
- El martillo de bongó en corcheas es el mismo en salsa y en son (en son con fantasmas).
- La clave 2-3 [4, 8] + [16, 22, 28] es la misma en salsa 2-3, son, mambo y chacha.

**Extra del repo (no son patrones de tambor, pero sirven después):** las líneas del ARP en `firmware/src/arp.c` (también en `hub/coqui-data.js`, campo `lines`): TUMBAO (bajo de salsa), MONTUNO (piano), GUAJEO (tres), MERENGUE (bajo) y BACHATA (bajo), cada una de 32 pasos. Para cuando el Traductor quiera traducir también líneas de bajo o de piano, no solo tambores.

**División de los tambores en 3 grupos (del repo, `groove.c`, función `gr_sect`):** KIT = bombo, caja, hi-hats, palmas, rim, boom; MANOS = congas, bongós, timbales, tambora, quinto, tumba, slap; CAMPANA = campanas, clave, cuá, güiro, maracas, cáscara. Sirve para la tabla de mapeo del Traductor (qué instrumento de origen se cambia por cuál de destino).

---

## Qué se incorpora al Traductor y qué queda por confirmar

### Se incorpora (verificado por coqui-fm1)

1. **Dembow canónico, cuarta confirmación:** bombo [0,4,8,12] + caja [3,6,11,14]. Ya no hay duda.
2. **Clave 3-2 correcta:** [0, 6, 12] en el compás 1 y [20, 24] en el compás 2. Corrige la propuesta [0,3,7,8,12] de `rea-tomidi-analisis.md`.
3. **Clave 2-3:** [4, 8] en el compás 1 y [16, 22, 28] en el compás 2.
4. **Martillo de bachata:** bongó agudo en corcheas [0, 2, 4, 6, 8, 10, 12, 14], grave en el 4º tiempo [12], slap en [14]. Corrige la propuesta [3,7,11,15].
5. **Tumbao de conga (salsa, mambo, son, chacha):** conga abierta [12, 14] + tumba [14]; bombo de salsa [6, 14] (no [0, 8]).
6. **Patrón de güiro/guacharaca X.xx:** [0, 2, 3, 4, 6, 7, 8, 10, 11, 12, 14, 15], con acento en 0, 4, 8, 12. Vale para salsa, mambo, cumbia, merengue y bachata.
7. **Tambora de merengue:** [4, 12, 14] (no [0, 8]).
8. **Llamador de cumbia:** [4, 12]; tambora de cumbia [0, 8]; maracas en corcheas.
9. **Cha-cha-chá:** güiro [12, 14] (tiempo 4 y su "y").
10. **Perreo como destino aparte:** el rim lleva el dembow, el boom 808 en 1 y 3, el bombo en 2 y 4.
11. **Son como destino aparte:** 90 BPM, clave 2-3, martillo con fantasmas, sin campana.
12. **Mambo como destino aparte:** campana de mambo + cencerro en 1 y 3 + timbal grave en [15, 31].
13. **Bomba sica:** cuá + buleador [0, 6, 8, 14] + quinto. La bomba SOLO existe en este repo.
14. **Plena:** seguidor [0, 6, 8, 14], punteador [2, 6, 10, 14], requinto con el tresillo [0, 3, 6].
15. **Soca como destino aparte:** bombo en los 4 tiempos + caja [4, 12] + hierro X.xx.
16. **Velocidades para el mapa del Traductor:** X = acento, x = normal, o = suave, g = fantasma (en el repo: 170 / 130 / 90 / 50).
17. **BPM de demostración por género** (tabla de arriba). Son el punto de partida, no la última palabra.
18. **Los fills de un compás** por género (en cada tabla), por si el Traductor los usa para el último compás.

### Queda por confirmar con el oído de Geral

1. **La clave 3-2 [0, 6, 12]:** la propuesta anterior era distinta ([0,3,7,8,12]). Hay que oír cuál suena a salsa de verdad.
2. **El martillo de bachata en corcheas derechas:** la propuesta anterior lo ponía en los contratiempos ([3,7,11,15]). Hay que oír cuál suena a bachata.
3. **El BPM del merengue:** coqui usa 128, la propuesta decía 160. El merengue rápido de verdad va 150–160. Geral decide el BPM final.
4. **La plena:** el 3+3+2 exacto no aparece como un solo carril en coqui; son tres tambores trabajando juntos. Hay que oír si el conjunto suena a plena o si falta el pandero.
5. **La bomba yubá en 6/8:** ¿el Traductor soporta una rejilla de 24 pasos, o la yubá se queda fuera de la primera versión? Decisión de diseño.
6. **Los BPM de todos los géneros:** son tempos de demostración del autor de coqui, no tempos canónicos. Geral decide el BPM final de cada destino (ej.: bachata 124 en coqui vs 128 en la batería de Jorge Nasser; dembow 95 en coqui vs 92 en Nasser).
7. **Los sonidos de cada carril:** coqui suena con los tambores sintetizados del FM-1. El Traductor necesita sus propios sonidos (sintetizados o con samples) para clave, conga, timbal, güiro, tambora, cuá, quinto, etc. Las reglas dicen DÓNDE va cada golpe, no CÓMO suena.
8. **El son a 90 BPM y el mambo a 106:** confirmar que esos tempos se sienten bien (el son es el más lento; el mambo un poco más rápido que la salsa).
9. **El quinto de la bomba y la plena:** en coqui el quinto improvisa (subidor/requinto). En el Traductor, ¿el quinto es fijo o varía? Geral decide.

---

## Cuidado de licencia

**Esto es estudiar, no copiar código GPL.** Las reglas de este archivo se sacaron observando qué pasos suenan y con qué instrumento en el repo coqui-fm1, y están escritas con palabras propias. **Si el Traductor de Ritmos copiara código de coqui-fm1, heredaría la licencia GPL-3.0** y tendría que publicarse bajo esa misma licencia. Las reglas rítmicas en sí (qué golpe va en qué paso) son hechos musicales, no código.

**De qué archivo salió cada cosa:**
- Todos los pasos, carriles, fills y feels: `firmware/src/groove.c`, tabla `GR_STYLE` (14 estilos).
- Todos los BPM: `tools/gen_hub.py`, tabla `STYLE_PLAY` (tempo de demostración por estilo).
- Las descripciones en palabras de cada estilo: `MANUAL.md`, sección 2 ("GROOVE: the styles").
- Los nombres de los 27 tambores: `hub/coqui-data.js`, campo `keys` (generado desde `groove.c` y `perc.c`).
- Las líneas de bajo/piano (extra): `firmware/src/arp.c`, tabla `ARP_LAT` (también en `hub/coqui-data.js`, campo `lines`).
- La división en 3 grupos (KIT / MANOS / CAMPANA): `firmware/src/groove.c`, función `gr_sect`.
- Las velocidades X/x/o/g (170/130/90/50): `firmware/src/groove.c`, función `gr_vel`.

*Investigación hecha el 23-nov-2026, solo lectura, sin ejecutar nada del repo. No se copió código de coqui-fm1.*
