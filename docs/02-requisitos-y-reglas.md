# 2. Requisitos y reglas de negocio

Esta sección separa capacidades observables de garantías de integridad. Una validación en un servicio o una pantalla se describe como regla del flujo correspondiente; solo se llama **restricción de base de datos** cuando el modelo la declara.

## Inventario y procedencia

| Necesidad | Respuesta del sistema |
| --- | --- |
| Identificar qué material existe | Producto, variante y unidad de medida. |
| Distinguir ubicación | Almacén con tipo y saldo agregado por variante. |
| Distinguir procedencia de compra | Origen de tipo proveedor o contenedor, asociado a la tarjeta. |
| Consultar un lote operativo | Tarjeta de estiba con variante, almacén, origen, saldo e historial. |
| Explicar un cambio de cantidad | Movimiento con tipo, fecha, cantidad, saldo posterior y posible línea documental. |
| Conservar historial al retirar un registro | Borrado lógico de tarjetas y orígenes en los flujos de API. |

En los flujos revisados, las cantidades de movimientos deben ser positivas. Para productos medidos en unidades se exige una cantidad entera. Las salidas validan el saldo de la tarjeta, y el saldo de tarjeta tiene una restricción de base de datos que impide valores negativos. El saldo agregado por almacén se actualiza en las operaciones y se puede contrastar con la suma de tarjetas.

**Matiz de identidad del lote:** la combinación variante + almacén + origen se usa para buscar tarjetas en algunos servicios, pero el modelo no declara una restricción única para esa terna. La recepción crea una tarjeta nueva por línea. Por tanto, varias tarjetas pueden compartir esa combinación y la procedencia no debe confundirse con un identificador único de lote.

## Documentos y producción

La recepción requiere almacén destino y origen. Los documentos pueden representar recepción, vale de salida, merma, ajuste, devolución, informe de producción terminada o conduce. Un documento tiene líneas; una línea puede relacionarse con varios movimientos, por ejemplo una salida de materia prima y su entrada a fábrica.

Una orden de producción registra producto a obtener, cantidad prevista, almacén fuente, fábrica, estado y, opcionalmente, cliente. Los requisitos describen material necesario. Las extracciones se registran por tarjeta de origen y pueden ser parciales; la asignación en proceso guarda cantidad extraída y cantidad consumida. Al cancelar una orden se contempla la devolución de material extraído que aún no fue consumido.

En el servicio de creación revisado, la suma de cantidades requeridas debe coincidir con la cantidad prevista de producto final. Esta es una regla implementada para ese flujo; no se presenta como receta universal de fabricación.

## Facturación y despacho

Una factura pertenece a un cliente y contiene variantes con cantidades. Las líneas admitidas corresponden a producto terminado o material de venta directa. Una misma variante aparece una sola vez por factura: esto sí tiene restricción única de base de datos. La factura puede despacharse en varios conduces. Cada línea de conduce debe apuntar a una línea de esa misma factura y a la variante correspondiente.

El despacho valida la cantidad pendiente de la factura y el saldo de la tarjeta seleccionada. La cantidad despachada por línea se recalcula desde documentos de despacho activos; el estado de la factura deriva de sus líneas: pendiente, parcial, completo o anulada. Una factura con cantidades ya despachadas no se puede anular desde la acción de API revisada.

## Disponibilidad y análisis

El panel separa existencias fuera de fábrica, existencias en fábrica, compromisos pendientes de producción y pendientes de factura. La disponibilidad informativa se calcula **por variante**: máximo de existencia fuera de fábrica menos compromisos pendientes, con piso cero. El faltante tampoco se compensa con sobrantes de otra variante. Si se filtra por almacén u origen, el saldo físico mostrado cambia; las reservas permanecen globales porque el modelo no las asigna de forma persistente a un almacén.

Los períodos de los gráficos se aplican a la actividad del período. Los pendientes actuales de facturas incluyen compromisos anteriores. Los gráficos excluyen documentos anulados o movimientos revertidos según la consulta correspondiente. Las unidades distintas se presentan por separado.

## Roles y acceso

La API requiere autenticación por defecto y usa una clase global de permisos por rol, complementada con permisos de vistas concretas. Los roles definidos son administrador, almacenero, usuario, gestor y contadora. Administrador y almacenero reciben acceso amplio en la regla global; gestor puede escribir en producción y facturación; contadora puede escribir en clientes y facturas según el modelo de la vista; las consultas seguras se permiten a usuarios autenticados. Acciones específicas pueden imponer reglas adicionales, como la ventana de 24 horas para que el almacenero cancele un movimiento.

La navegación del frontend restringe rutas por rol para orientar la experiencia. **La decisión de acceso real corresponde a la API**, no a la cookie de rol ni a ocultar una página.
