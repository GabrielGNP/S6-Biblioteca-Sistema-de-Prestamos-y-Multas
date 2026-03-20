# SUBTASKS

## HU-01 - Consultar estado y disponibilidad de un libro

### Objetivo de la historia
Permitir al bibliotecario ver si un libro esta disponible o prestado y consultar su informacion operativa principal antes de registrar acciones sobre el.

### Subtareas DEV
- UI (input) para indicar el nombre del libro a buscar
    >inputs necesarios: 
    >    - Nombre del libro (string)
    >
    >No puede ser una cadena vacía
    >
    >Debe haber:
    >- un input para el texto. Debe tener un placeholder de ejemplo "El señor de los anillos". Debe tener validación para impedir campo vacio
    >- un botón de "Buscar"


- Exponer un endpoint GET api/v1/loan/{name} que reciba el identificador del libro a buscar y devuelva el contexto mínimo necesario del libro en concreto. (ID, nombre, disponibilidad)
    > La ruta debe recibir name como parámetro \
    > El name será el nombre del libro
    >
    >Endpoint con 3 códigos de respuesta: \
    >200=>
    >{
    >    "results": [
    >    {"id":integer,"name":string,"status":"AVAILABLE"},
    >    {"id":integer,"name":string,"status":"ON_LOAN"}
    >    ],
    >}
    >
    >si no se encuentra el libro en la tabla de historial, quiere decir que nunca fue prestado por lo que está disponible
    >
    >400 =>
    >- si name está vacío o inválido
    >
    >500 =>
    >- Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante

- Integrar UI y endpoint.
    >Si devuelve: \
    >200 => 
    >- se renderiza en pantalla todas las opciones del libro disponible devueltas por el endpoint
    >400 => 
    >- no se renderiza indicando que el nombre es inválido o está vacío
    >500 => 
    >- Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante

- Tabla DB de historial de prestamos de libros.
    >Tabla de la DB llamada loan_books que contiene los siguientes atributos:
    >    - loan_id : integer (ID del préstamo) (único y autoincremental)
    >    - id_book : integer (Identificador del libro)
    >    - title : string (Título del libro)
    >    - state: string (Estado del libro: AVAILABLE o ON_LOAN)
    >    - type_id_reader: string (tipo de identificador de lector: DNI o CEDULA)
    >    - Id_reader : integer (Identificador del lector)
    >    - name_reader : string (Nombre del lector responsable)
    >    - date_limite : Date (Fecha límite del préstamo)
    >    - date_return : Date (Fecha de devolución)

- Funcionalidad de búsqueda de libro en la DB.
    >Se busca todas las coincidencias del nombre pasado mediante el endpoint  \
    >La búsqueda no es key sensitive
    >
    >Se devuelven todos los libros con el mismo nombre pero distinto ID y que tengan state en AVAILABLE.  \
    >Si se repite ID se devuelve el más actual según date_return
    >
    >La información recuperada en esta funcionalidad es:
    >   - ID : integer
    >   - title : string 
    >   - state: string


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
> inputs necesarios: 
>    - [input] id del libro (integer)
>    - [input] nombre del lector (string)
>    - [select] selector de DNI o Cédula
>    - [input] id del lector (integer) (opciones: DNI o Cédula)
>    - [select] selector de tiempo de préstamo (integer) (opciones: 7, 14, 21)
> 
> Ninguno puede ser una cadena vacía
> 
> Debe haber: \
> Cada input debe tener un placeholder de ejemplo \
> id = 000000 \
> nombre del lector = "Pedro Hugo Ramon Castillo de la Rosa" \
> Id del lector = 00000000 \
> 
> Cada input debe tener validación para impedir campos vacíos \
> Según el tiempo de préstamo seleccionado. Se calcula y se muestra la fecha límite de devolución
> 
> - un botón de "registrar"


- Funcionalidad para calcular fecha límite de devolución del libro (FRONT)
> Funcionalidad para dar feedback visual de la fecha resultante antes de la confirmación
> 
> se toma el valor indicado en el select de tiempo de préstamo (7, 14, 21) \
> se toma la fecha actual y se aumenta la cantidad de días en la cantidad de días de préstamo especificados. \
> Por ejemplo: si tomamos prestado el libro por 7 días \
> 01/01/2026 => 08/01/2026

- Exponer endpoint POST api/v1/loan para registrar un préstamo.
> Request Body recivido:
> { \
>  "id_book": integer \
>  "name_reader": string \
>  "type_id_reader": DNI | CEDULA \
>  "id_reader": integer \
>  "loan_days": 7 | 14 | 21 \
>}
>
> Respuestas posibles:
>
> 201 =>
> { \
>  "loan_id": integer \
>  "id_book": integer \
>  "title" : string \
>  "date_limit": date \
>  "status": "ON_LOAN" \
>  "id_reader": integer, \
>  "name_reader": string \
> }
> 
> 400=>
> - si el parámetro loan_days es distinto de 7, 14 o 21
> - si el campo type_id_reader es distinto de DNI | CEDULA
> - si el campo "id_book", "name_reader" o "id_reader" están vacíos
> 
> 409 => 
> - si el libro no está disponible
> - si el lector tiene multa pendiente
> 
> 500 =>
> - Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante
> 


- Integrar UI y endpoint POST api/v1/loan
> Si devuelve:
> 201 => 
> - se muestra confirmación de carga exitosa
> 400 => 
> - se muestra los campos con valores incorrectos
> 409 =>
> - se muestra un mensaje con el error resultante
> 500 => 
> - Se muestra el mensaje de error surjido en el servidor

- Tabla DB con lectores morosos.
> Tabla de la DB llamada dept_reader que contiene los siguientes atributos:
>    - id_dept : integer (id de la multa) (único y autoincremental)
>    - loan_id : integer (ID del préstamo) (clave foranea)
>    - id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - amount_dept : real (monto de la deuda)
>    - state_dept : string (estado de la deuda: PENDING o PAID)

- Funcionalidad de búsqueda de lector moroso.
> El servicio busca en la DB si el lector con la ID recibida en el body del endpoint tiene alguna multa
> 
> Para esto busca en la tabla:
> - la tupla que tenga la "id_reader" = id_reader_recivida
> - que la tupla sea la más actual
> 
> Y se devuelve el state_dept de la tupla recuperada.


- Verificación de morosidad del lector.
> si el state_dept es "PENDING" entonces se finaliza la carga del prestamo y el endpoint devuelve el código 409


- Funcionalidad para calcular fecha límite de devolución del libro (servicio)
> Funcionalidad para evaluación temporal en el servicio. \
> Se valida que la opción recibida en "loan_days" es alguna de las opciones permitidas (7, 14 o 21)
> 
> Si no es permitido se devuelve código 400.
> 
> Si es correcto entonces se genera la fecha para su posterior guardado
> 
> para obtener la fecha se toma la fecha actual y se aumenta la cantidad de días en la cantidad de días de préstamo especificados. \
> Por ejemplo: si tomamos prestado el libro por 7 días \
> 01/01/2026 => 08/01/2026

- Tabla DB de historial de prestamos de libros.
> Tabla de la DB llamada loan_books que contiene los siguientes atributos:
>    - loan_id : integer (ID del préstamo) (único y autoincremental)
>    - id_book : integer (Identificador del libro)
>    - title : string (Título del libro)
>    - state: string (Estado del libro: AVAILABLE o ON_LOAN)
>    - type_id_reader: string (tipo de identificador de lector: DNI o CEDULA)
>    - Id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - date_limite : Date (Fecha límite del préstamo)
>    - date_return : Date (Fecha de devolución)

- Funcionalidad de verificación de si el libro está prestado
> Se busca todas las coincidencia más actual del id del libro pasado mediante el endpoint
> 
> Para esto busca en la tabla:
> - la tupla que tenga el "id_book" = id_book_recivida
> - que la tupla sea la más actual según la fecha "date_return"
> 
> Y se devuelve el "state" de la tupla recuperada.
> 
> Si el state es ON_LOAN, entonces se devuelve código 409. En caso contrario se continúa con el proceso


- Guardado de préstamo en el historial
>


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

### Objetivo de la historia
- Permitir al bibliotecario consultar los préstamos vencidos junto con el lector responsable y la información mínima de seguimiento para gestionar deudas atrasadas.

### Subtareas DEV
- UI de lista de préstamos
- Endpoint GET api/v1/loan/outTime
- Comunicación UI con endpoint
- Tabla DB de historial de prestamos de libros
- Funcionalidad de filtrado de prestamos fuera de tiempo

### Subtareas QA
- Diseñar escenarios para préstamos vencidos, préstamos aún vigentes y lista vacía sin atrasos.
- Preparar datos con al menos un libro fuera de plazo, uno dentro del plazo y lectores distintos sin validar asignación correcta.
- Validar que el sistema liste sólo los préstamos vencidos y muestre el lector responsable correcto en cada caso.
- Validar alternos como fechas límite mal calculadas, préstamos ya cerrados o datos incompletos del lector.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.

### Riesgo o notas de calidad
- Riesgo funcional: incluir préstamos vigentes o excluir préstamos vencidos distorsionaría la gestión de mora y seguimiento.
- Riesgo técnico: si la consulta no cruza bien préstamo, libro y lector, el responsable visible puede quedar errado o incompleto. 
- Calidad: conviene verificar el criterio exacto de vencimiento sobre la fecha límite para evitar falsos positivos en el borde del plazo.


## HU-06 - Registrar el pago total de una multa y rehabilitación del lector
### Objetivo de la historia
Registrar que la multa de un lector fue totalmente pagada y puede tomar prestado otro libro

### Subtareas DEV
- UI de multas
- UI (Inputs) para indicar el identificador del lector
- Endpoint GET api/v1/debt/{identificador} para obtener la multa
- Comunicación UI y endpoint GEt api/v1/debt/{identificador}
- Tabla DB con lectores morosos.
- Método de búsqueda de lector moroso.
- UI (elemento de confirmación) para confirmar el pago de la multa
- Endpoint PATCH api/v1/debt{identificador} para cambiar el estado de la multa a pagado
- Comunicación UI y endpoint PATCH api/v1/debt/{identificador}

### Subtareas QA
- Diseñar escenarios para pago total exitoso, lector sin deuda pendiente e intento de registrar pagos duplicados.
- Preparar datos con al menos un lector bloqueado por multa pendiente y otro lector ya habilitado sin deuda.
- Validar que el pago elimine la deuda pendiente y rehabilite correctamente al lector para futuros préstamos.
- Validar alternos como multa ya pagada, identificación incorrecta del lector o inconsistencia entre deuda y estado de habilitación.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.

### Riesgo o notas de calidad
- Riesgo funcional: si el sistema registra el pago pero no rehabilita al lector, se rompe la regla de negocio y el flujo queda incompleto.
- Riesgo técnico: si el pago no deja trazabilidad o no sincroniza deuda y estado del lector, pueden aparecer bloqueos o habilitaciones indebidas.
- Calidad: conviene verificar explícitamente que solo se permita pago total en el MVP y que no queden saldos residuales por error.


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