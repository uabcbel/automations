# Plantilla WhatsApp Business — Meta Approval

## Texto de la plantilla

```
¡Hola {{1}}! 👋 Te confirmamos tu reserva en Gran Hotel Peñíscola:

📅 Llegada: {{2}}
📅 Salida: {{3}}
🛏 Habitación: {{4}}
🔑 Nº reserva: {{5}}

Check-in: de 14:00 a 00:00 h
📍 Av. Papa Luna, 5, Peñíscola

Si tienes cualquier duda, responde a este mensaje. ¡Te esperamos!
```

## Mapa de variables

| Variable en plantilla | Campo en Make (módulo 4) | Ejemplo |
|---|---|---|
| `{{1}}` | `4.nombre_huesped` | María García |
| `{{2}}` | `4.fecha_checkin` | 15/07/2026 |
| `{{3}}` | `4.fecha_checkout` | 18/07/2026 |
| `{{4}}` | `4.tipo_habitacion` | Doble Superior Vista Mar |
| `{{5}}` | `4.id_reserva` | BK-3847291 |

---

## Checklist de envío a Meta

Completa estos datos al crear la plantilla en el portal de 360dialog o Twilio:

- **Nombre de la plantilla:** `bienvenida_reserva_ghp`
  _(solo minúsculas, guiones bajos, sin espacios — anótalo aquí y en el blueprint.json en `__FILL_IN__META_TEMPLATE_NAME`)_
- **Categoría:** `UTILITY` _(confirmación de transacción — mayor probabilidad de aprobación que MARKETING)_
- **Idioma:** `es` (Spanish)
- **Tipo de mensaje:** Text only
- **Header:** ninguno _(simplifica la aprobación)_
- **Body:** pegar el texto de arriba
- **Footer:** ninguno
- **Botones:** ninguno _(opcional: añadir botón "Ver ubicación" en iteración futura)_

---

## Notas importantes

- Las plantillas con categoría `UTILITY` se aprueban en 24–48 h. Las de `MARKETING` pueden tardar más o ser rechazadas.
- **No modificar** el texto de la plantilla una vez aprobado. Cualquier cambio requiere un nuevo proceso de aprobación.
- Meta puede rechazar la plantilla si contiene información de precios o llamadas a la acción comerciales. Este texto está diseñado para ser aprobado como utilidad transaccional.
- Una vez aprobada, anota el nombre exacto en `blueprint.json` → módulo 7 → campo `__FILL_IN__META_TEMPLATE_NAME`.

---

## Variante sin emojis (si Meta rechaza la versión con emojis)

```
Hola {{1}}, te confirmamos tu reserva en Gran Hotel Peñíscola:

Llegada: {{2}}
Salida: {{3}}
Habitacion: {{4}}
N. reserva: {{5}}

Check-in: de 14:00 a 00:00 h. Av. Papa Luna, 5, Peniscola.

Si tienes cualquier duda, responde a este mensaje. Te esperamos.
```
