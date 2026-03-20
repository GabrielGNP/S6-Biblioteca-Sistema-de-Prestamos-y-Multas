# Título: HU-01 - Consultar estado y disponibilidad de un libro

## Descripción
```md
**Como** Bibliotecario
**Quiero** Consultar el estado actual del libro
**Para** Saber si está disponible o prestado
```

## Valor de Negocio
- Reduce errores al revisar o prestar libros.
- Permitiría validar disponibilidad sin depender de pura memoria o un registro manual disperso.

## Reglas de Negocio relacionadas
- Regla 1: Un libro solo puede prestrarse si está disponible.
- Regla 10: Cada libro conserva un historial de préstamos realizados.

## Dependencias
- Requiere que existe información base del libro y sus préstamos

## Story Points
- 3 puntos
- Aunque es una historia de consulta, no se limita a darte un solo dato aislado: requiere búsqueda del libro, cruce con el historial de préstamos, determinación de su disponibilidad actual y presentación de información suficiente (campos mínimos) para la toma de decisiones.

## Criterio de Aceptación

- El bibliotecario puede consultar un libro usando un dato identificador.
- El sistema indica si el libro está disponible o si tiene un préstamo activo.
- El sistema muestra información suficiente para interpretar el estado del libro.
- Si el libro no existe, el sistema lo muestra sin ambiguedad.

**Gherkin**:
```gherkin
    Scenario: Consultar un libro disponible
    Given existe un libro registrado y no tiene un préstamo activo
    When el bibliotecario consulta ese libro
    Then el sistema muestra que el libro está disponible
```
```gherkin
    Scenario: Consultar un libro inexistente
    Given no existe un libro con el identificador consultado
    When el bibliotecario realiza la búsqueda
    Then el sistema informa que el libro no fue encontrado
```

## Justificación de criterios INVEST - HU-01

**I (Independent)**: Sí; no depende del flujo de devolución.
**N (Negotiable)**: Sí; se puede ajustar el nivel de detalle, por ejemplo.
**V (Valuable)**: Sí; con esto evitas prestar a ciegas.
**E (Estimable)**: Sí, el alcance de la consulta está acotado.
**S (Small)**: Sí; es una necesidad puntual.
**T (Testable)**: Sí; se puede evaluar disponibilidad, préstamo activo e información sociada.

---

# Título: HU-02 - Registrar libro disponible a un lector habilitado

## Descripción
```md
**Como** Bibliotecario
**Quiero** Registrar el préstamo de un libro disponible a un lector sin multas impagas
**Para** Controlar la salida del libro y definir correctamente su fecha de devolución
```

## Valor de Negocio
- Habilita la operación principal del MVP.
- Asegura que el sistema bloquee los préstamos inválidos por indisponibilidad.

## Reglas de Negocio relacionadas
- Regla 1: Un libro solo puede prestrarse si está disponible.
- Regla 2: Un lector solo puede recibir un nuevo préstamo si no tiene multas impagas.
- Regla 3: Cada préstamo se registra con una fecha de inicio y una fecha de devolución calculada.
- Regla 4: Solo permitimos tres plazos de préstamo: 7, 14 y 21 días.

## Dependencias
- Se apoya en la consulta de disponibilidad del libro y el estado de deuda del lector.

## Story Points
- 5 puntos
- Combina varias reglas centrales del negocio en un mismo flujo: validación de disponibilidad del libro, validación de deuda del lector, control de plazos, cálculo de fecha de devolución y cambió de estado del préstamo.
  
## Criterio de Aceptación

- El préstamo solo puede registrarse si el libro está disponible.
- El préstamo solo puede registrarse si el lector no tiene multas pendientes.
- El lector se considera habilitado únicamente cuando no tiene deuda pendiente activa.
- El sistema calcula una fecha de devolución según el plazo elegido.
- Si alguna regla falla, el sistema impide el registro y lo informa.

**Gherkin**:
```gherkin
    Scenario: Registrar un préstamo válido
    Given el libro está disponible
    And el lector no tiene multas impagas
    And el plazo elegido es permitido
    When el bibliotecario registra el préstamo
    Then el sistema guarda el préstamo
    And deja el libro como no disponible
    And muestra la fecha de devolución calculada
```
```gherkin
    Scenario: Registrar préstamo a lector rehabilitado tras pago
    Given el lector tuvo una deuda pendiente
    And la deuda fue pagada totalmente
    And el libro está disponible
    When el bibliotecario registra un préstamo
    Then el sistema permite la operación
```
```gherkin
    Scenario: Intentar prestar a un lector con deuda
    Given el lector tiene una deuda pendiente
    When el bibliotecario intenta registrar un préstamo
    Then el sistema rechaza la operación
```
```gherkin
    Scenario: Intentar registrar un préstamo con plazo no permitido
    Given el libro está disponible
    And el lector no tiene multas impagas
    And el plazo elegido no es de 7, 14 ni 21 días
    When el bibliotecario registra el préstamo
    Then el sistema rechaza la operación
```

## Justificación de criterios INVEST - HU-02

**I (Independent)**: Sí; tiene valor propio.
**N (Negotiable)**: Sí; da libertad de implementación.
**V (Valuable)**: Sí; entra dentro del operativo principal del sistema.
**E (Estimable)**: Sí; queda claro qué hacer y reglas permiten una estimación más clara.
**S (Small)**: Sí; es una única tarea y no se incluye ninguna otra renovación o acción.
**T (Testable)**: Sí; permite validar casos válidos e intentos bloqueados.

---

# Título: HU-03 - Registrar devolución de un libro dentro del plazo

## Descripción
```md
**Como** Bibliotecario
**Quiero** Registrar que el libro fue devuelto en o antes de la fecha límite
**Para** Cerrar el préstamo sin generar multa y volver a dejar el libro disponible
```

## Valor de Negocio
- Formaliza el cierre correcto de un préstamo.
- Evita multas erróneas y recupera disponibilidad del libro.

## Reglas de Negocio relacionadas
- Regla 5: Si un libro se devuelve en o antes de la fecha límite, no se genera multa.
- Regla 10: Cada libro conserva un historial de préstamos realizados.
- Regla 11: Cada lector se identifica con un documento oficial; cédula o DNI.

## Dependencias
- Requiere que exista un préstamo activo registrado.

## Story Points
- 3 puntos.
- El flujo es más simple que el préstamo y que la devolución tardía, pero igual exige validar que exista un préstamo activo, comprobar el cumplimiento de la fecha límite, cerrar correctamente el préstamo y volver a dejar el libro disponible sin generar multa.

## Criterio de Aceptación
- Una devolución en fecha o antes de la fecha no genera multa.
- El préstamo debe quedar cerrado al registrar la devolución válida.
- El préstamo cerrado debe permanecer visible en el historial del libro con su fecha de devolución.
- El libro vuelve a quedar disponible.
- Si no existe un préstamo activo, la operación no debe avanzar.

**Gherkin**:
```gherkin
    Scenario: Registrar una devolución en fecha
    Given existe un préstamo activo
    And la devolución ocurre en o antes de la fecha límite
    When el bibliotecario registra la devolución
    Then el sistema cierra el préstamo
    And no genera multa
    And deja el libro disponible
```
```gherkin
    Scenario Outline: Registrar devolución sin multa dentro del plazo permitido
    Given existe un préstamo activo
    And la devolución ocurre <momento> de la fecha límite
    When el bibliotecario registra la devolución
    Then el sistema cierra el préstamo
    And no genera multa
    And deja el libro disponible

  Examples:
    | momento                      |
    | antes de la fecha límite     |
    | el mismo día de la fecha límite |
```
```gherkin
    Scenario: Intentar devolver un préstamo no activo
    Given no existe un préstamo activo para el libro consultado
    When el bibliotecario intenta registrar la devolución
    Then el sistema informa que no hay una devolución válida para procesar
```

## Justificación de criterios INVEST - HU-03

**I (Independent)**: Sí; como caso de uso de devolución; requiere como precondición que exista un préstamo activo.
**N (Negotiable)**: Sí; varían los detalles del registro pero no la regla central.
**V (Valuable)**: Sí; impacta directamente a la operación diaria.
**E (Estimable)**: Sí; el comportamiento esperado es simple.
**S (Small)**: Sí; no metemos nada sobre las multas ni sobre el pago.
**T (Testable)**: Sí; incluye fecha exacta y devolución anticipada.

---

# Título: HU-04 - Registrar devolución tardía y generar multa Fibonacci

## Descripción

```md
**Como** Bibliotecario
**Quiero** Registrar la devolución tardía de un libro
**Para** Generar la multa acumulada correspondiente y dejar trazabilidad de la deuda del lector
```

## Valor de Negocio
- Permite aplicar la política de mora definida por la biblioteca.
- Hace visible la deuda y habilita el posterior bloqueo del lector.

## Reglas de Negocio relacionadas
- Regla 6: Si un libro se devuelve después de la fecha límite, se genera una multa acumulativa.
- Regla 8: El modelo de multa es **Fibonacci**; la deuda aumenta siguiendo esta escala por cada semana de retraso completa.
- Regla 11: Cada lector se identifica con un documento oficial; cédula o DNI.

## Dependencias
- Requiere un préstamo vencido y la fecha efectiva de devolución.

## Story Points
- 8 puntos.
- No solo registra la devolución; exige calcular el retraso por cortes semanales, aplicar la acumulación de deuda con lógica Fibonacci, registrar correctamente la multa, cerrar el préstamo y sincronizar el estado del lector con la nueva deuda. El riesgo de errores justifica una estimación superior.

## Criterio de Aceptación
- Una devolución fuera de plazo genera una multa.
- El cálculo de la multa debe coincidir con los ejemplos oficiales del PRD para retrasos de 1, 7, 8, 15 y 22 días.
- El sistema deja registrada la deuda del lector.
- El préstamo queda cerrado aunque exista deuda pendiente.
- El préstamo cerrado por devolución tardía debe permanecer en el historial del libro, junto con la deuda generada para trazabilidad.

**Gherkin**:
```gherkin
    Scenario: Registrar una devolución tardía con multa
    Given existe un préstamo activo vencido
    And la fecha de devolución supera la fecha límite
    When el bibliotecario registra la devolución
    Then el sistema cierra el préstamo
    And calcula la multa según la lógica Fibonacci
    And deja la deuda asociada al lector
```
```gherkin
    Scenario Outline: Calcular deuda acumulada según los cortes oficiales de mora
    Given existe un préstamo vencido con <dias_retraso> días de retraso
    When el bibliotecario registra la devolución
    Then el sistema calcula una deuda acumulada de <deuda_esperada> unidades Fibonacci

  Examples:
    | dias_retraso | deuda_esperada |
    | 1            | 1              |
    | 7            | 1              |
    | 8            | 2              |
    | 15           | 4              |
    | 22           | 7              |
```

## Justificación de criterios INVEST - HU-04

**I (Independent)**: Sí; aunque depende de un préstamo vencido, entrega un resultado propio.
**N (Negotiable)**: Sí; puede ajustarse la base monetaria dentro de la lógica Fibonacci.
**V (Valuable)**: Sí; es una regla central del negocio.
**E (Estimable)**: Sí; la lógica del cálculo está en el PRD.
**S (Small)**: Sí; no incluye el pago de la multa, por ejemplo.
**T (Testable)**: Si, tiene casos borde claros de 1, 7, 8, 15 y 22 dias.

---

# Título: HU-05 - Consultar libros fuera de plazo y lector responsable

## Descripción

```md
**Como** Bibliotecario
**Quiero** Consultar los libros fuera de plazo junto al lector responsable
**Para** Gestionar deudas atrasadas y hacer seguimiento de los préstamos vencidos
```

## Valor de Negocio
- Da visibilidad a préstamos vencidos sin tener que revisar todos los préstamos individualmente.
- Permite acción operativa sobre la mora ya generada.

## Reglas de Negocio relacionadas
- Regla 6: Si un libro se devuelve después de la fecha límite, se genera una multa acumulativa.
- Regla 8: El modelo de multa es Fibonacci; la deuda aumenta siguiendo esta escala por cada semana de retraso completa.
- Regla 10: Cada libro conserva un historial de préstamos realizados.
 
## Dependencias
- Requiere préstamos activos y lógica para identificar vencidos.

## Alcance específico
- La historia cubre únicamente la visualización de préstamos vencidos y del lector responsable.
- No incluye notificaciones, exportación de resultados, filtros avanzados, ordenamiento configurable ni paginación.

## Story Points
- 3 puntos.
- Sigue siendo una consulta, pero requiere filtrar correctamente solo los préstamos vencidos, excluir los vigentes y cerrados, mostrar al lector responsable sin mezclar estados.

## Criterio de Aceptación
- El sistema permite consultar los préstamos que ya están vencidos.
- Cada resultado muestra el libro, su estado, el lector responsable, la fecha límite del préstamo y la fecha de devolución.
- Los préstamos aún vigentes no deben aparecer en la lista.
- Si no hay atrasos, la consulta debe indicarlo claramente.

**Gherkin**:

```gherkin
    Scenario: Consultar préstamos vencidos
    Given existen préstamos fuera de plazo
    When el bibliotecario consulta préstamos vencidos
    Then el sistema lista solo los préstamos atrasados
    And muestra el lector responsable de cada uno
```
```gherkin
    Scenario: Consultar cuando no hay atrasos
    Given no existen préstamos vencidos
    When el bibliotecario consulta la lista de atrasos
    Then el sistema informa que no hay libros fuera de plazo
```

## Justificación de criterios INVEST - HU-05

**I (Independent)**: Sí; entrega valor propio de consulta, aunque requiere datos de préstamos activos y una regla para identificar vencidos.
**N (Negotiable)**: Sí; se puede ajustar el nivel de detalle mostrado, por ejemplo.
**V (Valuable)**: Sí; apoya la gestión de deuda atrasada.
**E (Estimable)**: Sí; el resultado esperado es a nivel de consulta.
**S (Small)**: Sí; se limita a préstamos vencidos, lo cual está ligado directamente al responsable.
**T (Testable)**: Si; se puede validar inclusion y exclusion por fechas.

---

# Título: HU-06 - Registrar el pago total de una multa y rehabilitación del lector

## Descripción

```md
**Como** Bibliotecario
**Quiero** Registrar el pago total de una multa pendiente
**Para** Rehabilitar al lector y permitirle solicitar nuevos préstamos
```

## Valor de Negocio
- Cierra el ciclo de deuda del lector.
- Reestablece su capacidad de volver a usar el servicio de préstamo.

## Reglas de Negocio relacionadas
- Regla 2: Un lector solo puede recibir un nuevo préstamo si no tiene multas impagas.
- Regla 9: El pago de la multa habilita nuevamente al lector para solicitar préstamos.
- Regla 11: Cada lector se identifica con un documento oficial; cédula o DNI.

## Dependencias
- Requiere que exista una multa pendiente para el lector.

## Story Points
- 3 puntos
- Requiere validar la existencia de deuda pendiente, registrar el pago total, dejar trazabilidad y rehabilitar correctamente al lector para futuros préstamos.

## Criterio de Aceptación
- El sistema permite registrar el pago total de una deuda pendiente.
- Después del pago, el lector vuelve a quedar habilitado.
- Después del pago total, el lector no debe seguir figurando como bloqueado por deuda.
- Si no existe deuda pendiente, el sistema no debe registrar el pago.
- El pago debe dejar registro mínimo del lector, la deuda cancelada y la fecha en que se realizó.

**Gherkin**:

```gherkin
    Scenario: Registrar pago total de multa
    Given el lector tiene una deuda pendiente
    When el bibliotecario registra un pago total
    Then el sistema deja la deuda en cero
    And cambia el estado del lector a habilitado
```
```gherkin
    Scenario: Intentar pagar una deuda inexistente
    Given el lector no tiene multas pendientes
    When el bibliotecario intenta registrar el pago
    Then el sistema rechaza la operación
```

## Justificación de criterios INVEST - HU-06

**I (Independent)**: Sí; resuelve un evento concreto del negocio.
**N (Negotiable)**: Sí; se puede variar el modo de registrar el pago.
**V (Valuable)**: Sí; sin esto, el flujo estaría incompleto.
**E (Estimable)**: Sí; el alcance queda claro.
**S (Small)**: Sí; se limita al pago total, que está directamente ligado a la rehabilitación del lector.
**T (Testable)**: Si; se valida cambio de estado del lector antes y después del pago.

---




