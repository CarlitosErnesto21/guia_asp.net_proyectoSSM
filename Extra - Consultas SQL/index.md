# Esta consulta se utilizó para generar una tabla visual, que se haría uso en la implementación de un PDF.

**c = clientes**

**p = pedidos**

**dp = detalle_pedidos**

**pr = productos**

```
SELECT
    p.fecha_pedido,
    c.nombre,
    c.email,
    p.estado,
    pr.nombre AS producto,
    pr.precio AS precio_unitario,
    dp.cantidad,
    dp.total
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.id
JOIN detalle_pedidos dp ON p.id = dp.pedido_id
JOIN productos pr ON dp.producto_id = pr.id
ORDER BY p.fecha_pedido DESC;
```
# Esta otra consulta, contiene la misma información, con la diferencia que, muestra el estado específico de los registros.

Estado: **WHERE p.estado = 'pendiente'**, lo que significa que entre las '' puede llevar otro estado como: **'cancelado'**, **'en preparacion'** o **'entregado'**.

```
SELECT
    p.fecha_pedido,
    c.nombre,
    c.email,
    p.estado,
    pr.nombre AS producto,
    pr.precio AS precio_unitario,
    dp.cantidad,
    dp.total
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.id
JOIN detalle_pedidos dp ON p.id = dp.pedido_id
JOIN productos pr ON dp.producto_id = pr.id
WHERE p.estado = 'pendiente'
	ORDER BY p.fecha_pedido DESC;
```
