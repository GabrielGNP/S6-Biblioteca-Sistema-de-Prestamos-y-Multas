# SUBTASKS

## HU-01 - Consultar estado y disponibilidad de un libro

### Objetivos de la historia
Permitir al bibliotecario ver si un libro esta disponible o prestado y consultar su informacion operativa principal antes de registrar acciones sobre el.

### Subtareas DEV
- Tabla DB de historial de prestamos de libros.
- Método de busqueda de libro.
- Exponer un endpoint GET api/v1/book/{name} que reciba el identificador del libro a buscar y devuelva el contexto mínimo necesario del libro en concreto.
- UI para indicar el nombre del libro a buscar
- Integrar UI y endpoint.

### Subtareas QA
- Diseñar escenarios para consulta de libro disponible, libro prestado y libro inexistente.
- Preparar datos con al menos un libro disponible, uno prestado y uno con historial de préstamos más extenso.
- Validar que el sistema muestre correctamente el estado del libro, préstamo activo e historial básico.
- Validar alternos como libro inexistente, o libro con información histórica incompleta.

### Riesgo o notas de calidad
- Mostrar un libro como disponible cuando en realidad tiene un préstamo activo llevaría a préstamos inválidos.
- Conviene verificar que la consulta no solo traiga el libro, si no también el contexto mínimo necesario para decidir si puede prestarse.

## HU-02 - Registrar libro disponible a un lector habilitado

## HU-03 - Registrar devolución de un libro dentro del plazo

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