# PROYECTO PROPESA · Plataforma marítima

> Documento base para seguir desarrollando el proyecto en una conversación nueva (Claude Pro, Claude Code o cualquier otra).
> Estado: **versión 1.0.0 (lista para uso diario)** · octubre 2026 · idioma de la interfaz y del trabajo: **español** (tutear al usuario).

---

## 0. Cómo usar este archivo

1. Abre una conversación nueva (o un proyecto de Claude) y sube **este .md** y **`index.html`** (el código es la fuente de verdad; está dentro de `propesa-plataforma.zip`).
2. Pega un primer mensaje como este:

   > Lee `PROYECTO_PROPESA.md` e `index.html`. Vamos a seguir con la plataforma PROPESA. Antes de cambiar nada, dime en 5 líneas qué entendiste del proceso y qué tarea del To-do vamos a atacar. No cambies el flujo ni los textos sin confirmármelo.

3. Pide **un cambio a la vez**, con una prueba al final. Los cambios grandes se validan primero con el equipo de piso (ver sección 9).
4. Después de cada sesión actualiza las secciones **4 (Estado)**, **12 (Decisiones abiertas)** y **13 (To-do)** de este archivo.

---

## 1. Qué es PROPESA y qué hace la plataforma

- **Empresa:** Proveedor del Pescador S.A. de C.V. (PROPESA), Ensenada, B.C. Langosta roja viva en almacenaje húmedo (tanques) y exportación por vía aérea a China.
- **Objetivo de la plataforma:** seguir cada lote de langosta desde que entra hasta que se envía y se calcula su mortalidad, en una tablet, por personal de piso con poca experiencia técnica (manos mojadas o con guantes, poca luz).
- **Principio de diseño:** quien mire la pantalla debe entender **dónde está, qué sigue y qué falta**.

### Flujo final (decidido por el usuario)

**Entrada → Órdenes → Empaque → Embarque → Mortalidad final**

| # | Paso | Qué se hace | Quién |
|---|---|---|---|
| 1 | **Entrada** | Se captura el formato de recepción del lote: fecha, hora, proveedor, tanque, temperaturas, lote (mayúsculas automáticas), kg por caja en tanque, kg por talla, kg total, kg desperdicio. | Administrador |
| 2 | **Órdenes** | Pedido de un empaque: ciudad destino → consignatario → cliente final → cajas. | Administrador |
| 3 | **Empaque** | Al empacar cada pedido se cuentan las langostas de cada caja (lote, piezas vivas, categoría). La caja se numera ahí. | Administrador y encargado de piso |
| 4 | **Embarque** | Un embarque puede juntar varias órdenes (mismo destino y consignatario). Por cada consignación: tallas, peso bruto, plan de carga, cierre del empaque e informe por correo. | Administrador |
| 5 | **Mortalidad final** | Conteo final por lote y langostas vivas al llegar; se calcula la pérdida. | Administrador y piso (captura); admin cierra |

**Hecho clave del negocio (corregido por el usuario):** en los tanques las cajas **no se numeran ni se cuentan**. Solo se conocen los **kilos por talla** del lote. El conteo de piezas por caja se hace **al empacar cada pedido**.

### Glosario

- **Lote:** una entrada de producto. Código tipo `WLRCO170926` = `WLR` (langosta roja) + proveedor (`CO` Cooperativa/SCPP Ensenada, `EM` Emancipación, `LE` Leyes de Reforma) + fecha `ddmmyy`. *(Inferido de los PDFs; confirmar.)*
- **Tallas (gramos):** 491-610, 611-850, 851-1220, 1221-UP. En el papel también aparecen como 0-650, 651-850, 851-1250, 1251-UP.
- **Caja / hielera:** en la plataforma se tratan como lo mismo. Una caja lleva 16 kg netos y 20 kg brutos (según la orden a proveedor).
- **Consignatario:** a quién se factura en China (lista oficial de 7).
- **Cliente final:** código de 3 letras (HXH, ZSM, ZYX, RSK, LHD, HSF, HYG, HJX, LMD, MXH). Una orden a PVG se reparte entre varios clientes.
- **Consignación:** en la plataforma, cada grupo (orden + ciudad + consignatario) genera una consignación en Embarque, llamada p. ej. `PVG · YISHUNHAI · 07/10`.
- **ETD:** fecha de empaque/salida de la orden.
- **PVG / HKG / XMN / SZX / CAN / SIN:** Shanghái, Hong Kong, Xiamen, Shenzhen, Guangzhou, Singapur.

---

## 2. Documentos de origen (los PDFs que dio el usuario)

| Documento | Qué contiene | ¿Ya está en la plataforma? |
|---|---|---|
| `PROPESA_Control_Recepcion.pdf` | Registro de control de recepción y tallas (fecha, hora, proveedor, tanque, T°F tanque, T°F producto, lote, kg por caja, kg por talla, total, desperdicio) | **Sí**: es el formulario de Entrada |
| `control_de_pesos_y_tallas.pdf` (Formato 02, FL 003) | Pesos por tina (1-180), totales por talla, cajas completas/incompletas, observaciones, temperatura 57.7–62.2 °F | **No** |
| `Recepcion_Variedades.pdf` | Registro de envío/logística: fecha, destino, consignatario, color de tape, proveedor, tanque, temperaturas, lotes mezclados, kg por talla, cajas, % mortalidad, temp. de arribo | **No** (solo se calcula mortalidad) |
| `orden_a_proveedor.pdf` | Orden con ETD, cliente, destino, cajas por cliente, cajas total por destino, peso neto y bruto | **Sí**: base del módulo Órdenes |
| `tabla_logistica.pdf` | Registro por cliente y destino (PVG, XMN, HKG) con cantidad de cajas y kg neto | Parcial (no se cargó) |
| `lista_de_consignatarios.pdf` | Lista oficial de 7 consignatarios con dirección, contacto y USCI | **Sí** (constante `CONS`) |
| `packing_list.pdf` (Formato 06) | Packing list por caja: número, piezas vivas, cliente; encabezado con placas, chofer, horas, temperaturas, peso bruto | Parcial (packing list sí; encabezado no) |
| Fotos | Tanques, langostas, pesaje, tarima (ya no están en la demo) | Solo como "Fotos y notas" |

Los nombres chinos de los clientes vienen ocultos (■■■) en los PDFs.

---

## 3. Estructura del proyecto

```
propesa-plataforma/
├── index.html              ← TODA la aplicación (HTML + CSS + JS, ~100 KB, sin dependencias)
├── manifest.webmanifest    ← "Agregar a pantalla de inicio" en tablet
├── favicon.svg · icon-192.png · icon-512.png · apple-touch-icon.png
├── vercel.json             ← encabezados de seguridad y caché
├── .gitignore
└── README.md               ← flujo, primer uso, despliegue, pruebas y vuelta atrás
├── CHANGELOG.md            ← cambios por versión
```

- **Sin build, sin npm.** Se abre con doble clic o con `python3 -m http.server`.
- **Despliegue:** GitHub + Vercel (Framework Preset: *Other*, sin comando de compilación ni carpeta de salida).
- **Archivo suelto:** `PROPESA_Plataforma_maritima.html` es la misma app en un solo archivo.

---

## 4. Estado actual (qué funciona y qué falta)

**Funciona (todo en el navegador):**
- Acceso con 3 usuarios y 3 roles.
- Entrada: formulario con los campos del formato en papel, tabla igual al formato, detalle de lote, edición y eliminación.
- Órdenes: crear orden, agregar/eliminar líneas, totales netos/brutos, avance de empaque, packing list y descarga CSV.
- Empaque: agregar cajas por cliente (lote, piezas, categoría), numeración automática, quitar cajas, bloqueo si el empaque está cerrado.
- Embarque: consignaciones creadas por las órdenes, plano de camión dinámico (210 hieleras), distribución por talla editable, peso bruto calculado, avisos, cierre con informe (PDF en Administración → Informes de cierre).
- Mortalidad final: conteo final por lote, cierre de lote, langostas vivas por envío, gráficas.
- Fotos y notas (hasta 6 por nota, cámara o galería) en Entrada, lote, Empaque, Embarque, Mortalidad final y Temperatura.
- Panel con "Tu siguiente paso", totales, tablero de lotes y actividad.
- Accesibilidad básica: foco visible, saltar al contenido, tamaño de letra (3 botones A), objetivos táctiles grandes, menú lateral que se oculta en teléfono.
- Los formularios **conservan lo escrito** si hay un error.

**Límites de la v1.0 / no existe todavía:**
- **Los datos viven solo en `localStorage` de cada navegador.** Nada se comparte entre dispositivos (respaldo `.json` para pasarlos).
- Usuarios creados en la app (Configuración inicial + Administración); contraseñas con hash SHA-256, validadas en el navegador (no es seguridad de servidor).
- «Enviar por correo» = menú de compartir / `mailto:`; no hay servidor de correo.
- **Temperatura:** bitácora real `ST.T` (ya no hay datos de ejemplo).
- No hay captura de: Formato 02 (pesos por tina), encabezado del packing list (placas, chofer, horas), color de tape, temperatura de arribo.
- El logo es una **recreación en SVG** (no el archivo original).

La plataforma **inicia vacía y sin usuarios**: pide crear el Administrador (`setupView`). «Borrar los datos de este equipo» (Administración, escribir BORRAR) la regresa a ese punto.

---

## 5. Arquitectura técnica

### Patrón general
- **Una sola página** con JavaScript vanilla. Render por plantillas de texto (`` `...${x}...` ``) que se escriben en `#app.innerHTML`.
- Estado global `ST` (objeto). `render()` vuelve a dibujar todo; `save()` guarda `ST` en `localStorage` con la clave **`propesa-v4`**. El tamaño de letra se guarda aparte en `propesa-z`.
- `go('ruta:parámetro')` cambia de página (`V = {v, id}`) y llama a `render()`.
- `E()` escapa texto antes de ponerlo en HTML. **Usar siempre `E()` con texto del usuario.**

### Rutas (`PG`) y paso del menú (`STEP`)
`inicio, recepcion, lote:<lote>, ordenes, orden:<id>, empaque, empacar:<ordenId>.<lineaId>, embarques, emb:<índice>, cierre, temperatura, admin, buscar:<texto>`

| Paso del menú | Rutas |
|---|---|
| 1 Entrada | `recepcion`, `lote` |
| 2 Órdenes | `ordenes`, `orden` |
| 3 Empaque | `empaque`, `empacar` |
| 4 Embarque | `embarques`, `emb` |
| 5 Mortalidad final | `cierre` |

*Los nombres internos de ruta no se cambiaron (p. ej. `recepcion` y `cierre`); solo cambiaron los textos visibles.*

### Eventos
Un manejador de clic principal usa atributos `data-*`: `data-go` (navegar), `data-a` (acciones: `sv`, `nf`, `el`, `dl`, `mv`, `cl`, `su`…). Hay manejadores propios (se registran con `document.addEventListener`) para: notas y fotos (`data-ev`), órdenes (`data-oa`), empaque (`data-pk`), tamaño de letra (`data-z`), tallas del embarque (`data-zs`).

### Funciones clave
`render, go, save, load, seed, migratePK, cleanOrders` · páginas: `inicio, recepcion, lote, ordenes, orden, empaque, empacar, embarques, emb, cierre, temperatura, admin` · lógica: `post` (movimientos), `bal` (existencia en gramos), `calc` (mortalidad por lote), `gross` (peso bruto), `shipFor / syncShip` (consignaciones desde órdenes), `ordAssigned / ordBoxes / ordTot / ordAsg`, `shipIssues / shipAlert` (alertas), `closeP / closeLot`, `saveLot / saveU` (formularios), `renderKeep / fail` (conservan lo escrito), `evid` (fotos y notas), `truck` (plano del camión), `nxt` (siguiente paso), `iconize` (convierte ⚠ ✔ ✕ ☰ ● en íconos SVG).

### Constantes
`KGN=16` kg netos/caja · `KGB=20` kg brutos/caja · `S` tallas · `SL` etiquetas de talla del formato · `TK` tanques T1–T6 · `RC` categorías · `CONS` consignatarios · `PORTS` ciudades · `CLI0` códigos de cliente · `STG` etapas del lote · `ROLE` · `PAL`/`XC` colores.

### Convenciones
- Los kilos se guardan en **gramos** (`m`), se muestran en kg con `f()`.
- Fechas: `rdate` ISO (`YYYY-MM-DD`) y `date` en `dd/mm/aaaa`. Las órdenes usan `etd` ISO.
- Cualquier cambio de estructura de datos debe traer **migración** con una bandera (`PK`, `ORc`, `E0`) para no romper los datos ya guardados en el navegador.
- Mantener la página en un solo archivo hasta tener backend.

---

## 6. Modelo de datos (`ST`)

```js
ST = {
  PK:1, BN:0, ORc:1, E0:1,          // banderas de migración y contador de cajas (BN)
  U:[{id,name,email,pw,role,notify}],// usuarios; role: 'admin' | 'piso' | 'consulta'
  R:[{                              // lotes (entradas)
    id,'R-0121', lot, origin /*proveedor*/, tank, rdate, time, date,
    tempT /*°F tanque*/, tempP /*°F producto, texto/rango*/, kgbox, desp /*kg desperdicio*/,
    final /*kg conteo final|null*/, closed
  }],
  L:[{id,t,type,lot,size,m /*gramos*/,rev,rc}],   // movimientos de existencia por talla (RECEPCION, SALIDA_EMBARQUE…)
  B:{ [lot]:[{ c /*piezas vivas*/, k /*'R'|'V'|'B'*/, d /*índice en P*/, o /*'ordenId.lineaId'*/, seq }] }, // SOLO cajas ya empacadas
  OR:[{ id, etd, lines:[{id,city,cons /*índice en CONS*/,cli,boxes}], sh:{ 'CIUDAD|cons': índiceEnP } }],
  P:[{ n, z0, col, exp, h /*hieleras*/, z:[4 tallas en hieleras], c:[[nombre,kgUnit,cantidad]], alive, closed }], // consignaciones
  O:[{subject,t,…}],                // informes de cierre
  T:[{id,t,tank,a,s,p,by}],         // lecturas de temperatura (°F)
  M:{ [clave]:[{id,t,by,text,ref,imgs:[dataURL]}] } // fotos y notas; claves: 'recepcion','lote:<l>','emp:<oid.lid>','emb:<i>','cierre','temp'
}
```

---

## 7. Reglas de negocio implementadas

- **Existencia por lote** = suma de movimientos (`L`). La entrada crea un movimiento `RECEPCION` por talla. La salida a embarque se registra como movimiento **manual** en la página del lote. *Empacar una caja NO descuenta kilos.*
- **Kg total de entrada** = suma de las 4 tallas (solo lectura, se calcula). **Desperdicio** es un dato informativo; no resta existencia.
- **Orden:** neto = cajas × 16 kg; bruto = cajas × 20 kg. Una línea no admite más cajas empacadas que las pedidas.
- **Consignación:** se crea una por (orden + ciudad + consignatario). Sus hieleras = cajas de la orden. Peso bruto = hieleras × (1.4 hielera + 1.5 gel + 16 producto + 0.7 caja de cartón) = **19.6 kg/hielera**.
- **Alertas de embarque (rojo):** peso bruto ≠ formato, tallas ≠ hieleras (con tallas en 0 también avisa), cajas sin cantidad/categoría, sin cajas empacadas.
- **Cerrar empaque (solo admin):** requiere al menos una caja empacada; guarda el informe de cierre (Administración → Informes de cierre, con PDF).
- **Mortalidad por lote:** esperado (existencia) − conteo final, y su %. El conteo final no puede exceder lo esperado. **Por envío:** enviadas − vivas al llegar.
- **Bloqueos:** con el empaque cerrado no se agregan ni quitan cajas de ese destino. No se elimina un lote con cajas empacadas para envíos. No se elimina una línea con cajas.
- **Plano del camión:** 10 celdas (2 filas × 5 columnas) de 15/20/20/25/25 hieleras = 210. Se llena desde la cabina; avisa si se excede.

---

## 8. Roles y permisos

| Acción | Administrador | Encargado de piso | Consulta |
|---|---|---|---|
| Ver todo | Sí | Sí | Sí |
| Empacar cajas, capturar conteos finales (`can('box')`, `can('fin')`) | Sí | Sí | No |
| Fotos y notas | Sí | Sí | No |
| Crear/editar/eliminar entradas y órdenes (`can('lot')`) | Sí | No | No |
| Cerrar empaques y lotes | Sí | No | No |
| Usuarios y administración | Sí | No | No |

Sin usuarios precargados. Las pruebas automáticas crean sus propios usuarios de prueba (solo dentro de `pruebas/`).

---

## 9. Diseño y preferencias del usuario (importante: ya se discutieron)

### Lo que el usuario pidió y se mantiene
- **Logo PROPESA** en el menú lateral y en el acceso; **pequeño** (≈128 px en el menú); **blanco** sobre fondos oscuros.
- **Menú lateral azul marino** con los pasos numerados e íconos de dibujo (no emojis).
- **Campos de captura con relleno blanco**, menús desplegables con flecha propia, números sin flechitas, teclado numérico en tablet, texto seleccionado al tocar.
- **Azul solo en secciones de marca** (menú, encabezado, banner, acceso). Fondo y textos neutros.
- **Pendientes y discrepancias en rojo y llamativos**, con ícono y texto (no solo color).
- **Cierre/Mortalidad final NO va en rojo como faltante** (es el último paso).
- **Colores de consignaciones PVG/HKG parecidos a los originales** (verde azulado, dorado, violeta).
- **Optimizado para tablet:** objetivos táctiles grandes, rejillas que se adaptan, menú fijo en tablet y desplegable en teléfono.
- **Los formularios no deben borrar lo escrito** cuando hay un error.
- Un usuario no técnico debe poder usarla: textos simples, un paso a la vez, mensajes claros.

### Lo que el usuario RECHAZÓ (no volver a hacerlo sin preguntar)
- El **sistema de color semántico oscuro** (cian/verde/ámbar/rojo/morado sobre fondo negro-navy, números gigantes, estados en el menú). Se revirtió por completo.
- La **galería de fotos de lotes en el Panel**. Se revirtió.
- **Cambiar muchas cosas sin validarlas**: el equipo sintió que se trabajaba "sin rumbo".

### Cómo trabajar con este proyecto
1. Confirmar el entendimiento del proceso **antes** de programar.
2. Un cambio a la vez, con prueba (ver sección 10).
3. Validar cambios de flujo con el compañero de piso (existe una hoja de una página con el proceso).
4. No inventar reglas de negocio: si falta un dato, preguntar (sección 12).

---

## 10. Pruebas

No hay suite formal. Se probó con **Playwright (Python)** sirviendo la carpeta con `python3 -m http.server` y recorriendo flujos: login → crear entrada → crear orden → empacar → embarque → mortalidad, además de un barrido de todas las páginas buscando `NaN` o `undefined`.

Pendiente: convertir esas pruebas en un archivo `tests/` dentro del repositorio y correrlas en CI.

---

## 11. Despliegue

```bash
cd propesa-plataforma
git init && git add . && git commit -m "PROPESA plataforma"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/propesa-plataforma.git
git push -u origin main
```

Vercel: <https://vercel.com/new> → importar el repositorio → *Framework: Other* → Deploy. Cada `git push` a `main` vuelve a desplegar. Sin variables de entorno por ahora.

---

## 12. Decisiones abiertas (preguntar al usuario antes de implementar)

1. **Peso bruto:** la orden usa **20 kg/caja** y la consignación calcula **19.6 kg/hielera**. ¿Con cuál se trabaja y cómo se compone?
2. **Categorías:** la app usa Rojo/Verde/Blanco por caja; el packing list en papel muestra R/V/A y D (despatada) ligadas a la talla. ¿La categoría debe ser en realidad la **talla**? ¿Existe "Azul"?
3. **Inventario:** empacar no descuenta kilos del tanque. ¿Debe descontar? Eso exige capturar la **talla** (y quizá el peso real) de cada caja.
4. **Desperdicio:** ¿resta de la existencia o es solo informativo?
5. **Varios tanques por lote** (en el papel aparecen "1/2", "3 y 1"). Hoy es un solo tanque.
6. **Lotes mezclados en un envío** (en el papel un envío lleva 2-3 lotes): ya se permite al empacar; ¿se necesita verlo por envío?
7. **Cajas = hieleras:** ¿siempre equivalen?
8. **Mortalidad:** ¿la definición actual (esperado − conteo final por lote; enviadas − vivas por envío) es la que usan? El papel reporta "% mortalidad" y "temperatura de arribo" por envío.
9. **Encabezado del packing list:** placas, chofer, teléfono, horas, temperatura del producto, tanque y temperatura. ¿Se captura en la plataforma?
10. **Color de tape** por consignatario (aparece en el control de envío).
11. **Temperatura:** ¿quién captura, cada cuánto y con qué alertas? (rango de almacenamiento visto: 57.7–62.2 °F; transporte 48–49 °F).
12. **Pedidos de varios días hacia la misma ciudad y consignatario:** RESUELTO en parte (oct 2026): se pueden unir varias órdenes en un embarque (`shipLinks`, `moveGroup`, `dropShip`). Pendiente confirmar si se unen órdenes de fechas distintas y cómo se factura.
13. **Idioma:** ¿se necesita versión en inglés/chino para los socios?
14. **Logo oficial:** subir el archivo original para reemplazar la recreación.

---

## 13. To-do priorizado

### P0 · Antes de programar más
- [ ] Probar el flujo completo con el compañero de piso (usar la hoja de una página y el manual).
- [ ] Contestar las decisiones abiertas 1–4 (afectan el modelo de datos).
- [ ] Subir el logo original y fotos de pantalla/papel reales para ajustar textos.

### P1 · Convertirla en una herramienta real (sin esto no se puede usar en producción)
- [ ] **Backend y datos compartidos** (recomendado: Supabase — Postgres + Auth + Storage). Pasar `ST` a tablas: `lotes`, `movimientos`, `ordenes`, `lineas_orden`, `cajas`, `consignaciones`, `usuarios`, `notas`, `fotos`.
- [ ] **Inicio de sesión real** (Supabase Auth) y permisos por rol con políticas de seguridad en la base (RLS). Quitar contraseñas del código.
- [ ] **Fotos** a almacenamiento (hoy son base64 en el navegador).
- [ ] **Correo real** del informe de empaque (p. ej. Resend o SendGrid) en lugar del simulado.
- [ ] **Importar** los datos históricos de los PDFs/Excel (recepciones y envíos ya registrados).
- [ ] **Respaldo y registro de auditoría** (quién cambió qué y cuándo).

### P2 · Funciones del proceso que aún están en papel
- [ ] **Formato 02 · Control de pesos y tallas:** captura por tina (1–180), totales por talla, cajas completas e incompletas, observaciones, temperatura.
- [ ] **Encabezado del packing list** (placas, chofer, horas, temperaturas, peso bruto) y exportarlo a **PDF/Excel** con el formato oficial.
- [ ] **Temperatura:** captura de lecturas por tanque, con alertas fuera de rango.
- [ ] **Control de recepción y envío** (el registro por envío con % mortalidad y temperatura de arribo).
- [ ] **Editar/eliminar órdenes completas**, editar cajas ya empacadas y reordenar el packing list.
- [ ] **Descontar kilos al empacar** (según decisión 3) y conciliación de existencias.
- [ ] Alertas: lote sin movimiento, empaque con peso fuera de tolerancia, tallas que no suman.
- [ ] Reportes: mortalidad por proveedor/mes, productividad de empaque, existencias por tanque.

### P3 · Calidad y experiencia
- [ ] **Modo sin conexión** (PWA con service worker) para zonas con mala señal.
- [ ] Escaneo de código de barras/QR para lote y cajas; impresión de etiquetas.
- [ ] Pruebas automáticas (carpeta `tests/`) y CI en GitHub.
- [ ] Separar el código en módulos (cuando haya backend) o migrar a un framework solo si se justifica.
- [ ] Prueba de usabilidad con personal de piso (guantes, poca luz) y ajuste de tamaños.
- [ ] Auditoría de accesibilidad con lector de pantalla y contraste real en tablet.
- [ ] Versión en otros idiomas.

---

## 14. Historial resumido (para no repetir errores)

1. Se partió de un HTML ya funcional de PROPESA (lotes, cajas, envíos y mortalidad) y se le agregó logo, menú lateral, paleta del logo y campos blancos.
2. Se quitó el exceso de azul, se restauraron colores de PVG/HKG y se hicieron alertas rojas.
3. Se optimizó para tablet, se agregaron íconos de dibujo, fotos y notas, y la barra "contando el lote".
4. Se pulió con la guía de diseño Impeccable (contraste, íconos, sin franjas laterales). Un rediseño oscuro con colores semánticos fue **rechazado** y revertido.
5. Se agregó el módulo de **Órdenes** (consignatarios, clientes finales, packing list).
6. Se vació toda la demo (inicio desde cero) y se hizo dinámico el plano del camión.
7. **Cambio de modelo (el más importante):** se eliminaron las cajas numeradas en tanque; las langostas se cuentan en **Empaque**. Se renombró el flujo a *Entrada → Órdenes → Empaque → Embarque → Mortalidad final* y se rehizo la **Entrada** con los campos del formato en papel.
8. Se escribieron un manual de usuario y una hoja de una página (la hoja de una página **aún describe el flujo viejo**; actualizar).

---

## 15. Prompts útiles para continuar

- "Lee `PROYECTO_PROPESA.md` e `index.html`. Resume el flujo y dime qué entendiste antes de tocar código."
- "Vamos con la decisión abierta #3: propón las opciones para descontar kilos al empacar, con pros y contras, sin implementar todavía."
- "Implementa solo el encabezado del packing list (placas, chofer, horas, temperaturas) en la página de la orden, con migración de datos y una prueba de punta a punta."
- "Diseña el esquema de base de datos en Supabase para el modelo de la sección 6 y muéstrame las tablas antes de crearlas."
- "Actualiza el manual de usuario y este archivo con lo que cambió hoy."

---

*Última actualización: octubre 2026 · versión de la plataforma: 1.0.0 con flujo Entrada → Órdenes → Empaque → Embarque → Mortalidad final.*

## Cambios de la ronda del 10/oct/2026
- Lote de recepción se escribe en MAYÚSCULAS en vivo; el campo de Entrada ahora se llama **Kg por caja en tanque** (clave interna `kgbox`).
- **Empaque:** una fila por cada caja que falta (piezas vivas, categoría, lote), se guardan todas a la vez (`pkGrid`, `data-pk="addall"`); solo notas, sin fotos (`evid(key,title,1)`).
- **Embarque con varias órdenes:** `o.sh['CIUDAD|cons']` puede apuntar al mismo índice de `ST.P`; `syncShipIdx` suma hieleras de todas las órdenes; `moveGroup` une/separa (mueve también `b.d` de las cajas); `dropShip` borra embarques vacíos y reindexa. UI: «Unir» en la orden, «Agregar otra orden»/«Separar» en el embarque. El nombre del embarque junta las fechas (`PVG · YISHUNHAI · 12/10 + 13/10`).
- Ícono de Embarque = el de Recepción (camión). Entrada y Embarque hoy se ven iguales.
- Bug corregido: las notas de Empaque y Embarque no se guardaban (la clave con `:` se cortaba en `data-ev`).

## Ronda del 10/oct/2026 (tarde): PDF, vistas y plan de sincronización
**Decisiones del usuario:** la copia web será **Supabase** (**no conectar la cuenta todavía**); capturan **varias tablets** y además hay gente que **solo consulta** (computadora o celular, la vista debe adaptarse); hay internet **casi siempre**; el archivo final de cada paso es un **PDF**, y **ese mismo PDF es el que se manda por correo**.

**Hecho:**
- Generador de PDF propio (`PdfRep`, `pdfEntrada/Ordenes/Empaque/Embarque/Embarques/Mort`; Helvetica WinAnsi con métricas reales, logo en JPEG, tablas con encabezado repetido, paginación). Botones `data-pdf="dl|mail:<tipo>:<arg>"` en cada paso (`pdfBar`). `Enviar por correo` usa Web Share con archivo (tablet/celular) y, si no existe, descarga + `mailto:` con los usuarios marcados «Recibe informes».
- El correo simulado del cierre trae `pn` (nombre del embarque) y `att` (nombre del PDF); Administración permite descargar ese PDF.
- Vistas: revisadas a 390, 820 y 1440 px; en celular las filas de Empaque se vuelven tarjetas.
- Administración → «Datos y respaldo»: respaldo y restauración `.json`.
- `supabase/schema.sql`: tablas con UUID, RLS por rol (admin/piso/consulta), realtime y bucket privado `reportes`. **No se ha aplicado.**

### Sincronización (pendiente, requiere conectar la cuenta)
Hoy cada tablet tiene sus propios datos. Para compartir hay que cambiar antes el modelo de la app:
1. **IDs estables:** hoy los embarques se identifican por su posición en `ST.P`, las órdenes/líneas con `max+1` y las cajas con `++BN`; dos tablets chocarían. Pasar a UUID (o `Date.now()+aleatorio`) y que `b.d` y `o.sh` apunten a ids, no a índices.
2. **Local primero:** seguir guardando en localStorage y mandar los cambios a una cola; subirlos cuando haya internet (casi siempre) y marcar «pendiente de sincronizar».
3. **Conflictos:** cajas, movimientos y notas solo se agregan (no chocan); órdenes, embarques y lotes se editan por campo con «último cambio gana» y `updated_at`.
4. **Usuarios reales:** reemplazar los usuarios demo (contraseña en claro) por Supabase Auth + tabla `profiles`.
5. **Vista de consulta:** la misma página, en solo lectura, con Realtime.
6. **Correo con PDF:** al cerrar un embarque, subir el PDF al bucket `reportes` y mandarlo con una Edge Function (Resend o SMTP) a los usuarios con «Recibe informes». Hace falta decidir remitente/dominio.
**Preguntas abiertas:** ¿quién puede editar una orden ya empacada? ¿Se permite capturar sin internet y sincronizar después (ya se asume que sí)? ¿Dominio y remitente del correo? ¿Los de consulta entran con correo y contraseña?

## Paleta de color (10/oct/2026)
Se aplicó `PROPESA_Color_System_Refinement.md` solo como cambio de colores (sin tocar layout ni lógica). Tokens nuevos en `:root`: `--brand-navy #0B1D59`, `--brand-blue #087DBA`, `--brand-cyan #16A6D1`, `--ok/--okb/--okf` (verde de éxito). Tema oscuro: `--bg #111827`, `--cd #1C2638`, `--ln #2D3B50`, `--ink #F1F5F9`, `--mut #A9B7CA`, `--in #243249`, `--hl #2D3B50`. `--ac` (botones, chips seleccionados) pasó de turquesa a azul océano; el verde de «Completa» ya no usa el color de marca; progreso, línea de tiempo e íconos de notas usan cian; el item activo del menú lleva una barra cian sobre un navy más claro (`#1B3680`). El encabezado, que era blanco fijo, ahora es pizarra en tema oscuro. Contrastes medidos: texto principal 13.9:1 sobre tarjeta, secundario 7.5:1, blanco sobre azul 4.5:1, cian sobre tarjeta 5.4:1, verde 6.7:1. Rojo/ámbar/verde se dejaron como estados, no como marca. Pendiente: el logo (`LGP`) conserva sus azules originales (`#0a2373`, `#0583c3`), que difieren un poco de los tokens.

## Ronda del 12/oct/2026: error corregido, colores de talla y Modo piso
- **Bug grave corregido:** las filas de Empaque usaban el atributo `data-r`, el mismo que el botón «Revertir» de movimientos. Tocar el campo de piezas de la caja 2, 3… **revertía un movimiento de recepción** (bajaba el inventario sin avisar) y la caja 1 causaba el error `reading 'lot'`. Ahora es `data-row`, `rev()` valida, y en la página del lote hay **«Anular reversa»** (admin) para deshacer reversas que no se hicieron a propósito.
- **Colores de talla** (`SZC`, `szDot`): 491-610 Blanco · 611-850 Rojo · 851-1220 Verde · 1221-UP Amarillo; borde de 4 px en los campos y punto de color en etiquetas y encabezados.
- **Modo piso** (`PISO`, `pisoShell`, `pisoHome`, `pisoEmp`, `pisoEnt`; rutas `piso`, `pemp[:orden.línea]`, `pent`): inspirado en la investigación de McDonald's (cero fricción, botones grandes, revelación progresiva, a prueba de errores, colores que se reconocen sin leer). El rol piso entra directo; el admin lo activa desde el menú. Preferencia por equipo en `localStorage['propesa-piso']`.
- **Permiso nuevo `ent`:** el Encargado de piso ya puede registrar entradas (editar/eliminar lotes sigue siendo solo del admin).
- Pendiente de validar con el piso: si prefieren capturar caja por caja con teclado (modo piso) o varias filas a la vez (vista completa); si «Cerrar embarque» y «Conteo final» también necesitan su propio flujo paso a paso (hoy abren la pantalla normal sin menú).
- **12/oct (cont.):** el aviso rojo «X de Y cajas empacadas» (orden, Empaque, Panel) ahora es un botón que lleva al primer cliente con cajas pendientes (`pendKey`, `goPend`, `pendChip`); al completar un cliente aparece «Siguiente cliente que falta». El Modo piso ya **no es una caja a la vez**: muestra una tarjeta grande por cada caja pendiente y guarda todas juntas (reutiliza `PKD` y `data-pk="addall"`); «Poner a todas las vacías» (`data-pe="all:K"`, marca `auto`) no obliga a llenar las cajas que siguen vacías. Se quitó el teclado numérico.
- **12/oct (cont. 2):** Modo piso → cada cliente muestra **todas** sus cajas como tarjetas en lugares fijos (`peSlots`, campo nuevo `b.ps` = lugar 1..N del cliente). Una caja se guarda sola cuando tiene piezas y categoría (`peTry`), queda en gris con ✔ y sigue en pantalla: se corrigen piezas (`.psc`, al salir del campo) o categoría (`data-pe="sk:seq:K"`) ahí mismo, o se quita con confirmación (`cf`/`rm`) y el lugar queda libre. Al completar, aparece un aviso arriba y las tarjetas no desaparecen. Ya no hay botón «Guardar cajas» en piso. La vista completa sigue con su tabla + «Guardar cajas».
- **12/oct (cont. 3):** el logo es un botón de inicio en ambos modos (`.lgb`: vista completa → `inicio`, piso → `piso`). El cambio de modo es un interruptor estilo iOS (`.iosrow`/`.iossw`, `role="switch"`, verde #34C759 encendido) en el menú lateral y en el encabezado de piso; al tocarlo, la perilla se desliza y luego toda la pantalla cambia con una transición suave (`switchMode`, View Transitions API; sin animación si el equipo pide movimiento reducido). Encabezado de piso translúcido con desenfoque, título grande y tarjetas con degradado y cuadros de ícono al estilo iOS.
- **12/oct (cont. 4):** al iniciar sesión, el Administrador siempre entra a la vista completa y el Encargado de piso al Modo piso (antes la preferencia del equipo hacía que el admin entrara en piso).
- **12/oct (cont. 5):** todos los avisos rojos llevan a donde se resuelven: chips «Faltan N · Empacar ›» (Panel, Empaque, Orden, Órdenes del embarque → cliente con cajas pendientes; `goLine`, `lineSt(n,t,go)`), «Sin líneas · Agregar ›» para órdenes vacías (antes decía «Faltan 0»), tarjetas KPI rojas del Panel con «Resolver ›», y cada renglón de la alerta del embarque con «Resolver ›» (`shipIssues` ahora devuelve `{t,go|jump}`; `data-jump` hace scroll al campo, lo enfoca y lo resalta). Los chips «NO SUMA» y «DISCREPANCIA» también llevan al campo a corregir.

## Correcciones del /review (12/oct/2026)
1. **Guardado fallido visible:** `save()` devuelve true/false; si la memoria se llena, aparece un aviso rojo fijo con «Descargar respaldo» y «Reintentar guardar», y ningún mensaje dice «Guardado» mientras falle (`SAVE_ERR`, `topBanners`).
2. **Acceso:** se quitaron los botones demo del inicio de sesión; las contraseñas se guardan como hash SHA-256 con el id del usuario (`pwHash`, `migrateUsers`, migración automática de datos viejos); aviso rojo mientras haya contraseñas de prueba; mínimo 6 caracteres; no se puede dejar sin Administrador. *Sigue siendo seguridad del lado del navegador: la protección real requiere Supabase Auth.*
3. **Fechas locales:** `dLoc()` reemplaza `toISOString()` (fecha de recepción, ETD sugerida, nombres de PDF, entrada en Modo piso).
4. **Respaldo seguro:** `E()` escapa también la comilla simple; el tanque se escapa al mostrarse; `cleanST()` revisa campo por campo cualquier respaldo antes de cargarlo (descarta código, imágenes no válidas, índices fuera de rango, respaldos sin administrador).
5. **Embarque cerrado:** ya no se pueden agregar líneas a un destino cuyo embarque está cerrado (aviso para reabrirlo o crear otra orden).
6. **Número de lote:** `newLotId()` con contador `ST.RN` que solo crece.
Pendientes sugeridos por la revisión: mover fotos a IndexedDB, índice de cajas por cliente (rendimiento), dividir `index.html` en módulos, guardar las pruebas automáticas en el repositorio.

## Rendimiento (/webperf, 12/oct/2026)
**Medición:** Lighthouse (lab) 99 celular / 100 computadora en la pantalla de entrada. Con datos simulados de un año (500 lotes, 37,400 cajas, 250 órdenes y embarques) y CPU frenada 4× (≈ tablet): Panel 7,943 → **37 ms**, Empaque 5,669 → **30 ms**, Modo piso 3,223 → **20 ms**, guardar una caja en piso 1,662 → **130 ms**.
**Cambios:**
- Índices por dibujo (`RX`, `rxLine`, `rxShip`, `rxMov`, `rxLinks`, `rxLot`, `rxRvd`): `render()` crea los índices una vez y `ordAssigned`, `boxesOf`, `bal`, `rcv`, `rvd`, `calc`, `shipLinks` y `R_` los usan; fuera del dibujo siguen calculando directo (los manejadores ven siempre datos frescos). Verificado: resultados idénticos al cálculo original con un año de datos. Se corrigió además un cálculo cuadrático en `calc()` (`rvd()` se rehacía por cada movimiento).
- Listas activas (`histList`, `HIST`, `data-hist`): Entrada, Órdenes, Panel, Empaque, Embarque y Mortalidad muestran solo lo abierto o reciente (lotes 30 días, órdenes y embarques 14 días); «Ver historial» abre de 100 en 100. El tablero de lotes muestra 12 por columna. El plan de carga del camión cuenta solo embarques abiertos. Las gráficas de mortalidad muestran los últimos 20.
**Pendiente:** `save()` reescribe todo (≈80 ms en tablet con un año): archivo de cerrados / IndexedDB / Supabase. Fotos a IndexedDB.

## Panel de Ajustes (12/oct/2026, con /spec)
Especificación aprobada en `SPEC.md`, plan en `tasks/plan.md`, pruebas en `pruebas/ajustes_t.py` (88 verificaciones) y `pruebas/e2e.py`.
- Botón **Ajustes** en el encabezado (vista completa y Modo piso); panel lateral `#setp` fuera de `#app` (abrirlo no redibuja ni borra lo capturado), foco atrapado, Esc / ✕ / tocar fuera cierran y el foco regresa al botón.
- **Tema:** `PREF.theme` → `data-theme` en `<html>`; sin elección sigue al equipo; script temprano en `<head>` evita el parpadeo; `color-scheme` por tema.
- **Texto:** `html{font-size:var(--app-font-size)}` (12–20 px, inicial 15); los ~105 `font-size` en px pasaron a `rem`; se quitó el `zoom` y los botones A A A (se migra `propesa-z`); campos de captura ≥16 px; íconos y logo no cambian. Se corrigieron desbordes a 20 px (avisos largos, tarjetas de embarque, selector de consignatario).
- **Ajustes (cambio):** la pantalla de atrás ya no se oscurece al abrir Ajustes (panel translúcido con desenfoque; en celular, hoja desde abajo de máx. 66% de alto). El tamaño del texto vuelve a 3 tamaños con deslizador: Normal 15 · Grande 17 · Muy grande 20 px (`FS_STEPS`, `fsSnap`); los valores guardados se ajustan al más cercano. 100 verificaciones en `pruebas/ajustes_t.py`.
- **Menú de perfil (12/oct):** la barra superior ya solo tiene el buscador y un círculo con la inicial (`avatarBtn`). Al tocarlo abre `#pmenu` (fuera de `#app`): nombre, correo y rol; *Usuarios y permisos* (solo admin → Administración), *Ajustes* y *Cerrar sesión*. Igual en Modo piso. Accesible (role=menu, flechas, Esc, foco de regreso). Pruebas: `pruebas/menu_t.py`.
- **Corrección:** el botón **Ajustes** regresa a la barra (junto al círculo) por accesibilidad; el menú del círculo queda con **Administración** (solo admin: usuarios, permisos, correos y respaldo) y **Cerrar sesión**.

## /ship · versión 1.0.0 (12/oct/2026)
Se preparó la app como versión final con el método /ship del plugin agent-skills (lista previa + revisiones de código, seguridad y pruebas).
- **Sin rastros de demo:** sin usuarios precargados (`seed()` con `U:[]`); primer uso = **Configuración inicial** (`setupView`/`setupSave`, `data-a="s0"`) que crea el Administrador o restaura un respaldo. Se quitaron textos «demo/simulado», la tabla técnica de PostgreSQL y los datos inventados de temperatura. «Restablecer demo» → «Borrar los datos de este equipo» (escribir BORRAR). Versión visible en el menú del perfil (`Versión 1.0.0`).
- **Temperatura real:** `ST.T`, formulario (`saveTemp`, 20–110 °F, al menos un valor), últimos 7 días con `histList('temp')`, admin elimina (`data-tqdel`), `cleanST` valida `T`.
- **Seguridad:** `vercel.json` con CSP (`default-src 'self'`, `object-src 'none'`, `connect-src 'self'`, `base-uri 'none'`…), HSTS, Permissions-Policy (cámara solo propia), COOP, `no-cache` para `index.html`. Verificado en `pruebas/csp_t.py` (sin violaciones; un script externo de prueba sí se bloquea). Lista de contraseñas fáciles prohibidas ampliada.
- **Accesibilidad:** Lighthouse 100 en 14 pantallas (`pruebas/accesibilidad.mjs`): botón rojo #b8321f, `--cor` #c23a26, `<th aria-label="Acciones">`, `aria-label` en conteo final, títulos h2 donde saltaban de h1 a h3.
- **Pruebas:** todo en `pruebas/` con usuarios de prueba propios (`FIX`); `sh pruebas/correr_todo.sh` = 12 grupos, 322 verificaciones, TODO BIEN. Rendimiento con un año de datos sin cambios (Panel 36 ms, guardar caja 138 ms con CPU 4×).
- **Documentos:** README de producción (primer uso, pruebas, actualizar y volver atrás, límites), `CHANGELOG.md`, manual de usuario actualizado (sin usuarios demo; Configuración inicial; Ajustes y menú del perfil; Temperatura; Informes de cierre; sección «Límites de la versión 1.0»).
- **Volver atrás:** Vercel → Deployments → versión anterior → Promote to Production. Respaldo `.json` antes de cada actualización.

## Documentación del repositorio (12/oct/2026)
Se agregaron `CLAUDE.md`, `AGENTS.md`, `.claude/settings.json` y la carpeta `docs/` (PRODUCTO, UX, BRAND, ARQUITECTURA, DATOS, DECISIONES, ROADMAP, PRUEBAS, SEGURIDAD, SUPABASE, FLUJO_DE_TRABAJO, HISTORIAL y referencias). Desde ahora, la bitácora vive en `docs/HISTORIAL.md` dentro del repositorio.
