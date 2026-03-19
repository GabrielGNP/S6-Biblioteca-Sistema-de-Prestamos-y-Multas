# SUBTASKS

## HU-01 - Consultar estado y disponibilidad de un libro

### Objetivo de la historia
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
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.

### Riesgo o notas de calidad
- Riesgo funcional: Mostrar un libro como disponible cuando en realidad tiene un préstamo activo llevaría a préstamos inválidos.
- Riesgo técnico: Si la consulta no une bien libro e historial, la información visible puede quedar parcial o contradictoria.
- Calidad: Conviene verificar que la consulta no solo traiga el libro, si no también el contexto mínimo necesario para decidir si puede prestarse.

## HU-02 - Registrar libro disponible a un lector habilitado

### Objetivo de la historia
Permitir al bibliotecario registrar el préstamo de un libro disponible a un lector sin multas impagas, definiendo correctamente el plazo y la fecha de devolución.

### Subtareas DEV
- UI (inputs) para ingresar datos necesarios para un préstamo.
- Exponer endpoint POST api/v1/loan para registrar un préstamo.
- Integrar UI y endpoint POST api/v1/loan
- Tabla DB con lectores morosos.
- Funcionalidad de búsqueda de lector moroso. 
- Verificación de morosidad del lector.
- Tabla DB de historial de prestamos de libros.
- Guardado de préstamo en el historial

### Subtareas QA
- Diseñar escenarios para préstamos exitosos, libros ya prestados, lector con multa impaga y plazo no permitido.
- Preparar datos con al menos un libro disponible, uno prestado, un lector habilitado y un lector bloqueado por deuda.
- Validar que el sistema registre correctamente un préstamo cuando se cumplen todas las reglas del negocio.
- Validar alternos como libro inexistente, lector inexistente o error en el cálculo de la fecha de devolución.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.

### Riesgo o notas de calidad
- Riesgo funcional: permitir un préstamo sobre un libro no disponible o a un lector moroso rompería reglas centrales del negocio.
- Riesgo técnico: si el sistema calcula mal la fecha límite o no persiste el estado del préstamo, se afectarían devoluciones y multas posteriores.
- Calidad: conviene verificar los tres plazos permitidos y asegurar que cualquier otro valor quede explícitamente rechazado.

## HU-03 - Registrar devolución de un libro dentro del plazo

### Objetivo de la historia
- Permitir al Bibliotecario registrar la devolución de un libro en o antes de la fecha límite para cerrar el préstamo sin generar multa y dejar nuevamente el libro disponible.

### Subtareas DEV
- UI (inputs) para indicar nombre del libro y/o identificador, y el identificador del lector
- Endpoint PATCH api/v1/loan con la información actualizada del libro (fecha actual y confirmación de devolución)
- Comunicación UI con endpoint PATCH api/v1/loan
- Tabla DB de historial de prestamos de libros
- Funcionalidad para buscar el libro en el historial
- Funcionalidad para calcular el tiempo de demora y evaluar cumplimiento de tiempo
- Funcionalidad para marcar como devuelto el libro

### Subtareas QA
- Permitir cerrar un préstamo a tiempo para que el libro vuelva a estar utilizable sin generar deuda.
- Diseñar escenarios para devolución en la fecha límite, devolución antes de la fecha limite y devolución sobre préstamo no activo.
- Preparar datos con al menos un préstamo vigente, un libro asociado y casos de fecha exacta y fecha anticipada.
- Validar que el sistema cierre correctamente el préstamo, no genere multa y deje el libro disponible para futuros préstamos.
- Validar alternos como devolución duplicada, error en identificación del préstamo o cambio incorrecto del estado del libro.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.


**Riesgo o notas de calidad**
- Riesgo funcional: Si una devolución dentro del plazo genera multa o no libera el libro, se rompe el flujo operativo básico.
- Riesgo funcional: Si el identificador del lector se escribió mal previamente, no se puede recuperar la información del préstamo.
- Riesgo técnico: Si el cierre del préstamo y la actualización del libro no ocurren de forma consistente, el sistema puede dejar estados cruzados.
- Calidad: Conviene verificar el borde exacto de la fecha límite para evitar penalizar devoluciones válidas realizadas a tiempo.

## HU-04 - Registrar devolución tardía y generar multa Fibonacci

### Objetivo de la historia
- Permitir al bibliotecario registrar la devolución tardía de un libro para calcular la multa acumulada con la lógica Fibonacci y dejar trazabilidad de la deuda del lector.

### Subtareas DEV
- UI (inputs) para indicar nombre del libro y/o identificador, y el identificador del lector
- Endpoint PATCH api/v1/loan con la información actualizada del libro (fecha actual y confirmación de devolución)
- Comunicación UI con endpoint PATCH api/v1/loan
- Tabla DB de historial de prestamos de libros
- Funcionalidad para buscar el libro en el historial
- Funcionalidad para calcular el tiempo de demora y evaluar cumplimiento de tiempo
- Funcionalidad para calcular multa utilizando fibonacci
- Funcionalidad para marcar como devuelto el libro
- Tabla DB con lectores morosos.
- Método de guardado de multa. 
                                            
### Subtareas QA
- Diseñar escenarios para devolución tardia con retrasos de una, dos, tres y cuatro semanas de mora acumulada.
- Preparar datos con préstamos vencidos y fechas de devolución que permitan validar los cortes semanales y la acumulación Fibonacci.
- Validar que el sistema calcule correctamente la deuda, cierre el préstamo y bloquee al lector cuando corresponda por multa pendiente.
- Validar alternos como errores en el conteo de días, préstamo sin lector asociado o multa duplicada sobre la misma devolución.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.


### Riesgo o notas de calidad
- Riesgo funcional: una multa mal interpretada puede percibirse como injusta o incoherente.
- Riesgo técnico: si la devolución, la multa y el estado del lector no se actualizan en conjunto, pueden quedar deudas inconsistentes o lectores mal bloqueados.
- Calidad: conviene verificar explícitamente los casos borde definidos en el PRD para confirmar que la acumulación Fibonacci sea correcta.

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