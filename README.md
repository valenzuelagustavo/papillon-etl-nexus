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

Papillon Libros S.A. es una librería fundada en 1972 que administra su inventario y facturación con el sistema de gestión **Nexus**. Nexus permite importar el ingreso de stock mediante archivos Excel (`.xlsx`) con ISBN, cantidad, precio y descuento, pero las editoriales y distribuidoras envían sus facturas, remitos y notas de crédito en **PDF**, y muchas veces con **códigos internos** en lugar de ISBN.

Hoy esa información se transcribe a mano (o con IA generativa online), lo que genera demoras, errores de tipeo y datos inconsistentes en el stock.

**Papillon ETL Nexus** es un módulo de software local que actúa como puente entre los proveedores y Nexus:

1. **Extrae** los datos de cada comprobante PDF según el formato del proveedor.
2. **Transforma** los códigos editoriales en ISBN mediante una tabla de equivalencias y normaliza cantidades, precios y descuentos.
3. **Carga** el resultado en un archivo XLSX listo para importar en Nexus.
4. **Deriva a revisión manual** los documentos que no tienen un formato reconocido o contienen códigos sin equivalencia.

## Metas del sistema

| ID | Meta |
|---|---|
| M1 | Procesar un lote de hasta 10 facturas PDF en menos de 15 segundos. |
| M2 | Precisión ≥ 98 % en los campos clave (ISBN, título, precio, cantidad, impuestos). |
| M3 | Completar el ciclo de desarrollo en ≤ 6 semanas y ≤ 80 horas-hombre. |
| M4 | 100 % de los XLSX generados importados en Nexus sin errores de formato. |

## Contenido del repositorio

| Carpeta / archivo | Contenido |
|---|---|
| `docs/historias-usuario.md` | Historias de usuario del backlog ágil. |
| `docs/bpmn/` | Modelado BPMN del proceso de ingreso de mercadería. |
| `docs/indicadores-control.md` | Indicadores de control y estándares de desempeño. |
| `docs/diccionario-datos.md` | Entidades EquivalenciaISBN, Comprobante y DetalleComprobante. |

## Flujo de trabajo con Git

- `main` contiene solo versiones revisadas.
- Cada entregable se desarrolla en una rama `docs/<tema>` y se integra mediante Pull Request con revisión.
- Los mensajes de commit siguen el formato `tipo: descripción` (por ejemplo, `docs: agrega historias de usuario del backlog`).

## Declaración de Uso de IA

_[Se completa en la rama `docs/declaracion-ia`.]_
