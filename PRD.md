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
7. La fecha límite se evalúa por fecha calendario de la biblioteca. Si la devolución ocurre el mismo día de la fecha límite, no genera multa. La mora inicia al día calendario siguiente.
8. El modelo de multa es **Fibonacci**; la deuda aumenta siguiendo esta escala por cada semana de retraso completa.
9. El pago de la multa habilita nuevamente al lector para solicitar préstamos.
10. Cada libro conserva un historial de préstamos realizados.
11. Cada lector se identifica con un documento oficial; cédula o DNI.

### Sistema Fibonacci
1. El retraso empieza a contarse desde el día siguiente a la fecha límite de devolución.
2. La mora se agrupa en semanas (cada 7 días); días 1 a 7 equivalen a una (1) semana de retraso; días 8 a 14 a Semana 2, días 15 a 21 a Semana 3, y así sucesivamente.
3. Cada semana vencida agrega una nueva porción de deuda según la secuencia Fibonacci: 1, 1, 2, 3, 5, 8...
4. La deuda es acumulativa; no reemplaza a la multa anterior, si no que se suma el valor correspondiente a cada nueva semana de mora.
5. La biblioteca puede definir un valor base monetario para convertir cada unidad Fibonacci en dinero; este documento fija la lógica del crecimiento, no el monto exacto a utilizar.

### Ejemplo de interpretación

| Retraso | Semanas de mora consideradas | Incremento aplicado | Deuda acumulada en unidades Fibonacci |
| :-- | :-- | :-- | :-- |
| 1 día | 1 | 1 | 1 |
| 7 días | 1 | 1 | 1 |
| 8 días | 2 | 1 + 1 | 2 |
| 15 días | 3 | 1 + 1 + 2 | 4 |
| 22 días | 4 | 1 + 1 + 2 + 3 | 7 |


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

### Consulta de préstamos vencidos

La consulta de préstamos vencidos cubre únicamente la visualización de préstamos fuera de plazo y del lector responsable.

#### Salida mínima esperada
- Identificador del libro
- Título del libro
- Estado del libro: disponible o prestado
- Identificador del lector
- Nombre del lector responsable
- Fecha límite del préstamo
- Fecha de devolución

#### Fuera de alcance de esta consulta
- Envío de notificaciones
- Pago de multas
- Exportación de resultados
- Filtros por lector o por rango de fechas
- Ordenamiento configurable
- Paginación


## Riesgos de Negocio

### Riesgo N-1
- Si no se bloquean correctamente los préstamos a los lectores con deudas, la mora podría aumentar.
    **Mitigaciones**

  - Definir en historias y criterios de aceptación que la validación de deuda es obligatoria antes de registrar el préstamo.
  - Incluir un escenario Gherkin explícito de rechazo a lector con multa impaga.
  - Verificar en Subtareas QA datos de prueba con lector habilitado y lector bloqueado.
### Riesgo N-2
- La complejidad de la escala de Fibonacci puede generar confusión en los usuarios; si el cálculo no es transparente o varía por errores de carga, el usuario lo percibirá como arbitrario o injusto.
    **Mitigaciones**

    - Mantener en el PRD una definición operativa inequívoca del cálculo por semanas.
    - Incluir tabla de ejemplos oficiales de retraso y deuda acumulada.
    - Alinear USER_STORIES y SUBTASKS con esos mismos ejemplos sin redefinir la regla en cada documento.



## Riesgos Técnicos
- Una implementación inconsciente del estado de deuda puede impedir o habilitar préstamos de forma errónea.
- Si el historial de préstamos no se registra bien, se pierde trazabilidad del libro.
- Calcular mal la fecha de vencimiento puede generar multas incorrectas.
- Interpretar mal los cortes semanales o la acumulación Fibonacci puede generar multas inconsistentes.