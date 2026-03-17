# SUBTASKS

## HU-01 - Consultar estado y disponibilidad de un libro
### Objetivos de la historia
Permitir al bibliotecario ver si un libro esta disponible o prestado y consultar su informacion operativa principal antes de registrar acciones sobre el.

### Subtareas DEV
- Crear un almacenamiento de información para guardar de historial de prestamos de libros si no existe ya.
- Crear una funcionalidad de busqueda dentro de la tabla para el libro consultado.
- Exponer un endpoint GET que reciba el identificador del libro a buscar y devuelva la disponibilidad de un libro en concreto.
- Crear una funcionalidad para comunicar al endpoint GET cual es el libro a buscar.

### Subtareas QA

### Riesgo o notas de calidad

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