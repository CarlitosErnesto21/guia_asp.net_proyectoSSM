# A continuación se muestra la construcción de la autenticación para el proyecto.

## 1. Crear el controlador vacío "AccesoController.cs" en la carpeta "Controllers".
Debe tener la siguiente estructura:
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
## 2. Crear una Carpeta "Acceso" en "Views" y dentro crear un vista "Index.cshtml".

<img width="267" height="93" alt="image" src="https://github.com/user-attachments/assets/57a6f3dc-09da-4e5c-9ac9-b42818587a1d" />


**Código de la vista:**

```
@model migajas_amor.app.Models.Usuario
@{
    Layout = null;
}

<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Migajas de Amor - Ingreso</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.2/font/bootstrap-icons.css">
    <style>
        body {
            background: linear-gradient(120deg, #fceabb 0%, #f8bfae 100%);
            min-height: 100vh;
            font-family: 'Segoe UI', 'Arial', sans-serif;
        }

        .login-container {
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .login-card {
            background: #fff;
            border-radius: 1.5rem;
            box-shadow: 0 8px 32px rgba(44, 62, 80, 0.12);
            overflow: hidden;
            display: flex;
            max-width: 850px;
            width: 100%;
            transition: box-shadow 0.3s;
        }

            .login-card:hover {
                box-shadow: 0 12px 40px rgba(44, 62, 80, 0.18);
            }

        .login-illustration {
            background: linear-gradient(135deg, #ffe5ec 0%, #e0c3fc 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            width: 45%;
            min-width: 300px;
            padding: 2.5rem 1.5rem;
        }

            .login-illustration img {
                max-height: 300px;
                filter: drop-shadow(0 4px 16px rgba(0,0,0,0.08));
            }

        .login-form-section {
            width: 55%;
            padding: 3rem 2.5rem;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

            .login-form-section h2 {
                font-family: 'Pacifico', cursive, 'Brush Script MT';
                color: #b5838d;
                font-size: 2.3rem;
                margin-bottom: 0.3rem;
                letter-spacing: 1px;
            }

            .login-form-section p {
                color: #a1887f;
                margin-bottom: 1.5rem;
                font-size: 1.05rem;
            }

        .form-control {
            border-radius: 0 1.5rem 1.5rem 0 !important;
            background: #f9f6f2;
            border: 1px solid #f7b2ad;
            font-size: 1rem;
            transition: border-color 0.2s, box-shadow 0.2s;
        }

            .form-control:focus {
                border-color: #f7b267;
                box-shadow: 0 0 0 0.15rem #f7b26733;
                background: #fff;
            }

        .input-group-text {
            background: #fff8f0;
            border: 1px solid #f7b2ad;
            color: #b5838d;
            border-radius: 1.5rem 0 0 1.5rem !important;
            font-size: 1.2rem;
        }

        .btn-pastel {
            background: linear-gradient(90deg, #f7b267 0%, #f4845f 100%);
            color: #fff;
            font-weight: 600;
            border-radius: 2rem;
            font-size: 1.1rem;
            border: none;
            transition: background 0.2s, box-shadow 0.2s;
            box-shadow: 0 2px 8px rgba(244, 132, 95, 0.08);
        }

            .btn-pastel:hover, .btn-pastel:focus {
                background: linear-gradient(90deg, #f4845f 0%, #f7b267 100%);
                color: #fff;
                box-shadow: 0 4px 16px rgba(244, 132, 95, 0.16);
            }
    </style>
    <!-- Fuente Pacifico para el logo -->
    <link href="https://fonts.googleapis.com/css2?family=Pacifico&display=swap" rel="stylesheet">
</head>
<body>
    <div class="login-container">
        <div class="login-card">
            <div class="login-illustration">
                <!-- Puedes reemplazar la imagen por una propia -->
                <img src="@Url.Content("~/Images/logo.png")" alt="Bakery Illustration" class="img-fluid">
            </div>
            <div class="login-form-section">
                <h2>Migajas de Amor</h2>
                <p>Bienvenido/a, inicia sesión para disfrutar de nuestros productos.</p>
                <form asp-controller="Acceso" asp-action="Index" method="post">
                    <div class="mb-3">
                        <div class="input-group">
                            <span class="input-group-text"><i class="bi bi-person"></i></span>
                            <input type="text" class="form-control border-0" asp-for="Login" placeholder="Usuario" autocomplete="username" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <div class="input-group">
                            <span class="input-group-text"><i class="bi bi-lock"></i></span>
                            <input type="password" class="form-control border-0" asp-for="Password" placeholder="Contraseña" autocomplete="current-password" required>
                        </div>
                    </div>
                    <button type="submit" class="btn btn-pastel w-100 py-2">Ingresar</button>
                </form>
            </div>
        </div>
    </div>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
## 3. Configurar en Program.cs.
**Agregar este fragmento de código:**
```
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(option =>
    {
        option.LoginPath = "/Acceso/Index";
        option.ExpireTimeSpan = TimeSpan.FromMinutes(20);
        option.AccessDeniedPath = "/Home/Privacy";
    });
```
**Debe estar abajo de:** (ESTE CÓDIGO YA ESTÁ EN EL ARCHIVO)
```
// Add services to the container.
builder.Services.AddControllersWithViews();

// Obtiene la cadena de conexión y lanza una excepción si es nula
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection")
    ?? throw new InvalidOperationException("Connetion string 'DbContext'" + " not found. ");

builder.Services.AddDbContext<MigajasAmorContext>(o =>
{
    o.UseMySQL(connectionString);
});
```
## 4. Agregar esta línea de código abajo de: "app.UseRouting();".
```
app.UseAuthentication();
```
## 5. Cambiar en este fragmento de código el controlador "Home" por "Acceso".
**Código anterior:**
```
app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}")
    .WithStaticAssets();
```
**Código actualizado:**
```
app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Acceso}/{action=Index}/{id?}")
    .WithStaticAssets();
```
## 6. Gracias a esta función "Salir();" en el controlador "AccesoController" se logra la funcionalidad del botón "Cerrar Sesión" configurado en la vista "_Layout.cshtml"
**Función del controlador:**
```
public async Task<IActionResult> Salir()
{
    await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
    return RedirectToAction("Index", "Acceso");
}
```
**Botón agregado para cerrar sesión:**
```
<!--Cerrar Sesión-->
    <ul class="navbar-nav ms-auto d-flex align-items-center">
        <li class="nav-item">
            <a class="btn btn-danger" asp-area="" asp-controller="Acceso" asp-action="Salir">Cerrar Sesión</a>
        </li>
    </ul>
<!--Cerrar Sesión-->
```
