# Packing list de camiones

Un camión llega, se le hace el packing, pasa por el túnel y se expide.
La app registra cada uno de esos pasos con su hora y enseña en todo momento
dónde está cada camión.

Es un solo archivo sin dependencias. Funciona sin cobertura y los datos se
quedan en el dispositivo.

## Archivos

| Archivo | Para qué |
|---|---|
| `index.html` | La app entera |
| `manifest.json` | Para poder instalarla como aplicación |
| `sw.js` | Hace que funcione sin cobertura |
| `icon.svg` | El icono |
| `.nojekyll` | Evita que GitHub Pages procese los archivos |
| `.gitattributes` | Fuerza finales de línea consistentes |

---

## Paso a paso para publicarla en GitHub

### 1 · Crear el repositorio

1. Entra en <https://github.com> con tu cuenta.
2. Arriba a la derecha, botón **+** → **New repository**.
3. En *Repository name* escribe algo como `packing-list`.
4. Marca **Public**. GitHub Pages gratis sólo funciona con repositorios
   públicos. Se vería el código, pero **ningún dato de la operativa se sube
   ahí**: se queda en cada PDA.
5. No marques nada más. Botón **Create repository**.

### 2 · Subir los archivos

1. En la pantalla que sale, pulsa el enlace **uploading an existing file**
   (o ve a **Add file → Upload files**).
2. Arrastra los **seis** archivos de esta carpeta. Ojo con `.nojekyll` y
   `.gitattributes`: empiezan por punto y en Windows están ocultos. Si no los
   ves, en el Explorador activa *Vista → Elementos ocultos*.
3. Abajo, botón verde **Commit changes**.

### 3 · Encender GitHub Pages

1. Pestaña **Settings** (arriba del repositorio).
2. Menú izquierdo, **Pages**.
3. En *Source* elige **Deploy from a branch**.
4. Rama **main**, carpeta **/ (root)**. Botón **Save**.
5. Espera un minuto y recarga la página: aparecerá la dirección
   `https://TU-USUARIO.github.io/packing-list/`.

### 4 · Instalarla en la PDA

1. Abre esa dirección en Chrome.
2. Menú de los tres puntos → **Añadir a pantalla de inicio** (o *Instalar app*).
3. Queda como una aplicación normal, a pantalla completa y sin barra del
   navegador. Ya funciona aunque la PDA se quede sin cobertura.

### 5 · Publicar cambios más adelante

1. En el repositorio, entra en `sw.js` y pulsa el lápiz para editarlo.
2. Cambia `packing-v1` por `packing-v2` (y así sucesivamente). **Este paso es
   obligatorio**: si no subes el número, las PDAs seguirán con la versión vieja.
3. Sube el `index.html` nuevo con **Add file → Upload files**; GitHub te
   preguntará si quieres reemplazar el que ya está y le dices que sí.
4. Las PDAs se actualizarán solas la próxima vez que tengan red.

---

## Cómo se usa

### Tablero

Se da de alta el camión con su código y su fecha. Desde ese momento está en la
etapa **Llegado**.

Arriba hay un contador por etapa: de un vistazo sabes cuántos camiones hay
llegados, cuántos en packing, cuántos esperando túnel, cuántos dentro del túnel
y cuántos expedidos. Tocando un contador se filtra la lista.

Cada camión enseña su raíl de progreso, cuánto lleva parado en la etapa actual
y **un solo botón: el del paso siguiente**. No hay que buscar nada.

### Grabar bultos

El operario escribe su nombre una vez y a partir de ahí escanea bultos sin
tocar la pantalla. Cada lectura se guarda al instante y suena un pitido corto.

El **primer bulto del camión fija el patrón**: cuántos caracteres tiene el
código y si son sólo números. Lo que no encaje salta como anomalía con dos
botones, *Sí, está bien* y *No, descartar*. Nunca se bloquea nada: decide el
operario, y lo aceptado queda marcado como anomalía en el Excel.

Si el lector dispara dos veces seguidas con el mismo código en menos de un
tercio de segundo, la segunda lectura se ignora y se avisa en pantalla.

Tocando un bulto de la lista se corrigen sus unidades o se quita. La corrección
queda registrada: en el Excel salen las unidades que se leyeron y quién las
cambió.

Al terminar, **Packing terminado** cierra el packing y lleva a la pantalla del
packing list.

### Packing list

Arriba, el estado del camión con las cinco marcas de tiempo: llegada, packing
terminado, entrada al túnel, salida del túnel y expedición. Cada una guarda la
hora y quién la puso. Si alguien marcó tarde, se corrige la hora a mano con el
selector de abajo, y queda anotado como *(a mano)*.

**Deshacer** un hito borra también los posteriores, para que no quede un camión
expedido sin haber pasado por el túnel.

Debajo, el packing list completo y el botón para descargarlo en Excel.

### Datos

Reglas de lectura del código, descarga del histórico de todos los camiones,
respaldo en JSON y restauración.

## Reglas de lectura del código

Configurables en la pestaña Datos. Por defecto, sobre `84178016200200287`:

| Dato | Regla | Resultado |
|---|---|---|
| Unidades por bulto | 2 dígitos, empezando 3 por el final | `28` |
| Artículo (mocacota) | 12 primeros caracteres | `841780162002` |

## El Excel

Tres hojas:

1. **Packing list** — una línea por bulto: camión, fecha, nº de bulto, código,
   mocacota, unidades, unidades leídas antes de corregir, quién corrigió,
   operario, anomalía, observaciones, hora, y las tres marcas de túnel y
   expedición repetidas en cada línea para poder filtrar.
2. **Resumen por artículo** — cuántos bultos hay de cada artículo y tamaño, y
   cuántas unidades suman.
3. **Camiones** — una línea por camión con su estado y todas sus horas.

Los códigos van como texto, así que Excel no se come los ceros de delante.

## Dónde se guardan los datos — léelo

Los datos viven en el almacenamiento del navegador de **cada dispositivo**:

- **No se sincronizan entre PDAs.** Si dos operarios descargan el mismo camión
  desde PDAs distintas, tendrás dos registros parciales.
- **Se pierden si se borran los datos de navegación** o se desinstala la app.
- **El espacio es limitado** (unos 5 MB). En la pestaña Datos ves cuánto llevas
  ocupado.

Por eso: **descarga el respaldo JSON al cerrar cada jornada**, y guarda también
el Excel. El JSON se puede restaurar en cualquier PDA, y al restaurar no pisa
lo que ya había: sólo añade los camiones que faltan.
