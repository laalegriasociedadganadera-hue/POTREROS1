# La Alegría — Gestión de Potreros

## Descripción general
Aplicación web de página única (SPA) para gestión de inventario ganadero de la estancia "La Alegría". Sin backend — todo corre en el browser con `localStorage` como persistencia. Futura integración con Supabase + Google Auth (mencionada en comentarios del código pero no implementada aún).

## Archivo único
Todo el código (HTML, CSS, JS) vive en un único archivo:
```
La Alegria - Gestion Potreros.html
```
No hay build, no hay npm, no hay servidor. Se abre directo en el browser.

## Dependencias externas (CDN)
- **Chart.js** — gráficos de barras y torta
- **Leaflet 1.9.4** — mapa interactivo (KMZ/GeoJSON)
- **JSZip 3.10.1** — parsear archivos .xlsx y .kmz
- **@tmcw/togeojson 5.8.1** — convertir KMZ a GeoJSON para Leaflet

## Estructura de secciones (tabs del menú)
| Tab ID | Descripción |
|--------|-------------|
| `tabla` | Vista principal — tarjetas de potreros + ingreso rápido |
| `dashboard` | KPIs + gráficos Chart.js |
| `mapa` | Heatmap de densidad por potrero |
| `inventario` | Vista tabla estilo Excel, exportable |
| `actividades` | Auditoría inmutable de movimientos |
| `reportes` | Exportar CSV / JSON / Excel / PDF |
| `rrhh` | Gestión de trabajadores y gastos |
| `caravanas` | Integración con Baqueano App (Excel .xlsx) |

El menú lateral (hamburguesa) llama a `showTab(id)`. El plano KMZ se abre como modal separado (`abrirPlano()`).

## Datos y persistencia
- **`localStorage`** con claves versionadas (`_v6`)
  - `potreros_data_v6` — array de objetos potrero
  - `potreros_historial_v6` — array de movimientos (auditoría)
  - `rrhh_trabajadores_v6` — trabajadores y sus movimientos
  - `caravanas_v6` — grupos de caravanas importados
  - `plano_kmz_v6` — KMZ guardado en base64

## Potreros
21 potreros fijos definidos en `POTREROS_BASE`. Cada potrero tiene:
```js
{ nombre, ha, toros, vacas_cria, vacas_inver, nov_mas3, nov_23, nov_12,
  vaq_mas2, vaq_12, terneros_as, terneros, terneras, total }
```
`total` se recalcula siempre con `recalcularTotal(p)`.

## Categorías de animales
`toros`, `vacas_cria`, `vacas_inver`, `nov_mas3`, `nov_23`, `nov_12`, `vaq_mas2`, `vaq_12`, `terneros_as`, `terneros`, `terneras`

## Caravanas (Baqueano)
Se importan desde `.xlsx` exportado por Baqueano App. Cada importación crea un "grupo/lote" con metadatos (nombre del evento, fecha recordatorio, notas, usuario, potrero). Las caravanas individuales se buscan por número. Las alertas de vacuna se calculan por fecha de vencimiento.

## RRHH
Trabajadores con historial de pagos, adelantos y adicionales. Cada entrada tiene: fecha, monto, tipo (`pago`/`adelanto`/`adicional`/`razon`), concepto.

## Convenciones de UI
- Paleta: azul `#1d4e89` (60%), verde `#3a7d44` (30%), ámbar `#e8a33d` (10%)
- Font base: 17px (legibilidad en campo/móvil)
- Área táctil mínima: 48px
- Toasts para feedback: `mostrarToast(msg, tipo)`
- Botón ✕ cerrar: `.btn-x-cerrar` (círculo gris, absoluto) o `.btn-x-cerrar-inline` (inline en barras)
- PWA: manifest dinámico con ícono SVG inline

## Lo que NO está implementado aún
- Login con Google / Supabase Auth (placeholder `usuario: 'local'`)
- Sincronización en la nube
- Control de Gastos por Concepto (RRHH → sección marcada "en desarrollo")
- Filtros de vacuna específicos en caravanas (aftosa, brucelosis, antiparasitario)
