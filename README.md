# Nepali Products — Buddhabhumi

Tienda online bilingüe (inglés / nepalí) para **Nepali Products Barcelona**, construida como una única página estática con carrito, checkout y envío en tiempo real. Es el frontend que consume el [Nepali Shop Backend](https://github.com/Gatsbys-stocks/nepali-shop-backend).

## El problema que resuelve

El negocio necesitaba una tienda online completa — catálogo, carrito, cálculo de envío real y cobro con tarjeta — sin depender de una plataforma de e-commerce de terceros (Shopify, WooCommerce...) ni de un framework pesado, y con la identidad visual y cultural del negocio (bandera de oración nepalí, tipografía Devanagari, textos en nepalí e inglés). Esta página es esa solución: un catálogo interactivo autocontenido en un solo archivo HTML, que se comunica con un backend propio para la parte que sí requiere servidor (tarifas de envío, pedidos y pago).

## Qué hace

- **Catálogo con más de 80 productos**, organizados por categorías (harinas, fideos, legumbres, encurtidos, especias, té y bebidas, varios), con nombre en nepalí, traducción al inglés, formato/tamaño y precio.
- **Buscador y filtro por categoría** en tiempo real sobre el catálogo.
- **Carrito de compra** persistente durante la sesión, con contador y total visibles en todo momento.
- **Checkout con validación de formulario** (nombre, teléfono, email, dirección) y **cálculo de tarifas de envío reales** llamando al backend, que a su vez consulta a las transportistas.
- **Pago con tarjeta**: al confirmar el pedido, redirige a Stripe Checkout y, al volver, muestra un aviso de éxito o cancelación según el resultado.
- **Interfaz bilingüe** (English / नेपाली) con un selector de idioma que traduce toda la interfaz al vuelo, sin recargar la página.
- **Selección de imágenes por categoría** con override para productos concretos (harina de trigo, garbanzos, comino...), usando fotos de Wikimedia Commons con su atribución correspondiente.

## Decisiones técnicas destacables

- **Una sola página, cero dependencias de build**: todo el HTML, CSS y JavaScript vive en `index.html` — no hay bundler ni framework, lo que hace que se pueda alojar en cualquier hosting estático (GitHub Pages, Netlify...) sin pipeline de despliegue.
- **Diseño por tokens CSS** (`:root` con variables de color, radios, etc.), lo que permite mantener consistencia visual y cambiar la paleta desde un único punto.
- **Separación de datos y presentación**: el catálogo es un array de objetos de producto y la UI se renderiza a partir de él — añadir o quitar productos no toca la lógica de renderizado.
- **Internacionalización propia**: un diccionario `I18N` con claves por idioma y una función `t()` que resuelve el texto, sin librerías externas.
- **Manejo cuidadoso del estado de envío**: al recalcular tarifas se limpia explícitamente cualquier tarifa seleccionada previamente, para no dejar en pantalla un resumen de precio desactualizado si la nueva petición falla.
- **Recuperación del resultado del pago vía query params**: al volver de Stripe, lee `?payment=success|cancelled&order=...` de la URL para mostrar el aviso correspondiente, y limpia la URL después para que un refresco no repita el banner.

## Cómo encaja con el backend

```
index.html (esta página)
   → calcular envío   → POST {API_BASE_URL}/api/rates    → Nepali Shop Backend → Shippo
   → confirmar pedido → POST {API_BASE_URL}/api/orders   → Nepali Shop Backend → guarda pedido + emails
   → pagar            → POST {API_BASE_URL}/api/orders/:id/checkout → Stripe Checkout
```

Toda la lógica de negocio sensible (claves de API, cálculo de tarifas reales, verificación de pagos) vive en el backend; esta página solo se ocupa del catálogo y la experiencia de compra.

## Stack

HTML5 · CSS3 (variables/custom properties, sin framework) · JavaScript vanilla (ES6+) · Google Fonts (Newsreader, Noto Serif Devanagari, Work Sans) · Stripe Checkout · Wikimedia Commons (imágenes con licencia libre)
