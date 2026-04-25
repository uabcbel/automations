# Guía de activación — Flujo 2 Upselling Pre-Llegada (v2.0)

> 📄 **Documento de diseño:** [Flujo2\_Upselling\_Prellegada\_GHP\_v2](https://docs.google.com/document/d/1WTW2VcbqysJXW_6XTkUQG71sSsNlVWbqrSQN_r3_5fM/edit) *(Google Drive — acceso interno)*

## Prerrequisito obligatorio

**El Flujo 1 debe llevar al menos 1 semana activo** y la hoja `Reservas_GHP_2026` debe tener reservas reales con `fecha_upsell` calculada. Activar este flujo antes significa que no habrá filas con `fecha_upsell` en el futuro próximo y el escenario correrá en vacío.

Además, antes de activar, confirmar con Acigrup que los merge fields `GUESTS` y `ROOMTYPE` llegan correctamente a Mailchimp — la personalización de la IA depende de ellos.

## Resumen del flujo

```
Scheduler (10:00 AM diario)
  → Google Sheets: busca fecha_upsell = hoy AND upsell_enviado = No AND estado_reserva ≠ Cancelada
    → Iterator (por cada fila encontrada)
      → Filter: teléfono disponible?
          ├── [con teléfono] → Gemini genera mensaje personalizado
          │                  → WhatsApp al huésped (360dialog)
          │                  → Sheets: upsell_enviado = Sí, fecha_envio_upsell = hoy
          └── [sin teléfono] → Email alerta recepción
```

**Cadena de datos completa:**
`PMS Acigrup → Mailchimp webhook → Make (Flujo 1) → Google Sheets → (3 días antes del check-in) → Make (Flujo 2) → WhatsApp`

Anti-duplicado: si el scheduler se ejecuta dos veces el mismo día, la segunda pasada no encuentra ninguna fila (ya están marcadas como "Sí") y termina sin enviar nada.

---

## Columnas adicionales a añadir en Google Sheets

Antes de activar, añadir estas columnas a la hoja `Reservas_GHP_2026` (a continuación de las 12 columnas del Flujo 1):

| Columna | Tipo | Quién rellena | Para qué |
|---------|------|---------------|----------|
| `fecha_envio_upsell` | Fecha/hora | Make (automático) | Auditoría de envíos |
| `upsell_convertido` | Sí / No | Recepción (manual) | Calcular tasa de conversión |
| `upsell_servicio` | Texto | Recepción (manual) | Qué servicio reservó el huésped |
| `upsell_importe` | Número | Recepción (manual) | ROI del flujo |

---

## Fase 1 — Preparación (Día 1)

### 1.1 Confirmar precios con el hotel
El prompt de Gemini tiene estos precios codificados — verificar con el equipo antes de activar:

| Servicio | Precio en prompt | ¿Correcto? |
|----------|-----------------|------------|
| Upgrade vista al mar | +30 €/noche | ☐ Confirmar |
| Cena romántica | 45 €/persona | ☐ Confirmar |
| Parking cubierto | 12 €/día | ☐ Confirmar |
| Early check-in | +25 € | ☐ Confirmar |
| Acceso spa + masaje | 60 €/persona | ☐ Confirmar |

Si algún precio no es correcto, editar el prompt en el módulo 9 del blueprint antes de importar.

### 1.2 Confirmar la URL de reserva de extras
El prompt termina siempre con `https://granhotelpeniscola.com/extras`. Verificar que esta URL existe y funciona.

### 1.3 Añadir columnas de métricas a Sheets
Añadir las 4 columnas descritas en la sección anterior al libro `Reservas_GHP_2026`.

### 1.4 Verificar que GUESTS y ROOMTYPE llegan desde Mailchimp
Abrir una fila reciente del Flujo 1 en Sheets y confirmar que las columnas `Personas` y `Habitacion` tienen valores correctos (no están vacías ni tienen valores por defecto). Si están vacías, contactar con Acigrup antes de continuar.

---

## Fase 2 — Montaje en Make.com (Día 2)

### 2.1 Importar el blueprint
1. Make.com → Scenarios → crear nuevo escenario
2. `...` → **Import Blueprint** → subir `blueprint.json`

### 2.2 Rellenar los `__FILL_IN__` en cada módulo

| Módulo | Campo | Valor |
|--------|-------|-------|
| 6 (Scheduler) | `startDate` | Fecha de hoy en formato YYYY-MM-DD |
| 7 (Sheets Search) | Connection | Misma conexión Google que Flujo 1 |
| 7 (Sheets Search) | Spreadsheet | Misma hoja `Reservas_GHP_2026` |
| 9 (HTTP Gemini) | URL | Reemplazar `__FILL_IN__GEMINI_API_KEY` con tu clave |
| 10 (HTTP 360dialog) | Header D360-API-KEY | Misma API key que Flujo 1 |
| 10b (Sheets Update) | Connection + Sheet | Igual que módulo 7 |
| 8b (Gmail) | Connection | Conexión Gmail del hotel |
| 8b (Gmail) | To | Email de recepción |

### 2.3 Verificar columnas en el Iterator (módulo 7b)
Confirmar que el módulo 9 referencia estas columnas de Sheets:
- `{{Nombre}}` → columna B
- `{{Check-in}}` → columna D
- `{{Personas}}` → columna G
- `{{Habitacion}}` → columna F
- `{{Telefono}}` → columna C

Si los nombres de columna en Sheets difieren, ajustar las referencias en los módulos 9 y 10.

### 2.4 Verificar el filtro de cancelaciones (módulo 8)
El módulo 8 (Filter) debe incluir tres condiciones:
1. `upsell_enviado = No`
2. `Telefono` no está vacío
3. `WhatsApp_enviado ≠ Cancelada` (o el campo `estado_reserva` que el Flujo 1 marca cuando el PMS envía STATUS=CANCELLED)

---

## Fase 3 — Pruebas (Día 3)

### Test 1: Huésped pareja (2 personas) con teléfono
Añadir manualmente en Sheets:

| ID_Reserva | Nombre | Telefono | Check-in | Check-out | Habitacion | Personas | Canal | WhatsApp_enviado | Fecha_registro | fecha_upsell | upsell_enviado |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TEST-001 | Carlos García | +34612345678 | [hoy+3] | [hoy+5] | Doble Superior | 2 | Test | Sí | [hoy] | [hoy] | No |

Ejecutar el escenario manualmente (▶ Run once).

**Verificar:**
- [ ] WhatsApp recibido en +34612345678 con tono correcto
- [ ] Mensaje menciona upgrade vista al mar y/o cena romántica (perfil pareja, 2 personas)
- [ ] Mensaje escrito en prosa, sin viñetas, máx. 160 palabras
- [ ] Mensaje termina con la URL de extras
- [ ] Sheets: `upsell_enviado` = "Sí" y `fecha_envio_upsell` rellena

### Test 2: Huésped familia (3+ personas) con teléfono
| ID_Reserva | Nombre | Telefono | Check-in | Check-out | Habitacion | Personas | Canal | WhatsApp_enviado | Fecha_registro | fecha_upsell | upsell_enviado |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TEST-002 | Ana Martínez | +34698765432 | [hoy+3] | [hoy+6] | Suite Familiar | 4 | Test | Sí | [hoy] | [hoy] | No |

**Verificar:**
- [ ] Mensaje menciona parking y/o early check-in (perfil familia, 4 personas)

### Test 3: Huésped sin teléfono
| ID_Reserva | Nombre | Telefono | ... | fecha_upsell | upsell_enviado |
|---|---|---|---|---|---|
| TEST-003 | Pedro Sanz | | ... | [hoy] | No |

**Verificar:**
- [ ] Email de alerta enviado a recepción con el nombre del huésped y su RESID
- [ ] No se llama a Gemini ni a la API de WhatsApp

### Test 4: Anti-duplicado
Con las filas de test ya marcadas como "Sí", ejecutar el escenario de nuevo.

**Verificar:**
- [ ] El módulo 7 devuelve 0 resultados
- [ ] El escenario termina sin procesar nada ni enviar mensajes

### Test 5: Reserva cancelada
Cambiar manualmente `WhatsApp_enviado` de una fila a "Cancelada" y poner `fecha_upsell` = hoy, `upsell_enviado` = No.

**Verificar:**
- [ ] El filtro del módulo 8 descarta esta fila
- [ ] No se envía WhatsApp ni se genera mensaje con Gemini

---

## Fase 4 — Activación en producción (Semana 2)

- [ ] Eliminar las filas de prueba de Sheets (TEST-001, TEST-002, TEST-003)
- [ ] Activar el scheduler (toggle verde en el escenario)
- [ ] Pedir a recepción que verifiquen los primeros WhatsApp enviados a huéspedes reales
- [ ] Revisar el panel de ejecuciones de Make.com cada mañana los primeros 7 días

**Señales de alarma:**
- Escenario con errores en el módulo 10 (WhatsApp) → puede indicar ventana de conversación cerrada (ver nota abajo)
- Mensaje con tono inadecuado → ajustar el prompt del módulo 9
- Precio incorrecto mencionado → corregir en el prompt INMEDIATAMENTE y avisar a recepción

---

## Nota: ventana de conversación de WhatsApp

El módulo 10 envía el mensaje como **texto libre** (sin template), lo cual solo funciona si el huésped respondió al mensaje del Flujo 1 en las últimas 24 horas. Si no respondió, 360dialog devolverá error 400.

**Opción A (recomendada a corto plazo):** Ignorar el error y dejar que Make lo registre. Recepción gestiona manualmente si lo ve en el log.

**Opción B (más robusta):** Añadir un manejador de errores en el módulo 10 que, en caso de fallo, reenvíe usando un **template de WhatsApp aprobado** para upselling. Requiere crear y aprobar un nuevo template en Meta (proceso similar al del Flujo 1).

---

## Fase 5 — Optimización (Mes 2)

- [ ] Analizar columnas `upsell_convertido` e `upsell_importe` para calcular ROI real
- [ ] Si conversión < 5%: revisar el prompt (tono, servicios ofrecidos, hora de envío)
- [ ] Si conversión > 10%: considerar añadir variantes del mensaje por tipo de habitación
- [ ] Explorar añadir un segundo mensaje recordatorio 1 día antes del check-in

---

## KPIs objetivo (primer mes)

| Métrica | Objetivo |
|---------|----------|
| Tasa de envío | > 95% de reservas con teléfono |
| Tasa de apertura WhatsApp | > 70% (benchmark del canal) |
| Tasa de conversión | > 5% (conservador) / 8–12% (sector) |
| Ingreso adicional estimado | 100 reservas × 10% × 35 € ticket medio = 350 €/mes |
| ROI | Ingresos upselling >> ~35–50 €/mes coste Make + API |

---

## Costes adicionales respecto al Flujo 1

| Componente | Herramienta | Coste adicional |
|------------|-------------|-----------------|
| Make.com | Mismo escenario — operaciones adicionales | Incluido en plan Core (bajo volumen) |
| Gemini API | Google AI Studio | < 6 €/mes para 600 reservas |
| WhatsApp Business API | 360dialog / Twilio | Mensajes en ventana activa: gratuitos* |
| Google Sheets | Mismo libro del Flujo 1 | Sin coste adicional |

*Si el huésped respondió al WhatsApp del Flujo 1, la conversación está activa y el mensaje se envía sin coste de sesión. Si no respondió, aplica el coste de template (~0,07 €/mensaje).
