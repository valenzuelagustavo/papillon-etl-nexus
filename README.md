# Papillon ETL Nexus

**Middleware ETL de extracción y transformación de datos documentales (PDF → XLSX) para el sistema de gestión Nexus — Caso Papillon Libros S.A.**

Trabajo Práctico Integrador y Progresivo (TPI) · Organización Empresarial
Tecnicatura Universitaria en Programación a Distancia (TUP AD) · Universidad Tecnológica Nacional

| | |
|---|---|
| **Alumno** | Gustavo Enrique Valenzuela |
| **Docente titular** | Prof. Gabriela Martínez |
| **Docentes adjuntos** | Prof. Carolina Bruno · Prof. Mario Raúl López · Prof. Andrea Ramos |
| **Tablero de gestión** | _[link a GitHub Projects — completar]_ |

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

_[Se completa en la rama `docs/declaracion-ia`.]_
