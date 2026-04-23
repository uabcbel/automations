# Guía de activación — Flujo 1 Bienvenida WhatsApp (v2.0)

## Resumen del flujo

```
Mailchimp webhook (subscribe / profile_update)
  → Router: ¿alta nueva o modificación?
      ├── [alta] → Sheets upsert → WhatsApp al huésped → Email interno recepción (opcional)
      └── [modificación] → ¿cambió check-in, habitación o personas?
              ├── [sí] → Sheets update → WhatsApp actualizado al huésped
              └── [no] → Sheets update (sin WhatsApp — evita spam)
  └── [PHONE vacío] → Sheets upsert con WhatsApp_enviado = "Pendiente teléfono" → Email alerta recepción
```

**Cambios respecto a v1.0:** se elimina el módulo de Gmail Watch (lectura de emails de Booking.com) y el módulo de extracción con IA (Gemini). Mailchimp ya entrega los datos de reserva estructurados vía webhook. Esto reduce la latencia de ~15 min a segundos, elimina ~5 €/mes de coste de API de IA, y amplía la cobertura a todas las OTAs conectadas al PMS (no solo Booking.com).

---

## Prerequisito crítico: validar el contrato de datos con Acigrup

Antes de montar nada en Make, confirmar con el equipo de Acigrup que la audiencia de Mailchimp publica estos merge fields con los formatos indicados:

| Merge tag | Campo PMS | Formato | Obligatorio |
|-----------|-----------|---------|-------------|
| `*\|FNAME\|*` | Nombre del huésped | Texto | Sí |
| `*\|PHONE\|*` | Teléfono móvil | E.164 (+34...) | Sí |
| `*\|CHECKIN\|*` | Fecha de entrada | DD/MM/YYYY | Sí |
| `*\|CHECKOUT\|*` | Fecha de salida | DD/MM/YYYY | Sí |
| `*\|ROOMTYPE\|*` | Tipo de habitación | Texto | Sí |
| `*\|GUESTS\|*` | Nº de personas | Entero | Sí |
| `*\|RESID\|*` | ID de reserva PMS | Texto único | Sí (clave anti-duplicados) |
| `*\|CHANNEL\|*` | Canal de entrada | Booking / Expedia / Web / … | Recomendado |

Pedir a Acigrup una exportación de muestra de 5–10 contactos reales y revisar que PHONE y RESID llegan con el formato correcto. Si PHONE no está en E.164, el módulo de WhatsApp fallará.

---

## Fase 1 — Preparación (Días 1–2)

### 1.1 Crear cuenta Make.com
- Registrarse en [make.com](https://make.com) con el email del hotel
- Plan recomendado: **Core** (~10 €/mes)
- Activar zona EU si el hotel opera en Europa (GDPR)

### 1.2 Crear la hoja de Google Sheets

Crear un nuevo libro llamado `Reservas_GHP_2026` con estas columnas **exactamente en este orden**:

| A | B | C | D | E | F | G | H | I | J | K | L |
|---|---|---|---|---|---|---|---|---|---|---|---|
| ID_Reserva | Nombre | Telefono | Check-in | Check-out | Habitacion | Personas | Canal | WhatsApp_enviado | Fecha_registro | fecha_upsell | upsell_enviado |

- Fila 1 = cabeceras (texto exacto)
- Fila 2 en adelante = datos (Make escribirá aquí)
- Compartir el sheet con la cuenta de Google conectada a Make
- Anotar el ID del spreadsheet desde la URL

### 1.3 Crear cuenta WhatsApp Business API (360dialog)
- Registrarse en [360dialog.com](https://360dialog.com)
- Vincular el número de teléfono del hotel a WhatsApp Business API
- Anotar el `D360-API-KEY` del dashboard

### 1.4 Enviar plantilla a Meta para aprobación
- Ver `whatsapp_template.md` para el texto exacto y checklist
- Enviar desde el dashboard de 360dialog → Templates
- Tiempo de aprobación: 24–48 horas
- **No continuar con el montaje del módulo 4 hasta tener la plantilla aprobada**

---

## Fase 2 — Configurar el webhook en Mailchimp (Día 3, antes de Make)

1. En Mailchimp → Audience → seleccionar la audiencia `Reservas_GHP_2026`
2. Settings → Webhooks → Create New Webhook
3. La URL del webhook la genera Make al añadir el trigger Mailchimp (ver paso 3.2)
   - Para obtenerla, crear primero el escenario en Make y copiar la URL del módulo trigger
4. En el webhook de Mailchimp, activar únicamente:
   - ✅ Subscribes
   - ✅ Profile updates
   - ❌ Campaign sending (desactivar)
   - ❌ Cleaned addresses (desactivar)

---

## Fase 3 — Montaje del flujo en Make.com (Día 3)

### 3.1 Importar el blueprint
1. Make.com → Scenarios → crear nuevo escenario
2. Click en `...` → **Import Blueprint** → subir `blueprint.json`
3. El escenario aparecerá con todos los módulos conectados

### 3.2 Rellenar los `__FILL_IN__` en cada módulo

| Módulo | Campo a rellenar | Valor |
|--------|-----------------|-------|
| 1 (Mailchimp Watch) | Connection | Conectar cuenta Mailchimp del hotel |
| 1 (Mailchimp Watch) | Audience | Seleccionar `Reservas_GHP_2026` |
| 3 (Sheets Search + Upsert) | Connection | Seleccionar conexión Google |
| 3 (Sheets Search + Upsert) | Spreadsheet | Buscar y seleccionar `Reservas_GHP_2026` |
| 4 (HTTP 360dialog) | Header D360-API-KEY | Pegar tu API key de 360dialog |
| 4 (HTTP 360dialog) | Body → template name | Reemplazar `__FILL_IN__META_TEMPLATE_NAME` con el nombre aprobado |
| 3b (Sheets Update) | Connection + Sheet | Igual que módulo 3 |
| 5 (Gmail Send) | Connection | Conexión Gmail del hotel |
| 5 (Gmail Send) | To | Email de recepción |

### 3.3 Verificar el Router (módulo 2)
- Ruta A: `event_type = subscribe` → continúa al módulo 3 (upsert) y módulo 4 (WhatsApp)
- Ruta B: `event_type = profile_update` → compara los campos clave antes de decidir si reenviar WhatsApp

### 3.4 Configurar reintentos en módulo 4 (WhatsApp)
- Click en módulo 4 → engranaje (settings) → Enable error handling
- Retry: 3 intentos, intervalo 5 minutos
- Tras 3 fallos: Gmail que notifique a recepción del fallo (WhatsApp_enviado queda en "Error" en Sheets)

---

## Fase 4 — Pruebas (Día 4)

### Test 1: Alta nueva con teléfono
Añadir un suscriptor de prueba en Mailchimp → Audience → Add contacts → Add a subscriber con estos merge fields:

| Merge tag | Valor de prueba |
|-----------|----------------|
| FNAME | María García Test |
| PHONE | +34612345678 (número real tuyo) |
| CHECKIN | [fecha en 10 días, DD/MM/YYYY] |
| CHECKOUT | [fecha en 13 días, DD/MM/YYYY] |
| ROOMTYPE | Doble Superior Vista Mar |
| GUESTS | 2 |
| RESID | TEST-001 |
| CHANNEL | Test |

**Verificar:**
- [ ] Webhook disparado en Make (ver ejecuciones del escenario)
- [ ] Fila añadida en Sheets con todos los campos
- [ ] `fecha_upsell` = CHECKIN − 3 días (DD/MM/YYYY)
- [ ] `upsell_enviado` = "No"
- [ ] `WhatsApp_enviado` = "Si"
- [ ] WhatsApp recibido en el número de prueba con los datos correctos
- [ ] Email interno recibido en recepción

### Test 2: Alta sin teléfono
Añadir suscriptor de prueba con PHONE vacío.

**Verificar:**
- [ ] Fila en Sheets con `WhatsApp_enviado` = "Pendiente teléfono"
- [ ] Email de alerta enviado a recepción
- [ ] No se llama a la API de WhatsApp

### Test 3: Modificación relevante (profile_update)
Editar el suscriptor TEST-001 en Mailchimp → cambiar CHECKIN a otra fecha.

**Verificar:**
- [ ] Sheets actualiza la fila existente (sin duplicado — misma fila por RESID)
- [ ] WhatsApp de actualización enviado al huésped
- [ ] `fecha_upsell` recalculada con la nueva fecha

### Test 4: Modificación no relevante
Editar TEST-001 → cambiar solo el email del contacto (campo no clave).

**Verificar:**
- [ ] Sheets actualiza la fila
- [ ] No se envía WhatsApp (modificación no afecta check-in, habitación ni personas)

### Test 5: Webhook duplicado (anti-duplicado)
Hacer un segundo profile_update trivial sobre TEST-001 inmediatamente después del test anterior.

**Verificar:**
- [ ] Sheets tiene una sola fila para TEST-001 (upsert por RESID)
- [ ] `WhatsApp_enviado` no se duplica ni se resetea

---

## Fase 5 — Activación en producción (Semana 2)

- [ ] Activar el escenario (toggle verde)
- [ ] Confirmar con Acigrup que el webhook de Mailchimp apunta a la URL de Make en producción
- [ ] Revisar el panel de ejecuciones de Make.com cada mañana durante los primeros 7 días
- [ ] Comprobar que las filas en Sheets se crean correctamente con todas las columnas
- [ ] Comprobar que los huéspedes reales reciben el WhatsApp

**Señales de alarma a vigilar:**
- Ejecuciones con estado "Warning" o "Error" en Make
- `WhatsApp_enviado` ≠ "Si" en huéspedes con teléfono disponible
- Fila duplicada (mismo RESID dos veces) → revisar lógica del upsert

---

## Fase 6 — Optimización (Semanas 3–4)

- [ ] Si el flujo lleva 7 días sin errores: **desactivar el módulo 5** (email interno de recepción) para reducir ruido en el buzón
- [ ] Verificar que `fecha_upsell` y `upsell_enviado` están correctos para el Flujo 2
- [ ] Revisar métricas de entrega en el dashboard de 360dialog

---

## Costes mensuales estimados

| Servicio | Coste |
|----------|-------|
| Make.com Core | ~10 €/mes |
| 360dialog WhatsApp API | 5–15 €/mes + ~0,07 €/mensaje |
| Google Sheets | Gratis |
| Mailchimp | Ya contratado — sin coste adicional |
| **Total (300 reservas/mes)** | **~15–25 €/mes** |

Comparado con v1.0 (con Gemini para extracción): ahorro de ~5 €/mes al eliminar el consumo de Gemini API.

---

## Conexión con Flujo 2 (Upselling pre-llegada)

Las columnas `fecha_upsell` (checkin − 3 días) y `upsell_enviado` ("No" por defecto) que este flujo añade al libro de Sheets son el punto de entrada del Flujo 2. Una vez activo el Flujo 1, el Flujo 2 puede activarse sin modificar ningún módulo de este documento.
