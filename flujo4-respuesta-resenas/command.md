# Comando /responder_reseñas

Copia el bloque completo a continuación y escríbelo (o pégalo) en el panel de **Claude in Chrome** cuando tengas abierta la página de reseñas del hotel. Claude in Chrome lee automáticamente la pestaña activa — no hace falta copiar el contenido de la página.

No modifiques la estructura de los pasos ni el formato de salida. Solo actualiza la línea de cierre del Paso 3 si el nombre del hotel cambia.

---

```
/responder_reseñas

TAREA: Analizar la página de reseñas de hoteles actualmente visible y generar un borrador de respuesta listo para usar para cada reseña que aún NO tenga respuesta del propietario.


## PASO 1 — RECOPILAR LAS RESEÑAS SIN RESPONDER

Desplázate por todas las reseñas visibles en la página. Para cada reseña, registra lo siguiente:
- Nombre del reseñador
- Calificación (p. ej., 4/5, 8/10)
- Fecha de publicación
- Idioma de la reseña
- Tipo de viaje (si se muestra): Familia, Pareja, Solo, Amigos…
- Texto completo de la reseña (expande "Leer más" si está presente)
- Si hay un bloque de "Respuesta del propietario" ya publicado

Conserva solo las reseñas que NO tengan respuesta del propietario.


## PASO 2 — ANALIZAR CADA RESEÑA

Para cada reseña sin respuesta, identifica:
- Sentimiento general (Positivo / Mixto / Negativo)
- Aspectos destacados mencionados (p. ej., piscina, personal, desayuno, habitación, spa)
- Principales quejas o sugerencias (p. ej., ruido, variedad del bufé, aire acondicionado)
- Cualquier persona o servicio específico que el huésped haya mencionado por su nombre


## PASO 3 — REDACTAR UNA RESPUESTA PERSONALIZADA

Escribe una respuesta en el MISMO IDIOMA que la reseña. Sigue esta estructura:

1. Saludo: diríjete al reseñador por su nombre de pila.
2. Agradécele sinceramente (adapta el mensaje al tipo de viaje que realizó).
3. Reconoce los elogios de forma específica (repite brevemente sus palabras exactas).
4. Aborda las quejas de forma constructiva: pide disculpas sin prometer más de lo que puedes cumplir, explica qué está haciendo o hará el hotel, y ofrece un contacto directo (p. ej., "ponte en contacto con nuestro equipo de Atención al Cliente").
5. Invítale a volver.
6. Cierre: "El equipo del Gran Hotel Peñíscola" (o su equivalente en el idioma de la reseña).

Normas de tono: cordial, profesional, nunca a la defensiva. Máximo ~120 palabras por respuesta. No uses frases genéricas como "Valoramos todos los comentarios".


## PASO 4 — FORMATO DE SALIDA

Para cada reseña sin respuesta, genera un bloque con este formato exacto:

—
Reseñador: [Nombre] | Calificación: [X/5] | Fecha: [Fecha] | Idioma: [Idioma] | Tipo de viaje: [Tipo]

RESUMEN DE LA RESEÑA: [Resumen de 2 frases de lo que dijeron]

SENTIMIENTO: [Positivo / Mixto / Negativo]
ELOGIOS: [lista los aspectos positivos clave]
QUEJAS: [lista los aspectos negativos principales, o "Ninguna"]

RESPUESTA PROPUESTA (lista para copiar y pegar):
[Texto completo de la respuesta aquí]
—

Después de todos los bloques, añade una TABLA RESUMEN con las columnas:
# | Reseñador | Calificación | Sentimiento | Tema clave | Idioma de respuesta
```

---

## Apéndice B — Ejemplos de output

### Bloque de muestra — Reseña positiva (español)

```
—
Reseñador: Beatriz | Calificación: 6.5/10 | Fecha: Hace 2 años | Idioma: Español | Tipo de viaje: No especificado

RESUMEN DE LA RESEÑA: Beatriz destacó la amabilidad y la profesionalidad de todo el personal durante su estancia en julio de 2023. Su comentario fue breve y totalmente positivo.

SENTIMIENTO: Positivo
ELOGIOS: Amabilidad del personal, profesionalismo del personal
QUEJAS: Ninguna

RESPUESTA PROPUESTA (lista para copiar y pegar):

Hola Beatriz,

Muchas gracias por tomarte el tiempo de compartir tu experiencia con nosotros. Nos alegra enormemente saber que la amabilidad y profesionalidad de nuestro equipo hicieron de tu estancia algo especial: es exactamente lo que buscamos cada día.

Esperamos poder darte la bienvenida de nuevo en el Gran Hotel Peñíscola muy pronto.

Un cordial saludo,
El equipo del Gran Hotel Peñíscola
—
```

### Bloque de muestra — Reseña mixta (inglés)

```
—
Reseñador: John T. | Calificación: 3/5 | Fecha: Hace 1 mes | Idioma: Inglés | Tipo de viaje: Pareja

RESUMEN DE LA RESEÑA: John disfrutó de la piscina y el spa, pero consideró que la selección del bufé era limitada y el aire acondicionado de la habitación poco fiable durante su estancia. En general, fue un viaje agradable, pero con aspectos que podrían mejorarse.

SENTIMIENTO: Mixto
ELOGIOS: Limpieza de la piscina, el spa y la habitación
QUEJAS: Variedad limitada en el bufé, fiabilidad del aire acondicionado

RESPUESTA PROPUESTA (lista para copiar y pegar):

Dear John,

Thank you for sharing your experience with us. We're glad to hear you enjoyed the pool and spa, and we sincerely apologise that the buffet variety and air conditioning did not meet your expectations. Your feedback has been shared with our operations team and we are reviewing both areas. We'd love the opportunity to welcome you back and show you the improvements in person. Please don't hesitate to reach out to our Customer Care team at any time.

Warm regards,
The Gran Hotel Peñíscola Team
—
```

### Tabla resumen de muestra

| # | Reseñador | Calificación | Sentimiento | Tema clave | Idioma de respuesta |
|---|-----------|-------------|-------------|------------|---------------------|
| 1 | Beatriz | 6.5/10 | Positivo | Amabilidad y profesionalidad del personal | Español |
| 2 | John T. | 3/5 | Mixto | Variedad de bufé, fiabilidad del aire acondicionado | Inglés |
