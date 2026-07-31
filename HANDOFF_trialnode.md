# Handoff — Prototipo TrialNode (para retomar en otro chat)

> Actualizado tras varias iteraciones. Reemplaza al handoff anterior.

## Cómo retomar
- **Archivo activo: `buscador-de-estudios.html`** (renombrado en la Tanda 14; se llamaba `match_ensayos_trialnode_fixed.html`) — único con el que se continúa. Un solo archivo HTML autónomo (con `<head>`), anda offline.
- **Adjuntar ese .html + este handoff al nuevo chat.** Los archivos no persisten entre sesiones.
- Antes de tocar nada, leer los comentarios `// FIX N:`, `/* FIX ... */`, `/* ajuste N */`, `/* cambio N */` dentro del archivo: son el mapa de todo lo hecho.

## Qué es
Prototipo clickeable de **TrialNode**: app para que un paciente cargue su historia clínica y encuentre ensayos clínicos compatibles. Español rioplatense. Paleta teal (`#007567`). Poppins + íconos Tabler embebidos en base64 (subset de 14 glifos).
**Uso previsto:** demo para mostrar a un cliente. Prioridad: que no falle visualmente y que el flujo se entienda.

## Arquitectura del archivo (estado actual)
- `<head>`: meta viewport + title + **favicon** (PNG embebido en base64, `rel="icon"`).
- `<body>`:
  - **Preloader** `#tn-preloader` (primer bloque del body): logo TrialNode con barrido izq→der; se muestra ~1.9s y se funde. NO loopea. Respeta `prefers-reduced-motion` (se va en ~0.7s, sin barrido). Es a **tiempo fijo** (prototipo), no atado a `load` real.
  - `h2.sr-only` descriptivo.
  - `<style>` principal (todo el CSS del sistema, incluida la malla y el preloader escopados).
  - Shell: `#tn` → `#tn-mesh` (fondo) + `#tn-topbar` (`#navbar`) + `#stage` (`#viewport` + `#modalLayer`).
  - `<script>` app (IIFE) + `<script>` del preloader (ocultado).
- **Apilado (z-index):** `#tn-mesh` z0 (fondo) · `#stage` z1 (contenido) · topbar z5 desktop / z50 mobile · modales z1000 · preloader z9999.

### JS (IIFE)
- Estado: `uploaded`, `uploadedName` (nombre real del archivo soltado), `formData`, `formErrors`, `applied{id}`, `current`, `modalType`.
- Navegación: `backMap`, `render(name,arg)` (setea `data-screen` en `#viewport`), `handle(e)` (delegación por `data-act`), `resetHome()` (limpia estado y vuelve a intake).
- Pantallas: `screenIntake`, `screenAnalyzing`, `screenResults`, `screenDetail(id)`, `screenEmpty`; modales `openConfirm`/`openSuccess`/`closeModal`.
- Drag & drop del upload: listeners `dragover/dragleave/drop` sobre `#viewport` (delegado a `.up-wrap`). El drop marca `uploaded` y toma `files[0].name`. **No sube nada** (prototipo).
- Datos: array `trials` (3 ensayos hardcodeados: `id, match, title, cond, phase, loc, sponsor, dur, crit[], info[]`).

## Flujo
intake (form: nombre, contacto, historia clínica [drag&drop o click], consentimiento) → validación → analyze (~2.6s) → results (grilla) → detail (2 col) → **confirmar** (modal) → éxito → **vuelve a intake con estado reseteado**.

## Responsive / layout
- Mobile-first. Desktop desde **860px**. Contenedor centrado **max 1200px** (`--maxw`).
- **Desktop: shell `100dvh`, SIN scroll de página** (verificado a 1440×900, 1280×800/720/680; a <~640 de alto queda al límite: caveat viejo, no resuelto).
- **intake:** 2 paneles a alto completo. Izquierda (`.intake-pitch`, con `padding:0 24px 0 48px` de aire lateral): badge en MAYÚSCULA sin ícono + h1 (42px, forzado a 2 líneas en desktop vía `.pitch-br`) + subtítulo + 3 pasos + ilustración (**oculta**, ver abajo). Derecha (`.form-card`): elementos alineados al top, el dropzone (`.up-wrap`) crece y llena el alto; "Tus datos viajan cifrados" va debajo del CTA (sin fondo ni borde).
- **results (pantalla 2) y detail (pantalla 3):** contenido **alineado al top** en desktop, y **tipografía unificada** entre ambas (h1 30 · body 15 · labels/eyebrows 13 · badge compatibilidad 13). Escala compartida vía clase `.scr`.
- results: grid 3 col desktop / 2 col ≥600 / 1 col mobile.
- detail: 2 col (contenido + sidebar responsable/CTA).
- **Mobile: 1 columna, aprobado por Luis. No tocar sin que lo pida.** (el salto de título `.pitch-br` desactivado en mobile.)

### Malla de fondo (`#tn-mesh`)
- Cuadrícula **recta** (tile SVG ortogonal que repite), cuadros de 120px, teal, muy sutil.
- **Intensidad = un solo valor:** `#tn-mesh { opacity }` (hoy **.30**). Subir/bajar ahí.
- Anclada abajo, con `mask-image` que la funde hacia arriba (arriba queda limpio). `background-repeat:repeat` → sin huecos.

## Cambios aplicados en estas sesiones (resumen)
- **FIX 1–15 (base):** íconos embebidos, `.sr-only`, vars CSS, consentimiento opt-in, validación con errores, contraste AA, labels reales, tipos de input, modales accesibles (role/dialog/Esc/foco), estado "Postulado", paso de confirmación, "Requisitos del estudio" (no afirma que cumplís), análisis sin loop, touch target, estado vacío, responsive real, 100dvh sin scroll.
- **Ajustes (tanda 1):** ancho 1200 · form-card top + dropzone fill · cifrado debajo del CTA sin fondo · **drag&drop** con nombre real · badge sobre h1 · h1 desktop +8 · fin de flujo → intake · modales responsivos + copy (punto final, sin viuda) · ilustración IA/medicina (luego descartada).
- **Cambios (tanda 2):** ilustración **oculta** (no borrada) · **malla** de fondo · badge MAYÚSCULA sin ícono · aire lateral en pitch · **preloader** integrado.
- **Cambios (tanda 3):** malla más sutil / cuadros grandes / sin huecos abajo · título en 2 líneas desktop · **favicon** · pantalla resultados más grande (legibilidad).
- **Cambios (tanda 4, esta sesión):** malla **recta** (cuadrícula ortogonal) opacity **.30** · pantallas 2 y 3 **alineadas al top** · **tipografía unificada** entre pantalla 2 y 3 (clase `.scr`).

## Pendientes / caveats conocidos
- **Ilustración del intake: borrada (tanda 10).** Estaba oculta y vacía desde la tanda 2 (no tenía SVG adentro). Si se quiere retomar la idea, hay que rediseñarla y agregarla de cero.
- **"Pantalla dos" = results.** Interpretado como la lista de ensayos (intake=1). Si Luis se refería a otra, re-mapear.
- **Malla:** intensidad es `#tn-mesh { opacity }`. Ajuste fino conviene hacerlo mirando en pantalla real.
- **Preloader:** a tiempo fijo (1.9s). Si esto va a un sitio real, cambiar el timeout por evento `load`/carga real de assets.
- **Es prototipo:** upload no sube nada (drag&drop simula, toma solo el nombre); análisis y "datos cifrados" **simulados**; sin backend ni persistencia. Fuera de alcance salvo que Luis pida hacerlo funcional (otro proyecto).
- **Split de criterios "cumplís vs a verificar":** NO hecho. Requiere data por ensayo. **No inventar data médica.**
- **Altura desktop:** intake es la pantalla más alta; a <~640px de alto de ventana queda al límite (la ilustración estaba oculta igual). No resuelto.
- El copy del intake en mobile quedó igual al original a pedido de Luis.

## Cómo validar cambios (entorno de trabajo)
- **Sintaxis JS:** extraer cada `<script>` y `node --check`. (Hay 2 scripts: app + preloader.)
- **Flujo funcional:** `npm i jsdom`, simular clicks por `data-act`. Cubre intake→analyze→results→detail→confirmar→éxito→intake.
- **No-scroll desktop + screenshots:** Chromium de Playwright ya instalado → `/opt/pw-browsers/chromium-1194/chrome-linux/chrome`; Playwright global en `$(npm root -g)` (`export NODE_PATH=$(npm root -g)`). Bloquear `fonts.googleapis.com` (sin salida a Google Fonts → cae a system font; no afecta layout). Medir `documentElement.scrollHeight` vs `innerHeight`. Dejar pasar ~2.7s para que se vaya el preloader antes de interactuar.
- **Ver SVG suelto rasterizado:** `pip install cairosvg --break-system-packages`. OJO: cairosvg subrenderiza líneas finas/teal claras (se ven casi blancas); para juzgar de verdad usar screenshot de Playwright, no cairosvg.
- **Medir sutileza de la malla:** con Pillow sobre el screenshot, comparar brillo promedio arriba vs abajo, y buscar el pixel de línea más oscuro (contraste vs bg 249).
- **Regenerar la malla:** es un tile SVG (borde superior+izquierdo) en base64 dentro de `#tn-mesh`. Editar el tile, base64, reemplazar. Tamaño de cuadro = `background-size` (120px). Repite con `background-repeat:repeat`.
- **Favicon:** PNG embebido en base64 en `<link rel="icon">` del head.
- **Re-subsetear íconos** si se agregan `ti-*` nuevos: `npm i @tabler/icons-webfont`, codepoints de `dist/tabler-icons.min.css`, `pyftsubset` (necesita `fonttools brotli`) sobre el `.ttf`, base64, reemplazar el `@font-face`. Codepoints actuales: arrow-left `ea19`, flask `ebd2`, map-pin `eae8`, chevron-right `ea61`, circle-check `ea67`, point `eb0c`, clock `ea70`, check `ea5e`, file-check `ea9c`, file-upload `ec91`, x `eb55`, lock `eae2`, search `eb1c`, search-off `f19c`. Evitar los `-filled` (no cargan).

## Knobs rápidos (dónde tocar)
- **Intensidad malla:** `#tn-mesh { opacity }` (hoy .30). Tamaño de cuadro: `background-size` (120px).
- **Duración preloader:** en el script del preloader, `var wait=reduce?700:1900;`.
- **Escala tipográfica desktop pantallas 2/3:** reglas `#tn .scr ...` y `#tn .res-screen ...` dentro de la media 860.
- **Salto de título desktop:** `.pitch-br` (base `display:none`, desktop `display:inline`).

## Cómo trabajar con Luis (importante)
- Español rioplatense, tono directo y económico, **sin humo ni jerga vacía**. Diseñador UX/UI (ex ingeniero civil), lee código bien.
- Valora que **muestres el proceso y la decisión**, y que seas **honesto sobre las limitaciones** ("perfecto" no aplica a un prototipo).
- Tiene criterio propio: **proponé con fundamento**, pero las decisiones de gusto son suyas; cede rápido en lo creativo, se pone firme en plata/compromisos.
- **No inventar** datos (médicos, de cliente, de proyecto).
- Cuando hay una **decisión de producto real** (no un bug), **alinear antes** de gastar esfuerzo grande. Ya se perdió una iteración por asumir mal el eje "responsive" (quería reflujo real, no mockup de teléfono escalado). Dejar explícitas las decisiones/asunciones antes de ejecutar.

---

## Tanda 5 — 8 cambios (hover, breadcrumbs, i18n, compartir, upload, preloader, panel de acción)

Validado con `node --check` (2 scripts), test de flujo jsdom (33/33) y screenshots Playwright (desktop 1440×900 sin scroll de página + mobile 390×844).

- **task 1 — hover CTAs.** El fondo del CTA estaba inline y le ganaba en especificidad al `:hover`/`:active`, así que el cambio de color nunca se aplicaba. Se movió `background/color` a la clase `#tn .cta{}` (CSS) y `cta()` ya no lo setea inline. Hover: teal más oscuro + sombra. Los botones de modal (Confirmar/Entendido) y Cancelar ahora usan `.cta` / `.btn-ghost` para heredar el hover. Back/quitar/cerrar usan `.navbtn`.
- **task 2 — sombra hover en cards de resultados.** Mismo patrón: la sombra base pasó de inline a `#tn .rcard{}` para que `:hover` pueda subir por encima. Hover = sombra más marcada + `translateY(-2px)`. Envuelto en `@media (hover:hover)` para no ensuciar touch.
- **task 3 — breadcrumbs desktop.** `renderNav(screen,arg)` ahora dibuja **back arrow** (`.nav-back`, visible solo en mobile) **y** breadcrumbs (`.nav-crumbs`, visibles solo ≥860px). Trails: Inicio / Inicio›Analizando / Inicio›Ensayos / Inicio›Ensayos›[título]. "Inicio" navega con `render('intake')` (preserva el formulario, no resetea).
- **task 4 — upload en estado cargado.** En desktop, `.up-wrap > *{flex:1 1 auto}` estiraba el chip compacto hasta llenar el dropzone alto → parecía una caja vacía enorme. Fix: se togglea la clase `is-filled` en `#uploadSlot`; en desktop `.up-wrap.is-filled` deja de crecer (`flex:0 0 auto; min-height:0`).
- **task 5 — preloader sostiene el logo lleno ≥1s.** El barrido era `infinite` y el sitio se ocultaba a 1.9s (mid-ciclo, con el logo ya desvaneciéndose). Ahora el barrido llena una sola vez (`forwards`, sin loop, sin fade en el keyframe) y el JS espera **2100ms** (1.1s de llenado + 1.0s de sostén) antes del fade de salida. `reduce`: 1000ms.
- **task 6 — panel de acción del detalle.** El "Responsable" quedaba solo y después el CTA suelto. Se reordenó como panel: eyebrow Responsable → sponsor → divisor → CTA → nota corta que baja fricción ("Postularte no te compromete a nada, y es sin costo"). No duplica la nota del cuerpo. Card principal (y por ende mobile) intactas salvo el agregado del panel.
- **task 7 — compartir en el detalle.** Botón "Compartir" (ícono SVG inline, no está en el subset de la webfont) en la fila superior del detalle. Usa `navigator.share` en mobile; si no existe, copia al portapapeles y muestra un toast "Enlace copiado". **El link es simulado** (`https://trialnode.app/estudio/<id>`, no hay backend) — es prototipo.
- **task 8 — selector de idioma ES/EN/PT.** Diccionario `I18N` (toda la UI) + `TRIALS` por idioma (los 3 ensayos traducidos, ciudades se mantienen). `t(k)` cae a ES si falta una clave. Selector segmentado en la navbar; al cambiar, reemplaza `trials`, setea `document.documentElement.lang` y re-renderiza la pantalla actual (preservando pantalla + arg). **EN/PT son un primer pase de traducción — falta revisión de tono.**

Riesgo latente resuelto: el patrón "inline gana al CSS" (tasks 1 y 2) estaba desactivando estados que se creían activos.

---

## Tanda 6 — 3 cambios (CTA estable en intake, dropdown de idioma, detail con scroll + panel fijo) + informe de usabilidad

Validado con `node --check` (2 scripts), test de flujo jsdom (25/25: intake→analyze→results→detail→confirmar→éxito→intake, más dropdown de idioma e `is-detail`) y screenshots Playwright (desktop 1440×900 + mobile 390×844).

- **task 1 — CTA estable en intake (desktop).** El bug: `.up-wrap.is-filled` colapsaba (`flex:0 0 auto`) al cargar el archivo, así que el dropzone perdía su alto y el CTA "Buscar ensayos compatibles" saltaba hacia arriba. Fix: el wrap **ya no colapsa** — mantiene el `flex:1 1 auto;min-height:120px` de siempre estando lleno o vacío, así el espacio reservado (y por lo tanto la posición del CTA) es el mismo antes y después de subir el archivo. Lo que cambia es que el contenido cargado se **centra** verticalmente (`justify-content:center` + `> * {flex:0 0 auto}`) en vez de estirarse. Asunción de producto: esto deja un área vacía arriba/abajo del chip cargado — es el trade-off que pide el propio punto 1 del brief ("conserve el mismo espacio vertical"). Si a Luis no le convence estéticamente, la alternativa es no fijar el alto y aceptar que el CTA se mueva (lo contrario de lo pedido). Escopeado a `@media (min-width:860px)`; mobile intacto.
- **task 2 — selector de idioma: de segmentado a dropdown.** El segmentado (`.lang-seg`, 3 botones ES/EN/PT) medía ~178px + back(44px si hay) + logo(~152px) y **desbordaba los 390px de un mobile real** (confirmado con screenshot antes del fix). Reemplazado por un dropdown: un botón compacto (bandera + código + chevron, `.lang-dd-btn`) que abre un menú flotante (`.lang-dd-menu`) con las 3 opciones. Mismo estado y lógica de siempre (`lang`, `I18N`, `TRIALS`, `t()`, re-render de la pantalla actual vía `render(current, currentArg)`) — lo único nuevo es `langMenuOpen` (booleano, controla si el menú está pintado) y las acciones `toggleLangMenu` / el `setLang` ahora también cierra el menú. Cierra con click afuera (`document` click listener con `.closest('.lang-dd')`) y con Escape (mismo listener de teclado que cierra modales, ahora con un chequeo adicional). Patrón "menú" simple (`role="menu"`/`menuitemradio`), no listbox con navegación por flechas — suficiente para un prototipo, dejarlo explícito si se lo lleva a producción.
- **task 3 — pantalla detail: scroll real + panel del CTA fijo.** Antes, **todas** las pantallas desktop vivían encerradas en `100dvh;overflow:hidden` (sin scroll de página). Ahora **solo** `detail` sale de esa jaula: el JS togglea una clase `is-detail` en `#tn` dentro de `render()` (`tnRoot.classList.toggle('is-detail', name==='detail')`), y el CSS, escopeado a `#tn.is-detail` dentro de `@media (min-width:860px)`, cambia `height:100dvh` por `height:auto;min-height:100dvh;overflow:visible`, deja `#viewport` con `padding-bottom:48px`, y vuelve sticky el topbar y el panel lateral (`.detail-side{position:sticky;top:84px}`, 84px = 60px de navbar + 24px de aire). Intake y results **no se tocaron** — siguen sin scroll de página. Para que la pantalla realmente tenga alto de sobra y se note el scroll, se agregó contenido genérico nuevo dentro del detalle: una sección "Cómo sigue el proceso" (4 pasos) y "Preguntas frecuentes" (3 preguntas) — **son copy genérico, no datos médicos ni por-ensayo**, están en `I18N` (keys `proceso_*` y `faq_*`, traducidas a los 3 idiomas) y se renderizan igual para los 3 ensayos. Verificado con Playwright: a 1440×900 el detalle mide ~1186px de alto real (scroll de ~286px) y el panel lateral efectivamente se clava en `top:84px` al scrollear.

### Cómo se validó esta tanda
- `node --check` sobre los 2 `<script>` extraídos: sin errores de sintaxis.
- Test de flujo con jsdom (`npm i jsdom`): 25 asserts, cubre intake→analyze→results→detail→confirmar→éxito→intake, más apertura/cierre del dropdown de idioma, cambio a EN y vuelta a ES, y que la clase `is-detail` se prende solo en detail y se apaga en el resto.
- Screenshots con Playwright + Chromium real (**no** el path del handoff anterior — ese sandbox no tenía nada instalado; se descargó Chrome for Testing con `curl -C -` en tramos porque cada llamada de shell tiene un tope de tiempo, y se resolvió una lib faltante, `libXdamage.so.1`, bajando el `.deb` de Ubuntu 22.04 y apuntando `LD_LIBRARY_PATH` ahí, sin necesitar root): desktop 1440×900 y mobile 390×844, bloqueando `fonts.googleapis.com`, esperando 2.7s el preloader. Confirmado visualmente: CTA en el mismo Y antes/después de cargar archivo (767px en ambos casos), dropdown de idioma funcionando y sin desborde en mobile, detail con scroll visible y panel lateral fijo, hover del CTA intacto, breadcrumbs intactos, no-scroll de intake/results sin cambios.
- Si se retoma en otra sesión y hace falta re-correr Playwright: Chromium no persiste entre sesiones de este entorno (era todo en `/tmp`, que se borra). Hay que volver a descargarlo y resolver la lib de X11 igual que acá — no asumir que el path del handoff previo (`/opt/pw-browsers/...`) existe.

### Informe de usabilidad (puntos 4/5 del pedido, NO implementado)
Se entregó `USABILIDAD_trialnode.md`: evaluación heurística (no investigación con usuarios reales) pensada para el perfil pedido — 30 a 50 años, secundario completo, bajo manejo de tecnología, muchos sin mail. Pain points concretos + lista numerada de 12 mejoras (problema / qué haría / esfuerzo). Nada de esto se tocó en el HTML — queda para que Luis lo revise y decida qué seguir.

### Pendiente / a decidir
- El vacío visual que deja el CTA estable en intake al cargar el archivo (task 1) es una asunción de producto, no un bug — confirmar si el trade-off convence estéticamente.
- El dropdown de idioma usa patrón "menú" simple, sin navegación por flechas del teclado — suficiente para prototipo, revisar si hace falta más si esto se vuelve real.
- El contenido nuevo del detalle ("Cómo sigue el proceso", "Preguntas frecuentes") es genérico y va igual en los 3 ensayos — si se quiere contenido específico por ensayo, hace falta data real (no inventarla).
- Las 12 mejoras de usabilidad están sin priorizar por Luis — es una lista de candidatos, no un plan.

---

## Tanda 7 — 9 mejoras de usabilidad implementadas (de las 12 del informe)

Origen: el informe `USABILIDAD_trialnode.md` (Tanda 6). Luis las revisó, le parecieron correctas y pidió aplicarlas. Se implementaron 9 de las 12; las otras 3 no son cambios de código (ver abajo). Perfil objetivo: 30–50 años, secundario completo, bajo manejo de tecnología, muchos sin mail y usando el celular + WhatsApp.

Validado con `node --check` (2 scripts) y test de flujo jsdom (26/26: intake→errores→upload→analyze→results→detail→postular→confirmar→éxito→intake, + dropdown de idioma ES/EN/ES, + stepper mobile, + toggle `is-detail`). **NO se rehicieron screenshots Playwright** esta tanda (rearmar Chromium en el entorno es costoso — ver nota al final); los cambios de CSS son acotados y escopeados. Queda pendiente una pasada visual si se quiere confirmación a nivel pixel.

### Qué se cambió
- **#1 + #10 — campo de contacto.** Label pasó de "Contacto (teléfono o mail)" a **"Tu teléfono o WhatsApp"**, placeholder "El mail es opcional". Deja explícito que el teléfono/WhatsApp alcanza (el mail es opcional) para el usuario que no tiene mail. La validación NO cambió: sigue aceptando mail o teléfono (`esMail || esTel`). Mensajes de error actualizados en los 3 idiomas. WhatsApp queda a nivel de copy — el flujo real de notificación por WhatsApp necesita backend, fuera de alcance del prototipo.
- **#2 — orden del copy del dropzone.** Antes "Arrastrá el archivo o tocá para elegir" (drag primero). Ahora lidera la acción accesible: título **"Tocá para subir tu historia clínica"** + hint "Sacá una foto o elegí un PDF (o arrastralo acá)". El drag queda al final entre paréntesis.
- **#3 — cámara / sacar foto.** Reflejado en copy e intención (el hint ahora dice "sacá una foto"). El input real con `capture="environment"` NO se agregó: el prototipo simula la carga con un click (no sube nada), y meter un `<input type=file capture>` real rompería el demo de un click y el test de flujo. **Para producción:** el dropzone mapea a `<input type="file" accept="image/*,application/pdf" capture="environment">`. Queda documentado, no implementado (consistente con el resto del prototipo: upload/compartir/análisis simulados).
- **#4 — lenguaje de seguridad.** "Tus datos viajan cifrados" → **"Nadie más ve tus datos"** (los 3 idiomas). Dice lo mismo sin la distancia técnica de "cifrado".
- **#5 — el % es preselección.** Nueva línea `res-nota` bajo el subtítulo de resultados: "El porcentaje es una preselección — un profesional confirma después si cumplís los requisitos." Baja el riesgo de que un número alto se lea como "ya calificás".
- **#6 — consentimiento con jerarquía.** El checkbox dejó de estar al nivel de un campo cualquiera: ahora es un bloque `.consent-box` (fondo tint, borde, padding, texto 13.5px, checkbox 15px→19px) y copy más humano ("Autorizás que usemos tu historia clínica solo para buscarte estudios compatibles. Nada más."). Sigue **dentro del form**, no como paso aparte (asunción: un paso propio es un cambio de flujo más grande y riesgoso — si se quiere, se evalúa después).
- **#7 — tamaños de texto y toque.** Botón del selector de idioma de 36px → **44px** de alto (mínimo táctil). Texto de consentimiento y de error subidos a 13–13.5px (antes 12 / 11.5).
- **#8 — stepper de progreso en mobile.** Nuevo `#tn-stepper` (barra sticky bajo la topbar, **solo <860px**; en desktop ya están los breadcrumbs). Muestra 3 segmentos + "Paso N de 3 · [label]". Mapa: intake=1 (Tus datos), analyze/results=2 (Resultados), detail=3 (El estudio). Se actualiza en `render()` vía `renderStepper(name)`. Traducido a los 3 idiomas (keys `paso`, `de`, `sp_datos`/`sp_result`/`sp_estudio`).
- **#9 — errores de validación más visibles.** `.fielderr` de 11.5px → 13px + un ícono de alerta (círculo rojo con "!") vía `::before`, así se lee como "esto está mal" sin depender de asociar el color. Además, al enviar con errores, `validateAndAnalyze` hace **scroll automático al primer error** y enfoca el primer input inválido (`scrollIntoView` + `focus({preventScroll:true})`).

### Lo que NO se implementó (y por qué)
- **#3 (input real de cámara):** ver arriba — documentado para producción, no metido para no romper el demo de un click.
- **#10 (WhatsApp como canal real):** implementado a nivel copy. El flujo de notificación real necesita backend → fuera de alcance del prototipo.
- **#12 (testear con usuarios reales):** no es código — es research. Queda como recomendación. Todo lo de arriba sale de una lectura heurística; conviene validarlo con 4–5 personas del perfil antes de dar por buenas las decisiones.

### Nuevas claves de I18N (es/en/pt)
`intake_que` (qué es un ensayo, se muestra solo en mobile), `res_nota` (nota de preselección), `paso`, `de`, `sp_datos`, `sp_result`, `sp_estudio` (stepper).

### Pendiente / a decidir (Tanda 7)
- La caja `intake_que` ("qué es un ensayo clínico") se muestra **solo en mobile**: en desktop el pitch ya tiene los 3 pasos y el alto es fijo sin scroll (meter otra caja arriesgaba el 100dvh). Si se quiere también en desktop, hay que rebalancear ese layout.
- Screenshots Playwright no rehechos en esta tanda. Si se retoma y se quiere pixel-check: hay que re-descargar Chromium (no persiste) y resolver la lib de X11, igual que en la Tanda 6.
- WhatsApp y cámara quedaron a nivel copy/afordancia — el salto a funcional real es otro proyecto (necesita backend).

### Revisión Tanda 7 (a pedido de Luis)
Sobre la versión **mobile** del intake:
- **Se eliminó el stepper** de progreso "Paso N de 3" (task 8): no le gustó. Se sacó el elemento `#tn-stepper`, la función `renderStepper`, su llamada en `render()`, el CSS y las claves I18N asociadas (`paso`,`de`,`sp_datos`,`sp_result`,`sp_estudio`). En mobile el back arrow sigue; en desktop siguen los breadcrumbs.
- **Se eliminó la caja "un ensayo clínico es un estudio…"** (task 11): se sacó el `<p class="pitch-que">`, su CSS y las claves `intake_que` (3 idiomas).
- **Se agregaron los 3 bullets del pitch a mobile** (antes eran desktop-only): "Cargás tu historia clínica / La cruzamos con los estudios activos / Te contactamos si aparece uno compatible". Ahora `.pitch-steps` se muestra en mobile y desktop (mismo estilo).
- Contacto: label "Tu teléfono o WhatsApp" + placeholder de ejemplo de número ("Ej: 11 2345 6789"), sin mencionar el mail (evita la mezcla confusa). Validación intacta (acepta mail o teléfono).
- Stepper accesible ya no aplica (se eliminó). Revalidado: `node --check` OK + jsdom 21/21.

---

## Tanda 8 — mapa de ubicación del centro (en el detalle)

Pedido de Luis: mostrar dónde queda el centro del estudio. Elegido (entre 3 opciones) el enfoque **mock offline + botón a Google Maps**, para no romper el "anda offline / archivo autónomo" ni inventar direcciones.

- **Qué se agregó:** una tarjeta "Dónde queda" en el detalle (`screenDetail`, después de la card de requisitos), con un mock de mapa estilizado (SVG inline: calles + plaza + agua, teal sutil), un pin centrado (`ti-map-pin`), un chip con la ubicación (`x.loc`), una nota "Ubicación aproximada de la zona" y un botón "Ver en el mapa".
- **El botón es comportamiento real, no simulado:** `openMap(id)` abre `https://www.google.com/maps/search/?api=1&query=<loc>` en pestaña nueva (`window.open`). Pero a **nivel zona**: los centros son ficticios (salvo Hospital de Clínicas), no hay coordenadas exactas — **no se inventó ninguna dirección**. El pin del mock es decorativo/centrado, no georreferenciado.
- **Offline:** el mock no baja tiles ni depende de red; el botón solo actúa al click (si no hay internet, lo maneja el navegador). El archivo sigue siendo autónomo.
- **I18N:** claves nuevas `donde`, `ver_mapa`, `mapa_nota` (es/en/pt). **CSS:** bloque `tanda 8` (`.map-mock`, `.map-svg`, `.map-pin`, `.map-loc`, `.map-row`, `.map-note`, `.map-btn`). **Handler:** `act==='openMap'`.
- **Validado:** `node --check` (2 scripts) + jsdom 25/25 (incluye que la tarjeta y el botón existen y que el click no rompe). Screenshots Playwright NO rehechos (mismo motivo de siempre).

### A decidir (Tanda 8)
- Si esto va a producción con centros reales, el mock se reemplaza por un mapa real georreferenciado (coordenadas por ensayo) — ahí sí Leaflet/Google Maps embebido, y hay que decidir online vs offline.
- Placement: hoy la tarjeta va en la columna principal, después de requisitos. Si se quiere más arriba (la ubicación es muy decisoria), se sube fácil.

---

## Tanda 9 — carga real de la historia clínica (foto o archivo) en el intake

Pedido de Luis: dejar "ejecutable" el sacar una foto de la historia clínica (antes el upload era 100% simulado con un click).

- **Cómo:** el dropzone dejó de ser un botón que simula, y pasó a ser un `<label>` que envuelve un input real: `<input id="hc-file" type="file" accept="image/*,application/pdf" capture="environment">`. En el **celular**, `capture="environment"` abre directo la **cámara trasera**; en **desktop** se ignora y abre el explorador de archivos. El permiso de cámara lo pide el navegador.
- **Preview real:** al elegir/sacar la foto, se marca cargado **al instante** (`acceptFile` → `renderSlot`) y, si es imagen, se lee con `FileReader` (`readAsDataURL`) para mostrar el **thumbnail real** de la foto en el estado cargado (async). Si es PDF, muestra el ícono de archivo. Nombre de archivo = el real.
- **Quitar / sacar otra:** la X ahora es `data-act="removeUpload"` (antes `toggleUpload`, que se eliminó). Vuelve al dropzone para reintentar.
- **Sigue siendo prototipo:** **no sube nada** — la foto queda en el input/preview, en memoria. Subirla de verdad necesita un endpoint de backend (fuera de alcance). El drag&drop de desktop ahora también pasa por `acceptFile` (mismo preview).
- **Estado nuevo:** `uploadedPreview` (dataURL o null); se resetea en `resetHome`. **CSS tanda 9:** `.up-dropzone{display:block}` + `:focus-within` (foco por teclado). El input va `sr-only` (oculto pero operable y accesible vía el label del campo).
- **Nota de demo:** al ser input real, en desktop un click abre el diálogo de archivos del SO (antes un click simulaba la carga). Es más realista, pero si el presentador cancela el diálogo, no pasa nada. Para el demo, elegir cualquier archivo/foto.
- **Validado:** `node --check` (2 scripts) + jsdom 16/16 (input con capture, change→cargado+preview real, quitar, PDF sin preview de imagen, flujo completo hasta el reset). Screenshots Playwright NO rehechos (mismo motivo).

### Para producción (recomendado, no implementado)
- Varias hojas: permitir `multiple` o "agregar otra" (una historia clínica suele ser varias páginas).
- Comprimir la imagen en canvas antes de subir (fotos de celu pesan 5-8MB; importa con datos móviles).
- El upload real: endpoint de backend + manejo de progreso/errores.

---

## Tanda 10 — limpieza de código (sin cambios de producto)

Pedido de Luis: limpiar el código acumulado en 9 tandas. Cuatro frentes, todos sin tocar comportamiento ni visual (salvo el punto 1, que borra algo que ya estaba invisible):

- **Código muerto borrado:** el `<div class="pitch-illu">` del intake (la ilustración IA/medicina) estaba vacío (sin SVG adentro) y oculto con `display:none` en las dos versiones (mobile y desktop) desde la tanda 2 — no hacía nada. Se borró el div y las dos reglas CSS que lo escondían. Si en algún momento se quiere una ilustración ahí, hay que rediseñarla y agregarla de cero (no hay nada para reactivar).
- **Comentarios históricos reescritos:** se sacaron todas las referencias a `FIX N`, `ajuste N`, `cambio N`, `tanda N (task M)` (~90 comentarios). Donde el comentario explicaba algo no obvio (por qué el CTA no lleva `background` inline, por qué el `up-wrap` no colapsa, por qué el mapa es un mock, que el idioma EN/PT no es dato médico inventado, etc.) se mantuvo reescrito en presente, sin la numeración de tanda. Donde solo repetía lo que ya dice el nombre de la clase o el código, se borró.
- **CSS duplicado consolidado:** había reglas para el mismo selector definidas dos veces en distintas tandas (`.fielderr` con dos tamaños de fuente distintos, `.lang-dd-btn` con `min-height` en una regla aparte) — se unificaron en una sola declaración cada una. También había tres bloques `@media (min-width:860px)` separados (uno para `up-wrap.is-filled`, otro para `is-detail`) que se fusionaron en el bloque desktop principal.
- **Comentario contradictorio corregido:** en el CSS de resultados/detalle habían quedado dos comentarios que se contradecían (uno decía "contenido centrado vertical", el otro "alineado al top") — sobrevivía de una iteración vieja. Quedó solo el que describe el comportamiento actual (alineado al top).

**Validado:** `node --check` sobre los 2 `<script>` extraídos (sin errores). Flujo completo probado en el navegador vía JS (no jsdom esta vez): intake con upload simulado → analyze → results → detail (clase `is-detail`, panel lateral `sticky`, tarjeta de mapa, botón compartir) → postularse → confirmar → éxito → vuelve a intake; dropdown de idioma (abre, botón de 44px, cambia a EN) y errores de validación (los 4 mensajes + el ícono "!" fusionado) también funcionan. **No se pudo tomar screenshot visual** en este entorno (el panel no compone frames para archivos fuera de un proyecto/servidor) — la verificación fue funcional/DOM, no pixel a pixel.

Se guardó una copia del archivo previo a esta limpieza como `match_ensayos_trialnode_fixed.backup.html` en la misma carpeta, por si hace falta comparar o revertir algo puntual.

---

## Tanda 11 — footer legal, links a términos/privacidad, fix de altura del intake, preloader letra por letra

- **Footer.** Nuevo `#tn-footer`, responsivo (apilado y centrado en mobile, fila en desktop), fondo transparente, con copyright a la izquierda y links a la derecha con hover (color teal + subrayado). Se integró al shell de 100dvh sin scroll de desktop como tercer hijo flex (junto a topbar y stage) — el stage se achica automáticamente para dejarle lugar, sin tocar el resto del layout.
- **Pantallas de Términos de uso y Política de Privacidad.** Contenido real extraído de `app.trialnode.io/es/terms` y `/es/privacy` (texto de Trialtech, reformateado a la estética del sitio: h2/h3, listas, tarjetas). Quedan en español siempre, sin traducir (a pedido explícito, "por ahora"). Cada una tiene breadcrumb "Inicio › ..." (desktop) / flecha atrás (mobile) — ambos vuelven a intake — y un botón flotante "volver arriba" con scroll suave. Son documentos largos: reutilizan la clase `is-scrollable` (antes `is-detail`, renombrada porque ahora la comparten detail/terms/privacy) para salir del 100dvh sin scroll.
  - Bug encontrado y arreglado en el camino: el footer no tenía ningún listener de click registrado (solo lo tenían viewport/navbar/modalLayer) — los links se veían pero no navegaban a ningún lado.
  - Es texto real pero reestructurado por mí (prosa → listas/tarjetas); antes de que esto sea legal "de verdad" en producción, alguien de Trialtech debería revisarlo contra el original.
- **Fix: el form del intake pisaba el footer en desktop.** La fila del grid (`intake-grid`) se dimensionaba por contenido (`auto`), no por el alto disponible — con los 4 mensajes de error de validación visibles, `form-card` crecía más que el espacio real y quedaba encima del footer. Fix de raíz: `grid-auto-rows:minmax(0,1fr)` en `intake-grid`, así la fila queda genuinamente acotada al alto disponible.
  - Además: si el form no entra (mensajes de error, laptop bajo), nombre y teléfono pasan a una fila (`.name-phone-row.compact`) para ganar alto. Se decidió medir el overflow real en JS (`fitIntakeForm()`, llamada en cada render del intake y en resize) en vez de usar CSS container queries — las probé primero porque son la herramienta "correcta" en teoría, pero no reconocen bien el alto que da `align-self:stretch` desde un grid (disparaban el modo compacto siempre, con espacio de sobra o no). Queda `overflow-y:auto` en `form-card` como resguardo final si aun compactando no entra todo.
- **Preloader: letras una por una.** Se cambió el barrido (clip-path L→R) por un fade+settle escalonado por trazo (`opacity:0→1` + `translateY(12px)→0`, cascada de ~45ms). El SVG dibuja la "N" de Node en 3 trazos separados, así que el orden de aparición se mapeó a mano (T-r-i-a-l-N-N-N-o-d-e) contra las coordenadas reales de cada `<path>`, no por el orden en que están en el archivo. El subtítulo (16 trazos chicos) entra junto, no letra por letra. Duración total similar a antes (~2s). `prefers-reduced-motion` sigue mostrando el logo entero y quieto.
  - **Revisión (a pedido de Luis):** se sacó la capa "fantasma" (la silueta gris que se veía completa desde el arranque, de fondo de la animación de barrido). Ahora arranca en blanco: no hay nada visible hasta que cada letra empieza a aparecer. El SVG y la regla CSS de esa capa se borraron (no quedó oculta, no tiene uso sin el barrido). También se agrandó el desplazamiento vertical (5px → 12px) para que se note más el "de abajo hacia arriba".
  - **Segunda revisión (velocidad):** quedaba muy rápido. Se subió la duración por letra (0.38s → 0.55s) y el paso entre letras (45ms → 70ms) — el logo completo tarda ahora ~1.45s en aparecer (antes ~0.83s) y el tiempo total del preloader pasó de 2000ms a 2300ms para darle el mismo sostén al final.
  - **No se pudo verificar la animación corriendo en vivo en este entorno** — confirmé con una prueba aislada que el panel de preview no avanza ningún timeline de animación CSS (ni siquiera una de 50ms) mientras no está compuesto/visible; es la misma limitación que ya había encontrado con `scrollTo(..., {behavior:'smooth'})`. La corrección se validó revisando el CSS (selectores, orden de `nth-child`, keyframes) y confirmando que el estado inicial (`opacity:0`) se aplica bien — falta la mirada visual en un navegador real.

---

## Tanda 12 — consentimiento: de checkbox en el form a modal después del CTA

Pedido de Luis, probando un cambio de flujo (no un bug): sacar el checkbox de consentimiento del form-card y pedirlo como modal recién al tocar "Buscar ensayos compatibles", en vez de un checkbox más al nivel de los campos. Idea: para un dato sensible de salud, un modal que hay que leer y aceptar explícitamente se siente más deliberado que un checkbox que se puede tildar sin leer.

- **Qué cambió:** el `<label class="consent-box">` con el checkbox salió del `form-card` (y su CSS). `validateAndAnalyze()` ya no valida consentimiento junto con nombre/teléfono/archivo — si esos tres están OK, abre `openConsent()` (mismo patrón de modal accesible que `openConfirm`: `role="dialog"`, foco, Esc, Cancelar/Aceptar). "Cancelar" cierra el modal y vuelve al intake sin perder lo ya tipeado; "Aceptar y continuar" (`confirmConsent`) recién ahí dispara `render('analyze')`.
- **Qué NO cambió:** el texto de consentimiento (mismo copy, ahora como cuerpo del modal vía `t('consent')`) y la nota "Nadie más ve tus datos" bajo el CTA — se deja donde está, a pedido explícito.
- **I18N:** nuevas keys `consent_modal_title` y `consent_accept` (es/en/pt). Se borró `err_consent` (ya no hace falta un estado de error de "checkbox sin tildar" — ahora es un modal que se acepta o se cancela, no un campo que se puede dejar vacío por descuido).

**Validado:** `node --check`, y en el navegador: el checkbox ya no está en el form, el CTA con datos válidos abre el modal con el título/texto correctos, "Cancelar" vuelve al intake preservando nombre/contacto ya tipeados, "Aceptar y continuar" avanza a `analyze`, y se repitió el flujo completo hasta éxito sin romper nada.

### A decidir
Es un cambio de flujo real, no solo visual — vale la pena mostrárselo a alguien más antes de darlo por definitivo (agrega un paso/click entre completar el form y ver resultados). Si no convence, volver al checkbox inline es reabrir esta misma tanda.

---

## Tanda 13 — pulido visual: sombras unificadas + animación de "analizando"

- **Sombras de cards unificadas.** Las cards de resultados (`.rcard`) ya tenían la misma sombra en reposo que `.form-card` (`0 1px 3px rgba(0,0,0,.04), 0 8px 24px rgba(0,0,0,.03)`) — lo que no coincidía era el **hover**, que subía a una sombra más pesada. Se sacó esa escalada: `.rcard:hover` ahora solo hace un `translateY(-2px)` sutil, sin cambiar la sombra, así queda igual en reposo y al pasar el mouse — consistente con `.form-card`, que no tiene hover propio. Las 5 cards de la pantalla de detalle (`.card`: requisitos, dónde queda, proceso, FAQ, panel lateral) ya usaban ese mismo valor — no hizo falta tocarlas. Los modales (`0 12px 40px rgba(0,0,0,.18)`) quedaron afuera a propósito: es la sombra más pesada esperable para algo que "flota" por encima de la página.
- **Animación de "analizando":** se cambió el pulso simple (círculo que escala y pierde opacidad) por **radar + ADN formándose**: dos anillos teal que se expanden y se funden en cascada (efecto sonar/radar), y en el centro un ADN a medida en SVG inline (no es un ícono de la webfont — no hay ninguno en el subset embebido, y de cualquier forma un glifo de fuente no puede "armarse" por partes) cuyas dos hebras se dibujan con un efecto de trazo (`stroke-dasharray`/`stroke-dashoffset`) y los 4 travesaños aparecen en cascada a medida que se completa. Se aprovechó el cambio para sacar el `<style>` que se re-inyectaba en cada render de esta pantalla (patrón viejo) y mover las reglas al `<style>` principal, con su propio fallback de `prefers-reduced-motion` (muestra el ADN ya formado y quieto, sin el radar).
  - **Ajuste (a pedido de Luis):** más separación entre la animación y el texto (`gap` 16px → 32px, +16px) y todo más lento — anillos 2.4s → 3.2s, ADN 2.6s → 3.6s (delays de los travesaños reescalados en proporción). Para que el ciclo siga completando justo antes de pasar a resultados, el `setTimeout` a `render('results')` subió de 2600ms a 3600ms, y el intervalo de los 4 textos de paso (`an1`-`an4`) de 600ms a 800ms (si no, quedaban terminados mucho antes que la animación y el texto se veía "trabado" el resto de la espera).
  - Antes de tocar el archivo real, se probaron 3-4 direcciones (anillo giratorio, radar con lupa, radar con distintos íconos, ADN) como previews animados vía la tool de visualización, hasta que Luis eligió esta combinación.

**Validado:** `node --check`, y en el navegador: sombra de `.rcard` idéntica a `.form-card` en reposo (confirmado con `getComputedStyle`) y sin escalada al hover; radar+ADN con la estructura esperada (2 anillos, 2 hebras, 4 travesaños) y la transición a `results` a los 2.6s intacta; flujo completo repetido sin romper nada.

---

## Tanda 14 — a internet: GitHub + GitHub Pages

Pedido de Luis: subirlo a internet para poder mostrarlo con un link, no solo local.

- **Repo privado.** Se inicializó git en la carpeta (no lo era) y se creó `https://github.com/LuisKehm1989/trialnode-prototipo` (privado — el archivo tiene contenido real de Trialtech scrapeado de sus páginas de términos/privacidad, no tenía sentido exponerlo público sin necesidad). `.claude/` (settings locales del harness) va en `.gitignore`, no se sube.
- **Autenticación:** este entorno no tenía `gh` CLI ni credenciales de git guardadas. El primer `git push` falló (el Credential Manager de Windows no pudo abrir el login por navegador desde una herramienta no interactiva) — lo resolvió Luis corriendo `git push` él mismo desde su propia terminal, lo que disparó el login por navegador correctamente. Una vez logueado una vez, quedó guardado a nivel Windows y los pushes posteriores desde acá funcionaron solos.
- **GitHub Pages.** Luis lo activó a mano desde Settings → Pages del repo (Deploy from a branch, `main`, `/`) — eso no se puede hacer por API sin autenticación propia, así que quedó de su lado. Se agregó `index.html` en la raíz con un redirect (`meta http-equiv="refresh"`) al archivo real, para que la URL corta (`https://luiskehm1989.github.io/trialnode-prototipo/`) funcione sin tener que poner el nombre del archivo. **Importante:** un sitio de Pages es público para cualquiera con el link aunque el repo siga privado — Luis lo sabía y lo activó de todos modos.
- **Se probó primero publicarlo como Artifact de Claude** (privado, sin necesidad de GitHub) antes de que Luis aclarara que quería GitHub específicamente. Ese artifact quedó publicado pero es un camino aparte, no depende de este repo.
- **Archivo renombrado:** `match_ensayos_trialnode_fixed.html` → `buscador-de-estudios.html` (a pedido de Luis, ya subido). El redirect de `index.html` y esta referencia de "archivo activo" arriba en el handoff se actualizaron. Cualquier mención vieja a `match_ensayos_trialnode_fixed.html` en el resto de este documento (por ejemplo el backup ya borrado) es histórica, de cuando ese era el nombre.

**Validado:** `git log`/`git status` confirman el push; se abrió la URL real de Pages con el navegador (no `WebFetch`, que no ejecuta JS y solo ve el HTML crudo) y se corrió el flujo completo intake→consentimiento→analyze→results ahí mismo, en vivo.

### Cómo retomar esto en otra sesión
- El repo es `https://github.com/LuisKehm1989/trialnode-prototipo`, rama `main`.
- Para pushear se necesita que Luis haya logueado git con GitHub al menos una vez en esa máquina (Credential Manager de Windows) — si es una máquina nueva, el primer push hay que pedirle que lo corra él.
- Si se agregan archivos nuevos que deban ir al sitio público, recordar que quedan expuestos vía Pages sin login — no subir nada que no deba ser público.

---

## Tanda 15 — fix: overflow horizontal en mobile con nombre de archivo largo

Pedido de Luis, probando en su celular real vía el link de GitHub Pages: al cargar una foto con nombre largo (típico de WhatsApp/cámara), se rompía el responsive en mobile.

- **Causa real:** no era el texto del nombre en sí (ya tenía `white-space:nowrap;overflow:hidden;text-overflow:ellipsis` desde hacía varias tandas) — era que **`.form-card` se salía de su columna del grid**. `#tn .intake-grid{grid-template-columns:1fr;}` (mobile) y `grid-template-columns:1.05fr .95fr;` (desktop) usaban fracciones solas, y por default un grid item tiene un tamaño mínimo automático basado en su contenido — igual que ya nos pasó una vez con el alto (fix de la Tanda 11, `grid-auto-rows:minmax(0,1fr)`), pero esta vez en el ancho. Con un nombre de archivo largo sin espacios, `.form-card` se estiraba a 847px en un viewport de 375px, aunque el grid container medía bien 335px.
- **Fix:** `grid-template-columns:1fr` → `minmax(0,1fr)` (mobile), `1.05fr .95fr` → `minmax(0,1.05fr) minmax(0,.95fr)` (desktop). Con eso el grid item queda realmente acotado a su columna, y ahí sí el `min-width:0` + `ellipsis` que ya estaba en el nombre del archivo puede truncar como corresponde.
- **De paso:** se blindó también `.up-filled` (la caja del archivo cargado) con `width:100%;box-sizing:border-box;overflow:hidden` — no era la causa raíz, pero es la misma defensa que ya tenía `.up-dropzone` (la caja vacía) y no estaba en la versión "llena".

**Validado:** con un nombre de archivo de ~95 caracteres sin espacios (`WhatsApp_Image_2026-07-31_at_14.23.45_historia_clinica_completa_version_final_definitiva.jpeg`) en mobile (375px): sin overflow horizontal (`scrollWidth === clientWidth === 375`), `.form-card` mide los 335px correctos, y el nombre se trunca visualmente (quiere 680px, se corta a 175px visibles). En desktop (1440px) con el mismo nombre: tampoco hay overflow y los altos de `.intake-pitch`/`.form-card` siguen iguales entre sí (744px, sin regresión del fix de la Tanda 11). Flujo completo repetido de punta a punta sin romper nada.

### Estado al cierre del día
- Esta tanda se subió a GitHub (commit + push) antes de cerrar. Repo: `https://github.com/LuisKehm1989/trialnode-prototipo` (privado) · Pages: `https://luiskehm1989.github.io/trialnode-prototipo/` (pública, redirige a `buscador-de-estudios.html`). El fix del overflow ya está en producción — puede tardar 1-2 min en propagar por caché de GitHub Pages.
- Retomar mañana desde este handoff (Tanda 15 fue la última). Archivo activo: `buscador-de-estudios.html`.
