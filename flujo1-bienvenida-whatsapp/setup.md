# Guía de activación — Flujo 1 Bienvenida WhatsApp

## Resumen del flujo

```
Gmail (Booking.com email)
  → Gemini API (extrae datos → JSON)
    → Google Sheets (registra reserva)
      → Router
          ├── [con teléfono] → WhatsApp (360dialog) → Email interno recepción
          └── [sin teléfono] → Email alerta recepción
```

Tiempo de ejecución completo: < 30 segundos desde la llegada del email.

---

## Fase 1 — Preparación (Días 1–2)

### 1.1 Crear cuenta Make.com
- Registrarse en [make.com](https://make.com) con el email del hotel
- Plan recomendado: **Core** (~10 €/mes, suficiente para este flujo)
- Activar la zona EU si el hotel opera en Europa (GDPR)

### 1.2 Conectar Gmail del hotel
- En Make.com → Connections → Add → Gmail
- Conectar con la cuenta que recibe los emails de Booking.com
- Anotar el nombre de la conexión (se usará como `__FILL_IN__GMAIL_CONNECTION_ID`)

### 1.3 Crear la hoja de Google Sheets

Crear un nuevo libro en Google Sheets llamado `Reservas_GHP_2026` con estas columnas **exactamente en este orden**:

| A | B | C | D | E | F | G | H | I | J | K | L |
|---|---|---|---|---|---|---|---|---|---|---|---|
| ID_Reserva | Nombre | Telefono | Check-in | Check-out | Habitacion | Personas | Canal | WhatsApp_enviado | Fecha_registro | fecha_upsell | upsell_enviado |

- Fila 1 = cabeceras (texto exacto como aparece arriba)
- Fila 2 en adelante = datos (Make escribirá aquí)
- Compartir el sheet con la cuenta de Google conectada a Make
- Anotar el ID del spreadsheet desde la URL: `docs.google.com/spreadsheets/d/__ID__/edit`

### 1.4 Obtener clave API de Gemini
- Ir a [aistudio.google.com](https://aistudio.google.com) → Get API key
- Crear una clave nueva (proyecto: "GHP Automations")
- Copiar la clave — se usará en `__FILL_IN__GEMINI_API_KEY` en el blueprint

### 1.5 Crear cuenta WhatsApp Business API (360dialog)
- Registrarse en [360dialog.com](https://360dialog.com)
- Vincular el número de teléfono del hotel a WhatsApp Business API
  - El número no puede estar activo en WhatsApp normal durante el proceso
  - Necesitarás acceso al número para recibir el SMS/llamada de verificación
- Tras verificar: anotar el `D360-API-KEY` del dashboard

### 1.6 Enviar plantilla a Meta para aprobación
- Ver `whatsapp_template.md` para el texto exacto y checklist
- Enviar la plantilla desde el dashboard de 360dialog → Templates
- Tiempo de aprobación: 24–48 horas
- **No continuar con el montaje del módulo 7 hasta tener la plantilla aprobada**

---

## Fase 2 — Montaje del flujo en Make.com (Día 3)

### 2.1 Importar el blueprint
1. En Make.com → Scenarios → crear nuevo escenario
2. Click en `...` (menú superior derecho) → **Import Blueprint**
3. Subir el archivo `blueprint.json` de esta carpeta
4. El escenario aparecerá con todos los módulos conectados

### 2.2 Rellenar los `__FILL_IN__` en cada módulo

Haz click en cada módulo y completa:

| Módulo | Campo a rellenar | Valor |
|--------|-----------------|-------|
| 1 (Gmail Watch) | Connection | Seleccionar la conexión Gmail del hotel |
| 3 (HTTP Gemini) | URL | Reemplazar `__FILL_IN__GEMINI_API_KEY` con tu clave |
| 5 (Sheets Add Row) | Connection | Seleccionar conexión Google |
| 5 (Sheets Add Row) | Spreadsheet | Buscar y seleccionar `Reservas_GHP_2026` |
| 5 (Sheets Add Row) | Sheet | Seleccionar la hoja (pestaña) correspondiente |
| 7 (HTTP 360dialog) | Header D360-API-KEY | Pegar tu API key de 360dialog |
| 7 (HTTP 360dialog) | Body → template name | Reemplazar `__FILL_IN__META_TEMPLATE_NAME` con el nombre aprobado |
| 8 (Sheets Update) | Connection + Sheet | Igual que módulo 5 |
| 9 (Gmail Send) | Connection | Conexión Gmail del hotel |
| 9 (Gmail Send) | To | Email de recepción |
| 10 (Gmail Send) | Connection | Conexión Gmail del hotel |
| 10 (Gmail Send) | To | Email de recepción |

### 2.3 Configurar el trigger (módulo 1)
- Frecuencia: **cada 15 minutos** (plan Core)
- Al activar por primera vez, Make pregunta desde qué email empezar → seleccionar "From now on"

### 2.4 Configurar reintentos en módulo 7 (WhatsApp)
- Click en módulo 7 → engranaje (settings) → Enable error handling
- Añadir ruta de error: Retry → 3 intentos, intervalo 5 minutos
- Tras 3 fallos: añadir módulo Gmail que notifique a recepción del fallo

---

## Fase 3 — Pruebas (Día 4)

### Test 1: Email con teléfono
Enviar desde una cuenta de prueba un email al Gmail del hotel con este asunto y cuerpo:

**Asunto:** `Nueva reserva confirmada — Test GHP`

**Cuerpo:**
```
Booking.com - Nueva reserva

Reserva confirmada para: María García Test
Número de reserva: TEST-001
Teléfono: +34 612 345 678
Habitación: Doble Superior Vista Mar
Check-in: 20/07/2026
Check-out: 23/07/2026
Número de personas: 2
```

**Verificar:**
- [ ] Fila añadida en Sheets con todos los campos
- [ ] `fecha_upsell` = 17/07/2026 (20/07 - 3 días)
- [ ] `WhatsApp_enviado` = "Si"
- [ ] WhatsApp recibido en el número de prueba
- [ ] Email interno recibido en recepción

### Test 2: Email sin teléfono
Mismo email pero sin la línea de teléfono.

**Verificar:**
- [ ] Fila añadida en Sheets con `WhatsApp_enviado` = "Pendiente telefono"
- [ ] Email de alerta recibido en recepción con los datos del huésped
- [ ] No se dispara el módulo de WhatsApp

### Test 3: Email de cancelación (falso disparo)
**Asunto:** `Reserva cancelada — Test GHP`

**Verificar:**
- [ ] El flujo NO se ejecuta (filtro del módulo 2 bloquea el email)

---

## Fase 4 — Activación en producción (Semana 2)

- [ ] Desactivar el escenario de prueba y activar el de producción con el mismo blueprint
- [ ] Verificar que el trigger está activo (círculo verde en el escenario)
- [ ] Revisar el panel de ejecuciones de Make.com cada mañana durante los primeros 7 días
- [ ] Comprobar que las filas en Sheets se crean correctamente
- [ ] Comprobar que los huéspedes reales reciben el WhatsApp

**Señales de alarma a vigilar:**
- Ejecuciones con estado "Warning" o "Error" en Make
- Fila sin `WhatsApp_enviado` = "Si" en huéspedes que sí tienen teléfono
- Huéspedes que contactan diciendo que no recibieron confirmación

---

## Fase 5 — Optimización (Semanas 3–4)

- [ ] Si el flujo lleva 7 días sin errores: **desactivar el módulo 9** (email interno de recepción) para reducir ruido en el buzón
- [ ] Revisar las primeras métricas en el dashboard de 360dialog (tasa de entrega, aperturas)
- [ ] Verificar que `fecha_upsell` y `upsell_enviado` están bien calculados para el Flujo 2
- [ ] Si hay variaciones en el formato de email de Booking.com en inglés/francés, revisar que el prompt de Gemini extrae correctamente

---

## Costes mensuales estimados

| Servicio | Coste |
|----------|-------|
| Make.com Core | ~10 €/mes |
| 360dialog WhatsApp API | 5–15 €/mes + ~0,07 €/mensaje |
| Gemini API | < 5 €/mes |
| Google Sheets | Gratis |
| **Total (200 reservas/mes)** | **~34–44 €/mes** |

Con 600 reservas/mes: < 0,24 € por reserva automatizada.
Tiempo ahorrado en recepción: 5–10 min/reserva → equivale a horas de trabajo recuperadas cada semana.

---

## Conexión con Flujo 2 (Upselling pre-llegada)

La columna `fecha_upsell` (checkin − 3 días) y `upsell_enviado` ("No" por defecto) están ya en la hoja desde el Flujo 1. El Flujo 2 solo necesita leer esta hoja — no hay que modificar este flujo cuando se active el siguiente.
