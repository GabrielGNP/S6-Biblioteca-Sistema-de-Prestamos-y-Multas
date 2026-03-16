# Product Requirement Document (PRD) - Alexander Molina (QA) - Gabriel Perero (DEV)

## Visión y Objetivo

Construir un sistema simple y claro para gestionar préstamos de libros y multas por retraso de estos, permitiendo a la biblioteca controlar la disponibilidad, fechas de devolución, deudas pendientes y rehabilitación del lector tras el pago.

## Problemas que resolver
Actualmente la biblioteca necesita una forma consistente de registrar préstamos y devoluciones, evitar prestar libros a lectores morosos y calcular las multas generadas por retrasos.

## Alcance del MVP

| IN | OUT |
| :-- | :-- |
| Registrar el préstamo de un libro dispobible | Permitir prórrogas de préstamo |
| Elegir el plazo de devolución entre 7, 14 o 21 días | Gestionar altas y bajas de libros |
| Calcular automáticamente la fecha de devolución | Administrar reservas de libros |
| La multa se genera automáticamente apenas hay retraso | Administración de usuarios y membresías |
| Aumento de multa cada semana sin devolución del libro siguiendo la formula de Fibonacci | Pagos parciales de multas |
| Registrar el pago total de la multa | Notificaciones autómaticas |
| Consultar préstamos vencidos y el lector responsable para gestionar deudas atrasadas |  |
| Consultar lectores con multas pendientes luego de una devolución tardía |   |
| Bloquear préstamos a lectores con deudas impagas |  |
| Mantener historial de préstamos por libro |  |




## Riesgos de Negocio y Técnicos

### Riesgos de Negocio
- Si no se bloquean correctamente los préstamos a los lectores con deudas, la mora podría aumentar.

### Riesgo Técnico
- Una implementación inconsciente del estado de deuda puede impedir o habilitar préstamos de forma errónea.
- Si el historial de préstamos no se registra bien, se pierde trazabilidad del libro.
- Calcular mal la fecha de vencimiento puede generar multas incorrectas.