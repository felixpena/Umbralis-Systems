# README técnico — Landing page Umbralis Systems

> Uso interno. No publicar.

---

## Índice

1. [Descripción general](#descripción-general)
2. [Estructura del proyecto](#estructura-del-proyecto)
3. [Stack tecnológico](#stack-tecnológico)
4. [Arquitectura del archivo](#arquitectura-del-archivo)
5. [Sistema de diseño](#sistema-de-diseño)
6. [Secciones y componentes](#secciones-y-componentes)
7. [JavaScript — comportamientos](#javascript--comportamientos)
8. [SEO y metadatos](#seo-y-metadatos)
9. [Formulario de contacto](#formulario-de-contacto)
10. [Deploy y hosting](#deploy-y-hosting)
11. [Tareas pendientes](#tareas-pendientes)
12. [Variables a personalizar](#variables-a-personalizar)

---

## Descripción general

Landing page B2B de conversión para **Umbralis Systems**. Objetivo: captar leads calificados (gerentes de operaciones, responsables de seguridad, empresas solares utility-scale) y agendar pilotos.

Archivo único autocontenido: `index.html`. No requiere build, bundler ni dependencias locales. Todas las dependencias se cargan desde CDN.

---

## Estructura del proyecto

```
Umbralis-Systems/
└── index.html          # Landing page completa (único archivo de producción)
└── README.md           # Este documento
```

Si se añaden assets en el futuro, la estructura recomendada es:

```
Umbralis-Systems/
├── index.html
├── README.md
├── assets/
│   ├── img/
│   │   ├── og-image.jpg        # 1200×630px — requerido para Open Graph
│   │   └── favicon.ico
│   └── fonts/                  # Solo si se decide servir fuentes localmente
```

---

## Stack tecnológico

| Capa | Tecnología | Versión / Canal | Notas |
|------|-----------|-----------------|-------|
| Markup | HTML5 | — | `lang="es"`, UTF-8 |
| Estilos base | Tailwind CSS | CDN (latest) | Config custom embebida en `<script>` |
| Estilos custom | CSS puro | — | Bloque `<style>` en `<head>` |
| Tipografía | Google Fonts | Inter + JetBrains Mono | `preconnect` incluido para performance |
| JavaScript | Vanilla ES6+ | — | Sin frameworks, sin bundler |
| Iconos | SVG inline | — | No depende de librerías externas |

**Sin dependencias de build.** El archivo se sirve tal cual, incluso desde `file://` en desarrollo.

---

## Arquitectura del archivo

El `index.html` está organizado en bloques comentados secuencialmente:

```
<head>
  ├── SEO / metadatos
  ├── Open Graph + Twitter Card
  ├── Google Fonts (preconnect + stylesheet)
  ├── Tailwind CDN + tailwind.config (colores, fuentes, animaciones)
  └── <style> — CSS custom (ver detalle abajo)

<body>
  ├── <!-- NAVIGATION -->
  ├── <!-- HERO -->
  ├── <!-- LOGOS / COMPATIBILIDAD -->
  ├── <!-- PROBLEMA -->
  ├── <!-- SOLUCIÓN -->
  ├── <!-- BENEFICIOS -->
  ├── <!-- CÓMO FUNCIONA -->
  ├── <!-- PILOTO -->
  ├── <!-- FORMULARIO -->
  ├── <!-- FOOTER -->
  └── <script> — JS inline (comportamientos, formulario, scroll)
```

---

## Sistema de diseño

### Paleta de color

Todas las variables están definidas en `tailwind.config` y disponibles como clases `bg-*`, `text-*`, `border-*`:

| Token | Hex | Uso |
|-------|-----|-----|
| `um-black` | `#04060f` | Fondo base, footer |
| `um-navy` | `#080d1c` | Secciones alternadas |
| `um-dark` | `#0c1120` | Fondos de elementos UI internos |
| `um-panel` | `#0f1628` | Cards y paneles principales |
| `um-card` | `#141d32` | Cards secundarias, tooltips |
| `um-border` | `#1e2d4a` | Bordes sutiles |
| `um-cyan` | `#00c8f0` | Acento primario, CTAs, highlights |
| `um-cyan-dim` | `#006f87` | Variante oscurecida del acento |
| `um-electric` | `#38bdf8` | Acento secundario |
| `um-text` | `#8496b0` | Texto body / descriptivo |
| `um-light` | `#c4cfe0` | Texto secundario más claro |
| `um-white` | `#e8eef8` | Títulos y texto destacado |

### Tipografía

- **Inter** — todos los textos UI, títulos, body, botones
- **JetBrains Mono** — elementos técnicos: timestamps en el log, badges `LIVE`, labels de código, latencias

Escala usada:
- Títulos principales: `text-5xl` → `text-7xl`, `font-black` (900)
- Subtítulos de sección: `text-4xl` → `text-5xl`, `font-black`
- Body: `text-sm` → `text-lg`
- Labels / badges: `text-xs`, `uppercase`, `tracking-widest`

### Clases CSS custom relevantes

| Clase | Efecto |
|-------|--------|
| `.grid-bg` | Grid de líneas sutiles con `background-image` |
| `.gradient-text` | Gradiente blanco → cyan → eléctrico en texto |
| `.gradient-text-subtle` | Gradiente slate → cyan |
| `.glow-cyan` | `box-shadow` con halo cyan exterior |
| `.border-cyan-glow` | Borde + `inset box-shadow` con cyan |
| `.btn-primary` | Botón CTA principal con gradiente cyan, hover lift |
| `.btn-secondary` | Botón outline cyan con hover bg subtle |
| `.reveal` | Estado inicial `opacity:0 translateY(32px)` — animado por JS |
| `.reveal.visible` | Estado final activado por IntersectionObserver |
| `.card-hover` | Hover con `translateY(-6px)` + glow + cambio de borde |
| `.live-badge` | Badge pill con borde cyan y punto blink |
| `.form-input` | Input base con foco cyan |
| `.scan-line` | Línea de escaneo animada sobre los feeds de cámara |
| `.blink` | Parpadeo 1.5s step-end (usado en puntos de estado live) |
| `.radial-glow` | Gradiente radial cyan difuso como fondo de sección |

### Animaciones CSS

| Nombre | Duración | Uso |
|--------|----------|-----|
| `fadeUp` | 0.7s | Reveal base de elementos (por IntersectionObserver) |
| `scanAnim` | 3s linear infinite | Línea de escaneo en mockup de cámaras |
| `blinkAnim` | 1.5s step-end infinite | Puntos de estado "LIVE" y badges activos |

---

## Secciones y componentes

### 1. Navigation (`#navbar`)

- Fija (`position: fixed`), `z-50`
- Al hacer scroll > 20px agrega clase `.scrolled` → `backdrop-filter: blur(20px)` + borde inferior cyan
- Logo: SVG hexagonal con círculo central cyan
- Desktop: links de ancla + 2 CTAs
- Mobile: botón hamburguesa + menú desplegable `#mobile-menu` con clase `.open`

### 2. Hero

- Layout: 2 columnas en desktop (`lg:grid-cols-2`)
- Columna izquierda: badge live, H1, subtítulo, párrafo, 2 CTAs, 3 stats de confianza
- Columna derecha: **Dashboard mockup** — construido puramente en HTML/CSS/SVG
  - Barra de título estilo macOS (dots rojo/amarillo/verde)
  - Grid 3×1 de feeds de cámara con SVGs de paneles solares
  - Cámara CAM-04 con bounding box de detección humana animado + scan line
  - Log de eventos en tiempo real (estático, simulado)
  - Barra de 4 estadísticas (cámaras, alertas, precisión, latencia)
  - Tarjeta flotante de alerta (posición absoluta `-bottom-6 -right-6`)
- Orbs decorativos: `div` con `blur-[120px]` para glow ambiental

### 3. Franja de compatibilidad

- Lista de marcas compatibles: Hikvision, Dahua, Axis, Bosch, Hanwha, ONVIF
- Texto puro, `opacity-50`, sin logos (evita problemas de marca)

### 4. Problema (`#problema`)

- 3 cards con métricas clave:
  - `+200` falsas alarmas/día/sitio — bar chart mini SVG-free (divs con % height)
  - `3×` carga por operador — progress bars CSS con ratio 96%/4%
  - `$0` recuperados — bloque con costo estimado por incidente
- Lista de 6 puntos del problema con iconos X circulares

### 5. Solución (`#solucion`)

- Layout 2 columnas
- Izquierda: 4 bullets con iconos SVG inline
- Derecha: **diagrama de arquitectura** vertical con:
  - Nodo "Cámaras existentes" → flecha → Nodo "Edge AI" (resaltado) → flecha → Grid de clasificación (Humano / Animal / Ruido) → flecha → Nodo "Centro de monitoreo"
  - Flechas: SVG de triángulo + línea vertical con `bg-um-cyan/40`

### 6. Beneficios (`#beneficios`)

- Grid 3×2 de tarjetas con:
  - Ícono SVG en contenedor `12×12` con bg/border cyan
  - Métrica grande (`text-3xl font-black text-um-cyan`)
  - Título + descripción
  - Efecto `.card-hover` en todas

### 7. Cómo funciona (`#como-funciona`)

- 4 pasos en fila horizontal (desktop) / columna (mobile)
- Cada paso: número badge en posición absoluta, ícono, título, descripción, badge de tiempo/precisión
- Conectores entre pasos: `div` con gradiente + SVG de flecha (`.hidden md:flex`)

### 8. Piloto (`#piloto`)

- Card contenedora con `border-um-cyan/20` y glow sutil
- Izquierda: propuesta de valor + checklist de 5 garantías + CTA
- Derecha: cronograma visual de 4 semanas con línea de tiempo vertical

### 9. Formulario (`#contacto`)

- Campos: nombre, empresa, email, cargo, mensaje
- Validación inline en cliente: borde rojo si campo requerido vacío
- Submit simulado con `setTimeout(1200ms)` → oculta form, muestra `#form-success`
- Trust signals: 3 iconos con texto (sin compromiso, 24h, equipo técnico)

**Ver sección [Formulario de contacto](#formulario-de-contacto) para integración real.**

### 10. Footer

- Grid 3 columnas: brand + tagline / links internos / contacto
- Email: `contacto@umbralis.com` (placeholder — actualizar)
- LinkedIn: apunta a `linkedin.com/company/umbralis-systems` (actualizar slug)
- Indicador "live" con blink en la barra inferior

---

## JavaScript — comportamientos

Todo el JS está en un único bloque `<script>` al final del `<body>`. Sin librerías externas.

### Scroll nav
```js
window.addEventListener('scroll', () => {
  if (window.scrollY > 20) navbar.classList.add('scrolled');
  else navbar.classList.remove('scrolled');
}, { passive: true });
```

### Reveal on scroll (IntersectionObserver)
```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
      observer.unobserve(entry.target);  // fire once
    }
  });
}, { threshold: 0.12, rootMargin: '0px 0px -60px 0px' });

document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
```

Los `transition-delay` inline en cada `.reveal` escalonan la aparición de elementos dentro de una misma sección.

### Smooth scroll para anclas
```js
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
  anchor.addEventListener('click', function(e) {
    const target = document.querySelector(this.getAttribute('href'));
    if (target) {
      e.preventDefault();
      const offset = 90; // altura de nav fija
      window.scrollTo({ top: target.getBoundingClientRect().top + window.scrollY - offset, behavior: 'smooth' });
    }
  });
});
```

### Formulario
```js
form.addEventListener('submit', (e) => {
  e.preventDefault();
  // validación → animación de loading → mostrar success
});
```

---

## SEO y metadatos

### Tags implementados

```html
<!-- Primary -->
<title>Umbralis Systems | Seguridad Perimetral Inteligente...</title>
<meta name="description" content="...">
<meta name="keywords" content="seguridad perimetral Chile, parques solares seguridad, ...">
<meta name="robots" content="index, follow">
<meta name="geo.region" content="CL">
<link rel="canonical" href="https://umbralis.com">

<!-- Open Graph (Facebook, LinkedIn) -->
<meta property="og:type" content="website">
<meta property="og:url" content="https://umbralis.com">
<meta property="og:title" content="...">
<meta property="og:description" content="...">
<meta property="og:image" content="https://umbralis.com/og-image.jpg">
<meta property="og:locale" content="es_CL">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">
```

### Pendiente antes de producción

- Reemplazar `https://umbralis.com` por el dominio real en `canonical` y todas las OG tags
- Crear y subir `og-image.jpg` (1200×630px) — actualmente la URL es placeholder
- Añadir `<link rel="icon" href="/favicon.ico">` con favicon real
- Considerar añadir Schema.org `Organization` structured data para rich snippets

---

## Formulario de contacto

El formulario actualmente es **solo frontend**. Al hacer submit muestra un mensaje de éxito simulado. Para conectarlo a un backend real, reemplazar el bloque `setTimeout` en el submit handler por una de estas opciones:

### Opción A — Formspree (ACTIVO)

Endpoint: `https://formspree.io/f/mkoewoor`

```js
fetch('https://formspree.io/f/mkoewoor', {
  method: 'POST',
  body: new FormData(form),
  headers: { 'Accept': 'application/json' }
})
.then(res => { /* muestra success o error */ });
```

### Opción B — Endpoint propio (REST API)

```js
const res = await fetch('/api/contacto', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    nombre: document.getElementById('nombre').value,
    empresa: document.getElementById('empresa').value,
    email: document.getElementById('email').value,
    cargo: document.getElementById('cargo').value,
    mensaje: document.getElementById('mensaje').value,
  })
});
```

### Opción C — HubSpot / Mailchimp embed

Reemplazar el `<form>` completo por el embed script del CRM correspondiente y ajustar los estilos con las clases CSS custom del archivo.

---

## Deploy y hosting

El archivo es **estático puro**. Compatible con cualquier servicio de hosting estático:

| Servicio | Método |
|----------|--------|
| **Netlify** | Drag & drop la carpeta, o conectar repositorio Git |
| **Vercel** | `vercel deploy` desde la carpeta |
| **GitHub Pages** | Push a rama `gh-pages` o configurar Pages desde `main` |
| **S3 + CloudFront** | Upload del archivo, habilitar Static Website Hosting |
| **Servidor propio** | Nginx/Apache sirviendo el directorio directamente |

### Nginx — configuración mínima

```nginx
server {
    listen 80;
    server_name umbralis.com www.umbralis.com;
    root /var/www/umbralis;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    gzip on;
    gzip_types text/html text/css application/javascript;
}
```

### Consideraciones de performance en producción

- **Tailwind CDN** no está purgeado. Para producción, migrar a Tailwind CLI con `purge` habilitado para reducir el CSS de ~350KB a ~5–15KB.
- **Google Fonts** se carga desde CDN externo. Si la latencia es crítica, descargar y servir las fuentes localmente.
- **Sin imágenes reales**: el dashboard usa SVG inline. Si se añaden imágenes (fotos de parques, capturas de pantalla reales del producto), usar `loading="lazy"` y formatos WebP.

---

## Tareas pendientes

### Antes de ir a producción

- [ ] Definir y registrar dominio (`umbralis.com` o `.cl`)
- [ ] Actualizar URL canónica y Open Graph con dominio real
- [ ] Crear imagen OG (`og-image.jpg`, 1200×630px)
- [ ] Añadir favicon
- [ ] Conectar formulario a backend real o Formspree
- [ ] Reemplazar email `contacto@umbralis.com` con casilla real
- [ ] Actualizar slug de LinkedIn cuando la página de empresa esté creada
- [ ] Configurar Google Analytics 4 o Plausible para tracking de conversiones
- [ ] Configurar evento de conversión en el submit del formulario

### Mejoras opcionales

- [ ] Migrar Tailwind a CLI para purgar CSS no utilizado
- [ ] Añadir imágenes reales de parques solares (con lazy loading)
- [ ] Reemplazar dashboard mockup por captura real del producto cuando esté disponible
- [ ] Añadir Schema.org `Organization` para SEO estructurado
- [ ] Internacionalizar a inglés para expansión regional
- [ ] Implementar tests de accesibilidad (WCAG AA): contrastes, aria-labels en iconos SVG
- [ ] Agregar `aria-label` a todos los botones icon-only del nav mobile

---

## Variables a personalizar

Búsqueda rápida por texto para cambiar los datos clave:

| Dato | Buscar en el archivo | Reemplazar por |
|------|---------------------|----------------|
| Email | `contacto@umbralis.com` | Casilla real |
| LinkedIn | `linkedin.com/company/umbralis-systems` | Slug correcto |
| Dominio canonical | `https://umbralis.com` | Dominio real |
| OG image URL | `https://umbralis.com/og-image.jpg` | URL real de la imagen |
| Ubicación | `Santiago, Chile` | Ciudad específica si corresponde |
| Formspree ID | `mkoewoor` | Configurado — `https://formspree.io/f/mkoewoor` |

---

*Documento interno Umbralis Systems — no distribuir.*
