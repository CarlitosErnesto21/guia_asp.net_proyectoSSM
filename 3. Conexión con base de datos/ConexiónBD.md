# A continuación se muestra la configuración del proyecto para que pueda tener interacción con base de datos.

## 1. Ejecutar el comando de actualización del contexto de la base de datos en la Consola del Administrador de paquetes.

<img width="1888" height="242" alt="image" src="https://github.com/user-attachments/assets/1b7b8218-3289-4efb-ad07-4b8234586b1f" />

```
Scaffold-DbContext "Server=localhost;Database=migajas_amor;User=root;" MySql.EntityFrameworkCore -OutputDir Models -Force
```
De esta forma se está generando el contexto con la base de datos: (Contexto generado y actualizado)

<img width="1878" height="338" alt="image" src="https://github.com/user-attachments/assets/457e0bc9-359b-44c6-a40f-3b0d59b796f3" />

## 2. En el archivo appsettings.json, se debe agregar la siguiente línea de código:
Debe estar justo debajo de **"AllowedHosts": "*",**.
Esto permite la conexión con la base de datos, donde se define el nombre de la base de datos de MySQL y el superusuario Root.

<img width="805" height="320" alt="image" src="https://github.com/user-attachments/assets/b8caaeca-d1b5-4cee-afd9-d7248cd225ab" />

```
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=migajas_amor;User=root;"
  }
```
## 3. Agregar en el archivo Program.cs el siguiente fragmento de código:
Debe estar abajo de este fragmento de código 

**// Add services to the container.**

**builder.Services.AddControllersWithViews();**
```
// Obtiene la cadena de conexión y lanza una excepción si es nula
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection")
    ?? throw new InvalidOperationException("Connetion string 'DbContext'" + " not found. ");

builder.Services.AddDbContext<MigajasAmorContext>(o =>
{
    o.UseMySQL(connectionString);
});
```
## 4. Verificar las importaciones Usings.
```
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.EntityFrameworkCore;
using migajas_amor.app.Models;
```
# Adicional.
Se creó una carpeta **Images** a nivel del **wwwroot** para almacenar el logo del proyecto.

<img width="213" height="110" alt="image" src="https://github.com/user-attachments/assets/d4f46296-84c2-48e7-a110-63d4a2036662" />

