# A continuación se muestra como restringir el acceso a usuarios con base a su rol.

## 1. En este caso, se tienen dos roles agregados a bases de datos. Es necesario tener al menos dos usuarios, con un rol diferente para cada uno.
En el proyecto se tiene que, el usuario 1, tiene el rol de Administrador y el usuario 2, tiene el rol de Empleado; eso puede depender del proyecto.

Comprobar la tabla de Roles Asignados para saber qué rol está asignado a cada usuario.

```
SELECT * FROM `usuarios`;
SELECT * FROM `roles`;
SELECT * FROM `rolesasignados`;
```
## 2. En el controlador de Productos y Proveedores, se agregó esta importación using.

```
using Microsoft.AspNetCore.Authorization;
```

## 3. Al inicio de cada función que se desea restringir, se agregó la siguiente línea.
Esta línea indica, que solo el usuario con el rol de **Administrador** podrá tener acceso a ejecutar dicha función.

```
[Authorize(Roles = "Administrador")]
```

**De esta forma, ya se restringe, qué los usuarios que no tengan un rol definido como "Administrador", no podrá ejecutar dicha acción.**

<img width="1282" height="531" alt="image" src="https://github.com/user-attachments/assets/820dd1ba-2ff3-4451-ae78-357e0c26b206" />

## 4. Se debe restringir el botón que ejecuta la función. 
Aunque no es tan indispensable, ya que la función ya está restringida, pero por estética, no es relevante que el botón aparezca para los otros usuarios.

Para ello, se agrega esta instrucción con una estructura "if" para indicar que no se verá ese botón (u otro apartado) cuando el usuario no sea Administrador.
```
@if (User.IsInRole("Administrador"))
{
    //código que a restringuir
}
```

# En este proyecto, se restringió el acceso a la vista de ListUsuarioRol.
Esta es una función en AccesoController, que lista una arreglo de usuario con su respectivo rol.


**Función restringida en AccesoController:**
```
[Authorize(Roles = "Administrador")]
public async Task<IActionResult> ListUsuarioRol()
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

    return View(usuarios);
}
```


**Importación agregada a la vista _Layout.cshtml:**
```
@using Microsoft.AspNetCore.Identity
```


**Botón restringido en la vista _Layout.cshtml:**
```
@if (User.IsInRole("Administrador"))
{
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="" asp-controller="Acceso" asp-action="ListUsuarioRol">Usuarios</a>
    </li>
}
```
