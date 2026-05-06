# Winflex — Producción Termoformado (Tablet)

App web para que los operarios de termoformado registren la producción del turno
desde la tablet del piso de planta.

## Acceso

- **Operarios**: PIN **8376** (Garbia, Luna, Vera Alan, Vera Juan, Zayas)
- **Admin**: PIN **3080** (Stancato, Delgado)

## Cómo funciona

1. El operario abre la URL en la tablet y entra con su nombre + PIN
2. Carga el turno: hora inicio/fin, producto, bobinas (una por una con N°, partida,
   peso, cajas, etc.). El sistema calcula scrap teórico y eficiencia automáticamente.
3. Al finalizar, ve un resumen y confirma. Los datos se postean al Apps Script de
   Google Sheets (mismo script que se venía usando antes).
4. El Apps Script escribe en la hoja del mes correspondiente y en "Detalle Producción".

## Flujo de datos

```
Tablet (operario) → POST → Apps Script de Google → Google Sheet
                                                       ↓
                                           App de Ensaladeras (lee/importa)
```

## Backend (Apps Script)

El script vive en el Google Sheet "Produccion Ensaladeras Winflex":
https://docs.google.com/spreadsheets/d/1QzSgr5KstD8K1dwVxH7jp6xLihmpM60MJTHY4YcRbYU/edit

Endpoint: `AKfycbxc4zqrqDkklK1hZt9aaNtOjpXQ0U9uBv9h2craDwqojryOMt3-U2nTiWhIrIVjGayZGg`

El URL del endpoint está hardcodeado en `termoformado.html` (constante `SCRIPT_URL`).
Si por alguna razón hay que regenerar el endpoint (nueva implementación → nueva URL),
hay que actualizar también esa constante en el HTML.

## Productos y parámetros

- E900 → medida 640×500 — scrap 28.57%
- T900 → medida 640×350 — scrap 31.03%
- S900 → medida 640×300 — scrap 33.33%
- E910 → medida 640×500 — scrap 28.57%

Capacidad teórica: 6 bocas × 20 golpes/min × 60 min = **7.200 unidades/hora**

Turnos:
- M1 (Mañana) — 9 hs
- M2 (Tarde) — 12 hs
- N (Noche) — 12 hs

## Cambiar PINs

Buscá en el HTML las constantes al inicio del `<script>`:
```js
const PIN_OPERARIO = '8376';
const PIN_ADMIN    = '3080';
```

## Agregar/quitar operarios

Buscá la lista `OPERARIOS_LIST` y modificála:
```js
const OPERARIOS_LIST = [
  {nombre:'Garbia Luis',   tipo:'op'},
  ...
];
```

También hay que actualizar el `<select>` con id `op-select` en el HTML para mantener
consistencia con la lista de operarios que pueden compartir un turno.
