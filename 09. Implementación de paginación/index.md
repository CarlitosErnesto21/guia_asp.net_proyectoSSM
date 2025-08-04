# A continuación, se presenta el proceso de como implementar la paginación en el proyecto.

## 1. Crear la carpeta Utilidades y la clase Paginacion.cshtml:

<img width="209" height="52" alt="image" src="https://github.com/user-attachments/assets/0a93b4a3-2d2d-4723-9a95-b0a473283cba" />


**Código de la clase:**

```
namespace migajas_amor.app.Utilidades 
{
    public class Paginacion
    {
        public int TotalRegistros { get; private set; }
        public int PaginaActual { get; private set; }
        public int RegistrosPagina { get; private set; }
        public int TotalPaginas { get; private set; }
        public int PaginaInicio { get; private set; }
        public int UltimaPagina { get; private set; }
        public string Controlador { get; private set; }
        public string Accion { get; private set; }
        public int Salto { get; private set; }
        public Paginacion() { }
        public Paginacion(int totalRegistros, int pagina, int registrosPagina = 10, string controlador = "Home", string accion = "Index")
        {
            int totalPaginas = (int)Math.Ceiling((decimal)totalRegistros / (decimal)registrosPagina);
            int paginaActual = (pagina < 1) ? 1 : pagina;
            int paginaInicio = paginaActual - 5;
            int ultimaPagina = paginaActual + 4;
            int salto = (paginaActual - 1) * registrosPagina;
            if (paginaInicio <= 0)
            {
                ultimaPagina = ultimaPagina - (paginaInicio - 1);
                paginaInicio = 1;
            }
            if (ultimaPagina > totalPaginas)
            {
                ultimaPagina = totalPaginas;
                if (ultimaPagina > 10)
                {
                    paginaInicio = ultimaPagina - 9;
                }
            }
            TotalRegistros = totalRegistros;
            PaginaActual = paginaActual;
            RegistrosPagina = registrosPagina;
            TotalPaginas = totalPaginas;
            PaginaInicio = paginaInicio;
            UltimaPagina = ultimaPagina;
            Controlador = controlador;
            Accion = accion;
            Salto = salto;
        }
    }
}
```

## 2. Crea vista _Paginacion.cshtml, en la carpeta Views/Shared/_Paginacion.cshtml:

<img width="344" height="319" alt="image" src="https://github.com/user-attachments/assets/eea08fa7-2111-401d-853a-4dd326f19af2" />

**Código de la vista:**

```
@model migajas_amor.app.Utilidades.Paginacion;

<div class="container">
    @if (Model.TotalPaginas > 0)
    {
        <ul class="pagination justify-content-end">
            @if (Model.PaginaActual > 1)
            {
                <li class="page-item">
                    <a class="page-link" asp-controller="@(Model.Controlador)" asp-action="@(Model.Accion)" asp-route-pg="1">Primera</a>
                </li>
                <li>
                    <a class="page-link" asp-controller="@(Model.Controlador)" asp-action="@(Model.Accion)" asp-route-pg="@(Model.PaginaActual - 1)">Anterior</a>
                </li>
            }
            @for (var pge = Model.PaginaInicio; pge <= Model.UltimaPagina; pge++)
            {
                <li class="page-item @(pge == Model.PaginaActual ? "active" : "")">
                    <a class="page-link" asp-controller="@(Model.Controlador)" asp-action="@(Model.Accion)" asp-route-pg="@pge">@pge</a>
                </li>
            }
            @if (Model.PaginaActual < Model.TotalPaginas)
            {
                <li class="page-item">
                    <a class="page-link" asp-controller="@(Model.Controlador)" asp-action="@(Model.Accion)" asp-route-pg="@(Model.PaginaActual + 1)">Siguiente</a>
                </li>
                <li>
                    <a class="page-link" asp-controller="@(Model.Controlador)" asp-action="@(Model.Accion)" asp-route-pg="@(Model.TotalPaginas)">Última</a>
                </li>
            }
        </ul>
    }
</div>
```


## 3. Configuración de rutas personalizadas en Program.cs:

**Son nesarias para encontrar el controlador donde está definidas los métodos de enlistamiento, ya que no contienen un controlador independiente.**

```
app.MapControllerRoute(
    name: "listPedidosDirect",
    pattern: "ListPedidos",
    defaults: new { controller = "Acceso", action = "ListPedidos" });

app.MapControllerRoute(
    name: "listUsuarioRolDirect",
    pattern: "ListUsuarioRol",
    defaults: new { controller = "Acceso", action = "ListUsuarioRol" });

app.MapControllerRoute(
    name: "listClientesDirect",
    pattern: "ListaClientes",
    defaults: new { controller = "Acceso", action = "ListaClientes" });
```


## 4. Modificaciones en metodos de toListAsync() en el controlador de AccesoController.
Estas modificaciones corresponden al correcto funcionamiento de los filtros de búsqueda y paginación.

**Este método es el correspondiente a enlistar los Usuarios y sus Roles designados:**

```
[Authorize(Roles = "Administrador")]
public async Task<IActionResult> ListUsuarioRol(int pg = 1, string? search = null)
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

    var totalRegistros = usuarios.Count();
    var paginacion = new Paginacion(totalRegistros, pg, 1, "ListUsuarioRol");
    var data = usuarios
        .Skip(paginacion.Salto)
        .Take(paginacion.RegistrosPagina)
        .ToList();
    this.ViewBag.Paginacion = paginacion;

    return View(data);
}
```

**Este método es el correspondiente a enlistar los Pedidos:**

```
 public async Task<IActionResult> ListPedidos(int pg = 1, string? search = null)
 { 
     var pedidosQuery = await _context.Pedidos.ToListAsync();

     if (!string.IsNullOrWhiteSpace(search))
     {
         pedidosQuery = pedidosQuery.Where(p =>
             (!string.IsNullOrEmpty(p.Cliente) && p.Cliente.Contains(search, StringComparison.OrdinalIgnoreCase)) ||
             (!string.IsNullOrEmpty(p.Producto) && p.Producto.Contains(search, StringComparison.OrdinalIgnoreCase)) ||
             (!string.IsNullOrEmpty(p.Estado) && p.Estado.Contains(search, StringComparison.OrdinalIgnoreCase))
         ).ToList();
     }

     var totalRegistros = pedidosQuery.Count();
     var paginacion = new Paginacion(totalRegistros, pg, 5, "ListPedidos");
     var data = pedidosQuery
         .Skip(paginacion.Salto)
         .Take(paginacion.RegistrosPagina)
         .ToList();
     this.ViewBag.Paginacion = paginacion;

     return View(data);
     
 }
```

**Este método es el correspondiente a enlistar los Clientes:**

```
public async Task<IActionResult> ListaClientes(int pg = 1, string? search = null)
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

    var totalRegistros = clientes.Count();
    var paginacion = new Paginacion(totalRegistros, pg, 5, "ListaClientes");
    var data = clientes
        .Skip(paginacion.Salto)
        .Take(paginacion.RegistrosPagina)
        .ToList();
    this.ViewBag.Paginacion = paginacion;

    return View(data);
}
```


**Controlador AccesoController completo:**


```
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using migajas_amor.app.Models;
using migajas_amor.app.Utilidades;
using MySqlX.XDevAPI;
using System.Security.Claims;
using System.Security.Cryptography;
using System.Text;

namespace migajas_amor.app.Controllers
{
    public class AccesoController : Controller
    {
        private readonly MigajasAmorContext _context;

        public AccesoController(MigajasAmorContext context)
        {
            _context = context;
        }
        public IActionResult Index()
        {
            return View();
        }

        
        [HttpPost]
        public async Task<IActionResult> Index(Usuario infoLogin)
        {
            if (infoLogin != null)
            {
                SHA256 mySHA256 = SHA256.Create();
                byte[] datos = Encoding.UTF8.GetBytes(infoLogin.Password);
                byte[] hashValue = mySHA256.ComputeHash(datos);

                string hash = BitConverter.ToString(hashValue).Replace("-", "").ToLower();
                Usuario? usuario = _context.Usuarios.FromSqlRaw("SELECT Id, Login, Password FROM Usuarios WHERE Login = {0} AND Password = {1}", infoLogin.Login, hash).FirstOrDefault();

                if (usuario != null)
                {
                    var claims = new List<Claim> {
                        new Claim(ClaimTypes.Name, usuario.Login) // usuario.Login
                        
                    };

                    List<Role> lista = (from rls in _context.Roles
                                        join rlsa in _context.Rolesasignados
                                        on rls.Id equals rlsa.RolId
                                        where rlsa.UsuarioId == usuario.Id
                                        select rls).ToList();

                    foreach (Role rol in lista)
                    {
                        claims.Add(new Claim(ClaimTypes.Role, rol.Nombre));
                    }

                    var claimsIdentity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);

                    await HttpContext.SignInAsync(CookieAuthenticationDefaults.AuthenticationScheme, new ClaimsPrincipal(claimsIdentity));

                    return RedirectToAction("Index", "Home");
                }
                else
                {
                    ViewBag.Error = "Usuario o contraseña incorrectos.";
                    return View();
                }
            }
            else
            {
                return View();
            }
        }
        public async Task<IActionResult> Salir()
        {
            await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
            return RedirectToAction("Index", "Acceso");
        }

        [Authorize(Roles = "Administrador")]
        public async Task<IActionResult> ListUsuarioRol(int pg = 1, string? search = null)
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

            var totalRegistros = usuarios.Count();
            var paginacion = new Paginacion(totalRegistros, pg, 1, "ListUsuarioRol");
            var data = usuarios
                .Skip(paginacion.Salto)
                .Take(paginacion.RegistrosPagina)
                .ToList();
            this.ViewBag.Paginacion = paginacion;

            return View(data);
        }

        public async Task<IActionResult> ListPedidos(int pg = 1, string? search = null)
        { 
            var pedidosQuery = await _context.Pedidos.ToListAsync();

            if (!string.IsNullOrWhiteSpace(search))
            {
                pedidosQuery = pedidosQuery.Where(p =>
                    (!string.IsNullOrEmpty(p.Cliente) && p.Cliente.Contains(search, StringComparison.OrdinalIgnoreCase)) ||
                    (!string.IsNullOrEmpty(p.Producto) && p.Producto.Contains(search, StringComparison.OrdinalIgnoreCase)) ||
                    (!string.IsNullOrEmpty(p.Estado) && p.Estado.Contains(search, StringComparison.OrdinalIgnoreCase))
                ).ToList();
            }

            var totalRegistros = pedidosQuery.Count();
            var paginacion = new Paginacion(totalRegistros, pg, 5, "ListPedidos");
            var data = pedidosQuery
                .Skip(paginacion.Salto)
                .Take(paginacion.RegistrosPagina)
                .ToList();
            this.ViewBag.Paginacion = paginacion;

            return View(data);
            
        }

        public async Task<IActionResult> ListaClientes(int pg = 1, string? search = null)
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

            var totalRegistros = clientes.Count();
            var paginacion = new Paginacion(totalRegistros, pg, 5, "ListaClientes");
            var data = clientes
                .Skip(paginacion.Salto)
                .Take(paginacion.RegistrosPagina)
                .ToList();
            this.ViewBag.Paginacion = paginacion;

            return View(data);
        }
    }
}
```

## 5. En las vistas Views/Acceso/"listas", se debe agregar la paginación y a través de ViewBag así mismo el cuadro de búsqueda previamente implementado:

**Implementación de ambos casos:**

```
@using migajas_amor.app.Utilidades;
@{
    ViewData["Title"] = "Clientes ";


    Paginacion paginacion = new Paginacion();
    int paginaActual = 0;
    if (ViewBag.Paginacion != null)
    {
        paginacion = ViewBag.Paginacion;
        paginaActual = paginacion.PaginaActual;
    }

    var search = Context.Request.Query["search"].ToString();

}
```


**Línea de código del cuadro de búsqueda:**

```
@await Html.PartialAsync("_CuadroBusqueda", search)
```


**Línea de código de la paginación:**

```
<partial name="_paginacion" model="@paginacion" /> 
```


# Deben agregarse en las vistas deseadas y correspondientes.
