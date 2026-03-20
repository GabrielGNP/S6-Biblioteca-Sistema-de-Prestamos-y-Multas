# Biblioteca: Sistema de Préstamos y Multas

MVP documental para definir cómo una biblioteca controla préstamos de libros, fechas de devolución, multas por retraso y rehabilitación del lector después del pago.

## Equipo

- [Alexander Molina](https://github.com/AlexRieger47) - QA
- [Gabriel Perero](https://github.com/GabrielGNP) - DEV

## Qué problema resuelve

La biblioteca necesita una forma clara y consistente de:

- saber si un libro está disponible o prestado;
- registrar préstamos con fecha de devolución válida;
- detectar devoluciones tardías;
- calcular multas acumulativas por retraso;
- bloquear nuevos préstamos a lectores con deuda pendiente;
- rehabilitar al lector cuando paga la multa completa.

Esta entrega no construye todavía el software. Su objetivo es dejar una base de producto y backlog lista para implementación posterior.

## Qué incluye este repositorio

- Un PRD con visión, reglas del negocio, alcance del MVP y riesgos.
- Historias de usuario con valor de negocio, criterios de aceptación, escenarios Gherkin y Story Points.
- Subtareas DEV y QA por cada historia.
- Trazabilidad documental del flujo principal del MVP.
- Referencia al tablero de GitHub Projects para el backlog del taller.

## Alcance del MVP documental

### Dentro del alcance

- Registrar el préstamo de un libro disponible.
- Permitir solo plazos de 7, 14 o 21 días.
- Calcular automáticamente la fecha de devolución.
- Registrar devoluciones dentro del plazo sin multa.
- Registrar devoluciones tardías con multa acumulativa.
- Aplicar lógica de multa Fibonacci por semanas de mora.
- Consultar préstamos vencidos y lector responsable.
- Registrar el pago total de una multa para rehabilitar al lector.
- Bloquear préstamos a lectores con deuda pendiente.

### Fuera del alcance

- Prórrogas de préstamo.
- Reservas.
- Administración completa del catálogo.
- Membresías o administración de usuarios.
- Notificaciones automáticas.
- Pagos parciales.
- Reportería avanzada.

## Historias del MVP

- HU-01: Consultar estado y disponibilidad de un libro.
- HU-02: Registrar libro disponible a un lector habilitado.
- HU-03: Registrar devolución de un libro dentro del plazo.
- HU-04: Registrar devolución tardía y generar multa Fibonacci.
- HU-05: Consultar libros fuera de plazo y lector responsable.
- HU-06: Registrar el pago total de una multa y rehabilitación del lector.

## Reparto de trabajo DEV y QA

### DEV

- Traducir cada historia a componentes técnicos concretos.
- Definir subtareas de UI, endpoints, persistencia, validaciones y lógica de dominio.
- Aterrizar el comportamiento esperado del sistema en trabajo implementable.

### QA

- Definir criterios de aceptación verificables.
- Redactar escenarios Gherkin centrados en comportamiento de negocio.
- Diseñar validaciones, alternos, bordes, datos de prueba y notas de calidad.

## Documentos principales

- [PRD.md](https://github.com/GabrielGNP/S6-Biblioteca-Sistema-de-Prestamos-y-Multas/blob/main/PRD.md)
- [USER_STORIES.md](https://github.com/GabrielGNP/S6-Biblioteca-Sistema-de-Prestamos-y-Multas/blob/main/USER_STORIES.md)
- [SUBTASKS.md](https://github.com/GabrielGNP/S6-Biblioteca-Sistema-de-Prestamos-y-Multas/blob/main/SUBTASKS.md)

## Tablero de trabajo

- GitHub Projects del repositorio: https://github.com/users/GabrielGNP/projects/8/

## Definition of Ready

Una historia se considera lista cuando:

- Tiene valor de negocio claro.
- Sus reglas relacionadas estan identificadas.
- Sus criterios de aceptacion son entendibles y verificables.
- Tiene subtareas DEV y QA coherentes.
- Tiene una estimacion razonable en Story Points.

## Definition of Done

Para esta entrega documental, una historia se considera terminada cuando:

- Queda redactada en USER_STORIES.md.
- Tiene criterios de aceptacion y escenarios Gherkin.
- Tiene subtareas DEV y QA en SUBTASKS.md.
- Su estimacion es coherente con el trabajo descrito.
- Mantiene consistencia con el PRD y con el tablero.