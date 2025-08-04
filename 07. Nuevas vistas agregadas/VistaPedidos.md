# A continuación se muestra la vista de Pedidos para mostrar una lista de datos.

## 1. Se crea la función en AccesoController:
```
public async Task<IActionResult> ListPedidos()
{
    return View(await _context.Pedidos.ToListAsync());
}
```
## 2. En carpeta "Acceso" se crea una nueva vista "ListPedidos.cshtml":
```
@model IEnumerable<migajas_amor.app.Models.Pedido>

@{
    ViewData["Title"] = "Pedidos ";
}

<h2>Pedidos Recibidos</h2>

<table class="table table-bordered">
    <thead>
        <tr>
            <th>Cliente</th>
            <th>Producto</th>
            <th>Cantidad</th>
            <th>Fecha De Pedido</th>
            <th>Estado</th>
            <th>Direcion De Entrega</th>
            <th>Telefono</th>
            <th>Comentarios</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var pedido in Model)
        {
            <tr>
                <td>@pedido.Cliente</td>
                <td>@pedido.Producto</td>
                <td>@pedido.Cantidad</td>
                <td>@pedido.FechaPedido</td>
                <td>@pedido.Estado</td>
                <td>@pedido.DireccionEntrega</td>
                <td>@pedido.Telefono</td>
                <td>@pedido.Comentarios</td>
            </tr>
        }
    </tbody>
</table>

```
## 3. En el archivo _Layout.cshtml, se agrega un nuevo nav-link para Pedidos:
```
<li class="nav-item">
    <a class="nav-link text-dark" asp-area="" asp-controller="Acceso" asp-action="ListPedidos">Pedidos</a>
</li>
```
