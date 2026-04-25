# Guía de uso — Flujo 4 Respuesta automática a reseñas

> 🎥 **Grabación de demostración:** [Ver en Google Drive](https://drive.google.com/drive/u/0/folders/1-Y7e6nToiQ55_jd0bfC1ycE78PKzbrkB?ths=true) — Recording Apr 25, 2026
>
> **Cómo ver el vídeo:** Salta directamente al minuto 0 para ver cómo se lanza el comando (primeros 2 minutos), luego salta al **minuto 7** para ver las propuestas finales ya generadas. El proceso intermedio de generación tarda algo más de lo habitual en este vídeo.

## Qué hace este flujo

El comando `/responder_reseñas` usa **Claude in Chrome** — la extensión oficial de Anthropic para el navegador — para escanear las reseñas sin respuesta de la página abierta y generar borradores listos para publicar directamente en el mismo navegador. Sin integraciones externas, sin mantenimiento.

El proceso completo para el equipo de atención al cliente:

1. Abrir la página de reseñas del hotel en Chrome (Google, TripAdvisor o Booking.com).
2. Escribir `/responder_reseñas` en el panel de Claude in Chrome.
3. Claude lee la página, analiza cada reseña sin respuesta y redacta una contestación con el tono del hotel (~120 palabras, en el idioma del reseñador).
4. Revisar los borradores, copiar y pegar en la plataforma. Sin más pasos.

Reemplaza el proceso manual actual en el que el equipo copia las reseñas a ChatGPT y pega las respuestas una a una.

**Plataformas compatibles:** Google Hotels / Perfil de Google Business · TripAdvisor · Booking.com

---

## Requisitos previos

- **Claude in Chrome** instalado — extensión oficial de Anthropic para Chrome. Descarga desde la [Chrome Web Store](https://chromewebstore.google.com/detail/claude/phmhbkjoedkajoolnmgilelnmfnkbeal).
- Una cuenta Claude activa (el plan gratuito es suficiente para un uso regular).
- El texto del comando guardado (ver `command.md`).
- Credenciales de propietario o administrador en la plataforma para publicar las respuestas *(solo necesarias para el paso final de publicación)*.

---

## Instalación de Claude in Chrome (una sola vez)

1. Ir a [Claude in Chrome en la Chrome Web Store](https://chromewebstore.google.com/detail/claude/phmhbkjoedkajoolnmgilelnmfnkbeal) → **Añadir a Chrome**.
2. Hacer clic en el icono de Claude en la barra de herramientas → iniciar sesión con la cuenta Claude del hotel.
3. Listo — el panel lateral de Claude estará disponible en cualquier pestaña.

---

## Paso a paso

### Paso 1 — Navegar a la página de reseñas

Abrir la plataforma de reseñas en Chrome:

- **Google Hotels / Perfil de Google Business:** busca el hotel en Google → haz clic en el nombre → pestaña "Reseñas".
- **TripAdvisor:** navega al perfil del hotel → sección de reseñas.
- **Booking.com:** navega al listado del hotel → sección "Opiniones de huéspedes".

Si la plataforma usa scroll infinito, **desplázate hacia abajo** para cargar todas las reseñas antes de ejecutar el comando.

### Paso 2 — Abrir Claude in Chrome

Hacer clic en el icono de Claude en la barra de herramientas de Chrome para abrir el panel lateral. Claude tiene acceso automático a la pestaña activa — no hace falta copiar ni pegar nada de la página.

### Paso 3 — Ejecutar el comando

Escribir `/responder_reseñas` en el campo de chat y pulsar **Intro**. Claude comenzará a escanear la página automáticamente. Dependiendo del número de reseñas, puede tardar entre 30 segundos y unos minutos.

### Paso 4 — Revisar el resultado

Claude genera un bloque estructurado por cada reseña sin respuesta:

```
—
Reseñador: [Nombre] | Calificación: [X/5] | Fecha: [Fecha] | Idioma: [Idioma] | Tipo de viaje: [Tipo]

RESUMEN DE LA RESEÑA: [2 frases]

SENTIMIENTO: [Positivo / Mixto / Negativo]
ELOGIOS: [aspectos positivos clave]
QUEJAS: [aspectos negativos o Ninguno]

RESPUESTA PROPUESTA (lista para copiar y pegar):
[Texto completo de la respuesta]
—
```

Al final, una tabla resumen con todas las reseñas procesadas.

### Paso 5 — Editar y personalizar los borradores

Antes de publicar, revisar cada borrador:

- Verificar que los datos específicos (nombres del personal, fechas, habitaciones) sean correctos.
- Ajustar el tono si es necesario para que coincida con la voz de la marca del hotel.
- Para reseñas negativas, considerar si es apropiado ofrecer contacto directo antes de publicar.

### Paso 6 — Publicar la respuesta

| Plataforma | Dónde responder |
|------------|----------------|
| Google Business | [business.google.com](https://business.google.com) → Reseñas → Responder |
| TripAdvisor | Centro de administración → Reseñas → Responder a reseñas |
| Booking.com | Extranet → Opiniones de huéspedes → Responder a la reseña |

---

## Buenas prácticas

- **Cadencia recomendada:** ejecutar semanalmente para que ninguna reseña quede sin respuesta más de 7 días.
- **Priorizar reseñas negativas (1–2 estrellas):** una respuesta empática en 24 horas puede cambiar una mala impresión.
- **No publicar sin revisión humana:** el borrador es un punto de partida, no el producto final.
- **Reseñas en idiomas que no hablas:** Claude genera la respuesta en el idioma de la reseña. Pide a un hablante nativo que revise el borrador antes de publicarlo.

---

## Coste

| Componente | Coste |
|------------|-------|
| Claude in Chrome | Incluido en el plan Claude (gratuito o Pro) |
| Make.com | No requerido |
| Integraciones / mantenimiento | Ninguno |
| **Total adicional** | **0 €/mes** |
