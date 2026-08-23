# Kyme Software — sitio web

Sitio de una sola página, sin build ni dependencias. Se abre haciendo doble clic
en `index.html` o se sube tal cual a cualquier hosting estático (Vercel, Netlify,
Cloudflare Pages, o un hosting común por FTP).

## Estructura

```
index.html      El sitio completo. Fuentes y logo van incrustados dentro,
                así que el archivo funciona solo, sin conexión.
brand/          El logo extraído del original con fondo transparente,
                los favicons y la tarjeta para compartir (og-image.png).
fonts/          Clarity City en woff2, por si prefieres enlazarlas en vez
                de incrustarlas.
```

`index.html` pesa ~216 KB porque lleva las 3 fuentes y el logo adentro en base64.
Si prefieres que el navegador los cachee por separado, reemplaza los `data:` por
rutas a `fonts/` y `brand/` — el sitio funciona igual.

## Marca

Todos los colores salen del logo, ninguno fue inventado:

| Token | Valor | De dónde sale |
|---|---|---|
| `--ink` | `#181818` | El negro del logo |
| `--grey` | `#807F80` | El gris de "SOFTWARE" |
| `--grey-2` | `#9F9F9F` | El gris claro del logo |
| `--paper` | `#FEFEFE` | El fondo del archivo original |
| `--wash`, `--rule`, `--rule-2` | | Neutros derivados de los anteriores |

No hay color de acento. El contraste lo dan la banda negra del Proceso y el
blanco del resto. El círculo abierto sobre una línea que aparece en los
marcadores de sección, en el hover de Servicios y en el "+" de las preguntas
frecuentes es el nodo de circuito del propio isotipo.

## Qué falta reemplazar

Está marcado con comentarios `PLACEHOLDER` dentro de `index.html`:

1. **Los tres casos** (Nórdika, Ríoplata Capital, Turnos.ar) son inventados,
   igual que sus métricas. Las maquetas son CSS puro: si tienes capturas,
   cambia el bloque `.shot` por `<img src="..." alt="...">`.
2. **Los números** (48 proyectos, 6 años, 96 Lighthouse, 24 h) son de relleno.
3. **Contacto**: el único canal es el correo `kymesolutions@gmail.com`.
   Aparece en cuatro lugares — la sección de contacto, el `action` del
   formulario, el `fetch` del JavaScript y el JSON-LD del `<head>`.

## El formulario

Los mensajes llegan a `kymesolutions@gmail.com` a través de **FormSubmit**
(gratis, sin cuenta ni backend propio).

**Hay que activarlo una sola vez.** La primera vez que alguien envíe el
formulario desde el dominio publicado, FormSubmit manda un correo de
confirmación a esa casilla con un enlace. Hasta que no se haga clic en ese
enlace, ningún mensaje se entrega. Se activa una vez y no se vuelve a tocar.

Cómo se comporta:

1. Con JavaScript, el envío va por `fetch` y el visitante no sale de la página.
   La nota bajo el botón hace de acuse de recibo.
2. Si la red o el servicio fallan, se abre el cliente de correo con el mensaje
   ya redactado — el plan B de antes, ahora sólo como red de seguridad.
3. Sin JavaScript, el `<form>` hace su POST normal y vuelve a `?enviado=1`,
   que muestra el mismo mensaje de éxito.

Hay un campo trampa (`_honey`) escondido fuera de pantalla: si un bot lo llena,
el envío se descarta.

Para mudarlo a otro servicio (Formspree, Basin, una función serverless) hay que
cambiar dos cosas: el `action` del `<form>` y la URL del `fetch` en el bloque
`Formulario` al final del archivo.

## Cómo se ve al compartir el enlace

`brand/og-image.png` (1200×630) es la tarjeta que muestran Facebook, WhatsApp,
LinkedIn, Telegram y X. Es el lockup en blanco sobre el negro de la marca, con
el lema debajo. Hay una variante clara lista en `brand/og-image-light.png`:
para usarla, cambia el nombre en las tres etiquetas `og:image` /
`twitter:image` del `<head>`.

Si vuelves a generar la imagen, **sube el `?v=1`** de esas etiquetas a `?v=2`.
Facebook y WhatsApp cachean la tarjeta por semanas y sin ese cambio siguen
mostrando la vieja. Para forzar el refresco: pega la URL en el
[depurador de Facebook](https://developers.facebook.com/tools/debug/) y pulsa
*Scrape Again*.

El `<head>` también lleva un bloque JSON-LD (`ProfessionalService`) con el
logo, el correo y los servicios, para lo que Google muestra en resultados.

## Tipografía

**Clarity City**, publicada por VMware bajo la SIL Open Font License 1.1.
La licencia permite uso comercial y web sin costo, pero pide que se conserve
el aviso de copyright. Antes de publicar, descarga el `OFL.txt` del repositorio
oficial y déjalo junto a `fonts/`.

## El hero y el objeto 3D

El hero es una sola pieza: **KYME** en grande al fondo y, delante, una masa de
vidrio en 3D. No es un vídeo ni una librería — es un *raymarcher* WebGL escrito
a mano al final de `index.html`. La forma sale de nueve esferas fundidas con
`smin` que orbitan lentamente, y el material mezcla reflexión y transmisión por
Fresnel, que es lo que la hace ver como cristal y deja leer la tipografía a
través de ella.

Para retocarlo, todo está en el bloque `FRAG`:

| Qué cambiar | Dónde |
|---|---|
| Forma y grumosidad | `map()` — el `0.15` de `smin` funde más o menos los lóbulos |
| Velocidad | `float t=uTime*0.30` |
| Luz y contraste | `envMap()` |
| Transparencia | la línea de `alpha` al final |

Si el navegador no tiene WebGL, el hero simplemente se queda con la tipografía.
Con `prefers-reduced-motion` se dibuja un solo fotograma, sin bucle.

## La barra inferior

No hay cabecera: navega la barra flotante. Además de navegar, **es ella quien
pinta el fondo de las secciones que cambian de color**: cada sección nace con
la forma exacta de la barra y se abre con `clip-path` hasta inundar la pantalla.
Al salir de la banda oscura, el negro queda colgando de la barra y se recoge
dentro. Los parámetros están en `paintMorphs()`; `MORPH_RANGE` controla en
cuántos píxeles de scroll ocurre la apertura.

Las secciones que participan llevan `data-morph` y `data-tone`; la que además
tiene `data-hang` es la que hace la animación de salida.

## Accesibilidad y detalles

- Respeta `prefers-reduced-motion`: con movimiento reducido no queda nada oculto.
- Sin JavaScript el sitio se ve completo — las animaciones de scroll sólo se
  activan si el navegador puede revertirlas.
- Los números de las métricas están escritos en el HTML; el contador los anima
  desde cero, no los inventa.
- Foco de teclado visible en todos los controles.
- El texto está en español peruano (tuteo).
