# Indicadores de control y estándares de desempeño

El control verifica que lo ejecutado coincida con lo planificado y corrige los desvíos, en cuatro pasos: **establecer estándares → medir → comparar → corregir**. Desde la Teoría General de Sistemas, es el mecanismo de retroalimentación del sistema. Los estándares parten de las metas M1 a M4 (Entrega N.º 2) y se completan con indicadores del proceso modelado en BPMN (`docs/bpmn/`). Como referencia de volumen, la librería recibe unos 440 comprobantes de editoriales por año.

## Indicadores

| Indicador | Fórmula / medición | Estándar | Frecuencia y fuente | Acción correctiva ante desvío |
|---|---|---|---|---|
| **I1.** Tiempo de procesamiento por lote (M1) | Segundos desde la carga hasta la generación del XLSX, para lotes de hasta 10 PDF | < 15 s | Cada lote · log del middleware | Perfilar el módulo de extracción y optimizar el parsing del formato más lento. |
| **I2.** Precisión de extracción (M2) | Campos clave correctos / campos verificados × 100, sobre una muestra auditada a mano | ≥ 98 % | Mensual · muestra de 20 comprobantes | Ajustar las reglas de extracción de la editorial con más errores y sumar el caso a las pruebas. |
| **I3.** Importación exitosa en Nexus (M4) | XLSX importados sin error / XLSX generados × 100 | 100 % | Cada importación · resultado de Nexus | Revisar el esquema del XLSX contra el formato vigente de Nexus. |
| **I4.** Tasa de derivación a revisión manual | Comprobantes en estado Pendiente o Error / comprobantes procesados × 100 | ≤ 10 % | Mensual · estados del Comprobante | Crear reglas para los formatos nuevos o completar la tabla de equivalencias. |
| **I5.** Cobertura de equivalencias ISBN | Líneas resueltas automáticamente / total de líneas con código editorial × 100 | ≥ 95 % | Mensual · DetalleComprobante | Cargar en bloque las equivalencias faltantes de la editorial con menor cobertura. |
| **I6.** Liquidaciones con diferencias respecto de la declaración *(mejora propuesta)* | Liquidaciones con diferencias / liquidaciones recibidas × 100 | ≤ 5 % (referencia) | Mensual · conciliación | Analizar si el origen es interno o de la editorial, y reclamar la refacturación. |
| **I7.** Tiempo de ciclo de registro | Días entre la recepción del comprobante y su registro en Nexus | ≤ 2 días hábiles | Por comprobante · fechas del Comprobante | Priorizar los pendientes y revisar la carga de trabajo de Administración. |
| **I8.** Cumplimiento del plan del proyecto (M3) | Horas reales / 80 h-h planificadas; avance real vs. Gantt | Desvío ≤ 10 % | Semanal · tablero y Gantt | Reprogramar tareas no críticas o ajustar el alcance de la versión inicial. |
| **I9.** Diferencias entre notas de crédito y remitos de devolución | Unidades faltantes + sobrantes / unidades devueltas según remitos × 100 | 0 % | Por nota de crédito · cruce con remitos | Reclamar a la editorial las unidades no acreditadas o revisar el remito de devolución. |
| **I10.** Títulos con aumento de precio | Títulos cuyo precio subió más del 15 % desde la última compra / títulos facturados × 100 | Se revisa el 100 % de los avisos | Cada comprobante · historial de precios | Compras verifica el aumento contra la lista de la editorial antes de importar. |

## Tipos de control

- **Preventivo (antes):** validaciones del middleware antes de generar el XLSX: formato reconocido, descarte de copias (Original/Duplicado/Triplicado en el mismo PDF), unificación de renglones repetidos sin alterar cantidad ni importe, verificación precio × cantidad × (1 − descuento) contra el importe impreso, aviso de descuentos al pie, ISBN resuelto, cruce de notas de crédito con remitos de devolución y conciliación de liquidaciones con la declaración.
- **Concurrente (durante):** los estados del comprobante (Pendiente, Procesado, Error, Anulado) permiten seguir cada documento en tiempo real.
- **Posterior o de retroalimentación (después):** la auditoría mensual de precisión (I2) y el análisis de I4 a I7 alimentan la mejora de las reglas y de la tabla de equivalencias.

## Estándares para la auditoría técnica

- **Trazabilidad:** cada comprobante conserva su PDF, su tipo, su estado y el motivo de cualquier derivación.
- **Registro de ejecución:** fecha, duración y resultado de cada lote (fuente de I1, I3 e I4).
- **Control de cambios:** toda modificación pasa por una rama y un Pull Request revisado antes de llegar a `main`.
- **Responsables:** Compras controla I5, I6, I8, I9 e I10; Administración controla I2, I4 e I7; el desarrollador controla I1 e I3.
