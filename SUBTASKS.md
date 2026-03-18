# SUBTASKS

## HU-01 - Consultar estado y disponibilidad de un libro

## HU-02 - Registrar libro disponible a un lector habilitado

## HU-03 - Registrar devolución de un libro dentro del plazo
**Objetivo de la historia**
Registrar la devolución en tiempo y forma de un libro prestado sin generar ninguna multa al lector.

**Subtareas DEV**
- inputs UI para indicar nombre del libro y el identificador del lector
- comunicación UI con endpoint
- endpoint PUT api/v1/loan con la información actualizada del libro (fecha actual y confirmación de devolución)
- método para calcular el tiempo de demora
- método para marcar como devuelto el libro

**Subtareas QA**


**Riesgo o notas de calidad**
- Si el identificador del lector se escribió mal previamente, no se puede recuperar la información del prestamo.

## HU-04 - Registrar devolución tardía y generar multa Fibonacci

## HU-05 - Consultar libros fuera de plazo y lector responsable

## HU-06 - Registrar el pago total de una multa y rehabilitación del lector

## PLANTILLA
```md
## HU-[00] - [Título]

**Objetivo de la historia**
- Resumen corto de su funcionalidad

**Subtareas DEV**
- [Modelo o entidad]
- [Regla o servicio de dominio]
- [Persistencia o repositorio]
- [Endpoint o caso de uso]
- [Pruebas unitarias técnicas]

**Subtareas QA**
- [Diseñar matriz de Casos de Prueba]
- [Preparar Datos de prueba]
- [Validar camino feliz]
- [Validar alternos y bordes]
- [Registrar evidencia y fallos si aplicase]

**Riesgo o notas de calidad**
- [Riesgo funcional, técnico o de aceptación]
```