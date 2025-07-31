# Este controlador ya fue creado en la Autenticacióno de Usuarios.

```
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using migajas_amor.app.Models;
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
    }
}
```

## 1. Se creó una nueva función al final de la función "Salir();".
Esta función es para hacer una lista de Usuario con su Rol.
```
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
## 2. Se creó la clase "UsuarioRol.cs" en la carpeta "Models".

<img width="332" height="224" alt="image" src="https://github.com/user-attachments/assets/26d234c5-b2aa-4832-a906-611b3128cfb8" />


**Estructura de clase:**
```
namespace migajas_amor.app.Models
{
    public class UsuarioRol
    {
        public int UsuarioId { get; set; }
        public string Login { get; set; } = null!;
        public List<string> Roles { get; set; } = new();
    }
}
```
## 3. Se crea una vista vacía en la carpeta Views/Acceso/ListUsuarioRol.cshtml.

<img width="330" height="95" alt="image" src="https://github.com/user-attachments/assets/1e740c54-9cf2-4f34-b8a0-5bbd9bdef838" />


**Estructura de la vista:**
```
@model List<migajas_amor.app.Models.UsuarioRol>

<h2>Usuarios y Roles</h2>
<table class="table">
    <thead>
        <tr>
            <th>Usuario</th>
            <th>Roles</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var usuario in Model)
        {
            <tr>
                <td>@usuario.Login</td>
                <td>@string.Join(", ", usuario.Roles)</td>
            </tr>
        }
    </tbody>
</table>
```
## 4. En el _Layout.cshtml se agrega un nuevo nav-link:
```
<li class="nav-item">
    <a class="nav-link text-dark" asp-area="" asp-controller="Acceso" asp-action="ListUsuarioRol">Usuarios</a>
</li>
```
