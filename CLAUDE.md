# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es esto

Landing de una sola página para **Gestoría Automotor Efata** (gestora automotriz, Argentina), primera clienta de Nico. La v1 es un **boceto de diseño** para que ella lo apruebe; las funcionalidades se suman después. Alcance de la v1: solo informativa, con **WhatsApp como único llamado a la acción**. Sin formularios, turnos, login, backend ni base de datos.

El `CLAUDE.md` de la carpeta padre (`claude-proyectos/`) define el rol y el tono de trabajo con Nico (español, asesor técnico, explicar el *por qué*). Respetarlo.

## Comandos

No hay build, dependencias ni tests. Todo es el archivo `index.html` (HTML + CSS + JS inline).

- Ver la página: abrir `index.html` en el navegador (Ctrl+F5 para saltear caché).
- Captura headless para verificar el layout (Windows, Chrome):
  `chrome.exe --headless=new --disable-gpu --hide-scrollbars --virtual-time-budget=4000 --window-size=1280,900 --screenshot=<salida.png> file:///<ruta>/index.html`
  Chrome headless no baja de ~500px de ancho de ventana, así que una captura "móvil" a 390px sale recortada: usar 500px para chequear móvil.

## Arquitectura (todo en `index.html`)

- **Tokens de diseño** en `:root`: paleta (`--navy`, `--navy-deep`, `--paper`, `--yellow`, `--wa`) y tipografías (`--display` Barlow Condensed para títulos/etiquetas, `--body` Plus Jakarta Sans para texto corrido). Cambiar colores/fuentes ahí, no inline. Las fuentes vienen de Google Fonts (`<link>` en el `<head>`; si se cambia una familia, actualizar también ese link).
- **Secciones en orden**: header/nav → hero (patente + sellos) → cinta de trámites → servicios (3 "carpetas" con los trámites del flyer; cada uno es una fila `.svc`) → infracciones (ticket + cámara de calle SVG de fondo, `.cam`) → Recupero de CAT (`#recupero`, badge con rayo + "rápido", "lo más rápido posible" — la clienta descartó la promesa de "24 hs") → "Así de simple" → Quiénes somos (`#nosotros`) → Testimonios (`#testimonios`) → CTA final → footer + botón flotante.
- **Infracciones según el diseño de la clienta (solo en la rama `foto-camara-infracciones`)**: la sección sigue `assets/camara-section.jpeg` (cámara de fondo con degradé, franjas diagonales en las esquinas, curva celeste, ticket claro con círculo de WhatsApp). La cámara en escritorio/tablet es `assets/camara-fondo.jpg`, generada con **Python + Pillow** desde `camara-section.jpeg` (hace falta Pillow: `python -m pip install Pillow`). Receta: recorte de la foto real `src.crop((550,24,1012,812))` (cámara + visera + poste + calle, foto nítida), se tapan los restos de texto del borde izquierdo con un overlay `navy` suave (la "S") y **clonando una banda de calle borrosa de más arriba** sobre el párrafo inferior-izquierdo (mantiene el bokeh, no un azul plano), y se escala ×2 con `UnsharpMask`. Queda foto real limpia, sin texto ni costuras. `.fines-photo-bg` la muestra a la derecha con `background-size: cover` + `aspect-ratio: 924/1576`: **no se estira ni deforma** en ningún ancho (probado 700/800/900/1280). En 641-900px la foto baja a 50% y el texto se acorta para que no se toquen. Si la clienta consigue la foto original de la cámara sin texto, reemplazarla directamente. **Sin difuminar la cámara**: Nico rechazó eso dos veces; los únicos fundidos permitidos son los del borde hacia el azul del fondo (fiel al diseño). La ilustración SVG `.cam` y la foto CC0 de internet se descartaron en esta rama. Los mockups de la clienta viven en `assets/` (`hero-section.jpeg`, `camara-section.jpeg`, `quineeSomos.md` con el texto de Quiénes somos): son la referencia a respetar.
- **Infracciones en celular (≤640px) = la imagen del diseño tal cual**: Nico pidió que quede *idéntico* a `assets/camara-section.jpeg`, y el fondo (escena fotográfica borrosa) no se puede rearmar con código. Por eso en el último bloque `@media (max-width: 640px)` de `index.html` se muestra un `<img class="fines-mobile">` con esa imagen (en escritorio va `display:none`): al estar en flujo con `width:100%; height:auto` **define el alto de la sección y se ajusta a cualquier ancho manteniendo su proporción** (responsive, no se estira). Encima van los elementos armados con HTML ocultos, el texto real `sr-only` (buscadores / lectores de pantalla) y los dos botones (`.fines-copy .btn-wa` y `.ticket .tk-wa`): links reales transparentes, ubicados en % exactos sobre los de la imagen, con animación al tocar (destello/hundido en el blanco, anillo `ping` pulsante en el círculo verde). **Si cambia el diseño de la imagen, hay que recalcular esos % (left/top/width/height).** En escritorio (>640px) sigue la versión armada con HTML + `assets/camara-fondo.jpg`. El texto de la imagen no es editable: para cambiarlo hay que pedir otra imagen.
- **Paleta actual**: sale de una referencia que pasó la clienta (blancos azulados + azul marino, sin amarillo). El acento es `--accent` (celeste); no reintroducir el amarillo.
- **Links de WhatsApp**: cada `<a data-wa="mensaje">` recibe su `href` por JS al final del archivo (`wa.me/<NUM>?text=<mensaje codificado>`). Para cambiar el número, editar la constante `NUM` (formato `549` + área + número, sin `+`) y los `href` de respaldo. Para un trámite nuevo, copiar una fila `.svc` y cambiar su `data-wa`.
- **Animaciones**: clase `.rv` + `IntersectionObserver` (aparición al scroll); respeta `prefers-reduced-motion`.
- Responsive con media queries a 900px y 520px al final del `<style>`.

## Decisiones de diseño a respetar

- **El verde (`--wa`) se usa poco**: de fondo solo en el botón flotante, el botón del cierre y el círculo de WhatsApp del ticket. El botón del hero es blanco (`.btn-wa.paper.wa-icon.arrow`) con ícono verde y flecha, como en `assets/hero-section.jpeg`. El resto usa variantes sin verde (`.btn-wa.ghost` en el nav, `.btn-wa.paper` en infracciones). Nico pidió bajar el verde; no volver a esparcirlo.
- Look deliberadamente no genérico: patente argentina, carpetas de trámite, ticket de infracción. Mantener ese lenguaje visual al agregar secciones.
- Los textos de "Así de simple" son provisorios (los inventé; ella debe confirmarlos). No inventar datos reales de ella (dirección, horarios, precios, plazos).
- **Los testimonios son de ejemplo**: los nombres (Martín Gutiérrez · Ezeiza, Carla Benítez · Monte Grande, Diego Fernández · Cañuelas) y las frases son **falsos, de muestra**. Nico pidió sacar la nota aclaratoria y las etiquetas "Ejemplo" visibles (2026-10-26) por diseño; **queda solo un comentario HTML** marcándolos como placeholder. **Siguen siendo inventados: hay que reemplazarlos por opiniones reales (con permiso de cada cliente) antes de publicar** — ya se le advirtió a Nico; no presentar reseñas falsas como reales en producción.
- Copy que pasó la clienta por WhatsApp y ya está aplicado: el texto del CTA final ("Tu tranquilidad empieza con una buena gestión… Hablemos de tu trámite") y el pie de página, que debe quedar **solo** con "Gestoría Automotor Efata · Ezeiza, Buenos Aires" y "Atención personalizada". La ubicación (Ezeiza, Buenos Aires) sale de ahí. El texto de "Quiénes somos" es el de `assets/quineeSomos.md` (suyo). Los cuatro puntos con tilde debajo (atención personalizada, consultas sin cargo, etc.) los armé yo con datos del flyer: confirmarlos con ella.

## Trampas conocidas

- **Un formateador (probablemente del editor) reescribe `index.html` en disco** y llegó a revertir ediciones previas (patente rota, título invadiendo la columna). Después de editar, volver a leer/verificar que los cambios sigan, y re-capturar si se toca el hero.
- La patente usa `display:flex` + `white-space:nowrap` en `.plate-chars`; con `display:grid` los caracteres se apilan y se corta.
- Los grids con `1fr` pueden desbordarse por contenido ancho (títulos largos): usar `minmax(0, …)`.

## Pendientes (no están en la v1)

- Fotos de ella / del local. El header usa su logo **redibujado en SVG inline** (`svg.brand-logo`, en blanco sobre el azul): auto con techo y parabrisas, espejos y tres curvas, más "EFATA" y "GESTORÍA AUTOMOTOR" como texto SVG con las fuentes de la página. El dibujo del auto es un `<symbol id="car-mark">` compartido (definido junto al de WhatsApp) que usan el header y la tarjeta de Quiénes somos: cambiarlo ahí cambia los dos. Es una aproximación del `logo-efata.jpeg` original (negro, 3D, sobre blanco). Si manda el logo en vector, PNG transparente o en blanco, reemplazar el SVG por el archivo real.
- Dominio, hosting, zona/dirección y horarios.
- Ideas futuras ya guardadas en Trello (BrainStorm → Ideas 💡, tarjeta "Gestora Efata: descripciones de trámites + minitutoriales en video"): descripción breve de cada trámite (hay un lugar reservado en `.svc .t small`; el contenido lo valida ella) y minitutoriales en video.
- Si ella aprueba el diseño y piden funcionalidades (turnos, formularios), migrar a Next.js + Tailwind (el stack recomendado en la conversación).
