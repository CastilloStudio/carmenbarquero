# Carmen Barquero Psicología — sitio web

Sitio estático de una consulta de psicología general sanitaria.
**En producción: https://carmenbarqueropsicologia.es**

HTML, CSS y JavaScript escritos a mano. Sin framework, sin bundler y sin
`node_modules`: lo que hay en el repositorio es, casi, lo que se sirve. El
`build` solo optimiza.

## Ramas: trabaja en `source`, nunca en `main`

- **`source`** — la rama de trabajo y donde está el código fuente.
- **`main`** — **artefacto generado**. El workflow `build-inline.yml` compila en
  cada push a `source` y hace `git push origin main --force` sobre una rama
  huérfana.

Cualquier commit hecho a mano en `main` desaparece sin aviso en la siguiente
compilación. Si algo se ve mal en producción, se arregla en `source`.

## Reglas que no son negociables

### Los textos legales no se tocan de pasada

`aviso-legal.html`, `politica-de-privacidad.html` y `politica-cookies.html` no son
contenido de relleno: son las obligaciones de la LSSI-CE y del RGPD de una consulta
sanitaria, y dicen cosas concretas y comprobables.

Si un cambio toca **qué datos se piden, a quién se mandan o dónde acaban**, la
política de privacidad se actualiza en el mismo commit. Eso incluye añadir un campo a
un formulario.

### Los consentimientos se rellenan en el navegador y no salen de ahí

`js/pdf-fill.js` y `js/pdf-fill-pareja.js` generan el PDF de consentimiento
**íntegramente en local**, con `pdf-lib`. Los datos que escribe el paciente —nombre,
DNI, fecha de nacimiento, contacto, domicilio— **no se envían a ningún servidor**.

Los `fetch` que hay en esos archivos cargan la plantilla y la firma; no suben nada.
Es una decisión de diseño, no una casualidad: **no añadas ahí una llamada de red.**

Los PDF de `docs/` son **plantillas en blanco**. No se sube a este repositorio ningún
documento relleno.

### La analítica solo arranca con consentimiento

`js/cookie-consent.js` usa Consent Mode v2 con todo en `denied` por defecto y **solo
carga GTM cuando la persona acepta**. Al retirar el consentimiento, borra las cookies
`_ga` del host y de los dominios padre.

Cargar cualquier script de terceros fuera de esa puerta rompe la política de cookies
publicada. Si hace falta uno nuevo, pasa por el mismo mecanismo y se declara.

### El formulario de contacto va a un tercero, y está declarado

`js/contacto-form.js` envía a **Formspree**, que es un encargado del tratamiento en
Estados Unidos. Está nombrado en la política de privacidad junto con la transferencia
internacional. Si se cambia de proveedor, se cambia también ese texto.

### No bajar las puntuaciones

El sitio va en 100 de rendimiento y SEO y 93 de accesibilidad. La carpeta
`lighthouse/` guarda los informes. Antes de añadir una imagen sin optimizar, una
fuente más o un script que bloquee el render, mira lo que cuesta.

Las imágenes van en **WebP**. Las fuentes están servidas desde el propio dominio, en
`fonts/`.

## Estructura

```
index.html, contacto.html, reserva-cita.html, ficha-clinica.html
servicios/            Una página por servicio
consentimientoinformado*.html   Formularios que generan el PDF
aviso-legal.html, politica-*.html   Textos legales
css/                  base, layout, fonts, menu-superior, modal, breakpoints
js/                   Ver las reglas de arriba
  pdf-lib.min.js      Dependencia vendorizada (512 KB)
img/                  WebP + favicons
docs/                 Plantillas de consentimiento en blanco, en PDF
fonts/, lib/, lighthouse/
sitemap.xml, robots.txt, llms.txt, CNAME
```

`breakpoints.css` centraliza las media queries: el corte móvil está en 768px. No
añadas media queries sueltas en otros archivos.

## SEO y datos estructurados

El sitio lleva Schema.org (`ProfessionalService`, servicios y grafo de entidades),
Open Graph, sitemap y un `llms.txt`. Hay datos que aparecen en más de un sitio a la
vez —número de colegiada, dirección, precios— y tienen que decir lo mismo en el HTML
visible, en el JSON-LD y en `docs/ficha-maestra-directorios.md`.

Si cambia un precio o un dato de contacto, búscalo en todos: `grep -rn` antes de dar
por hecho que está en un solo archivo.

## Mantenimiento

Lo mantiene **Castillo Studio** (castillostudio.es). Los pull request los mezcla
Emilio.

Este repositorio es **público**. Antes de escribir algo aquí —código, comentario o
mensaje de commit—, la pregunta es si molestaría verlo citado por un tercero. Ningún
dato de ningún paciente entra aquí, en ninguna forma y por ningún motivo.
