# Kyme Software — sitio web

Sitio de una sola página, sin build ni dependencias. Se abre haciendo doble clic
en `index.html` o se sube tal cual a cualquier hosting estático (Vercel, Netlify,
Cloudflare Pages, o un hosting común por FTP).

## Estructura

```
index.html      El sitio completo. Fuentes y logo van incrustados dentro,
                así que el archivo funciona solo, sin conexión.
brand/          El logo extraído del original, con fondo transparente.
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
3. **Contacto**: `hola@kymesoftware.com`, el WhatsApp y el LinkedIn son de
   ejemplo. Aparecen en tres lugares — la sección de contacto, el menú móvil
   y el JavaScript del formulario.

## El formulario

No tiene backend. Al enviar arma el correo y abre el cliente de mail del
visitante con todo escrito. Si quieres que los mensajes lleguen a una bandeja
o a un CRM, se conecta con Formspree, Basin o una función serverless cambiando
el `addEventListener("submit", ...)` del final del archivo.

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
