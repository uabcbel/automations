# Plantilla WhatsApp Business — Meta Approval (v2.0)

## Texto de la plantilla

```
¡Hola {{1}}! 👋 Te confirmamos tu reserva en Gran Hotel Peñíscola:

📅 Llegada:    {{2}}
📅 Salida:     {{3}}
🛏 Habitación: {{4}}
🔑 Nº reserva: {{5}}

Check-in: de 14:00 a 00:00 h
📍 Av. Papa Luna, 5, Peñíscola

Si tienes cualquier duda, responde a este mensaje. ¡Te esperamos!
```

## Mapa de variables

Las variables se obtienen directamente de los merge fields que el PMS Acigrup publica en Mailchimp. Make las inyecta en el cuerpo del template al llamar a la API de 360dialog.

| Variable en plantilla | Merge field Mailchimp | Módulo Make | Ejemplo |
|---|---|---|---|
| `{{1}}` | `FNAME` | Módulo 4 → `merge_fields.FNAME` | María García |
| `{{2}}` | `CHECKIN` | Módulo 4 → `merge_fields.CHECKIN` | 15/07/2026 |
| `{{3}}` | `CHECKOUT` | Módulo 4 → `merge_fields.CHECKOUT` | 18/07/2026 |
| `{{4}}` | `ROOMTYPE` | Módulo 4 → `merge_fields.ROOMTYPE` | Doble Superior Vista Mar |
| `{{5}}` | `RESID` | Módulo 4 → `merge_fields.RESID` | BK-3847291 |

---

## Checklist de envío a Meta

Completa estos datos al crear la plantilla en el portal de 360dialog:

- **Nombre de la plantilla:** `bienvenida_reserva_ghp`
  _(solo minúsculas, guiones bajos, sin espacios — anótalo en el blueprint.json en `__FILL_IN__META_TEMPLATE_NAME`)_
- **Categoría:** `UTILITY` _(confirmación de transacción — mayor probabilidad de aprobación que MARKETING)_
- **Idioma:** `es` (Spanish)
- **Tipo de mensaje:** Text only
- **Header:** ninguno
- **Body:** pegar el texto de arriba
- **Footer:** ninguno
- **Botones:** ninguno _(opcional: añadir botón "Ver ubicación" en iteración futura)_

---

## Notas importantes

- Las plantillas con categoría `UTILITY` se aprueban en 24–48 h.
- **No modificar** el texto de la plantilla una vez aprobado. Cualquier cambio requiere un nuevo proceso de aprobación.
- Meta puede rechazar la plantilla si contiene precios o llamadas a la acción comerciales. Este texto está diseñado para ser aprobado como utilidad transaccional.
- Una vez aprobada, anotar el nombre exacto en `blueprint.json` → módulo 4 → campo `__FILL_IN__META_TEMPLATE_NAME`.

---

## Variante sin emojis (si Meta rechaza la versión con emojis)

```
Hola {{1}}, te confirmamos tu reserva en Gran Hotel Peniscola:

Llegada:    {{2}}
Salida:     {{3}}
Habitacion: {{4}}
N. reserva: {{5}}

Check-in: de 14:00 a 00:00 h. Av. Papa Luna, 5, Peniscola.

Si tienes cualquier duda, responde a este mensaje. Te esperamos.
```
