# 9. Evolución, decisiones y límites

## Historia del producto

La evolución conocida parte de la explicación del autor: tarjetas de estiba y movimientos inicialmente sin las restricciones posteriores; después, separación de lotes por origen de compra —proveedor o contenedor—; más tarde, ampliación continua por solicitudes del cliente hasta el sistema actual. El código permite describir **qué existe ahora**, pero no reconstruir con certeza cuándo se pidió cada función ni qué alternativas se descartaron. Por eso esta sección evita una cronología inventada.

## Decisiones arquitectónicas visibles

La retrospectiva se amplió el 15 de octubre de 2026 con un aprendizaje comunicado directamente por el autor: la organización del frontend por tipo de archivo dificultó navegar y completar cambios de una misma funcionalidad al crecer el proyecto. Para próximos desarrollos prefiere agrupar componentes, servicios, esquemas y estado por módulo, con infraestructura compartida fuera de ellos. La [decisión de arquitectura frontend](10-decision-frontend-por-modulos.md) desarrolla esa preferencia, sus beneficios esperados y sus costes. Se trata de una reflexión sobre mantenibilidad; no de una migración aplicada ni de una mejora de rendimiento medida.

| Decisión | Necesidad que atiende | Consecuencia |
| --- | --- | --- |
| Tarjeta como unidad de seguimiento | Consultar inventario por variante, ubicación y procedencia. | Las operaciones deben seleccionar tarjeta concreta. |
| Origen separado de la tarjeta | Reutilizar y clasificar procedencias. | Un origen no identifica por sí solo un lote único. |
| Documentos y movimientos relacionados | Justificar cambios de cantidad con una operación de negocio. | Una línea documental puede producir más de un movimiento. |
| Saldos por tarjeta y por almacén | Lecturas rápidas en varios niveles. | Cada escritura y cancelación debe mantener coherencia entre resúmenes. |
| WIP con tarjeta de origen | Registrar extracciones parciales y procedencia de insumos. | Para rastrear material en fábrica se consultan asignaciones, no solo el saldo WIP. |
| Factura separada del conduce | Permitir entregas parciales. | El estado comercial depende de cantidades acumuladas de despachos activos. |
| Servicios transaccionales | Agrupar operaciones que cruzan modelos. | Las reglas se reparten entre servicio, modelo, señal y permiso; los cambios requieren revisión integral. |
| Panel con reservas calculadas | Mostrar compromisos y faltantes sin registrar asignaciones por lote. | La disponibilidad es informativa, no bloqueo operativo. |

## Límites que conviene comunicar

1. **Identidad de lote:** el origen clasifica procedencia, pero no hay una clave única de lote en el esquema de tarjetas ni una restricción única para variante + almacén + origen.
2. **Conciliación:** los saldos resumidos pueden contrastarse con la información operativa; el panel avisa de discrepancias, pero no reescribe datos automáticamente.
3. **Reservas:** los compromisos de órdenes y facturas se calculan a nivel de variante; no se asignan de forma persistente a una tarjeta, almacén u origen.
4. **Producción:** el modelo actual expresa un objetivo de producto por orden y requisitos de materiales. No debe presentarse como motor genérico de recetas multicomponente o planificación industrial sin revisión adicional.
5. **Facturación:** registra cantidades y estado de entrega. No representa importes, pagos o contabilidad completa.
6. **Operación:** los archivos definen despliegue, backups y CI; su ejecución en producción necesita evidencia del entorno, fuera del alcance de esta revisión.

## Preguntas para una próxima revisión con el autor

- ¿Qué hito o solicitud llevó a introducir producción, facturación y el panel, y cuáles fueron los problemas operativos más importantes que resolvieron?
- ¿Qué significa exactamente «lote» para el cliente: una recepción, una tarjeta, un contenedor/proveedor o una identificación externa adicional?
- ¿Qué reglas deben impedir una operación y cuáles deben seguir siendo solo avisos de disponibilidad?
- ¿Existe una necesidad de trazabilidad de procedencia del insumo hasta cada unidad de producto terminado?
- ¿Qué volumen de productos, movimientos y usuarios se espera para dimensionar la analítica?
- ¿Qué evidencias del despliegue, restauración de backups y uso real pueden publicarse sin revelar información privada?

Estas respuestas permitirían enriquecer la historia de decisiones y añadir casos de uso reales anonimizados. La documentación actual ya distingue hechos implementados de esas preguntas abiertas.
