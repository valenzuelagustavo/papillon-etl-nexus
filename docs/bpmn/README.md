# Modelado BPMN — Registro de facturas de consignación y notas de crédito

![Diagrama BPMN del proceso](proceso-registro-comprobantes.png)

**Archivos**
- `proceso-registro-comprobantes.bpmn`: modelo editable en BPMN 2.0 (se abre con bpmn.io, Camunda Modeler o cualquier herramienta compatible con BPMN 2.0).
- `proceso-registro-comprobantes.png` / `.svg`: el diagrama exportado.

## Contexto del proceso

La mayor parte del material que vende Papillon Libros S.A. llega **en consignación**: la editorial deja los libros en la librería y solo cobra lo que se vende. Por eso la factura no llega junto con la mercadería. Al cierre de cada mes, el encargado de Compras (Matías) declara a cada editorial lo vendido, y la editorial emite una factura por esas unidades. **Esa factura es el comprobante que procesa el middleware.**

Además, las editoriales envían **notas de crédito** por devoluciones de material comprado en firme o por errores en facturas anteriores. Esas notas siguen el mismo circuito de carga en Nexus.

## Descripción del proceso

| | |
|---|---|
| **Proceso** | Registro de facturas de consignación y notas de crédito en Nexus |
| **Tipo** | Proceso de apoyo (administrativo): asegura que la información de compras y stock que usa el negocio sea correcta |
| **Dueño del proceso** | Departamento Comercial y Compras (Matías) |
| **Entradas** | Declaración mensual de ventas; factura o nota de crédito en PDF de la editorial |
| **Salida** | Comprobante registrado en Nexus |
| **Disparadores** | Cierre de mes (evento de inicio de tiempo) y llegada de una nota de crédito (evento de inicio de mensaje) |

### Pools y lanes

| Pool | Lane | Qué representa |
|---|---|---|
| Editorial / Distribuidora | — | Actor externo (pool colapsado): recibe la declaración y emite facturas y notas de crédito |
| Papillon Libros S.A. | Comercial y Compras | Declara las ventas, carga los comprobantes, mantiene las equivalencias ISBN y pide correcciones a la editorial |
| | Middleware ETL (sistema) | Comportamiento automatizado del software propuesto |
| | Sistema Nexus | Sistema de gestión de la librería que recibe la importación |
| | Administración | Control documental: revisa los comprobantes con diferencias |

### Flujo normal

1. Al **cierre de mes**, Compras **elabora la declaración de ventas en consignación** y la **envía a la editorial**.
2. El proceso queda en espera hasta que llega la **factura** (evento intermedio de recepción de mensaje). Una **nota de crédito** inicia el proceso directamente, sin declaración previa.
3. Compras **carga el comprobante PDF** en el middleware.
4. El middleware **identifica la editorial y el tipo de comprobante**, **extrae los datos** y **resuelve los códigos editoriales a ISBN** con la tabla EquivalenciaISBN.
5. Según el tipo de comprobante, el middleware **concilia la factura con la declaración de ventas** o **registra las líneas de la nota de crédito como crédito**.
6. El middleware **valida los datos** y, si son válidos, **genera el XLSX**. Nexus **importa el comprobante** y el proceso termina en *Comprobante registrado*.

### Compuertas de decisión (excepciones)

| Compuerta | Respuestas | Qué pasa |
|---|---|---|
| ¿Formato reconocido? | Sí / No | Si la editorial no tiene reglas de extracción, el comprobante pasa a revisión manual en Administración. |
| ¿Tipo de comprobante? | Factura / Nota de crédito | La factura se concilia con lo declarado. La nota de crédito se registra como crédito. |
| ¿Resultado de la validación? | Válido / Códigos sin equivalencia / Con diferencias | Una sola compuerta con tres salidas, en lugar de compuertas anidadas sobre la misma pregunta. Los códigos sin equivalencia vuelven a Compras para registrar el ISBN y se reprocesan. Las diferencias (con la declaración o en los totales) van a revisión manual. |
| ¿Origen de la diferencia? | Error de lectura / Error de la editorial | Si el error es de extracción, Administración corrige los datos y se regenera el XLSX. Si el error es de la editorial (por ejemplo, facturó unidades no declaradas), Compras pide la corrección o refacturación (fin: *Corrección solicitada*). |
| ¿Importación exitosa? | Sí / No | Si Nexus rechaza el archivo, Administración revisa el comprobante. |

La editorial responde a una solicitud de corrección con una factura corregida o una nota de crédito, que vuelve a entrar al proceso como un comprobante nuevo.

### Convenciones aplicadas (apunte de la Unidad 5)

- Diagramado de izquierda a derecha, desde la esquina superior izquierda.
- Un pool por proceso. La editorial se modela como pool colapsado (actor externo). Entre pools solo hay líneas de mensaje (punteadas), nunca líneas de secuencia.
- Eventos de inicio de tiempo y de mensaje, y un evento intermedio de recepción de mensaje para la espera de la factura.
- Actividades nombradas con verbo en infinitivo y tipificadas (usuario, servicio, script, regla de negocio, envío), porque el proceso se modela para automatizarlo.
- Compuertas exclusivas sin el marcador "X", con la pregunta debajo y todas las salidas etiquetadas. Se usa "Sí/No" solo cuando esas son las respuestas reales. Antes de cada compuerta hay una tarea que evalúa la decisión.
- Data objects (declaración de ventas, comprobante PDF, tabla EquivalenciaISBN, archivo XLSX) unidos con líneas de asociación, y anotaciones que vinculan el diagrama con las Historias de Usuario y las metas.

### Relación con el resto del TPI

| Elemento del diagrama | Historia de Usuario | Meta (Entrega 2) | Entidad (Entrega 3) |
|---|---|---|---|
| Cargar comprobante, generar XLSX, importar en Nexus | HU-01 | M1, M4 | Comprobante, DetalleComprobante |
| Resolver códigos a ISBN, registrar equivalencia | HU-02 | M2 | EquivalenciaISBN |
| Revisión manual y estados del comprobante | HU-03 | M2, M4 | Comprobante (`estado`, `tipo_comprobante`) |
