# Historias de Usuario — Papillon ETL Nexus

Backlog del Middleware ETL de Papillon Libros S.A. Los usuarios de cada historia salen de la estructura formal definida en la Entrega N.º 3: el Departamento Comercial y Compras recibe la mercadería y la documentación de los proveedores, y el Departamento de Administración hace el control documental y la facturación.

Cada historia se vincula con las metas cuantitativas de la Entrega N.º 2 (M1 a M4) y con las entidades del diccionario de datos de la Entrega N.º 3.

| ID | Historia | Usuario | Prioridad | Metas |
|---|---|---|---|---|
| HU-01 | Convertir facturas y notas de crédito PDF a XLSX para Nexus | Compras | Alta | M1, M4 |
| HU-02 | Resolver códigos editoriales a ISBN | Compras | Alta | M2 |
| HU-03 | Derivar comprobantes con problemas a revisión manual | Administración | Media | M2, M4 |

---

## HU-01 · Convertir facturas y notas de crédito PDF a XLSX para Nexus

**Como** encargado del Departamento Comercial y Compras,
**quiero** cargar las facturas de liquidación, las facturas en firme y las notas de crédito en PDF que envían las editoriales y obtener un archivo XLSX con el formato de importación de Nexus,
**para** registrarlas en el sistema sin transcribir los datos a mano.

**Criterios de aceptación**
- [ ] Se puede seleccionar uno o varios PDF a la vez (hasta 10 por lote).
- [ ] El sistema identifica la editorial por el encabezado o por el formato de la tabla, y el tipo de comprobante (liquidación de venta, factura en firme o nota de crédito).
- [ ] Si el PDF trae Original, Duplicado y Triplicado, los ejemplares se cargan una sola vez.
- [ ] Los renglones repetidos de un mismo código se unifican en una sola línea, sin alterar la cantidad total ni el importe neto.
- [ ] Cada renglón se valida: precio × cantidad × (1 − descuento) debe coincidir con el importe impreso.
- [ ] Las notas de crédito se cruzan contra los remitos de devolución y se informan faltantes y sobrantes.
- [ ] Las cantidades de cada liquidación de venta se concilian contra la declaración de ventas del mes y las diferencias se informan.
- [ ] Se avisa qué títulos aumentaron de precio más del 15 % desde la última compra.
- [ ] El XLSX generado tiene las columnas que exige Nexus (ISBN, cantidad, precio y descuento) y las cantidades siempre son enteros positivos.
- [ ] Al importar en Nexus, Compras indica el código de tipo de comprobante (por ejemplo, 10 para factura en firme y 19 para liquidación de venta), que define el movimiento de stock.
- [ ] Un lote de 10 comprobantes se procesa en menos de 15 segundos (M1).
- [ ] El archivo se importa en Nexus sin errores de formato, delimitadores ni tipos de datos (M4).

**Entidades involucradas:** Comprobante, DetalleComprobante.

---

## HU-02 · Resolver códigos editoriales a ISBN

**Como** encargado del Departamento Comercial y Compras,
**quiero** que el sistema reemplace los códigos internos de cada editorial por el ISBN correspondiente usando la tabla de equivalencias,
**para** que el stock en Nexus quede registrado con el ISBN correcto aunque el proveedor no lo informe.

**Criterios de aceptación**
- [ ] Cada línea con código editorial se cruza con la tabla EquivalenciaISBN según la editorial de origen.
- [ ] Si el código ya es un ISBN válido de 13 dígitos, se conserva sin cambios.
- [ ] Los códigos sin equivalencia se informan en un listado y no se inventa ningún ISBN.
- [ ] Se puede agregar una equivalencia nueva y queda registrada su fecha de actualización.
- [ ] La precisión de los campos clave es igual o mayor al 98 % (M2).

**Entidades involucradas:** EquivalenciaISBN, DetalleComprobante.

---

## HU-03 · Derivar comprobantes con problemas a revisión manual

**Como** responsable del Departamento de Administración,
**quiero** que los comprobantes con formato no reconocido o con datos inconsistentes queden marcados para revisión manual, con el motivo indicado,
**para** controlar que ningún documento se importe en Nexus con errores.

**Criterios de aceptación**
- [ ] Un PDF de un proveedor sin reglas de extracción definidas queda en estado "Error" con el motivo "Formato no reconocido".
- [ ] Un comprobante con códigos sin equivalencia o con totales que no coinciden queda en estado "Pendiente" de revisión.
- [ ] Los comprobantes marcados no se incluyen en el XLSX hasta que se revisan.
- [ ] Se puede consultar el listado de comprobantes por estado (Pendiente, Procesado, Error, Anulado).

**Entidades involucradas:** Comprobante (campo `estado`).
