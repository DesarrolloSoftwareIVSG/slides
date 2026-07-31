# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

Presentaciones (slides) del curso *Desarrollo de Software IV* de la UCR, escritas en Markdown y renderizadas con **Marp** (`marp-cli` + `bespoke`). El contenido está en español. Cada tema es un mazo independiente: `src/<tema>/index.md`.

## Comandos

```bash
pnpm dev      # servidor con recarga en caliente sobre ./src (http://localhost:8080)
pnpm build    # renderiza todos los mazos a ./dist y copia src/assets -> dist/assets
pnpm deploy   # publica ./dist en la rama gh-pages
```

- `marp-cli` **no** está en `devDependencies`; los scripts asumen `marp` disponible en el PATH (instalación global) o vía `npx @marp-team/marp-cli`. Solo `cpx` y `gh-pages` son dependencias locales.
- El gestor de paquetes es **pnpm** (`packageManager: pnpm@11.18.0`).
- No hay tests ni linters configurados. La verificación es visual: `pnpm dev` y revisar la diapositiva.
- Los flags son parte del contrato: `--html=true` (los mazos usan HTML embebido y `<script>`), `--bespoke.progress=true`, `--bespoke.osc=false`. No los quites.

## Arquitectura

### Temas CSS (`src/theme/`)

`alo.css` (oscuro, el que usan **todos** los mazos) y `alolight.css` (claro, variante sin usar por ahora). Marp CLI los registra automáticamente porque `-I ./src` escanea el directorio de entrada en busca de CSS con la cabecera `/* @theme <nombre> */`; por eso `theme: alo` en el front matter funciona sin archivo de configuración de Marp (no existe `.marprc`).

Cambiar `alo.css` afecta a los 18 mazos a la vez. Si tocas algo en `alo.css`, replica el cambio en `alolight.css` para que las dos variantes no se desincronicen.

### Elementos personalizados definidos por el tema

El tema estiliza etiquetas HTML inventadas (no son Web Components reales, solo CSS + un script):

- `<steps>` / `<step>` — revelado progresivo. `src/assets/steps.js` recorre los `<steps>`, añade `data-marpit-fragment` a cada `<step>` salvo el primero (para que bespoke los trate como fragmentos) y pinta un indicador `n / total` en la esquina. El CSS de `alo.css` es el que realmente muestra/oculta según `data-bespoke-marp-fragment`. Los dos lados —CSS y JS— tienen que cambiar juntos.
- `<split-slide>` — grid de dos columnas, ajustable con las variables inline `--left`, `--right`, `--font-size`.
- `<spoiler>` — texto oculto hasta el hover.
- `.grid` — grid auto-fit de tarjetas.

#### Líneas en blanco alrededor de las etiquetas inventadas

`<steps>`, `<step>` y `<split-slide>` **no** están en la lista de etiquetas de bloque de markdown-it (a diferencia de `<div>`), así que **no pueden interrumpir un párrafo o una lista**. Si un `</step>` va pegado a la última línea de una lista o un párrafo, markdown-it lo trata como texto en línea, se lo traga dentro del `<li>` y el revelado progresivo se rompe en silencio (el indicador muestra `1 / 1` y todos los pasos se ven a la vez).

Regla: deja una **línea en blanco antes y después** de cada `<steps>`, `<step>`, `</step>`, `</steps>` y `</split-slide>` cuando el vecino sea contenido Markdown (lista, párrafo, imagen, blockquote):

```markdown
<steps>
<step>

- Primer punto
- Último punto del paso        ⬅ línea en blanco obligatoria antes de </step>

</step>
<step>

![w:850 contain](../assets/diagrama.png)

</step>
</steps>
```

No hace falta si el vecino ya es un bloque HTML (`</div>`) o un bloque de código cerrado con ``` — esos sí cierran el bloque por sí solos, que es por lo que los mazos con `<step>` de puro código funcionan sin la línea extra. Ante la duda, ponla siempre.

### Scripts de assets

Se incluyen **al final** del `index.md` que los necesita, con rutas relativas:

```html
<script src="../assets/steps.js"></script>
<script src="../assets/image-modal.js"></script>
```

`image-modal.js` convierte los enlaces a imágenes (`.png`, `.jpg`, `.svg`, …) en un lightbox; `alo.css` les añade además un icono con `::before`. Si un mazo usa `<steps>` o enlaces a imágenes y no se ven bien, lo primero a revisar es si falta el `<script>` correspondiente.

### Convención de un mazo

```markdown
---
marp: true
theme: alo
paginate: true
---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00001_dom.png);
}
</style>
# Título
## Contenidos
- ...
```

La clase `cover` es la única clase de diapositiva del tema; la imagen de fondo se pasa siempre por la variable `--cover` dentro de un `<style scoped>`, nunca hardcodeada en el CSS. Los mazos cierran con una sección `## Referencias` (enlaces a MDN, lenguajejs.com, bootcamp.manz.dev) y la mayoría acredita en la portada el bootcamp de Manz.dev.

### Assets

Todo vive plano en `src/assets/` (imágenes, demos HTML sueltas como `audio.html` o `dragdrop.html`, `api/data.json`). Las rutas desde los mazos son siempre `../assets/...`, lo que funciona igual en `pnpm dev` (sirve `src/`) y en `dist/` (gracias al `cpx` del build).

El build solo emite un `index.html` por mazo: `marp` no copia nada que no sea Markdown y el `cpx` únicamente traslada `src/assets`. En cambio `pnpm dev` sirve **todo** `src/` como estático, así que cualquier archivo suelto que dejes ahí queda accesible por URL. Por eso los PDFs de material de origen que hay junto a algunos `index.md` están en `.gitignore` (`src/*/*.pdf`): son ~41 MB que no aportan al sitio.
