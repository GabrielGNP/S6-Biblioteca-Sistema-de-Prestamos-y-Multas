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
- Regla 9: Cada libro conserva un historial de préstamos realizados.

## Criterio de Aceptación

**Gherkin**:
```gherkin
    
```

## Justificación de criterios INVEST - HU-01

**I (Independent)**: Sí; no depende del flujo de devolución.
**N (Negotiable)**: Sí; se puede ajustar el nivel de detalle, por ejemplo.
**V (Valuable)**: Sí; con esto evitas prestar a ciegas.
**E (Estimable)**: Sí, el alcance de la consulta está acotado.
**S (Small)**: Sí; es una necesidad puntual.
**T (Testable)**: 

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
  
## Criterio de Aceptación

**Gherkin**:
```gherkin
    
```
## Justificación de criterios INVEST - HU-02

**I (Independent)**: Sí; tiene valor propio.
**N (Negotiable)**: Sí; da libertad de implementación.
**V (Valuable)**: Sí; entra dentro del operativo principal del sistema.
**E (Estimable)**: Sí; queda claro qué hacer y reglas permiten una estimación más clara.
**S (Small)**: Sí; es una única tarea y no se incluye ninguna otra renovación o acción.
**T (Testable)**: 

---

# Título: HU-03 - Registrar devolución de un libro dentro del plazo

## Descripción
```md
**Como** Bibliotecario
**Quiero** Registrar que el libro fue devuelto en o antes de la fecha límite
**Para** Cerrar el préstamo sin generar multa y volver a dejar el libro disponible

## Valor de Negocio
- Formaliza el cierre correcto de un préstamo.
- Evita multas erróneas y recupera disponibilidad del libro.

## Reglas de Negocio relacionadas
- Regla 5: Si un libro se devuelve en o antes de la fecha límite, no se genera multa.
- Regla 9: Cada libro conserva un historial de préstamos realizados.
- Regla 10: Cada lector se identifica con un documento oficial; cédula o DNI.
  
## Criterio de Aceptación

**Gherkin**:
```gherkin
    
```

## Justificación de criterios INVEST - HU-03

**I (Independent)**: Sí; es un cierre limpio de préstamo.
**N (Negotiable)**: Sí; varían los detalles del registro pero no la regla central.
**V (Valuable)**: Sí; impacta directamente a la operación diaria.
**E (Estimable)**: Sí; el comportamiento esperado es simple.
**S (Small)**: Sí; no metemos nada sobre las multas ni sobre el pago.
**T (Testable)**:


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
- Regla 7: El modelo de multa es **Fibonacci**; la deuda aumenta siguiendo esta escala por cada semana de retraso completa.
- Regla 10: Cada lector se identifica con un documento oficial; cédula o DNI.
  
## Criterio de Aceptación

**Gherkin**:
```gherkin
    
```

## Justificación de criterios INVEST - HU-04

**I (Independent)**: Sí; aunque depende de un préstamo vencido, entrega un resultado propio.
**N (Negotiable)**: No estamos seguros; imponemos Fibonacci pero se puede ajustar la base monetaria.
**V (Valuable)**: Sí; es una regla central del negocio.
**E (Estimable)**: No; no aclaramos la lógica del cálculo de Fibonacci aún.
**S (Small)**: Sí; no incluye el pago de la multa, por ejemplo.
**T (Testable)**: 

---

# Título: HU-05 - Consultar libros fuera de plazo y lector responsable

## Descripción

```md
**Como** Bibliotecario
**Quiero** Consultar los libros fuera de plazo junto al lector responsable
**Para** Gestionar deudas atrasadas y hacer seguimiento de los prestamos vencidos
```

## Valor de Negocio
- Da visibilidad a préstamos vencidos sin tener que revisar todos los préstamos individualmente.
- Permite acción operativa sobre la mora ya generada.

## Reglas de Negocio relacionadas
- Regla 6: Si un libro se devuelve después de la fecha límite, se genera una multa acumulativa.
- Regla 7: El modelo de multa es Fibonacci; la deuda aumenta siguiendo esta escala por cada semana de retraso completa.
- Regla 9: Cada libro conserva un historial de préstamos realizados.

## Criterio de Aceptación

**Gherkin**:

```gherkin

```

## Justificación de criterios INVEST - HU-05

**I (Independent)**: Sí; entrega valor de consulta y seguimiento por sí misma.
**N (Negotiable)**: Sí; se puede ajustar el nivel de detalle mostrado, por ejemplo.
**V (Valuable)**: Sí; apoya la gestión de deuda atrasada.
**E (Estimable)**: Sí; el resultado esperado es a nivel de consulta.
**S (Small)**: Sí; se limita a préstamos vencidos, lo cual está ligado directamente al responsable.
**T (Testable)**: 

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
- Regla 8: El pago de la multa habilita nuevamente al lector para solicitar préstamos.
- Regla 10: Cada lector se identifica con un documento oficial; cédula o DNI.

## Criterio de Aceptación

**Gherkin**:

```gherkin

```

## Justificación de criterios INVEST - HU-06

**I (Independent)**: Sí; resuelve un evento concreto del negocio.
**N (Negotiable)**: Sí; se puede variar el modo de registrar el pago.
**V (Valuable)**: Sí; sin esto, el flujo estaría incompleto.
**E (Estimable)**: 
**S (Small)**: Sí; se limita al pago total, que está directamente ligado a la rehabilitación del lector.
**T (Testable)**: 

---




