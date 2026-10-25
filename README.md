# Papillon ETL Nexus

**Middleware ETL de extracción y transformación de datos documentales (PDF → XLSX) para el sistema de gestión Nexus — Caso Papillon Libros S.A.**

Trabajo Práctico Integrador y Progresivo (TPI) · Organización Empresarial
Tecnicatura Universitaria en Programación a Distancia (TUP AD) · Universidad Tecnológica Nacional

| | |
|---|---|
| **Alumno** | Gustavo Enrique Valenzuela |
| **Docente titular** | Prof. Gabriela Martínez |
| **Docentes adjuntos** | Prof. Carolina Bruno · Prof. Mario Raúl López · Prof. Andrea Ramos |
| **Repositorio** | [valenzuelagustavo/papillon-etl-nexus](https://github.com/valenzuelagustavo/papillon-etl-nexus) |
| **Tablero de gestión** | [Papillon ETL Nexus - Backlog](https://github.com/users/valenzuelagustavo/projects/1) |

---

## Descripción del sistema

Papillon Libros S.A. es una librería fundada en 1972 que administra su inventario y facturación con el sistema de gestión **Nexus**.

La mayor parte del material que vende llega **en consignación**: la editorial deja los libros en la librería y solo cobra lo vendido. Al cierre de cada mes, el encargado de Compras declara a cada editorial las unidades vendidas, y la editorial emite una **factura** por ese detalle. Además, las editoriales envían **notas de crédito** por devoluciones de material comprado en firme o por errores en facturas anteriores.

Nexus permite importar comprobantes mediante archivos Excel (`.xlsx`) con ISBN, cantidad, precio y descuento. Pero las editoriales envían sus facturas y notas de crédito en **PDF**, y muchas veces con **códigos internos** en lugar de ISBN. Hoy esos datos se transcriben a mano (o con IA generativa online), lo que genera demoras, errores de tipeo y diferencias difíciles de detectar entre lo declarado y lo facturado.

**Papillon ETL Nexus** es un módulo de software local que actúa como puente entre las editoriales y Nexus:

1. **Extrae** los datos de cada factura o nota de crédito en PDF según el formato de la editorial.
2. **Transforma** los códigos editoriales en ISBN mediante una tabla de equivalencias, normaliza cantidades, precios y descuentos, y **concilia** cada factura con la declaración de ventas del mes.
3. **Carga** el resultado en un archivo XLSX listo para importar en Nexus.
4. **Deriva a revisión manual** los comprobantes con formato no reconocido, códigos sin equivalencia o diferencias.

## Metas del sistema

| ID | Meta |
|---|---|
| M1 | Procesar un lote de hasta 10 comprobantes PDF en menos de 15 segundos. |
| M2 | Precisión ≥ 98 % en los campos clave (ISBN, título, precio, cantidad, impuestos). |
| M3 | Completar el ciclo de desarrollo en ≤ 6 semanas y ≤ 80 horas-hombre. |
| M4 | 100 % de los XLSX generados importados en Nexus sin errores de formato. |

## Contenido del repositorio

| Carpeta / archivo | Contenido |
|---|---|
| `docs/historias-usuario.md` | Historias de usuario del backlog ágil. |
| `docs/bpmn/` | Modelado BPMN del proceso de registro de facturas de consignación y notas de crédito. |
| `docs/indicadores-control.md` | Indicadores de control y estándares de desempeño. |

## Flujo de trabajo con Git

- `main` contiene solo versiones revisadas.
- Cada entregable se desarrolla en una rama `docs/<tema>` y se integra mediante Pull Request con revisión.
- Los mensajes de commit siguen el formato `tipo: descripción` (por ejemplo, `docs: agrega historias de usuario del backlog`).

## Declaración de Uso de IA

Para este trabajo utilicé un asistente de IA generativa (Claude, de Anthropic) como apoyo en la estructuración de la documentación.

### Prompts utilizados y propósito

| Prompt (resumido) | Propósito y justificación |
|---|---|
| "Chequeá mis anteriores entregas para comenzar a trabajar en la cuarta entrega." | Revisar la coherencia entre las entregas 1 a 3 y la consigna 4. Permitió detectar que las Historias de Usuario no estaban definidas en ninguna entrega previa. |
| Pedidos de nombre del repositorio, texto del README y descripción de los Pull Requests. | Ordenar la portada y los mensajes según convenciones profesionales. Las operaciones de Git (ramas, commits, push y merge) las realicé yo con mi cuenta. |
| "Generame el diagrama del proceso organizativo con la notación BPMN en el estilo descripto por la teoría de la materia", adjuntando los apuntes de la Unidad 5. | Producir un modelo que respete las convenciones de la cátedra. |
| Redacción de Historias de Usuario, indicadores de control y el documento de la entrega. | Proponer una estructura inicial que luego revisé y ajusté al caso real. |

### Correcciones humanas sobre los resultados

- **Rediseño del proceso BPMN.** La primera versión asumía que la factura llegaba con la mercadería y que Depósito hacía un control físico. Corregí el modelo con el circuito real: material en consignación, declaración mensual de ventas, factura emitida por la editorial sobre lo declarado, y notas de crédito por devoluciones en firme o por errores de facturación. El diagrama se rehízo por completo.
- **Ajuste de la HU-01 y de este README.** Hablaban de "facturas y remitos" e "ingreso de stock"; los adapté al circuito de consignación y agregué la conciliación contra la declaración de ventas.
- **Tratamiento de las notas de crédito y del tipo de comprobante.** El diagrama incluía una tarea para "registrar las líneas como crédito", que suponía transformar las cantidades. En Nexus todos los comprobantes se cargan con cantidades enteras positivas, y el código de tipo de comprobante (por ejemplo, 10 para factura en firme o 19 para liquidación de venta) define el movimiento de stock. Ese código lo indica Compras al importar. Eliminé esa tarea, asigné la importación a Compras y sumé las facturas en firme al circuito.
- **Controles reales del proceso.** Agregué al modelo el cruce de las notas de crédito contra los remitos de devolución y, a los indicadores, controles que se usan en la práctica (descarte de copias duplicadas, unificación de renglones, aviso de aumentos de precio). La conciliación de la liquidación contra la declaración de ventas quedó señalada como mejora propuesta por el proyecto.
- **Revisión de pares real.** Los Pull Requests los revisó un compañero de la cursada con su propia cuenta.

### Reflexión

La IA fue útil para estructurar y aplicar las convenciones de notación, pero completó con supuestos genéricos lo que no sabía del negocio. Sin la revisión basada en el conocimiento real de la librería, se habría modelado un proceso inexistente. La responsabilidad intelectual sobre todo el contenido es mía.
