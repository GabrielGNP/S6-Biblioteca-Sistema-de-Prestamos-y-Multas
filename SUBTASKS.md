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
    >    {"id":integer,"name":string,"status":"RETURNED"},
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
    >    - state: string (Estado del libro: RETURNED o ON_LOAN)
    >    - type_id_reader: string (tipo de identificador de lector: DNI o CEDULA)
    >    - Id_reader : integer (Identificador del lector)
    >    - name_reader : string (Nombre del lector responsable)
    >    - date_limite : Date (Fecha límite del préstamo)
    >    - date_return : Date (Fecha de devolución)

- Funcionalidad de búsqueda de libro en la DB.
    >Se busca todas las coincidencias del nombre pasado mediante el endpoint  \
    >La búsqueda no es key sensitive
    >
    >Se devuelven todos los libros con el mismo nombre pero distinto ID y que tengan state en RETURNED.  \
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
- Validar que la consulta del libro distinga préstamos activos y préstamos cerrados dentro del historial.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.

### Riesgo o notas de calidad
- Riesgo funcional: Mostrar un libro como disponible cuando en realidad tiene un préstamo activo llevaría a préstamos inválidos.
- Riesgo técnico: Si la consulta no une bien libro e historial, la información visible puede quedar parcial o contradictoria.
- Calidad: Conviene verificar que la consulta no solo traiga el libro, si no también el contexto mínimo necesario para decidir si puede prestarse.

## HU-02 - Registrar libro disponible a un lector habilitado

### Objetivo de la historia
Permitir al bibliotecario registrar el préstamo de un libro disponible a un lector sin deuda pendiente por multa, definiendo correctamente el plazo y la fecha de devolución.

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
>Tabla de la DB llamada dept_reader que contiene los siguientes atributos:
>    - id_dept : integer (id de la multa) (único y autoincremental)
>    - loan_id : integer (ID del préstamo) (clave foranea)
>    - type_id_reader : string, (CEDULA o DNI)
>    - Id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - amount_dept : real (monto de la deuda)
>    - state_dept : string (estado de la deuda: PENDING o PAID)


- Funcionalidad de búsqueda de lector moroso.
> El servicio busca en la DB si el lector con la ID recibida en el body del endpoint tiene alguna multa
> 
> Para esto busca en la tabla:
> - la tupla que tenga la "id_reader" = id_reader_recibida
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
>    - state: string (Estado del libro: RETURNED o ON_LOAN)
>    - type_id_reader: string (tipo de identificador de lector: DNI o CEDULA)
>    - Id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - date_limite : Date (Fecha límite del préstamo)
>    - date_return : Date (Fecha de devolución)

- Funcionalidad de verificación de si el libro está prestado
> Se busca todas las coincidencia más actual del id del libro pasado mediante el endpoint
> 
> Para esto busca en la tabla:
> - la tupla que tenga el "id_book" = id_book_recibida
> - que la tupla sea la más actual según la fecha "date_return"
> 
> Y se devuelve el "state" de la tupla recuperada.
> 
> Si el state es ON_LOAN, entonces se devuelve código 409. En caso contrario se continúa con el proceso


- Guardado de préstamo en el historial
> Si todas las comprobaciones pasan, entonces se guarda en la tabla "loan_books" los datos:
>    - loan_id : integer (generado automáticamente por la DB)
>    - id_book : integer (obtenido del body del endpoint)
>    - title : string (obtenido del body del endpoint)
>    - state: string (puesto en ON_LOAN)
>    - type_id_reader: string (obtenido del body del endpoint)
>    - Id_reader : integer (obtenido del body del endpoint)
>    - name_reader : string (obtenido del body del endpoint)
>    - date_limite : Date (generado en la funcionlidad)
>    - date_return : Date (NULL porque aún no se devolvió)


### Subtareas QA
- Diseñar escenarios para préstamos exitosos, libros ya prestados, lector con deuda pendiente y plazo no permitido.
- Preparar datos con al menos un libro disponible, uno prestado, un lector habilitado y un lector bloqueado por deuda.
- Validar que el sistema registre correctamente un préstamo cuando se cumplen todas las reglas del negocio.
- Validar alternos como libro inexistente, lector inexistente o error en el cálculo de la fecha de devolución.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.

### Riesgo o notas de calidad
- Riesgo funcional: permitir un préstamo sobre un libro no disponible o a un lector con deuda pendiente rompería reglas centrales del negocio.
- Riesgo técnico: si el sistema calcula mal la fecha límite o no persiste el estado del préstamo, se afectarían devoluciones y multas posteriores.
- Calidad: conviene verificar los tres plazos permitidos y asegurar que cualquier otro valor quede explícitamente rechazado.

## HU-03 - Registrar devolución de un libro dentro del plazo

### Objetivo de la historia
- Permitir al Bibliotecario registrar la devolución de un libro en o antes de la fecha límite para cerrar el préstamo sin generar multa y dejar nuevamente el libro disponible.

### Subtareas DEV
- UI (inputs) para indicar identificador del libro o el identificador del lector, y nombre del libro
> inputs necesarios: 
>    - [input] id del libro (integer)
>    - [input] nombre del libro (string)
>    - [select] selector de DNI o Cédula (opciones: DNI o Cédula)
>    - [input] id del lector (integer) 
>
> uno de los siguientes inputs puede estar vacío
>    - [input] id del libro (integer)
>    - [input] id del lector (integer) 
> Ambos pueden estar con datos, pero no pueden estar vacíos o nulos los dos campos
> 
> nombre del libro puede ser nulo solo si se define el la id del libro.
> 
> Cada input debe tener un placeholder de ejemplo: \
> id del libro = 000000 \
> nombre del libro = "el señor de los anillos" \
> Id del lector = 00000000 \
> 
> Se debe controlar bien la combinación de campos vacíos.
> 
> - un botón de "confirmar"


- Endpoint PATCH api/v1/loan con la información actualizada del libro
> Body del endpoint: \
> { \
>  "date_return": Date \
>  "name_reader" : string (puede ser null) \
>  "id_book" : integer (puede ser null) \
>  "type_id_reader" : string (Opciones: DNI o CEDULA) \
>  "Id_reader" : integer (puede ser null) \
> }
> 
> Respuestas posibles:
> 
> 200=> \
> { \
>  "loan_id": integer \
>  "id_book": integer \
>  "title" : string \
>  "date_limit": date \
>  "status": "RETURNED" \
>  "id_reader": integer \
>  "name_reader": string \
> }
> 
> 404 =>
> - si no se encuentra el préstamo (porque no existe o porque se encontraon varias opciones)
> 
> 409 =>
> - si el estado del préstamo ya está en "RETURNED"
> 
> 400 => 
> - Si el alguno de los parámetros es inválido o faltan datos
> 
> 500 =>
> - Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante


- Comunicación UI con endpoint PATCH api/v1/loan
> Si devuelve: \
> 200 => 
> - se muestra confirmación de devolución exitosa
> 400 => 
> - se muestra el error indicando que hay parámetros inválidos
> 409 =>
> - se muestra un mensaje indicando que el préstamo ya está marcado como devuelto
> 500 => 
> - Se muestra el mensaje de error surgido en el servidor

- Tabla DB de historial de prestamos de libros
> Tabla de la DB llamada loan_books que contiene los siguientes atributos: \
>    - loan_id : integer (ID del préstamo) (único y autoincremental) \
>    - id_book : integer (Identificador del libro) \
>    - title : string (Título del libro) \
>    - state: string (Estado del libro: RETURNED o ON_LOAN) \
>    - type_id_reader: string (tipo de identificador de lector: DNI o CEDULA) \
>    - Id_reader : integer (Identificador del lector) \
>    - name_reader : string (Nombre del lector responsable) \
>    - date_limite : Date (Fecha límite del préstamo) \
>    - date_return : Date (Fecha de devolución) \

- Funcionalidad para buscar el libro en el historial
> Se busca la coincidencia más actual del id del libro pasado mediante el endpoint
>
> Para esto busca en la tabla:
> - la tupla que tenga el "id_book" = id_book_recibida
> - que la tupla sea la más actual según la fecha "date_return"
> 
> Y se devuelve el "loan_id", el "date_limite", "name_reader", "id_reader" de la tupla recuperada.
> 
> Si el state es RETURNED, entonces se devuelve código 409. En caso contrario se continúa con el proceso


- Funcionalidad para calcular el tiempo de demora y evaluar cumplimiento de tiempo
> Funcionalidad para evaluar que el tiempo de préstamo esté dentro del tiempo estipulado
>
> se necesita que:
> - el servicio obtiene la fecha actual
> - se obtuviera el "date_limite" de la consulta a la DB
>
> Se calcula la diferencia de tiempo entre "date_limite" y "fecha actual". \
> Si el resultado es 0 o un número negativo de días, entonces significa que está dentro del tiempo estipulado. \
> Si es superior a 0 días entonces está fuera del tiempo


- Funcionalidad para marcar como devuelto el libro
> - Si se cuenta con la "loan_id"
> 
> entonces se actualiza en la DB el state: string (se pone en RETURNED)
> 
> para encontrar la tupla en la DB se utiliza la "loan_id".
> 
> Una vez hecha la modificación en la DB se devuelve la respuesta 200



### Subtareas QA
- Diseñar validaciones para confirmar el cierre de un préstamo a tiempo sin generación de deuda.
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
- UI (inputs) para indicar identificador del libro o el identificador del lector, y nombre del libro
>inputs necesarios: 
>    - [input] id del libro (integer)
>    - [input] nombre del libro (string)
>    - [select] selector de DNI o Cédula (opciones: DNI o Cédula)
>    - [input] id del lector (integer) 
>
>uno de los siguientes inputs puede estar vacío
>    - [input] id del libro (integer)
>    - [input] id del lector (integer) 
> Ambos pueden estar con datos, pero no pueden estar vacíos o nulos los dos campos
> 
> nombre del libro puede ser nulo solo si se define el la id del libro.
> 
> Cada input debe tener un placeholder de ejemplo : \
> id del libro = 000000 \
> nombre del libro = "el señor de los anillos" \
> Id del lector = 00000000
> 
> Se debe controlar bien la combinación de campos vacíos.
> 
> - un botón de "confirmar"

- Endpoint PATCH api/v1/loan con la información actualizada del libro
>Body del endpoint: \
>{ \
>  "date_return": Date \
>  "name_reader" : string (puede ser null) \
>  "id_book" : integer (puede ser null) \
>  "type_id_reader" : string (Opciones: DNI o CEDULA) \
>  "Id_reader" : integer (puede ser null) \
>}
>
>Respuestas posibles:
>
>200=> \
>{ \
>  "loan_id": integer \
>  "id_book": integer \
>  "title" : string \
>  "date_limit": date \
>  "status": "RETURNED" \
>  "id_reader": integer \
>  "name_reader": string \
>}
>
>404 =>
>- si no se encuentra el préstamo (porque no existe o porque se encontraon varias opciones)
>
> 409 =>
> - si el estado del préstamo ya está en "RETURNED"
> 
> 400 => 
> - Si el alguno de los parámetros es inválido o faltan datos
> 
> 500 =>
> - Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante

- Comunicación UI con endpoint PATCH api/v1/loan
>Si devuelve:
>200 => 
>- se muestra confirmación de devolución exitosa
>400 => 
>- se muestra el error indicando que hay parámetros inválidos
>409 =>
>- se muestra un mensaje indicando que el préstamo ya está marcado como devuelto
>500 => 
>- Se muestra el mensaje de error surgido en el servidor

- Tabla DB de historial de prestamos de libros
>Tabla de la DB llamada loan_books que contiene los siguientes atributos:
>    - loan_id : integer (ID del préstamo) (único y autoincremental)
>    - id_book : integer (Identificador del libro)
>    - title : string (Título del libro)
>    - state: string (Estado del libro: RETURNED o ON_LOAN)
>    - type_id_reader: string (tipo de identificador de lector: DNI o CEDULA)
>    - Id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - date_limite : Date (Fecha límite del préstamo)
>    - date_return : Date (Fecha de devolución)

- Funcionalidad para buscar el libro en el historial
>Se busca la coincidencia más actual del id del libro pasado mediante el endpoint
>
>Para esto busca en la tabla:
>- la tupla que tenga el "id_book" = id_book_recibida
>- que la tupla sea la más actual según la fecha "date_return"
>
>Y se devuelve el "loan_id", el "date_limite", "name_reader", "id_reader" de la tupla recuperada.
>
>Si el state es RETURNED, entonces se devuelve código 409. En caso contrario se continúa con el proceso


- Funcionalidad para calcular el tiempo de demora y evaluar cumplimiento de tiempo
>Funcionalidad para evaluar que el tiempo de préstamo esté dentro del tiempo estipulado
>
>se necesita que:
>- el servicio obtiene la fecha actual
>- se obtuviera el "date_limite" de la consulta a la DB
>
>Se calcula la diferencia de tiempo entre "date_limite" y "fecha actual". \
>Si el resultado es 0 o un número negativo de días, entonces significa que está dentro del tiempo estipulado. \
>Si es superior a 0 días entonces está fuera del tiempo


- Funcionalidad para calcular multa utilizando fibonacci
>Si la diferencia obtenida de de los días es superior a 0, significa que se extendió del tiempo.
>
>Para calcular la multa se utiliza la fórmula Fibonacci: \
>1, 1, 2, 3, 5, 8, 13, 21, 34, ...
>
>Lo primero será calcular la cantidad de semanas de demora, para esto se dividirá la cantidad de días de demora por 7. \
>El resultado de esto se redondeará hacia arriba para obtener la semana de demora teniendo como resultado:
>- 1-7 días → 1 semana
>- 8-14 días → 2 semanas
>- 15-21 días → 3 semanas
>- 22-28 días → 4 semanas
>- ...
>
>la cantidad de semanas será la cantidad de iteraciones de fibonacci. Se debe tener en cuenta que la secuencia se relacionará de la siguiente manera: \
>1 -> semana 1 \
>2 -> semana 2 \
>3 -> semana 3 \
>5 -> semana 4 \
>8 -> semana 5 \
>13 -> semana 6 \
>... -> ...
>
>
>Esto se multiplicará por el valor de multa especificado por la biblioteca. 
>
>Por ejemplo, si la multa es de 2 U$D (dólares) como valor base, entonces:
>- 1-7 días → 1 semana -> 1 * 2 = 2 U$D (dólares)
>- 8-14 días → 2 semanas -> 2 * 2 = 4 U$D (dólares)
>- 15-21 días → 3 semanas -> 3 * 2 = 6 U$D (dólares)
>- 22-28 días → 4 semanas -> 5 * 2 = 10 U$D (dólares)
>- 29-35 días → 5 semanas -> 8 * 2 = 16 U$D (dólares)
>- ...
>
>Este resultado final será guardado en la carga de la multa

- Funcionalidad para marcar como devuelto el libro
>- Si se cuenta con la "loan_id"
>
>entonces se actualiza en la DB el state: string (se pone en RETURNED)
>
>para encontrar la tupla en la DB se utiliza la "loan_id".
>
>Una vez hecha la modificación en la DB se devuelve la respuesta 200


- Tabla DB con lectores morosos.
>Tabla de la DB llamada dept_reader que contiene los siguientes atributos:
>    - id_dept : integer (id de la multa) (único y autoincremental)
>    - loan_id : integer (ID del préstamo) (clave foranea)
>    - type_id_reader : string, (CEDULA o DNI)
>    - Id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - amount_dept : real (monto de la deuda)
>    - state_dept : string (estado de la deuda: PENDING o PAID)



- Método de guardado de multa. 
>- si el tiempo de demora es > 0, entonces se debe generar una multa.
>- se requiere "loan_id", "id_reader", "name_reader" obtenidos de la lectura del préstamo en la DB  
>- se utiliza el monto de la multa en la funcionalidad para almacenarlo en "amount_dept"
>- se establece el "state_dept" como "PENDING"
>- la DB debe generar la "id_dept" automática
>
>Si se cuenta con toda esta información, entonces se guarda en la DB la siguiente información:
>   - id_dept : integer (id de la multa) (único y autoincremental)
>    - loan_id : integer (ID del préstamo) (clave foranea)
>    - id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - amount_dept : real (monto de la deuda)
>    - state_dept : string (estado de la deuda: PENDING o PAID)
                                            
### Subtareas QA
- Diseñar matriz de validación para retrasos de 1, 7, 8, 15 y 22 días.
- Preparar datos con préstamos vencidos y fechas de devolución que permitan validar los cortes semanales y la acumulación Fibonacci.
- Validar que el sistema calcule correctamente la deuda, cierre el préstamo y bloquee al lector cuando corresponda por deuda pendiente.
- Validar deuda acumulada esperada de 1, 1, 2, 4 y 7 unidades Fibonacci para cada caso definido en la matriz.
- Validar alternos como errores en el conteo de días, préstamo sin lector asociado o multa duplicada sobre la misma devolución.
- Registrar evidencia del resultado esperado y obtenido para cada escenario, y documentar defectos si aparecen inconsistencias.


### Riesgo o notas de calidad
- Riesgo funcional: una multa mal interpretada puede percibirse como injusta o incoherente.
- Riesgo técnico: si la devolución, la multa y el estado del lector no se actualizan en conjunto, pueden quedar deudas inconsistentes o lectores mal bloqueados.
- Calidad: la validación de la multa debe contrastarse explícitamente contra los ejemplos oficiales definidos en el PRD para los retrasos de 1, 7, 8, 15 y 22 días; cualquier diferencia entre el resultado obtenido y la deuda esperada debe registrarse como defecto.

## HU-05 - Consultar libros fuera de plazo y lector responsable

### Objetivo de la historia
- Permitir al bibliotecario consultar los préstamos vencidos junto con el lector responsable y la información mínima de seguimiento para gestionar deudas atrasadas.

### Subtareas DEV
- UI de lista de préstamos
>Elementos importantes:
>- Tabla con columnas:
>    - id de préstamo (loan_id) (no visible en la tabla)
>    - id del libro (id_book)
>    - nombre de libro (title)
>    - tipo de identificador del lector (type_id_reader)
>    - identificador del lector (id_reader)
>    - nombre del lector (name_reader)
>    - fecha límite del préstamo (date_limite)
>    - multa acumulada
>
>- Esta tabla debe cargar los datos al mostrarse la pantalla
>- Solo se mostrarán los pedidos cuya fecha límite se haya excedido.
>- El orden de los datos será en orden alfabético del título del libro
>- No hay filtros ni métodos de ordenamientos configurables
>
> - Si la lista está vacía se debe mostrar el mensaje: "No hay libros con retrasos"

- Endpoint GET api/v1/loan/outTime
>el endpoint no recibe parámetros, solo tiene una tarea no configurable. Traer todos los préstamos con fechas límites excedidas en orden alfabético según el título del libro
>
>Respuestas posibles:
>
>200=>
>{
>  "loans": [
>    {
>      "loan_id": integer,
>      "id_book": integer,
>      "title": string
>      "type_id_reader" : string (CEDULA o DNI) 
>      "id_reader": integer,
>      "name_reader": string,
>      "date_limite": date,
>      "exceeded_days": integer,
>      "dept_amount": 2.5
>    },
>    {...}
>  ],
>
>}
>
>500 =>
>- Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante
>
>Los valores devueltos son: 
>"loan_id" => id del préstamo
>"id_book" => id del libro
>"title" => título del libro
>"type_id_reader" => tipo de identificador del lector
>"id_reader" => identificador del lector
>"name_reader" => nombre del lector
>"date_limite" => fecha límite de devolución
>"exceeded_days" => días excedidos de préstamo
>"dept_amount" => multa actual


- Comunicación UI con endpoint
>Si devuelve:
>200 => 
>- se renderiza la información en la tabla
>
>500 =>
>- Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante


- Tabla DB de historial de prestamos de libros
>Tabla de la DB llamada loan_books que contiene los siguientes atributos:
>    - loan_id : integer (ID del préstamo) (único y autoincremental)
>    - id_book : integer (Identificador del libro)
>    - title : string (Título del libro)
>    - state: string (Estado del libro: RETURNED o ON_LOAN)
>    - type_id_reader: string (tipo de identificador de lector: DNI o CEDULA)
>    - Id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - date_limite : Date (Fecha límite del préstamo)
>    - date_return : Date (Fecha de devolución)

- Funcionalidad de filtrado de prestamos fuera de tiempo
>debe buscar todos los préstamos fuera de tiempo en la base de datos y que aún no estén devueltos
>Las condiciones son:
>- fecha de devolución excedido
>- estado en "ON_LOAN"
>- resultado ordenado de menor a mayor por nombre
>
>Debe recuperar:
>- la id del préstamo
>- la id del libro
>- el título del libro
>- el tipo de identificador del lector
>- el identificador del lector
>- el nombre del lector
>- la fecha límite


- Funcionalidad para calcular el tiempo de demora y evaluar cumplimiento de tiempo
>Funcionalidad para evaluar que el tiempo de préstamo esté dentro del tiempo estipulado
>
>se necesita que:
>- el servicio obtiene la fecha actual
>- se obtuviera el "date_limite" de la consulta a la DB
>
>Se calcula la diferencia de tiempo entre "date_limite" y "fecha actual".
>
>debe ser superior a 0, de otra forma, significa que hay un error en la recuperación de la información
>
>Este valor será devuelto en el parámetro "exceeded_days" de la respuesta del endpoint

- Funcionalidad para calcular multa utilizando fibonacci
>Si la diferencia obtenida de de los días es superior a 0, significa que se excedió del tiempo.
>
>Para calcular la multa se utiliza la fórmula Fibonacci:
>1, 1, 2, 3, 5, 8, 13, 21, 34, ...
>
>Lo primero será calcular la cantidad de semanas de demora, para esto se dividirá la cantidad de días de demora por 7.
>El resultado de esto se redondeará hacia arriba para obtener la semana de demora teniendo como resultado:
>- 1-7 días → 1 semana
>- 8-14 días → 2 semanas
>- 15-21 días → 3 semanas
>- 22-28 días → 4 semanas
>- ...
>
>la cantidad de semanas será la cantidad de iteraciones de fibonacci. Se debe tener en cuenta que la secuencia se relacionará de la siguiente manera:
>1 -> semana 1
>2 -> semana 2
>3 -> semana 3
>5 -> semana 4
>8 -> semana 5
>13 -> semana 6
>... -> ...
>
>Esto se multiplicará por el valor de multa especificado por la biblioteca. 
>
>Por ejemplo, si la multa es de 2 U$D (dólares) como valor base, entonces:
>- 1-7 días → 1 semana -> 1 * 2 = 2 U$D (dólares)
>- 8-14 días → 2 semanas -> 2 * 2 = 4 U$D (dólares)
>- 15-21 días → 3 semanas -> 3 * 2 = 6 U$D (dólares)
>- 22-28 días → 4 semanas -> 5 * 2 = 10 U$D (dólares)
>- 29-35 días → 5 semanas -> 8 * 2 = 16 U$D (dólares)
>- ...
>
>este resultado final será devuelto en el parámetro "dept_amount" de la respuesta del endpoint

### Subtareas QA
- Diseñar escenarios para préstamos vencidos, préstamos aún vigentes y lista vacía sin atrasos.
- Preparar datos con al menos un libro fuera de plazo, uno dentro del plazo y lectores distintos para validar la asignación correcta.
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
- UI (Inputs) para indicar el identificador del lector
> inputs necesarios: 
>    - [input] nombre del lector (string)
>    - [select] selector de DNI o Cédula
>    - [input] id del lector (integer) (opciones: DNI o Cédula)
>
>Solo una de las dos opciones es necesaria, pueden escribirse los dos. \
>No pueden estar vacíos los dos campos
>
>Debe haber: \
>Cada input debe tener un placeholder de ejemplo  \
>nombre del lector = "Pedro Hugo Ramon Castillo de la Rosa" \
>Id del lector = 00000000
>
>Cada input debe tener validación para impedir que los campos estén vacíos
>
>- un botón de "buscar"


- Endpoint GET /api/v1/readers/debt?typeId=*tipo de id*&id=*identificador*&name=*nombre* para obtener la multa
>el endpoint puede recibir 3 parámetros:
>- "name" : string 
>y
>- "id" : integer
>- "typeId : string (CEDULA o DNI)
>
>si se incluye "id", entonces debe estar también "typeId"
>
>Pueden incluirse los tres a la vez. El endpoint no debe aceptar no recibir parámetros
>
>utilizará el nombre del lector, la id del lector o ambos para buscar la multa correspondiente al lector
>>
>Respuestas posibles:
>
>200 => \
>{ \
>    "id_dept" : integer, \
>    "load_id" : integer, \
>    "type_id_reader" : string, (CEDULA o DNI) \
>    "id_reader" : integer, \
>    "name_reader" string, \
>    "amount" : real, \
>    "state_dept" : string (PENDING o PAID) \
>}
>
>404 =>
>- si el lector no existe
>
>400 =>
>- si alguno de los campos obligatorios no es correcto
>
>500 =>
>- Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante


- Comunicación UI y endpoint GEt api/v1/debt/{identificador}
>Si devuelve:
>200 => 
>- se renderiza la información en la UI
>- se debe poder utilizar un botón para confirmar el pago de la multa y cambiar el estado
>
>404 =>
>- se muestra un mensaje de feedback indicando que no existe este lector
>
>400 =>
>- se muestra un mensaje indicando que alguno de los campos es inválido. Si es posible, se debe indicar cual es el campo incorrecto
>
>500 =>
>- Error interno del servidor. Se devuelve un mensaje de error que de información sobre el error resultante


- Tabla DB con lectores morosos.
>Tabla de la DB llamada dept_reader que contiene los siguientes atributos:
>    - id_dept : integer (id de la multa) (único y autoincremental)
>    - loan_id : integer (ID del préstamo) (clave foranea)
>    - type_id_reader : string, (CEDULA o DNI)
>    - Id_reader : integer (Identificador del lector)
>    - name_reader : string (Nombre del lector responsable)
>    - amount_dept : real (monto de la deuda)
>    - state_dept : string (estado de la deuda: PENDING o PAID)


- Método de búsqueda de lector moroso.
>El servicio busca en la DB si el lector con la ID o el nombre recibida en el body del endpoint tiene alguna multa
>
>Para esto busca en la tabla:
>- la tupla que tenga la "id_reader" = id_reader_recibida
>- la tupla que tenga "name_reader" = name_reader_recibido
>- que la tupla sea la más actual
>
>Y se devuelve toda la información correspondiente a esta multa obtenida


- UI (elemento de confirmación) para confirmar el pago de la multa
>se renderiza toda la información devuelta por el endpoint:
GET /api/v1/readers/debt?typeId=*tipo de id*&id=*identificador*&name=*nombre*
>
>- se activa/muestra/renderiza un botón para confirmar la multa como pagada. 
>El confirmar el pago de la multa debe tener una doble confirmación por seguridad y evitar errores.
>
>el botón de pago llama al endpoint:
>- Endpoint PATCH api/v1/debt/{identificador} para cambiar el estado de la multa a pagado
>pasándole como parámetro en identificador el "id_dept" de la multa

- Endpoint PATCH api/v1/debt/{identificador} para cambiar el estado de la multa a pagado
>Endpoint encargado de editar  el estado de la multa cuando se confirma el pago de esta. Permitiendo que el lector pueda volver a tomar prestado un libro
>
>el endpoint recibe la id de la multa como identificador en la url.
>
>Estructura del request Body:
>{
>    "state_dept" : "PAID"
>}
>
>Respuestas posibles:
>{
>  "id_dept": integer,
>  "loan_id": integer,
>  "type_id_reader": string, (CEDULA o DNI)
>  "id_reader" : integer,
>  "name_reader": string,
>  "amount_dept": real,
>  "state" : string (debería devolver PAID si todo sale bien)
>}
>
>404 =>
>- si no se encuentra la multa con el identificador dado
>409 =>
>- si la multa ya tiene el estado PENDING
>500 =>
>- Error interno del servidor. Se intenta devolver un mensaje de error que de información sobre el error resultante

- Comunicación UI y endpoint PATCH api/v1/debt/{identificador}
>Si devuelve:
>200 => 
>- se renderiza un mensaje de feedback indicando que la información fue actualizada y la multa se registró como pagada
>
>404 =>
>- se muestra un mensaje de feedback indicando que no existe la multa con esta id
>
>409 =>
>- se muestra un mensaje indicando que la multa ya está pagada
>
>500 =>
>- Se muestra un mensaje indicando el error que surgió



### Subtareas QA
- Diseñar escenarios para pago total exitoso, lector sin deuda pendiente e intento de registrar pagos duplicados.
- Preparar datos con al menos un lector bloqueado por deuda pendiente y otro lector ya habilitado sin deuda.
- Validar que el pago elimine la deuda pendiente y rehabilite correctamente al lector para futuros préstamos.
- Validar que, después del pago total, el lector pueda registrar un nuevo préstamo válido sin ser rechazado por deuda previa.
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