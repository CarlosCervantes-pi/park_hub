# Lección 6.3 — Sesión 19: pruebas unitarias con Jest

**Objetivo:** diseñar y ejecutar pruebas unitarias para las reglas puras de reservación. Una prueba comprueba una regla con datos concretos y permite detectar cambios inesperados rápidamente.

## Estructura de una prueba

1. **Arrange:** preparar los datos de entrada y el resultado esperado.
2. **Act:** llamar a la función.
3. **Assert:** comparar el resultado real con el esperado.

`describe(...)` agrupa pruebas por función, `test(...)` nombra un caso y `expect(...)` realiza la comprobación. Aquí usamos `toBe(...)` para booleanos o `null`, y `toEqual(...)` para comparar el contenido de un período con fechas.

```ts
test('allows an active spot with no conflict', () => {
  const active = true; // Arrange
  const hasConflict = false;
  const allowed = canReserveSpot(active, hasConflict); // Act
  expect(allowed).toBe(true); // Assert
});
```

## Ejecutar

- `npm test`: ejecutar una vez todas las pruebas.
- `npm run test:watch`: repetirlas al guardar cambios.

La suite `src/features/reservations/__tests__/reservation.rules.test.ts` cubre:

- `validateReservationPeriod`: períodos válidos (**happy path**), fechas iguales (**edge case**), orden inverso y entradas inválidas (**error cases**).
- `hasTimeConflict`: solapamientos (**happy path** para detectar conflictos), períodos adyacentes (**edge cases**) y períodos separados sin conflicto.
- `canReserveSpot`: espacio activo sin conflicto (**happy path**), espacio inactivo o con conflicto (**error cases**).

El servicio valida el período antes de llamar a `canReserveSpot(active, hasConflict)`. Esta función recibe solo dos booleanos, por lo que el caso de período inválido se prueba en `validateReservationPeriod`, no en `canReserveSpot`.

El servicio también consulta únicamente reservaciones con `status: 'ACTIVE'` antes de llamar a `hasTimeConflict`. Las canceladas quedan fuera de esa consulta; ninguna de las reglas puras recibe el estado de una reservación. Comprobar ese filtro requeriría una prueba del servicio con acceso a datos o mocks, fuera del alcance unitario de esta sesión.

## Mostrar PASS y FAIL en clase

Una prueba que pasa puede mostrar:

```text
PASS  reservation.rules.test.ts
✓ allows an active spot with no conflict
```

Para mostrar un fallo, el profesor puede cambiar temporalmente `expect(allowed).toBe(false)` por `expect(allowed).toBe(true)` en la prueba del espacio inactivo y ejecutar `npm test`:

```text
Expected: true
Received: false
```

Restaurar la expectativa correcta después de la demostración; la suite entregada debe pasar completa.

Una prueba verifica el comportamiento esperado en sus casos; no demuestra que toda la aplicación sea correcta.
