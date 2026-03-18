# SUBTASKS

## HU-01 - Consultar estado y disponibilidad de un libro

## HU-02 - Registrar libro disponible a un lector habilitado

## HU-03 - Registrar devolución de un libro dentro del plazo

## HU-04 - Registrar devolución tardía y generar multa Fibonacci

## HU-05 - Consultar libros fuera de plazo y lector responsable

## HU-06 - Registrar el pago total de una multa y rehabilitación del lector
### Objetivo de la historia
Registrar que la multa de un lector fue totalmente pagada y puede tomar prestado otro libro

### Subtareas DEV
- UI de multas
- Inputs UI para indicar el identificador del lector
- Endpoint GET api/v1/debt/{identificador} para obtener la multa
- Comunicación UI y endpoint GEt api/v1/debt/{identificador}
- Tabla DB con lectores morosos.
- Método de búsqueda de lector moroso.
- Endpoint PUT api/v1/debt{identificador} para cambiar el estado de la multa a pagado
- Comunicación UI y endpoint PUT api/v1/debt/{identificador}

### Subtareas QA


### Riesgo o notas de calidad


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