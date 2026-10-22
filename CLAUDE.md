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
- **Secciones en orden**: header/nav → hero (patente + sellos) → cinta de trámites → servicios (3 "carpetas" con los trámites del flyer; cada uno es una fila `.svc`) → infracciones (ticket + cámara de calle SVG de fondo, `.cam`) → "Así de simple" → Quiénes somos (`#nosotros`) → Testimonios (`#testimonios`) → CTA final → footer + botón flotante.
- **Foto en infracciones (solo en la rama `foto-camara-infracciones`)**: `assets/camara-transito.jpg`, foto CC0 de Wikimedia Commons (créditos y licencia en `assets/CREDITOS.md`). En esa rama la ilustración SVG `.cam` queda oculta (`display:none`) porque la foto la reemplaza. Si se suma otra imagen de internet, verificar licencia y dejarla asentada en `assets/CREDITOS.md`.
- **Paleta actual**: sale de una referencia que pasó la clienta (blancos azulados + azul marino, sin amarillo). El acento es `--accent` (celeste); no reintroducir el amarillo.
- **Links de WhatsApp**: cada `<a data-wa="mensaje">` recibe su `href` por JS al final del archivo (`wa.me/<NUM>?text=<mensaje codificado>`). Para cambiar el número, editar la constante `NUM` (formato `549` + área + número, sin `+`) y los `href` de respaldo. Para un trámite nuevo, copiar una fila `.svc` y cambiar su `data-wa`.
- **Animaciones**: clase `.rv` + `IntersectionObserver` (aparición al scroll); respeta `prefers-reduced-motion`.
- Responsive con media queries a 900px y 520px al final del `<style>`.

## Decisiones de diseño a respetar

- **El verde (`--wa`) es solo para tres CTA**: botón principal del hero, botón flotante y botón del cierre. El resto usa variantes sin verde (`.btn-wa.ghost` en el nav, `.btn-wa.paper` en infracciones) y el amarillo/blanco hueso. Nico pidió bajar el verde; no volver a esparcirlo.
- Look deliberadamente no genérico: patente argentina, carpetas de trámite, ticket de infracción. Mantener ese lenguaje visual al agregar secciones.
- Los textos de "Así de simple" son provisorios (los inventé; ella debe confirmarlos). No inventar datos reales de ella (dirección, horarios, precios, plazos).
- **Los testimonios son de ejemplo**: cada tarjeta lleva la etiqueta "Ejemplo", un nombre genérico y hay una nota visible y un comentario HTML. Se reemplazan por opiniones reales (con permiso del cliente) antes de publicar; nunca presentar reseñas inventadas como reales.
- Copy que pasó la clienta por WhatsApp y ya está aplicado: el texto del CTA final ("Tu tranquilidad empieza con una buena gestión… Hablemos de tu trámite") y el pie de página, que debe quedar **solo** con "Gestoría Automotor Efata · Ezeiza, Buenos Aires" y "Atención personalizada". La ubicación (Ezeiza, Buenos Aires) sale de ahí. El texto de "Quiénes somos" viene de la referencia que ella compartió; confirmarlo con ella.

## Trampas conocidas

- **Un formateador (probablemente del editor) reescribe `index.html` en disco** y llegó a revertir ediciones previas (patente rota, título invadiendo la columna). Después de editar, volver a leer/verificar que los cambios sigan, y re-capturar si se toca el hero.
- La patente usa `display:flex` + `white-space:nowrap` en `.plate-chars`; con `display:grid` los caracteres se apilan y se corta.
- Los grids con `1fr` pueden desbordarse por contenido ancho (títulos largos): usar `minmax(0, …)`.

## Pendientes (no están en la v1)

- Logo original de ella (el del auto es un trazo redibujado) y fotos.
- Dominio, hosting, zona/dirección y horarios.
- Ideas futuras ya guardadas en Trello (BrainStorm → Ideas 💡, tarjeta "Gestora Efata: descripciones de trámites + minitutoriales en video"): descripción breve de cada trámite (hay un lugar reservado en `.svc .t small`; el contenido lo valida ella) y minitutoriales en video.
- Si ella aprueba el diseño y piden funcionalidades (turnos, formularios), migrar a Next.js + Tailwind (el stack recomendado en la conversación).
