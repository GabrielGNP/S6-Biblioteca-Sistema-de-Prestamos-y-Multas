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

---

# Título: HU-03 - Registrar devolución de un libro dentro del plazo

# Título: HU-04 - Registrar devolución tardía y generar multa Fibonacci

# Título: HU-05 - Consultar libros fuera de plazo y lector responsable