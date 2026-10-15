# 1. Contexto y alcance

## Problema que resuelve

En un entorno con almacenes y fabricación, conocer la cantidad total de un producto no basta. Hace falta distinguir la variante, la ubicación, la procedencia del lote y el motivo de cada entrada o salida. También hay que relacionar el material extraído con una orden, el producto fabricado con su informe, y la mercancía entregada con una factura. Sin esas relaciones, es difícil responder qué hay físicamente, qué está en proceso, qué está comprometido y por qué cambió un saldo.

Rucair reúne esos procesos en una aplicación web con trazabilidad documental y consultas operativas. Está diseñada para usuarios con distintos roles, en escritorio y móvil.

## Evolución del alcance

La idea inicial consistía en tarjetas de estiba y movimientos sin las restricciones que después fueron necesarias. El primer cambio decisivo fue distinguir lotes por **origen de compra**, clasificado como proveedor o contenedor. Ese requisito hizo que el inventario ya no pudiera tratarse únicamente como una cantidad por producto: la tarjeta pasó a enlazar variante, almacén y origen, y las operaciones comenzaron a seleccionar tarjetas concretas.

Posteriormente, las necesidades del cliente ampliaron el sistema a almacenes, catálogo de productos y variantes, documentos de recepción y salida, producción en proceso, devoluciones, mermas, ajustes, informes de producto terminado, clientes, facturas, conduces y análisis. La documentación describe el resultado actual, sin atribuir fechas o un orden exacto a cambios que el historial revisado no permite confirmar.

## Capacidades actuales

- Catálogo de productos, categorías, unidades y variantes con atributos flexibles.
- Almacenes de materia prima, producción en proceso y producto terminado.
- Orígenes de procedencia por proveedor o contenedor.
- Tarjetas de estiba con saldo individual e historial de movimientos.
- Documentos operativos que originan o agrupan cambios de existencias.
- Órdenes de producción, requisitos de materiales, extracciones parciales y resultado producido.
- Facturas por cliente, cantidades pendientes y despachos parciales mediante conduces.
- Paneles de inventario, producción y facturación con filtros y comparaciones.
- Usuarios y permisos por rol.

## Alcance funcional y límites

El sistema controla **cantidades**, no una contabilidad financiera completa. En el panel de facturación se muestran cantidades facturadas, despachadas y pendientes; no importes ni pagos. Las reservas de producción y factura se calculan para informar disponibilidad, pero no constituyen un bloqueo persistente de existencias por almacén u origen. No se suman unidades de medida diferentes.

La trazabilidad de origen se conserva en la tarjeta de partida y en las asignaciones de material a producción. En fábrica existe un almacén de trabajo en proceso; su tarjeta puede agrupar material de una misma variante. Para interpretar la procedencia dentro de producción se consulta también la asignación que referencia la tarjeta de origen. Este es un límite importante al hablar de seguimiento por lote de extremo a extremo.

## Público de esta documentación

La explicación técnica está dirigida a desarrolladores que evalúan decisiones arquitectónicas, límites del modelo y flujos transaccionales. El resumen inicial también permite a empleadores y clientes entender el problema y el alcance sin acceder al código privado.
