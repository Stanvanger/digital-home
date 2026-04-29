# Digital Home — Web para Imprenta con Entrega a Domicilio en Madrid

> Proyecto real para una imprenta digital en Madrid.
> El reto más interesante fue el hero: un slider de comparación
> "diseño profesional vs sin diseño" que el usuario arrastra con el ratón
> o desliza con el dedo, construido desde cero sin librerías.

🔗 **Demo en vivo:** https://digitalhome.website

---

## ¿Qué problema resuelve este proyecto?

Digital Home tenía que convencer a negocios de que invertir en diseño
profesional vale la pena. El argumento era difícil de hacer con texto.

**El objetivo:** demostrar visualmente la diferencia entre tener diseño
profesional y no tenerlo, con un catálogo de productos con precios reales,
sistema de carrito y pedido directo por WhatsApp — sin backend.

---

## Problemas técnicos que resolví y por qué importan

### 1. Slider de comparación antes/después desde cero
**Problema:** el hero necesitaba un widget de comparación donde el usuario
arrastrara una línea divisoria para ver la diferencia entre diseño profesional
y sin diseño. Los plugins existentes añadían demasiado peso y no se integraban
bien con el diseño del sitio.

**Solución:** lo construí desde cero con eventos de ratón y táctil.
La línea divisoria (`splitHandle`) es un elemento posicionado absolutamente
cuya posición `left` se actualiza en tiempo real al arrastrar.
El lado izquierdo usa `clip-path` para recortarse al ancho del split.

**Impacto:** el usuario entiende el valor del diseño en 5 segundos
sin leer una sola línea de texto. Es el argumento de venta más fuerte
de toda la web, y funciona en móvil y desktop sin librerías externas.

```javascript
// El mismo handler para ratón y táctil
function onMove(clientX) {
  var rect = hero.getBoundingClientRect();
  var pos  = Math.max(0, Math.min(1, (clientX - rect.left) / rect.width));
  splitLine.style.left   = (pos * 100) + '%';
  hcardColor.style.width = (pos * 100) + '%';
  splitHandle.setAttribute('aria-valuenow', Math.round(pos * 100));
}
hero.addEventListener('mousemove', function(e) { if (dragging) onMove(e.clientX); });
hero.addEventListener('touchmove',  function(e) { onMove(e.touches[0].clientX); });
```

---

### 2. Slider accesible — WCAG 2.1 Slider Pattern
**Problema:** un slider visual es completamente inaccesible para usuarios
de teclado o lectores de pantalla si no se implementa con los atributos
ARIA correctos.

**Solución:** el elemento `splitHandle` tiene `role="slider"`,
`aria-valuemin`, `aria-valuemax` y `aria-valuenow` que se actualiza
dinámicamente. También responde a las teclas de flecha para mover
el split sin ratón.

**Impacto:** el widget cumple el patrón de slider de WCAG 2.1.
Aprendí que los widgets interactivos personalizados necesitan pensar
en el teclado desde el principio, no como un añadido.

```html
<div id="splitHandle"
     role="slider"
     aria-label="Comparar diseño profesional vs sin diseño"
     aria-valuemin="0" aria-valuemax="100" aria-valuenow="50"
     tabindex="0">
```

---

### 3. Catálogo dinámico con 12 categorías y precios reales
**Problema:** el catálogo tiene camisetas, tarjetas, flyers, vinilos,
roll-ups, menús y más — con tablas de precios por cantidad y tamaño.
Escribir ese HTML a mano hubiera sido inmantenible.

**Solución:** todos los productos están en `var SVCS` — un array
de objetos con categorías, productos, precios y variantes.
El HTML se genera con funciones `buildBody()` y `buildSizeBar()`.
Para actualizar un precio se cambia en un solo lugar.

**Impacto:** separé datos de presentación sin usar ningún framework.

---

### 4. DOMContentLoaded — el error silencioso que lo rompía todo
**Problema:** el catálogo no aparecía en pantalla. No había errores
en la consola. `getElementById('sGrid')` devolvía `null` porque
el script se ejecutaba antes de que ese elemento existiera en el DOM.

**Solución:** envolver todo el JavaScript del catálogo en
`DOMContentLoaded`. El script espera a que el HTML esté listo.

**Impacto:** aprendí algo fundamental sobre el orden de ejecución
en el navegador. Ahora es lo primero que compruebo cuando un script
no encuentra un elemento.

---

### 5. SEO para imprenta digital sin local físico
**Problema:** Digital Home no tiene tienda física. El Schema.org
estándar requiere dirección, pero publicar una dirección falsa
perjudica el SEO y engaña al usuario.

**Solución:** usé el tipo `PrintService` con `areaServed: Madrid`
en lugar de `streetAddress`. Google entiende que opera en Madrid
sin necesidad de ubicación física concreta.

**Impacto:** posicionamiento honesto. `PrintService` es más preciso
que `LocalBusiness` para una imprenta — el Schema.org tiene
un tipo para casi cada modelo de negocio si sabes buscarlo.

---

## Stack técnico

| Tecnología | Uso |
|---|---|
| HTML5 semántico | Estructura con landmarks, roles ARIA, live regions |
| CSS3 vanilla | Variables, Grid, Flexbox, clip-path, responsive |
| JavaScript ES6 | Slider drag, catálogo dinámico, carrito, extras |
| Schema.org PrintService | SEO para imprenta sin dirección física |
| ARIA Slider Pattern | Accesibilidad WCAG 2.1 en widget personalizado |
| Google Calendar | Reuniones de presupuesto integradas |
| AOS | Animaciones al hacer scroll |
| GitHub Pages | Despliegue estático |

---

## Estructura del proyecto

```
digital-home/
├── index.html
└── imagenes/
    ├── logo.png
    └── favicon_32x32.png
```

---

## Lo que aprendí construyendo esto

- Que un slider de comparación requiere pensar en tres inputs:
  ratón, táctil y teclado — no solo en el arrastre visual
- Que `DOMContentLoaded` no es opcional cuando el script
  referencia elementos que aparecen después en el HTML
- Que `clip-path` permite efectos de recorte sin Canvas ni librerías
- Que `PrintService` existe en Schema.org — no todo negocio
  es un `LocalBusiness`

---

## Otros proyectos

| Proyecto | Descripción | Demo |
|---|---|---|
| Gasomotores | Taller mecánico con calculadora de mantenimiento | [Ver](https://stanvanger.github.io/gasomotores/) |
| Zaira Masala | Restaurante indio con menú dinámico desde JSON | [Ver](https://stanvanger.github.io/demo-zaira-masala/) |
| PawStudio | Landing peluquería mascotas con reservas online | [Ver](https://stanvanger.github.io/pawstudioo/) |
| VorticeDev | Mi propia agencia frontend con Three.js | [Ver](#) |

---

## Sobre mí

Soy **Carolina Quintero**, diseñadora publicitaria reconvertida en desarrolladora frontend.
Aprendo construyendo proyectos reales para clientes reales.

📧 kinterocarolina0@gmail.com
📱 +34 655 607 610
🔗 github.com/Stanvanger
