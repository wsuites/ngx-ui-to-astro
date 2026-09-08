# ngx-ui → Astro

Ports a Astro de 7 efectos seleccionados de `@omnedia` NGXUI, **sin runtime de Angular**.
Monorepo con un paquete npm independiente por componente.

## Paquetes

| Efecto                | Paquete npm                                | Núcleo real                     |
| --------------------- | ------------------------------------------ | ------------------------------- |
| BackgroundBeams       | `ngx-astro-background-beams`               | canvas + SVG, autocontenido     |
| DarkVeil              | `ngx-astro-dark-veil`                      | WebGL con [`ogl`](https://www.npmjs.com/package/ogl) |
| FlickeringGrid        | `ngx-astro-flickering-grid`                | canvas, autocontenido           |
| WordMorph             | `ngx-astro-word-morph`                     | CSS/SVG filters, autocontenido  |
| Highlighter           | `ngx-astro-highlighter`                    | wrapper de [`rough-notation`](https://www.npmjs.com/package/rough-notation) |
| Timeline              | `ngx-astro-timeline`                       | CSS + TS, autocontenido         |
| ShineBorder           | `ngx-astro-shine-border`                   | CSS, autocontenido              |

## Cómo funciona

Cada componente Angular original es un cascarón (`.ts` + `.scss` + plantilla) que envuelve
efectos escritos en TypeScript/CSS vanilla o en `ogl` / `rough-notation`. Este monorepo
transcribe ese núcleo a componentes `.astro` que se ejecutan en el cliente sin dependencias de
Angular. Los paquetes se publican **tal cual** (no requieren build previo: Astro compila
`.astro` directamente desde `node_modules`).

## Demo

```bash
npm install
npm run dev
# o bien:
npm run build && npm run preview
```

## Uso en otro proyecto Astro

```bash
npm install ngx-astro-background-beams   # solo la que necesites
```

```astro
---
import BackgroundBeams from 'ngx-astro-background-beams';
import Highlighter from 'ngx-astro-highlighter';
---

<BackgroundBeams pathColor="#ffffff12" pathQuantity={40}>
  <h1>Hola mundo</h1>
</BackgroundBeams>

<Highlighter color="#f59e0b">texto resaltado</Highlighter>
```

Cada paquete exporta una sola entrada: `import Componente from 'ngx-astro-{efecto}'`.
Instala solo el paquete que uses para no arrastrar dependencias innecesarias.

### Props por componente

**`BackgroundBeams`**
| Prop | Default |
| --- | --- |
| `gradientColorValues` | `['#18CCFC', '#6344F5', '#AE48FF']` (exactamente 3) |
| `pathColor` | `'#00000025'` |
| `pathQuantity` | `50` |

**`DarkVeil`**
| Prop | Default |
| --- | --- |
| `hueShift` | `0` |
| `noiseIntensity` | `0` |
| `scanlineIntensity` | `0` |
| `speed` | `1` |
| `scanlineFrequency` | `0` |
| `warpAmount` | `0` |
| `resolutionScale` | `1` |

**`FlickeringGrid`**
| Prop | Default |
| --- | --- |
| `squareSize` | `4` |
| `gridGap` | `6` |
| `flickerChance` | `0.3` |
| `color` | `'#6B7280'` |
| `maxOpacity` | `0.3` |

**`WordMorph`**
| Prop | Default |
| --- | --- |
| `words` | requerido (`string[]`) |
| `morphDuration` | `1000` (ms) |
| `morphDelay` | `5000` (ms) |

**`Highlighter`**
| Prop | Default |
| --- | --- |
| `action` | `'highlight'` (`highlight`, `underline`, `box`, `circle`, `strike-through`, `crossed-off`, `bracket`) |
| `color` | `'#d1daff'` |
| `strokeWidth` | `1.5` |
| `animationDuration` | `600` |
| `iterations` | `2` |
| `padding` | `2` |
| `multiline` | `true` |
| `brackets` | `['left', 'right']` |
| `animateOnlyOnce` | `false` |
| `animate` | `true` |

**`Timeline`**
| Prop | Default |
| --- | --- |
| `entries` | requerido: `{ title: string; content: string }[]` (content admite HTML) |
| `orientation` | `'left'` (`left`, `right`, `switch`) |
| `entriesGap` | `'5rem'` |
| `entryGap` | `'4rem'` |
| `titleGap` | `'2rem'` |
| `pathWidth` | `'2px'` |
| `titleMaxWidth` | `'25rem'` |
| `pathColor` | `'#e2e8f0'` |
| `gradientColors` | `['#3b82f6', '#7f00ff']` (exactamente 2) |

**`ShineBorder`**
| Prop | Default |
| --- | --- |
| `gradientColorStart` | `'#ffaa40'` |
| `gradientColorMiddle` | `'#fe8fb5'` |
| `gradientColorEnd` | `'#9c40ff'` |
| `borderRadius` | `'0.5rem'` |
| `borderColor` | `'hsla(0, 0%, 0%, 0)'` |
| `animationDuration` | `'14s'` |

Todos los componentes aceptan `class` para estilos adicionales. Cada instancia genera
identificadores únicos (SVG/canvas), por lo que puedes usar varias en la misma página.

## Notas de la conversión

- Los ids globales del original (`linearGradient-0`, `threshold`, `gradient`, …) se
  scoped por instancia para evitar colisiones.
- `Timeline` usa `content` como HTML (`set:html`) en lugar de los `ng-template` de Angular.
- La animación de todos los componentes se pausa fuera de viewport (`IntersectionObserver`),
  igual que en los originales.
- El shader CPPN de `DarkVeil` se copia íntegro (es GLSL, no depende de Angular).

## Publicación

Cada paquete se publica a npm de forma independiente:

```bash
npm publish --workspace packages/ngx-astro-background-beams
# … uno por paquete
```

## Licencia

MIT. Efectos originales © Omnedia (MIT), ver repos en https://github.com/omnedia.
