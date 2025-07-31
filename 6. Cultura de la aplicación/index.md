# Esta configuración es para definir una estructura para la columna Precio de la tabla Productos.

## 1.	Asegurarse de que la cultura de la aplicación coincida con la de tus usuarios.
**Agregar esta línea de código abajo de esta línea de código:** **_var builder = WebApplication.CreateBuilder(args);_**
```
//Esto es para establecer la cultura por defecto de la aplicación
var cultureInfo = new CultureInfo("es-ES");
CultureInfo.DefaultThreadCurrentCulture = cultureInfo;
CultureInfo.DefaultThreadCurrentUICulture = cultureInfo;
```
**Verificar la importación:**
```
using System.Globalization;
```
