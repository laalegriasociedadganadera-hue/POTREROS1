# La Alegría — Pendientes para mañana

> Fecha de cierre: 2026-06-29. Sesión que terminó con la reunión OK.

## Contexto
La app ya tiene el **sistema central funcionando** (Firestore en tiempo real): potreros, caravanas, RRHH e historial compartidos entre todos los usuarios, con offline e indicador de sincronización. Login con Google + aprobación de admin + auditoría por usuario. Mapa con pines tappables y resaltado de recién editados.

El cliente quedó conforme. Pidió **2 cosas** para la próxima.

---

## 1. Exportar caravanas en hoja de texto (para SNIG)

**Estado:** medio hecho. Hay una función `exportarTexto()` y un botón **"Exportar caravanas (texto)"** en la pestaña Caravanas. Hoy exporta un `.txt` con el listado simple de números de caravana.

**IMPORTANTE — aclaración del cliente:**
- **SOLO caravanas, NO potreros.** Al SNIG no le importa el estado de los potreros, sólo los animales.
- Página destino: https://www.snig.gub.uy/principal/snig-principal-tramites-y-servicios-solicitud-de-caravanas-prueba

**Falta confirmar con el cliente / definir:**
- ¿Qué formato exacto espera el SNIG? ¿Sólo números de caravana, uno por línea? ¿O necesita columnas (categoría, sexo, fecha, peso)?
- ¿Separador? ¿Encabezados? ¿Algún número de DICOSE/establecimiento arriba?
- **Pedirle al cliente un ejemplo del archivo que sube hoy al SNIG** para calcar el formato exacto.

**Dónde está el código:** función `exportarTexto()` en `La Alegria - Gestion Potreros.html` (cerca de `imprimirReporte`). Botón en la pestaña Caravanas, al lado de "Limpiar todo".

---

## 2. Manejo por LOTE e INDIVIDUAL + pesaje + estadística de peso

El cliente quiere que quede **bien definido** poder manejar el ganado:
- **Por lote** (grupo/evento de manejo — ya existe `gruposManejo`)
- **Individual** (por caravana — ya existe `caravanas[ide]` con `lecturas[]`)

**Sumar:**
1. **Casilla para cargar PESAJE del animal** (manual). Hoy el peso sólo entra por import de Baqueano. Hay que poder agregar un pesaje a mano en la tarjeta de cada caravana → push a `caravanas[ide].lecturas` un objeto `{ fecha, peso, modo:'manual' }`.
2. **Cuadro estadístico de peso INDIVIDUAL** (por caravana): gráfico de evolución del peso en el tiempo, en base a las distintas lecturas/fechas. (Chart.js ya está cargado.)
3. **Cuadro estadístico de peso GLOBAL del lote/grupo cargado:** peso promedio, ganancia de peso (kg/día si hay fechas), distribución, etc. — un gráfico para todo el grupo.

**Datos que ya existen y sirven:**
- `caravanas[ide].lecturas[]` con campos `{ fecha, peso, categoria, sexo, ... }` (el `peso` ya se importa de Baqueano).
- `gruposManejo[gid].caravanas` = array de ides del lote.
- La tarjeta individual se arma en `generarCaravanaCardHTML(ide, datos, opts)`.
- El lote se arma en `renderizarEventosManejo()`.

**Sugerencia de implementación (para no recargar todo):**
- Botón "Ver evolución de peso" en cada caravana → abre modal con gráfico de línea (peso vs fecha).
- Sección/botón "Estadística de peso del lote" en cada grupo → modal con: promedio, mín/máx, ganancia diaria promedio, gráfico.

---

## Notas técnicas
- Todo el código vive en `La Alegria - Gestion Potreros.html` (un solo `<script>` clásico 1531→fin + un `<script type="module">` de Firebase al final).
- Para que el módulo Firebase toque variables del script clásico se usan "puentes" `window.aplicarXRemoto` / `window.obtenerX`.
- Idioma/formato unificado a **es-UY**.
- Deploy: push a GitHub `laalegriasociedadganadera-hue/POTREROS1` → Vercel auto-deploy → `laalegría.com`.
- Reglas de Firestore ya incluyen `potreros_estancia` y `datos_estancia` (sólo aprobados/admin).

## Hardening que sigue pendiente (no bloqueante)
- Email/aviso automático al admin cuando alguien pide entrar (necesita Cloud Functions).
- Exigir login para usar la app (hoy se puede entrar sin login y queda como "local").
- Afinar concurrencia "suma de movimientos" en potreros (hoy es last-write-wins por potrero, que alcanza).
