# Nepali Products — Buddhabhumi

Tienda online bilingüe (inglés / nepalí) que desarrollé para **Nepali Products Barcelona**, con catálogo, carrito, checkout y envío en tiempo real. Consume el [Nepali Shop Backend](https://github.com/Gatsbys-stocks/nepali-shop-backend), que también desarrollé, para todo lo que requiere servidor.

## El problema que resolví

El negocio necesitaba una tienda online completa — catálogo, carrito, cálculo de envío real, cobro con tarjeta y que el propio dueño pudiera mantener el catálogo al día — sin depender de una plataforma de e-commerce de terceros (Shopify, WooCommerce...) ni de un framework pesado, y con la identidad visual y cultural del negocio (bandera de oración nepalí, tipografía Devanagari, textos en nepalí e inglés). Construí esta página como esa solución: un catálogo interactivo en HTML/CSS/JS que se comunica con un backend propio para la parte que sí requiere servidor.

## Qué hice

- **Catálogo con más de 100 productos**, organizados por categorías (harinas, fideos, legumbres, encurtidos, especias, té y bebidas, varios), con nombre en nepalí, traducción al inglés, formato/tamaño y precio.
- **Catálogo gestionable por el propio cliente**: la página pide el catálogo al backend (`GET /api/products`) en vez de tenerlo escrito en el código, así que cuando el dueño del negocio añade o cambia un producto desde su panel de administración, aparece en la web sin que yo tenga que tocar nada. Si el backend no responde, la página se apoya en un catálogo de respaldo para no quedarse en blanco.
- **Buscador y filtro por categoría** en tiempo real sobre el catálogo.
- **Carrito de compra** persistente durante la sesión, con contador y total visibles en todo momento.
- **Checkout con validación de formulario** (nombre, teléfono, email, dirección) y **cálculo de tarifas de envío reales** llamando al backend, que a su vez consulta a las transportistas.
- **Pago con tarjeta**: al confirmar el pedido, redirijo a Stripe Checkout y, al volver, muestro un aviso de éxito o cancelación según el resultado.
- **Interfaz bilingüe** (English / नेपाली) con un selector de idioma que traduce toda la interfaz al vuelo, sin recargar la página.
- **Selección de imágenes por categoría** con override para productos concretos (harina de trigo, garbanzos, comino...), usando fotos de Wikimedia Commons con su atribución correspondiente, y con prioridad para la foto que el cliente suba desde su panel de administración.

## Decisiones técnicas de las que estoy contento

- **Cero dependencias de build**: HTML, CSS y JavaScript en archivos separados (`index.html`, `css/style.css`, `js/app.js`) pero sin bundler ni framework, así que se puede alojar en cualquier hosting estático (GitHub Pages, Netlify...) sin pipeline de despliegue.
- **Diseño por tokens CSS** (`:root` con variables de color, radios, etc.), lo que me permite mantener consistencia visual y cambiar la paleta desde un único punto.
- **Separación de datos y presentación**: el catálogo es un array de objetos de producto (ahora servido por el backend) y la UI se renderiza a partir de él — añadir o quitar productos no toca la lógica de renderizado.
- **Internacionalización propia**: un diccionario `I18N` con claves por idioma y una función `t()` que resuelve el texto, sin librerías externas.
- **Manejo cuidadoso del estado de envío**: al recalcular tarifas limpio explícitamente cualquier tarifa seleccionada previamente, para no dejar en pantalla un resumen de precio desactualizado si la nueva petición falla.
- **Recuperación del resultado del pago vía query params**: al volver de Stripe, leo `?payment=success|cancelled&order=...` de la URL para mostrar el aviso correspondiente, y limpio la URL después para que un refresco no repita el banner.
- **Catálogo con red de seguridad**: si la petición al backend falla, la tienda sigue mostrando el último catálogo conocido en vez de quedarse vacía.

## Cómo encaja con el backend

```
index.html (esta página)
   → cargar catálogo → GET  {API_BASE_URL}/api/products → Nepali Shop Backend
   → calcular envío   → POST {API_BASE_URL}/api/rates    → Nepali Shop Backend → Shippo
   → confirmar pedido → POST {API_BASE_URL}/api/orders   → Nepali Shop Backend → guarda pedido + emails
   → pagar            → POST {API_BASE_URL}/api/orders/:id/checkout → Stripe Checkout
```

Toda la lógica de negocio sensible (claves de API, cálculo de tarifas reales, verificación de pagos, gestión del catálogo) vive en el backend; esta página solo se ocupa de la experiencia de compra.

## Estructura del proyecto

```
index.html     # marcado
css/
  style.css     # estilos (tokens de diseño en :root)
js/
  app.js         # catálogo, carrito, checkout, i18n
```

## Stack

HTML5 · CSS3 (variables/custom properties, sin framework) · JavaScript vanilla (ES6+) · Google Fonts (Newsreader, Noto Serif Devanagari, Work Sans) · Stripe Checkout · Wikimedia Commons (imágenes con licencia libre)
