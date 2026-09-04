# Auditoria UX y Accesibilidad - Pagina Tributo a Moises Caicedo

**Proyecto:** `C:\Desarrollo de software\prueba1`  
**Archivos revisados:** `index.html` (380 lineas), `styles.css` (970 lineas)  
**Fecha:** 2026-09-04  

---

## Aspectos Correctos

### Estructura semantica HTML
- `lang="es"` correctamente declarado en `<html>` (`index.html:2`).
- `<meta name="viewport">` con `width=device-width, initial-scale=1.0` (`index.html:5`).
- `<meta name="description">` descriptiva y relevante (`index.html:6`).
- `<title>` descriptiva y unica (`index.html:7`).
- Uso correcto de `<header>`, `<main>`, `<footer>`, `<nav>`, `<section>`, `<article>`, `<aside>`.
- `<nav>` con `aria-label="Navegacion del sitio"` (`index.html:21`).
- `<blockquote>` con `<cite>` para la cita destacada (`index.html:351-354`).
- `<article>` para tarjetas de logros y biografia (`index.html:90, 227, 237, 245, 254, 263, 272`).

### Jerarquia de encabezados
- Jerarquia correcta sin saltos: `<h1>` (hero, `index.html:40`), `<h2>` (secciones: biografia, trayectoria, logros, estadisticas), `<h3>` (sub-contenido dentro de cada seccion).

### Textos alternativos e imagen remota
- Imagen principal con `alt` descriptivo y completo (`index.html:62`).
- Sistema de fallback elegante con `aria-hidden="true"` en el placeholder visual (`index.html:53`).
- `loading="lazy"` aplicado a la imagen externa (`index.html:64`).
- Manejo de error con `onerror` para degradar gracefully (`index.html:65`).

### Navegacion y estructura
- Anclajes internos (`#biografia`, `#trayectoria`, `#logros`, `#estadisticas`) correctamente vinculados con sus secciones.
- `scroll-behavior: smooth` en `<html>` (`styles.css:59`).
- Header con `position: sticky` para navegacion persistente (`styles.css:102`).

### Responsive design
- Media queries en 3 breakpoints: 992px, 768px, 480px (`styles.css:883-970`).
- Grid de hero colapsa a 1 columna en movil (`styles.css:884-888`).
- Botones apilados verticalmente en pantallas <=480px (`styles.css:950-957`).
- Bio-grid y metrics-comparison colapsan a 1 columna en <=992px (`styles.css:899-901`).
- Nav-links con `flex-wrap` para adaptarse en movil (`styles.css:922-925`).
- Uso de `clamp()` para tamanios de fuente fluidos en titulos (`styles.css:239, 439`).

### Legibilidad
- `line-height: 1.6` en body para legibilidad general (`styles.css:66`).
- Parrafos de biografia con `line-height: 1.7` y `font-size: 1.02rem` (`styles.css:484-485`).
- Uso de tipografias sans-serif modernas (Outfit y Plus Jakarta Sans) con fallbacks del sistema (`styles.css:37-38`).
- Jerarquia visual clara: tamanios de fuente, pesos y colores diferenciados por nivel.

### Paleta de colores y consistencia visual
- Paleta coherente inspirada en Ecuador y Chelsea FC.
- Variables CSS bien organizadas para mantenimiento (`styles.css:9-48`).
- Transiciones suaves con `cubic-bezier` personalizado (`styles.css:48`).

### Contenido y claridad
- Informacion biografica clara, bien estructurada y factual.
- Estadisticas presentadas con formato visual intuitivo (barras de progreso, tarjetas numericas).
- Timeline de trayectoria con cronologia visual clara.
- Tarjeta rapida (`hero-quick-card`) con posicion contextual junto a la imagen.

---

## Hallazgos por Severidad

### BLOQUEANTE

#### B-01: Sin estilos de foco visibles en enlaces y botones
- **Archivo:** `styles.css` (lineas 165-190, 264-299)
- **Evidencia:** No existe ningun estilo `:focus` ni `:focus-visible` en todo el CSS para enlaces de navegacion (`.nav-links a`), botones (`.btn`, `.btn-primary`, `.btn-secondary`), ni el logo. Solo hay `:hover`.
- **Impacto:** Usuarios que navegan por teclado (Tab/Shift+Tab) no ven ningun indicador visual de donde esta su foco. Es imposible saber que elemento esta seleccionado. Esto viola WCAG 2.4.7 (Focus Visible) y 2.4.13 (Focus Appearance).
- **Recomendacion:** Agregar reglas `:focus-visible` para todos los elementos interactivos. Ejemplo:
  ```css
  .nav-links a:focus-visible,
  .btn:focus-visible {
    outline: 2px solid var(--ecuador-yellow);
    outline-offset: 2px;
  }
  ```

#### B-02: Ausencia de enlace "Saltar al contenido" (Skip Navigation)
- **Archivo:** `index.html` (antes de linea 13)
- **Evidencia:** No existe un `<a href="#contenido-principal">Saltar al contenido</a>` u otro mecanismo equivalente al inicio del `<body>`.
- **Impacto:** Usuarios de lectores de pantalla y navegadores de teclado deben presionar Tab repetidamente para atravesar los 4 enlaces del nav antes de llegar al contenido principal. Esto viola WCAG 2.4.1 (Bypass Blocks).
- **Recomendacion:** Agregar un enlace visualmente oculto (que aparece al recibir foco) al inicio del body que apunte al `<main>` o al `<section id="biografia">`.

#### B-03: Elementos decorativos no ocultos de tecnologias asistenciales
- **Archivo:** `index.html` (lineas 16-18, 37, 38, 69, 228, 237, 246, 255, 264, 273, 324-345, 365-368)
- **Evidencia:** Multiples elementos son puramente decorativos pero carecen de `aria-hidden="true"`:
  - Rayas de bandera en header (`<span class="flag-stripe ...">`, lineas 16-18) y footer (lineas 366-368).
  - Lineas decorativas `<div class="decorative-line">` en las 4 secciones (lineas 86, 153, 223, 291).
  - Iconos emoji en tarjetas de logros: trofeo, medalla, globo, estrella, condecoracion, diana (lineas 228, 237, 246, 255, 264, 273).
  - Icono de pelota en `hero-quick-card` (linea 69).
- **Impacto:** Los lectores de pantalla anuncian cada elemento decorativo, ensuciando la experiencia con informacion irrelevante (ej. "raya amarilla, raya azul, raya roja" repetido dos veces). WCAG 1.3.1 (Info and Relationships).
- **Recomendacion:** Agregar `aria-hidden="true"` a todos los elementos decorativos. Para los emojis de logros, considerar reemplazarlos con texto oculto con `aria-label` en el `<article>` o usar `<span aria-hidden="true">` en cada emoji.

#### B-04: Barras de progreso sin atributos ARIA
- **Archivo:** `index.html` (lineas 319-346)
- **Evidencia:** Las barras de progreso son `<div class="progress-track"><div class="progress-fill" style="width: 95%;"></div></div>`. No utilizan `<progress>`, `<meter>` ni roles ARIA (`role="progressbar"` + `aria-valuenow`, `aria-valuemin`, `aria-valuemax`, `aria-label`).
- **Impacto:** Los lectores de pantalla no pueden interpretar el valor ni el progreso representado. No hay forma de comunicar "95% de recuperacion e intercepciones" a un usuario no vidente. WCAG 1.3.1 y 4.1.2 (Name, Role, Value).
- **Recomendacion:** Usar `<progress>` nativo o agregar:
  ```html
  <div class="progress-track" role="progressbar" 
       aria-valuenow="95" aria-valuemin="0" aria-valuemax="100"
       aria-label="Recuperacion e Intercepciones: 95%">
  ```

---

### IMPORTANTE

#### I-01: Hover effects sin equivalencia para usuarios que no usan raton
- **Archivo:** `styles.css` (lineas 565-568, 632-636, 707-711, 470-473, 375-377, 282-286, 294-299)
- **Evidencia:** `:hover` aplicado a:
  - `.timeline-content` con `transform: translateX(6px)` (linea 566).
  - `.logro-card` con `transform: translateY(-4px)` (linea 633).
  - `.stat-box` con `transform: translateY(-5px)` (linea 708).
  - `.bio-card:hover` y `.bio-quick-facts:hover` (linea 470).
  - `.remote-profile-img:hover` con `scale(1.02)` (linea 376).
  - `.btn-primary:hover` y `.btn-secondary:hover` con `translateY(-2px)` (lineas 283, 296).
  No hay ningun estilo `:focus-visible` equivalente para ninguno de estos.
- **Impacto:** Los usuarios de teclado o dispositivos de puntero que no tienen raton no experimentan estas interacciones visuales de retroalimentacion, perdiendo contexto sobre la interactividad. WCAG 1.4.13 (Content on Hover or Focus).
- **Recomendacion:** Duplicar todos los efectos `:hover` con `:focus-visible` o usar una regla combinada:
  ```css
  .timeline-content:hover,
  .timeline-content:focus-within { transform: translateX(6px); }
  ```

#### I-02: Animaciones que respetan `prefers-reduced-motion` parcialmente
- **Archivo:** `styles.css` (lineas 48, 59, 186, 372, 376, 283, 296, 566, 633, 708)
- **Evidencia:** `scroll-behavior: smooth` esta en `<html>` (`styles.css:59`). Las transiciones de `transform`, `opacity` y `box-shadow` estan ampliamente distribuidas. No existe ninguna media query `@media (prefers-reduced-motion: reduce)` en todo el CSS.
- **Impacto:** Usuarios con vestibular disorders, migrañas o sensibilidad al movimiento no pueden desactivar el scroll suave ni las animaciones de elevacion/traslacion. WCAG 2.3.3 (Animation from Interactions) y 2.2.2 (Pause, Stop, Hide).
- **Recomendacion:** Agregar al final del CSS:
  ```css
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
    html { scroll-behavior: auto; }
  }
  ```

#### I-03: Botones sin tipo explícito
- **Archivo:** `index.html` (lineas 45-46)
- **Evidencia:** Los botones de accion son enlaces `<a>` estilizados como botones (`.btn`), no `<button>`. Aunque semantico para navegacion interna (anchor links), la Clase CSS `.btn` y `.btn-primary` sugiere una accion, no una navegacion. No hay ningun `<button>` en todo el documento.
- **Impacto:** No es un error per se, pero la consistencia de que todo interactivo sea un enlace podria confundir a usuarios avanzados de teclado que esperan Enter para activar y Space para interacciones de boton. WCAG 4.1.2.
- **Recomendacion:** Aceptar el patron de enlace-ancla para estos dos botones ya que navegan internamente. Sin embargo, si en el futuro se agregan botones de accion real (like, share), usar `<button>` con tipos explicitos.

#### I-04: Logo sin nombre accesible claro
- **Archivo:** `index.html` (lineas 15-19)
- **Evidencia:** El logo es un `<a href="#inicio" class="logo">` que contiene 3 `<span class="flag-stripe ...">` decorativos sin aria-hidden y un `<span class="logo-text">` con texto "Moises Caicedo #25". Los spans de bandera se anunciarian como contenido antes del nombre.
- **Impacto:** Un lector de pantalla podria anunciar: "Moises Caicedo 25" despues de procesar las rayas decorativas. El link solo dice "Moises Caicedo #25" sin contexto de que es un link al inicio.
- **Recomendacion:** Agregar `aria-hidden="true"` a los spans de bandera y un `aria-label="Ir al inicio - Moises Caicedo"` al enlace.

#### I-05: Indicador de seccion actual en navegacion ausente
- **Archivo:** `styles.css` (lineas 159-190), `index.html` (lineas 21-27)
- **Evidencia:** Los enlaces de navegacion no tienen mecanismo visual (clase `active`, `aria-current="section"`) que indique en que seccion esta el usuario al hacer scroll.
- **Impacto:** En una pagina de una sola pantalla con scroll largo, el usuario pierde contexto sobre su ubicacion. WCAG 2.4.8 (Location).
- **Recomendacion:** Implementar un script ligero de Intersection Observer que agregue una clase `.active` o `aria-current="true"` al enlace correspondiente cuando la seccion sea visible.

#### I-06: `overflow-x: hidden` en body puede ocultar contenido
- **Archivo:** `styles.css` (linea 67)
- **Evidencia:** `overflow-x: hidden` en `<body>`.
- **Impacto:** Si algun contenido se desborda horizontalmente (ej. en tablets en orientacion landscape o con zoom alto al 200%), sera silenciosamente recortado sin indicacion al usuario. WCAG 1.4.4 (Resize Text).
- **Recomendacion:** Investigar la causa del overflow en vez de ocultarlo, o al menos monitorear que no se recorte contenido con zoom al 200%.

---

### MEJORA

#### M-01: Importacion de Google Fonts como recurso bloqueante
- **Archivo:** `styles.css` (linea 7)
- **Evidencia:** `@import url('https://fonts.googleapis.com/css2?...')` al inicio del CSS. Esto bloquea la renderizacion del contenido hasta que la fuente se descargue.
- **Impacto:** Experience de carga inicial percibida mas lenta, especialmente en conexiones lentas o dispositivos moviles. El texto puede parpadear (FOUT/FOIT).
- **Recomendacion:** Mover la carga de fuentes al `<head>` del HTML como `<link rel="preconnect">` + `<link rel="stylesheet">` con `display=swap`, o usar `font-display: swap` ya incluido en la URL pero servido via CSS import.

#### M-02: Imagen externa sin dimensiones intrinsecas
- **Archivo:** `index.html` (lineas 60-66)
- **Evidencia:** El `<img>` de Wikipedia no tiene atributos `width` y `height`. El contenedor `.image-frame` tiene `height: 460px` fijo, pero la imagen no contribuye al calculo de layout.
- **Impacto:** Layout shift potencial (CLS) cuando la imagen se carga despues del fallback. El fallback tiene altura fija pero la imagen real puede tener proporcion diferente.
- **Recomendacion:** Agregar `width` y `height` estimados al `<img>` para mejorar CLS, o asegurar que `object-fit: cover` (ya presente en `styles.css:369`) mitigue el efecto visual.

#### M-03: Uso de `<strong>` dentro de parrafos sin semantica clara
- **Archivo:** `index.html` (lineas 93, 95, 100, 106)
- **Evidencia:** `<strong>` se usa para resaltar datos factuales (nombres propios, fechas, clubes).
- **Impacto:** Minimo, pero `<strong>` implica importancia semantica. Para enfasis visual, `<b>` o una clase CSS seria mas preciso. Los lectores de pantalla pondran mas enfasis en esas palabras.
- **Recomendacion:** Aceptarable para esta pagina tributo donde esos datos son semanticamente importantes. No requiere cambio urgente.

#### M-04: Navegacion movil sin hamburger menu
- **Archivo:** `styles.css` (lineas 915-926)
- **Evidencia:** En <=768px, la navegacion simplemente se envuelve (`flex-wrap: wrap`) con enlaces centrados. No hay hamburger menu ni toggle.
- **Impacto:** Con 4 enlaces funciona razonablemente, pero si se agregan mas secciones, la nav ocupara demasiado espacio vertical en movil.
- **Recomendacion:** Aceptable con 4 items. Considerar un menu colapsable si la navegacion crece.

#### M-05: Tarjetas de logros usan emoji como icono principal
- **Archivo:** `index.html` (lineas 228, 237, 246, 255, 264, 273)
- **Evidencia:** Emojis como trofeo, medalla, globo terraqueo, estrella, condecoracion y diana son el contenido visual principal de cada `.logro-icon-wrapper`.
- **Impacto:** La representacion visual depende del soporte emoji del SO/dispositivo. En algunos sistemas operativos los emojis pueden verse diferente o no renderizarse. No son semanticos.
- **Recomendacion:** Considerar iconos SVG o font-icons para consistencia visual, o aceptar emojis como solucion simple para esta pagina de demostracion.

#### M-06: No hay `meta` de theme-color para movil
- **Archivo:** `index.html` (head, lineas 3-9)
- **Evidencia:** No hay `<meta name="theme-color" content="#060e1d">` ni `<meta name="apple-mobile-web-app-capable">`.
- **Impacto:** En movil, la barra del navegador no se adapta al color del tema de la pagina. Experiencia visual menos inmersiva.
- **Recomendacion:** Agregar `<meta name="theme-color" content="#060e1d">` al `<head>`.

#### M-07: Emojis en `<span class="quick-card-icon">` y `stat-box` sin aria-hidden
- **Archivo:** `index.html` (linea 69), `index.html` (lineas 295, 299, 305, 310)
- **Evidencia:** El icono de pelota en `.hero-quick-card` y los valores numericos en stat-box no tienen problemas graves, pero los emojis como contenido text dentro de `<span>` podrian anunciarse de forma inconsistente.
- **Impacto:** Bajo. Los emojis como contenido textual generalmente se anuncian correctamente por lectores modernos.
- **Recomendacion:** Monitorear conNVDA/VoiceOver. Considerar `aria-hidden` en emojis puramente decorativos.

#### M-08: Formato de numero con "+" no semantico
- **Archivo:** `index.html` (lineas 295, 305)
- **Evidencia:** `+89%` y `+50` como contenido de `<span class="stat-number">`. El "+" es visual pero no tiene significado semantico alternativo.
- **Impacto:** Un lector de pantalla anunciaría "mas ochenta y nueve por ciento", lo cual es correcto.
- **Recomendacion:** Sin cambios necesarios. El formato es comprensible.

---

## Resumen de Severidad

| Severidad | Cantidad | IDs |
|-----------|----------|-----|
| Bloqueante | 4 | B-01, B-02, B-03, B-04 |
| Importante | 6 | I-01, I-02, I-03, I-04, I-05, I-06 |
| Mejora | 8 | M-01, M-02, M-03, M-04, M-05, M-06, M-07, M-08 |

---

## Prioridad de Remediacion Recomendada

1. **B-01** (Foco visible) - Impacto alto, esfuerzo bajo. Primera prioridad.
2. **B-02** (Skip navigation) - Impacto alto, esfuerzo bajo.
3. **B-04** (Barras de progreso ARIA) - Impacto alto, esfuerzo bajo-medio.
4. **B-03** (aria-hidden decorativos) - Impacto alto, esfuerzo bajo (muchos puntos, pero cambios simples).
5. **I-01** (hover/focus equivalence) - Impacto medio, esfuerzo bajo.
6. **I-02** (prefers-reduced-motion) - Impacto medio, esfuerzo bajo.
7. **I-05** (seccion activa) - Impacto medio, esfuerzo medio (requiere JS).
8. Los demas importan y mejoras pueden atenderse en iteraciones posteriores.
