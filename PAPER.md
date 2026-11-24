# Traductor de Ritmos

**Autor:** Geral Pink
**Fecha:** 24 de noviembre de 2026
**Estado:** papel de trabajo. El repo ya existe y es público: https://github.com/GeralPink123/rhythm-translator

## Qué es esto

Yo hago un patrón de ritmo de un género y quiero oírlo en otro género. Eso es todo. Pegas tu patrón, eliges el género destino y sale traducido. Y ves las reglas. Las reglas se pueden leer, no hay una caja negra que decide por ti. No hay inteligencia artificial en la nube. Es gratis, corre en el navegador, sin cuenta y sin instalar nada.

No quiero un resuelve, quiero algo que deje huellas. Por eso lo estoy haciendo en público, con el código abierto y con este papel que explica cada paso.

## El hueco

No existe una herramienta que haga esto. Lo buscamos el 23 de noviembre de 2026 con cinco agentes buscando a la vez por todo internet. GitHub, productos, papeles académicos, redes, foros. En español y en inglés.

"Drum pattern translator" da cero resultados en el buscador de GitHub. El tema drum-pattern está vacío, cero repositorios. En Reddit nadie preguntó por algo así en los foros de producción, de reguetón ni de batería.

Lo más cercano es un papel de 2020 que se llama Groove2Groove. Tiene 175 estrellas. Es una red neuronal. No explica nada de lo que hace, pide un Python viejo con TensorFlow viejo, no tiene producto y no tiene géneros latinos. Le puse 6 de 10.

El segundo es Magenta Interpolate, de Google. Mezcla dos patrones, pero no traduce tu patrón a un género con nombre. Le puse 5 de 10.

El tercero es ToMIDI. Genera patrones con reglas escritas a mano, gratis y en el navegador. Pero no acepta tu patrón para convertirlo. Le puse 4 de 10.

El hueco es triple. Nadie convierte con reglas que se puedan leer. Todo lo que existe usa redes neuronales que no dicen por qué cambiaron nada. Nadie lo vende como producto en el navegador. Todo lo cercano es un programa de terminal, un plugin de pago, un aparato o un papel sin código que se pueda usar. Y nadie cubre lo latino como destino. Ningún papel de transferencia tiene dembow, salsa, plena, bachata ni reguetón como género objetivo. La plena no aparece como destino en ninguna herramienta que encontramos.

Y la gente lo necesita. En 2008, en un foro que se llama Gearspace, un productor pidió un MIDI de dembow. La respuesta fue que le compartieran unos loops. Pasaron 18 años y la solución sigue siendo la misma: hacerlo a mano o pedir loops prestados. Miles de tutoriales enseñan a hacer un dembow a mano. Los DJs pasan de trap a dembow en vivo, mezclando, porque no existe una herramienta que lo haga.

Una cosa con honestidad: un producto muy nuevo, sin posicionamiento, en otro idioma, o que viva solo en un Discord, pudo no aparecer en las búsquedas. No encontramos indicios de ninguno. Tampoco probamos en vivo si la demo de Groove2Groove sigue funcionando, ni probamos los productos de pago.

## Lo que aprendí de ToMIDI

ToMIDI es un generador gratis en el navegador, con reglas escritas a mano y sin inteligencia artificial. Tiene 12 géneros, una rejilla de 16 pasos y exporta MIDI, WAV y texto. Es la prueba de que este formato funciona como producto. Gratis, en el navegador, con reglas a mano, sin IA.

De ToMIDI me llevé varias cosas.

El dembow canónico. Bombo en [0, 4, 8, 12] y caja en [3, 6, 11, 14], el tresillo 3+3+2. Cuatro fuentes dicen lo mismo: Dembow Lab, el desarme directo, el análisis con Antigravity y coqui-fm1. Ya no hay duda. ToMIDI lo tiene a 95 BPM. Yo lo quiero a 110, porque así suena el dembow de verdad. Eso lo decide mi oído.

Los anclajes. Cada género define pasos fijos que nunca cambian, y eso garantiza que el patrón siempre suene al género. Por ejemplo, en el rock la caja va en [4, 12]. En el house el bombo va en [0, 4, 8, 12]. En el trap la palmada va sola en el paso 8.

La representación intermedia. Un patrón es una tabla de pasos por instrumento. Cero es silencio, uno es fantasma, dos es normal, tres es acento. Más una tabla de redobles. Esa es la representación que necesita el Traductor. El patrón entra como tabla, se traduce como tabla y sale como tabla.

Las fórmulas exactas. El swing empuja cada segunda semicorchea hacia adelante según el porcentaje. El humanize mete un temblor pequeño de tiempo y de fuerza, con semilla, así que el mismo patrón siempre da el mismo resultado.

El mapa MIDI. Cada tambor tiene su nota General MIDI. Bombo 36, caja 38, hi-hat cerrado 42, hi-hat abierto 46. Y sus fuerzas: fantasma alrededor de 57, normal alrededor de 102, acento 127.

Lo que ToMIDI no tiene, y yo tengo que resolver: de 12 géneros, solo uno es latino, el reguetón. Faltan salsa, plena, bachata, merengue, cumbia, bomba, dancehall, afrobeat, bossa y samba. Solo tiene compases de 4/4. No hay 6/8 ni 3/4. No tiene tambores latinos, ni clave, ni conga, ni timbal, ni güira, ni pandero. Y no convierte. Genera un patrón nuevo desde cero por género. No acepta tu patrón.

## Lo que aprendí de Magenta

Magenta es el proyecto de investigación abierto de Google. Su herramienta estrella se llama Interpolate. Entran dos patrones MIDI, la red calcula un espacio latente y salen mezclas intermedias suaves. Las demos web son Melody Mixer, Beat Blender y Latent Loops.

De Magenta me llevé la intuición de que mezclar patrones suena musical. El Traductor usa esa intuición, pero cambia el espacio latente opaco por reglas que se ven.

También el control de temperatura. Un slider que va de fiel a tu patrón a más sabor del género. En el Traductor se llama Fidelidad / Creatividad.

También la paleta de opciones. En vez de una sola salida, mostrar varias variantes y que el usuario elija.

También separar el esqueleto de los adornos. Primero dónde caen los golpes, después los adornos, los fantasmas y los fills.

Y la semilla del usuario. El patrón que dibuja el usuario es el ancla. El género destino lo moldea. Y exportar a MIDI y correr en el navegador, claro.

Por qué Magenta no es la solución: no hay géneros latinos con nombre. No hay modelos entrenados en dembow, salsa, plena, bomba, bachata, merengue ni cumbia. No hay traductor de patrón a género destino. Solo interpola entre dos patrones que tú le das. No le puedes decir haz esto sonar a dembow. No hay reglas visibles. La red no explica por qué cambió una nota. Es caja negra. El plugin real exige Ableton Live Suite con Max for Live, de pago. Y el repo principal está archivado desde el 6 de enero de 2026, solo lectura.

La diferencia es simple: Magenta mezcla a ciegas entre dos patrones. El Traductor lleva tu patrón a un género con nombre, con reglas que se leen.

## Las reglas latinas

La referencia es coqui-fm1, un repo en GitHub de saulofilho. Es el firmware de una groovebox latina y caribeña para el aparato M-VAVE FM-1, con emulador en el navegador. Lo crearon el 5 de noviembre de 2026. Tiene cero estrellas. Es el único repo que cubre los géneros destino del Traductor con las reglas visibles en el código: dembow, perreo, salsa 2-3, salsa 3-2, son, mambo, chachachá, bomba sicá, bomba yubá en 6/8, plena, merengue, bachata, cumbia y soca.

Coqui no es competencia. Solo toca los grooves prefijados del aparato. No acepta tu patrón ni lo convierte a nada. Es la referencia para sacar las reglas correctas.

Cuidado con la licencia. Coqui-fm1 es GPL-3.0. Se estudia para entender las reglas, no se copia su código. Si el Traductor copiara código GPL, heredaría esa licencia. Las reglas rítmicas, qué golpe va en qué paso, son hechos musicales, no código.

Las reglas, con los BPM que yo decidí. Rejilla de 16 pasos, base cero. X es acento, x es normal, o es suave, g es fantasma. Velocidades 170, 130, 90 y 50.

| Género | BPM | Patrón clave |
|---|---|---|
| Dembow | 110 | bombo [0, 4, 8, 12] + caja [3, 6, 11, 14] (tresillo 3+3+2) |
| Perreo | 92 | el rim lleva el dembow [3, 6, 11, 14]; boom 808 en [0, 8] |
| Salsa 2-3 | 88 a 94 | clave [4, 8] + [16, 22, 28]; tumbao de conga [12, 14] |
| Salsa 3-2 | 88 a 94 | clave [0, 6, 12] + [20, 24]; cáscara de timbal |
| Son | 90 | clave 2-3 + martillo de bongó con fantasmas |
| Mambo | 112 a 120 | campana de mambo + cencerro en [0, 8] |
| Chachachá | 118 | güiro cha-cha-chá en [12, 14] |
| Bomba sicá | 94 a 102 | cuá + buleador [0, 6, 8, 14] + quinto |
| Bomba yubá | 98 | 6/8, 24 pasos; tumba [0, 3, 6, 9] |
| Plena | 112 a 120 | seguidor [0, 6, 8, 14] + punteador [2, 6, 10, 14] + requinto [0, 3, 6] |
| Merengue | 160 | güira X.xx + tambora [4, 12, 14] |
| Bachata | 124 | martillo en corcheas [0, 2, 4, 6, 8, 10, 12, 14]; grave en [12], slap en [14] |
| Cumbia | 96 | guacharaca X.xx + llamador [4, 12] |
| Soca | 130 a 138 | bombo en los 4 tiempos + caja [4, 12] + hierro X.xx |

Hay atajos que sirven para construir sin repetir trabajo. El patrón de güiro y guacharaca X.xx, pasos [0, 2, 3, 4, 6, 7, 8, 10, 11, 12, 14, 15], es el mismo en salsa, mambo, cumbia, merengue y bachata. El tumbao de conga, pasos [12, 14] más tumba en [14], es el mismo en salsa, mambo, son y chachachá.

Coqui me corrigió cuatro cosas de los anclajes que yo tenía antes.

Primero, la clave de salsa 3-2 es [0, 6, 12] más [20, 24], en dos compases. No [0, 3, 7, 8, 12]. Y el bombo de salsa es [6, 14], no [0, 8].

Segundo, el martillo de bachata son corcheas derechas [0, 2, 4, 6, 8, 10, 12, 14], con grave en [12] y slap en [14]. No [3, 7, 11, 15].

Tercero, la tambora de merengue es [4, 12, 14], no [0, 8]. El BPM de la demo de coqui es 128, pero yo lo subí a 160.

Cuarto, la plena es parcial. El 3+3+2 solo aparece en el requinto [0, 3, 6]. No hay un solo pandero, son tres tambores trabajando juntos. La cumbia también es parcial. El llamador es [4, 12], sin el 8, y la guacharaca no son 16avos parejos sino el patrón X.xx.

## La bomba yubá

La yubá ya está resuelta. La puse a 98 BPM. Es el punto dulce, con aire para el balanceo sin perder pegada urbana. La vuelta es de 8 compases. Los primeros cuatro son hipnóticos y constantes. Del cinco al ocho va la misma base con una variación pequeña de acento en el hi-hat y el shaker, y con espacio abierto para que el quinto responda.

El patrón va por capa, en 16 pasos. La base es B y C en 1, 9, 5 y 13. El motor son los tambores en 16avos. La respuesta es el quinto en 14 y 15, o en 7 y 8. El peso es el 808 en 1 y 9, con el paso de 8 a 9. El giro es el fill del compás 8, en 13, 14, 15 y 16.

Una cosa que todavía tengo que resolver cuando construya: yo di este patrón en 16 pasos, pero la rejilla de 6/8 tiene 12. Eso se trabaja al construir. Lo dejo dicho aquí para no esconderlo.

El problema de fondo: la yubá es 6/8 y tiene 24 pasos, 12 por compás, pero la rejilla del Traductor es de 16 pasos en 4/4. Un compás de dembow se divide en 16 partes iguales y uno de yubá en 12. Son dos maneras distintas de partir el mismo compás.

El dato clave, verificado en el código de coqui: el compás dura lo mismo en 4/4 y en 6/8. 16 pasos por 24 ticks es igual a 12 pasos por 32 ticks, 384 ticks por compás. Cambiar de 4/4 a 6/8 no cambia la duración ni el tempo. Solo cambia en cuántas partes se divide.

La solución es que la rejilla cambia con el género. 16 pasos en 4/4 y 12 pasos en 6/8. En 6/8 la rejilla tiene 12 casillas por compás, agrupadas de 3 en 3, con los dos pulsos grandes marcados. Cuando el usuario elige yubá, la rejilla cambia sola y se ve el cambio. Es lo que hacen las herramientas que ya resolvieron esto.

La prueba, golpe por golpe, de dembow a yubá, compás uno: el bombo del dembow [0, 4, 8, 12] cae exacto sobre la tumba de la yubá [0, 3, 6, 9]. La caja del dembow [3, 6, 11, 14] cae sobre el cuá [2, 5, 8, 11]. El hi-hat del dembow cae exacto sobre las maracas reales [0, 2, 3, 5, 6, 8, 9, 11]. La traducción no se acerca al patrón real. Cae dentro de él. El esqueleto del dembow ya vive dentro de la yubá.

La vuelta, de yubá a dembow, es la dirección floja y lo digo así. El bombo sale perfecto [0, 4, 8, 12], pero la caja sale [3, 7, 11, 15], pareja, no el tresillo 3+3+2 del dembow. Por eso hay una regla visible: los anclajes del género destino siempre suenan, y el Traductor muestra qué golpes se movieron.

Las otras dos opciones las descarté. Aplastar el 6/8 en 16 pasos destruye el ritmo y miente, porque el tresillo se pierde y suena a 4/4 recto. Una rejilla universal de 48 micropasos es exacta por dentro pero no se puede tocar en el teléfono, y la exactitud extra no se oye.

## La clave y el martillo

Esto también quedó resuelto. El martillo de bachata es lo que suena a bachata. La clave 3-2 pertenece a otra familia, la del son y la salsa, y solo sirve como fusión o cruce experimental. Antes tenía las dos mezcladas. Ya no.

## Los sonidos

Las reglas dicen dónde va cada golpe, no cómo suena. El Traductor necesita sonidos reales grabados, no hechos por la computadora. Esa fue mi decisión el 23 de noviembre.

El 24 de noviembre buscamos en 28 fuentes, en tres rondas. Bajamos y verificamos 35 archivos reales. Y lo que encontramos es esto: los siete patrones que faltan existen como datos en coqui-fm1. Con los sonidos reales que ya tenemos se pueden armar seis de siete. Solo la tambora no tiene ni un golpe real en todo GitHub. Es el hueco total.

Los siete que no existen grabados solos en ningún lado son el pandero seguidor, el pandero punteador, el pandero requinto, el güicharo, el cuá, el quinto tocando yubá y la tambora.

La recomendación que me dieron es grabarlos. Todavía no lo he decidido.

La mejor referencia de requinto que encontré es un video de Emmanuel Santana, de la Casa de la Plena Tito Matos, tocando el requinto solo en vivo. Ahí se oye el requinto de verdad, con sus patrones y su improvisación. Ese video es la referencia de sonido del requinto.

## La demo

La demo ya existe y ya funciona. Es un solo archivo HTML, cero dependencias, con sonidos sintetizados en el navegador, sin samples, en español. La actualicé a 110 BPM y la envié por correo el 24 de noviembre a las 7:17 de la mañana. Todavía no la he oído con calma. Cuando la oiga, yo digo si suena a dembow de verdad. Eso no lo decide nadie más.

Qué hace. Primero el original: trap a 140 BPM. Bombo 808 suelto en [0, 7, 10], caja sola en el paso 8, hi-hat en los 16 pasos y hi-hat abierto en [2, 10]. Tiene botón para tocarlo.

Después el botón de traducir a dembow. Sale el dembow a 110 BPM. Bombo en [0, 4, 8, 12], caja en el tresillo [3, 6, 11, 14], hi-hat en corcheas y bajo 808 en [0, 6, 8, 14]. Tiene botón para tocarlo y compararlo con el trap.

Y la explicación en palabras simples: la velocidad baja de 140 a 110, el bombo se ordena en los 4 tiempos, la caja se mueve al tresillo 3+3+2, los hi-hats se calman de 16 a 8 golpes, el hi-hat abierto se quita y se añade el bajo 808 como voz aparte. También tiene botón para bajar el dembow traducido en WAV.

El patrón del dembow es real. Es el preset Dembow clásico de Dembow Lab, a 110 BPM. La demo no es una micro app.

También la verifiqué por dentro: cero errores, audio con duraciones exactas, sin silencios largos ni saturación. Al principio el dembow saturaba, con un pico de 1,42. Lo corregí con ganancias y un limitador, y la segunda medición dio 0,92. El play en vivo con cabezal funciona y el WAV sale con cabecera válida.

## Cómo se construye

El Traductor tiene cuatro partes.

Entrada. Pegas un patrón, importas un MIDI o dibujas en la rejilla. El patrón se guarda como una tabla: qué instrumento suena en qué paso, y con qué fuerza.

Análisis. El Traductor mira tu patrón y saca su estructura: dónde están los golpes fuertes, qué tan lleno está, cuánto swing tiene. Eso se guarda en una forma neutra que no pertenece a ningún género. De ToMIDI tomo la tabla de pasos. De Magenta, la idea de separar el esqueleto de los adornos.

Traducción por reglas. Cada género destino es una plantilla en JSON que cualquiera puede leer y editar: su patrón base, su BPM, su swing, sus instrumentos y sus reglas de transformación. La traducción aplica las reglas paso a paso y explica cada cambio en texto. "Alineé el inicio al compás 1". "Puse el bombo en los 4 tiempos". "Bajé la densidad un 30 por ciento". "Añadí el fill en el compás 4". El slider de Fidelidad y Creatividad, que es la temperatura de Magenta, controla cuánto se respeta tu patrón y cuánto sabor del género se inyecta. Y en vez de una sola salida, se muestra una paleta de variantes para elegir.

Salida. La rejilla traducida en el género nuevo, la comparación visual de antes y después, y la exportación a MIDI y WAV.

La pila técnica que propongo, sacada de los desarmes: interfaz con React, TypeScript y Vite, motor de reglas en TypeScript puro que corre en un Web Worker, sonido con Tone.js y Web Audio API, exportación de MIDI con @tonejs/midi, y despliegue estático gratis con PWA instalable.

El camino mínimo para construirlo, en siete pasos: rejilla de 16 pasos con play; selector de género con tres plantillas y motor de reglas básico; panel de explicación paso a paso; slider de Fidelidad y Creatividad con cuatro variantes; exportación a MIDI y PWA; biblioteca de plantillas que el usuario puede editar; y después más géneros, detección de audio a patrón y un modelo ligero solo para variaciones creativas, nunca para la traducción base.

## Qué falta

Tres cosas. Y las digo sin adornar.

Primero, que yo oiga la demo a 110 y diga si suena a dembow de verdad. Está enviada desde el 24 de noviembre a las 7:17 de la mañana. Falta mi oído.

Segundo, los sonidos. Decidir si se graban los siete que no existen. Seis de siete se pueden armar con lo que ya hay. La tambora no tiene nada.

Tercero, construir la app. La rejilla, el selector de género, el motor de reglas y exportar MIDI y WAV.

## Fuentes

- Verificación del hueco: verificacion-traductor.md, 23 de noviembre de 2026.
- Desarme de ToMIDI: rea-tomidi-analisis.md y rea-tomidi-directo.md.
- Desarme de Magenta: rea-magenta-kilo-analisis.md y rea-magenta-directo.md.
- Reglas latinas: reglas-latinas-coqui-fm1.md, del repo https://github.com/saulofilho/coqui-fm1 (GPL-3.0, estudiar, no copiar código).
- La bomba yubá en 6/8: alternativa-bomba-yuba-6-8.md.
- Demo: traductor-demo/index.html, actualizada a 110 BPM el 24 de noviembre de 2026.
- Repo público: https://github.com/GeralPink123/rhythm-translator

---

Papel de trabajo. Nada de esto es un producto terminado ni una promesa de fecha. El riesgo real del proyecto no es que ya exista. Es que las traducciones suenen bien, y eso lo decide mi oído.
