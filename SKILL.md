---
name: DataBye Grok ES
description: >-
  Use when running Spain data-deletion on Grok: wizard, Gmail/Outlook OAuth
  batch send, reply watching, and guided Lista Robinson. No Hermes.
---
# DataBye Grok (solo España)

Usa esta skill cuando alguien en Grok Bot quiera borrar o limitar datos personales frente a infomediarios, gestores de cobro o ficheros de solvencia en España, o inscribirse en Lista Robinson.

## Producto

Bot **solo Grok**. Sin Hermes. Sin SMTP. Sin JSON obligatorio.

Cadena completa:

1. Asistente (datos, categorías, cartas, Robinson)
2. **Conectar Gmail u Outlook (OAuth)**
3. **Enviar lote** a emails oficiales del roster
4. **Vigilar respuestas**
5. Si pidió guía Robinson: **formulario web asistido**

Material: `./` (roster, plantillas, wizard, playbooks).

## Hard gates

1. Consentimiento explícito antes de buscar o enviar.
2. Solo España (RGPD / LOPDGDD / AEPD).
3. Destinatarios solo emails oficiales documentados (o portal web_form).
4. Recobro y solvencia opt-in. Konecta solo si el usuario la ha tenido encima.
5. **Nunca adjuntar DNI/NIE** a los emails salvo petición expresa posterior del interesado.
6. Rate-limit entre envíos (pausa corta). Un mail por destinatario.
7. No inventar emails. Aktua histórica → Intrum. Recover cobros → Gemini Recoveries. Arrow ES sin DPD → no enviar.

## Flujo A — Envío OAuth (end-to-end)

Tras el wizard o intake en chat:

1. Preguntar **Gmail** o **Outlook** si no hay conector listo. InstallPlugin + tarjeta OAuth. Confirmar cuenta.
2. Listar destinos email del caso (roster + opt-ins). Separar `web_form`.
3. Por cada destino email:
   - Usar asunto/cuerpo editados (tono abogado, primera persona).
   - Enviar con la herramienta del conector (`send_message` o equivalente).
   - Registrar: broker, to, send_id/hora, ok/error.
   - Esperar unos segundos entre envíos.
4. Para `web_form` (p. ej. Experian): abrir URL y guiar; no enviar email vacío.
5. Entregar **informe de lote**: enviados, fallidos, portales pendientes, Robinson.
6. Ofrecer activar vigilancia de respuestas.

Playbook detallado: `playbooks/send-oauth.md`.

## Flujo B — Vigilancia de respuestas

Si el usuario acepta (o hay routine):

1. Buscar en Gmail/Outlook hilos de los destinatarios del roster / asuntos RGPD.
2. Clasificar: acuse, pide ID, acceso parcial, negativa, silencio.
3. Resumir en chat (sin pegar datos sensibles de más).
4. Ofrecer borrador de réplica o checklist AEPD si procede.
5. En routine programada: **solo avisar si hay algo nuevo**; si no hay cambios, silencio.

Playbook: `playbooks/watch-replies.md`.

## Flujo C — Lista Robinson (guiada)

No es email de borrado. Opciones del wizard: `skip` | `guide` | `done`.

Si `guide`:

1. Abrir https://www.listarobinson.es/register-consumer
2. Explicar campos (incl. DNI que pide **ellos**, no nosotros en las cartas)
3. Rellenar lo posible; **parar en captcha** y verificación de email
4. Pedir al usuario que complete captcha, pulse el enlace del correo y elija canales
5. Marcar estado `robinson=done` o `pending_verify`

Playbook: `playbooks/robinson-guide.md`.

## Categorías y artículos

| Categoría | Default | Citas |
|---|---|---|
| infomediarios | ON | 15, 21.1/21.2, 17, 12 |
| recobro | OFF | + 28, acreedor, 17.3 |
| solvency | OFF | + art. 20 LOPDGDD |
| robinson | skip | web_form Adigital |

## Tono

Cartas: registro de abogado, firmadas por el interesado. UI: español claro, neutro de género (Inicio, no Bienvenida).

## No hacer

- Hermes, JSON como entrega, CCPA/US
- Adjuntar DNI automático
- Prometer que la deuda desaparece
- Enviar sin OAuth / sin consentimiento
