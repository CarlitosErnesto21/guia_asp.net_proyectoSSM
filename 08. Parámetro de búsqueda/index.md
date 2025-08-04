# A continuación se muestra la implementación de un parámetro de búsqueda de forma global, para las listas (tablas) generadas.

## 1. En AccesoController, verificar estos cambios para los métodos correspondientes.
**Instrucciones agregadas para el párametro de búsqueda de la función de Listar Usuarios por Rol.**

```
 [Authorize(Roles = "Administrador")]
 public async Task<IActionResult> ListUsuarioRol(string? search)
 {
     var usuarios = await _context.Usuarios
         .Select(u => new UsuarioRol
         {
             UsuarioId = u.Id,
             Login = u.Login,
             Roles = (from rlsa in _context.Rolesasignados
                      join r in _context.Roles on rlsa.RolId equals r.Id
                      where rlsa.UsuarioId == u.Id
                      select r.Nombre).ToList()
         })
         .ToListAsync();

     if (!string.IsNullOrWhiteSpace(search))
     {
         usuarios = usuarios
             .Where(u => u.Login.Contains(search, StringComparison.OrdinalIgnoreCase)
                      || u.Roles.Any(r => r.Contains(search, StringComparison.OrdinalIgnoreCase)))
             .ToList();
     }

     return View(usuarios);
 }

```

**Instrucciones agregadas para el párametro de búsqueda de la función de Listar los Pedidos.**

```
 public async Task<IActionResult> ListPedidos(string? search)
 {
     var pedidos = await _context.Pedidos.ToListAsync();

     if (!string.IsNullOrWhiteSpace(search))
     {
         pedidos = pedidos
             .Where(p => (p.Cliente?.Contains(search, StringComparison.OrdinalIgnoreCase) ?? false)
                      || (p.Producto?.Contains(search, StringComparison.OrdinalIgnoreCase) ?? false)
                      || (p.Estado?.Contains(search, StringComparison.OrdinalIgnoreCase) ?? false))
             .ToList();
     }

     return View(pedidos);
 }
```

**Instrucciones agregadas para el párametro de búsqueda de la función de Listar los Clientes.**

```

 public async Task<IActionResult> ListaClientes(string? search)
 {
     var clientes = await _context.Clientes.ToListAsync();

     if (!string.IsNullOrWhiteSpace(search))
     {
         clientes = clientes
             .Where(c => (c.Nombre?.Contains(search, StringComparison.OrdinalIgnoreCase) ?? false)
                      || (c.Apellido?.Contains(search, StringComparison.OrdinalIgnoreCase) ?? false)
                      || (c.Email?.Contains(search, StringComparison.OrdinalIgnoreCase) ?? false))
             .ToList();
     }

     return View(clientes);
 }
```

## 2. En la carpeta "Views/Shared/_CuadroBusqueda.cshtml", se crea el archivo para contener, la vista correspondiente al parámetro de búsqueda.

<img width="346" height="170" alt="image" src="https://github.com/user-attachments/assets/79edfdb1-d700-4c07-82bc-58c95632be5d" />

**Código de la vista:**

```
@model string

<form method="get" class="mb-3">
    <div class="input-group">
        <input type="text" name="search" class="form-control" placeholder="Buscar..." value="@Model" />
        <button type="submit" class="btn btn-primary">Buscar</button>
    </div>
</form>
```

## 3. Código para implementar en las vistas que se desea agregar el parámetro de búsqueda.

**Se crea la variable, que contendra el parámetro de búsqueda:**

```
var search = Context.Request.Query["search"].ToString();
```

**Se agrega en la misma vista, el input que se va a visualizar (esto es correspondiente a la vista _CuadroBusqueda.cshtml):**

```
@await Html.PartialAsync("_CuadroBusqueda", search)
```

<img width="959" height="326" alt="image" src="https://github.com/user-attachments/assets/2f25e6d5-f609-477d-972d-756ed4c0b903" />


# ¡Listo!, el parámetro de búsqueda ya está implementado...
