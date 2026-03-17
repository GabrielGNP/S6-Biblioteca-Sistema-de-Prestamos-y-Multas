# Product Requirement Document (PRD) - Alexander Molina (QA) - Gabriel Perero (DEV)

## Visión y Objetivo

Construir un sistema simple y claro para gestionar préstamos de libros y multas por retraso de estos, permitiendo a la biblioteca controlar la disponibilidad, fechas de devolución, deudas pendientes y rehabilitación del lector tras el pago.

### Problemas que resolver
Actualmente la biblioteca necesita una forma consistente de registrar préstamos y devoluciones, evitar prestar libros a lectores morosos y calcular las multas generadas por retrasos.


### Reglas del Negocio
1. Un libro solo puede prestrarse si está disponible.
2. Un lector solo puede recibir un nuevo préstamo si no tiene multas impagas.
3. Cada préstamo se registra con una fecha de inicio y una fecha de devolución calculada.
4. Solo permitimos tres plazos de préstamo: 7, 14 y 21 días.
5. Si un libro se devuelve en o antes de la fecha límite, no se genera multa.
6. Si un libro se devuelve después de la fecha límite, se genera una multa acumulativa.
7. El modelo de multa es **Fibonacci**; la deuda aumenta siguiendo esta escala por cada semana de retraso completa.
8. El pago de la multa habilita nuevamente al lector para solicitar préstamos.
9. Cada libro conserva un historial de préstamos realizados.
10. Cada lector se identifica con un documento oficial; cédula o DNI.

### Sistema Fibonacci
1. El retraso empieza a contarse desde el día siguiente a la fecha límite de devolución.
2. La mora se agrupa en semanas (cada 7 días); días 1 a 7 equivalen a una (1) semana de retraso; días 8 a 14 a Semana 2, días 15 a 21 a Semana 3, y así sucesivamente.
3. Cada semana vencida agrega una nueva porción de deuda según la secuencia Fibonacci: 1, 1, 2, 3, 5, 8...
4. La deuda es acumulativa; no reemplaza a la multa anterior, si no que se suma el valor correspondiente a cada nueva semana de mora.
5. La biblioteca puede definir un valor base monetario para convertir cada unidad Fibonacci en dinero; este documento fija la lógica del crecimiento, no el monto exacto a utilizar.


## Alcance del MVP

| IN | OUT |
| :-- | :-- |
| Registrar el préstamo de un libro disponible | Permitir prórrogas de préstamo |
| Elegir el plazo de devolución entre 7, 14 o 21 días | Gestionar altas y bajas de libros |
| Calcular automáticamente la fecha de devolución | Administrar reservas de libros |
| La multa se genera automáticamente apenas hay retraso | Administración de usuarios y membresías |
| Aumento de multa cada semana sin devolución del libro siguiendo la fórmula de Fibonacci | Pagos parciales de multas |
| Registrar el pago total de la multa | Notificaciones automáticas |
| Consultar préstamos vencidos y el lector responsable para gestionar deudas atrasadas |  |
| Consultar lectores con multas pendientes luego de una devolución tardía |   |
| Bloquear préstamos a lectores con deudas impagas |  |
| Mantener historial de préstamos por libro |  |


## Riesgos de Negocio y Técnicos

### Riesgos de Negocio
- Si no se bloquean correctamente los préstamos a los lectores con deudas, la mora podría aumentar.
- La complejidad de la escala de Fibonacci puede generar confusión en los usuarios; si el cálculo no es transparente o varía por errores de carga, el usuario lo percibirá como arbitrario o injusto.

### Riesgo Técnico
- Una implementación inconsciente del estado de deuda puede impedir o habilitar préstamos de forma errónea.
- Si el historial de préstamos no se registra bien, se pierde trazabilidad del libro.
- Calcular mal la fecha de vencimiento puede generar multas incorrectas.
- Interpretar mal los cortes semanales o la acumulación Fibonacci puede generar multas inconsistentes.