# Guía de uso — Flujo 4 Respuesta automática a reseñas

## Qué hace este flujo

El comando `/responder_reseñas` automatiza la revisión de reseñas sin respuesta y la generación de borradores listos para publicar. Cuando se ejecuta con una página de reseñas del hotel abierta en el navegador, el asistente de IA:

1. Escanea todas las reseñas visibles e identifica las que **no tienen respuesta del propietario**.
2. Analiza el sentimiento, los elogios y las quejas de cada una.
3. Genera un borrador de respuesta personalizado en el **mismo idioma** que la reseña (~120 palabras), listo para copiar y pegar.
4. Produce una tabla resumen de todas las reseñas procesadas.

**Plataformas compatibles:** Google Hotels / Perfil de Google Business · TripAdvisor · Booking.com · cualquier plataforma que muestre reseñas de huéspedes en el navegador.

**Tipo de herramienta:** Comando de IA con acceso al navegador — no requiere Make.com ni configuración de webhooks. Solo necesitas un asistente de IA con acceso a la pestaña activa.

---

## Requisitos previos

- Un asistente de IA con acceso al navegador (Claude con extensión de navegador o herramienta equivalente).
- Una página de reseñas del hotel abierta en el navegador con las reseñas visibles.
- El texto del comando guardado (ver `command.md`).
- Credenciales de propietario o administrador en la plataforma para publicar las respuestas *(no son necesarias para generar los borradores, solo para publicarlos)*.

---

## Paso a paso

### Paso 1 — Navegar a la página de reseñas

Abre la plataforma de reseñas en tu navegador:

- **Google Hotels / Perfil de Google Business:** busca el hotel en Google → haz clic en el nombre → pestaña "Reseñas".
- **TripAdvisor:** navega al perfil del hotel → sección de reseñas.
- **Booking.com:** navega al listado del hotel → sección "Opiniones de huéspedes".

Si la plataforma usa scroll infinito, **desplázate hacia abajo** para cargar todas las reseñas antes de ejecutar el comando, de modo que el asistente vea el máximo posible en una sola pasada.

### Paso 2 — Abrir el asistente de IA

Abre el panel del asistente de IA con acceso a la pestaña activa:

- **Extensión de navegador Claude:** haz clic en el icono de Claude en la barra de herramientas para abrir el panel lateral.
- **Otra herramienta:** asegúrate de que esté conectada a la pestaña activa.

### Paso 3 — Ejecutar el comando

Copia el contenido completo de `command.md` y pégalo en el campo de chat del asistente. Pulsa **Intro** o **Enviar**.

El asistente comenzará a escanear la página automáticamente. Dependiendo del número de reseñas, puede tardar entre 30 segundos y unos minutos.

### Paso 4 — Revisar el resultado

El asistente genera un bloque estructurado por cada reseña sin respuesta:

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

Antes de publicar, revisa cada borrador:

- Verifica que los datos específicos (nombres del personal, fechas, habitaciones) sean correctos.
- Ajusta el tono si es necesario para que coincida con la voz de la marca del hotel.
- Añade una firma personal si el estilo del hotel lo requiere.
- Para reseñas negativas, considera si es apropiado ofrecer contacto directo antes de publicar.

### Paso 6 — Publicar la respuesta

| Plataforma | Dónde responder |
|------------|----------------|
| Google Business | [business.google.com](https://business.google.com) → Reseñas → Responder |
| TripAdvisor | Centro de administración → Reseñas → Responder a reseñas |
| Booking.com | Extranet → Opiniones de huéspedes → Responder a la reseña |

Pega el borrador, realiza los últimos ajustes y envía.

---

## Buenas prácticas

- **Cadencia recomendada:** ejecutar semanalmente para que ninguna reseña quede sin respuesta más de 7 días. Las respuestas rápidas mejoran la confianza del cliente y el posicionamiento en la plataforma.
- **Priorizar reseñas negativas (1–2 estrellas):** incluso una respuesta breve y empática en 24 horas puede cambiar una mala impresión.
- **No publicar sin revisión humana:** el borrador es un punto de partida, no el producto final.
- **Reseñas en idiomas que no hablas:** el comando genera la respuesta en el idioma de la reseña. Pide a un hablante nativo que revise el borrador antes de publicarlo.
- **Evitar duplicados:** mantén un registro de las respuestas publicadas (nombre del reseñador, fecha, plataforma) para no publicar dos veces.

---

## Ejemplo de output

Ver `command.md` → sección "Apéndice B" para ejemplos de bloque de salida completo (reseña positiva en español, reseña mixta en inglés) y tabla resumen.

---

## Coste

| Componente | Coste |
|------------|-------|
| Claude (extensión de navegador) | Incluido en el plan Claude que ya uses |
| Herramienta de IA alternativa | Variable según proveedor |
| Make.com | No requerido para este flujo |
| **Total adicional** | **0 €/mes** (si ya usas Claude) |
