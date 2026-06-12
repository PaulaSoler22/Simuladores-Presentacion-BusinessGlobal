# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es

Presentación interactiva de una sola página para el simulador de negocios **BusinessGlobal** (plataforma **CompanyGame**), usada comercialmente por **Simuladores de Negocios Colombia / Grupo Edutec** para demostrar el simulador a universidades colombianas (decanos, directores de programa y profesores de Administración, Economía e Ingeniería Industrial). El idioma de todo el contenido visible es español.

## Ejecutar

No hay build, lint ni tests. Es un único archivo estático que se abre directamente en el navegador:

```bash
start index.html      # Windows
open index.html       # macOS
xdg-open index.html   # Linux
```

## Arquitectura

Todo vive en **`index.html`** (~1800 líneas). No hay dependencias ni archivos externos salvo Google Fonts (Montserrat, Open Sans) por CDN. Tres bloques dentro del mismo archivo:

- **CSS** — dentro de `<style>` en el `<head>` (líneas ~7–686). Usa variables CSS como design tokens (`--primary`, `--accent`, etc.) y animaciones keyframe (`fadeIn`, `slideInRight`, `pulse`, `float`).
- **HTML** — los slides son `<div class="slide" id="slideN">` con N de 1 a 11. Solo el slide con la clase `active` es visible. Hay un `introOverlay` que se oculta al iniciar.
- **JS** — `<script>` antes de `</body>` (líneas ~1742–1811). Vanilla, sin framework.

### Modelo de navegación

Estado global: `currentSlide` (empieza en 1) y la constante **`totalSlides = 11`**. `updateSlide()` es la única función que toca el DOM de navegación: alterna la clase `active`, habilita/deshabilita `prevBtn`/`nextBtn`, cambia el texto del botón a "✓ Finalizar" en el último slide, actualiza `slideCounter` (`X / 11`) y la `progressBar`, y hace scroll al tope.

Entradas de navegación: botones Anterior/Siguiente, teclado (`→`/Espacio = siguiente, `←` = anterior) y swipe táctil (umbral de 50px).

Acordeones de contenido: `toggleAccordion(header)` y `toggleFactor(header)` reciben el **elemento header** (vía `this` en el onclick) y alternan la clase `active` en `header.parentElement`.

## Al editar

- **Agregar o quitar un slide** requiere dos cambios coordinados: el `<div class="slide" id="slideN">` correspondiente **y** la constante `totalSlides` en el script. El conteo de slides está codificado, no se deriva del DOM.
- Las imágenes están embebidas como **base64** dentro del HTML; por eso el archivo es grande. No hay carpeta de assets.
- Mantén los IDs `slideN` consecutivos sin huecos: `updateSlide()` itera de 1 a `totalSlides` y fallará si falta alguno.
