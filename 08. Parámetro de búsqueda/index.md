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
