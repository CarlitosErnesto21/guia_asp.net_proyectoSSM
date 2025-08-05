# A continuación se presenta la configuración de un PDF para el proyecto.

## 1. Instalar la librería de QuestPDF:
```
QuestPDF
```

<img width="1480" height="298" alt="image" src="https://github.com/user-attachments/assets/5a9adeaf-f123-402a-9264-53a48bdc898a" />

## 2. Crear a nivel de proyecto una carpeta llamada Pdf:

<img width="269" height="192" alt="image" src="https://github.com/user-attachments/assets/ae820c11-cf8f-42e9-aca6-d8d382169cac" />

## 3. Agregar licencia de QuestPDF en el archivo Program.cs:

**Importación de la licencia:**

```
using QuestPDF.Infrastructure;
```

**Utilizando la licencia:**

```
QuestPDF.Settings.License = LicenseType.Community;
```

<img width="816" height="814" alt="image" src="https://github.com/user-attachments/assets/319cacac-3d7d-4e09-8ea2-e8b5d8a30bfa" />

## 4. Es indispensble, actualizar el contexto de la base de datos, para traer las tablas que se han creado recientemente y evitar errores futuros.

```
Scaffold-DbContext "Server=localhost;Database=migajas_amor;User=root;" MySql.EntityFrameworkCore -OutputDir Models -Force
```

## 5. Crear un función en el contexto de la base de datos (MigajasAmorContext.cs):

<img width="329" height="122" alt="image" src="https://github.com/user-attachments/assets/a75da535-2158-42eb-8739-4293c2770fb0" />


```
public virtual DbSet<DetallePedidoPdf> PedidosPdf { get; set; }
```

## 6. Se mapea la entidad:

```
modelBuilder.Entity<DetallePedidoPdf>(entity =>
{
    entity.HasNoKey();

    entity.Property(e => e.FechaPedido)
        .HasColumnType("timestamp")
        .HasColumnName("fecha_pedido");

    entity.Property(e => e.Nombre)
        .HasMaxLength(50)
        .HasColumnName("nombre");

    entity.Property(e => e.Email)
        .HasMaxLength(150)
        .HasColumnName("email");

    entity.Property(e => e.Estado)
        .HasMaxLength(20)
        .HasColumnName("estado");

    entity.Property(e => e.Producto)
        .HasMaxLength(100)
        .HasColumnName("producto");

    entity.Property(e => e.PrecioUnitario)
        .HasColumnType("decimal(10,2)")
        .HasColumnName("precio_unitario");

    entity.Property(e => e.Cantidad)
        .HasColumnType("int")
        .HasColumnName("cantidad");

    entity.Property(e => e.Total)
        .HasColumnType("decimal(10,2)")
        .HasColumnName("total");
});
```

## 7. Agregar la importación de la carpeta Pdf al contexto:

```
using migajas_amor.app.Pdf;
```

## 8. Crear la clase "DetallePedidoPdf" en la carpeta Pdf:

<img width="283" height="67" alt="image" src="https://github.com/user-attachments/assets/6f242ad6-99c8-49d8-813b-ad880bfba742" />


**Debe contener el código siguiente:**

```
namespace migajas_amor.app.Pdf
{
    public class DetallePedidoPdf
    {
        public DateTime FechaPedido { get; set; }
        public string? Nombre { get; set; }
        public string? Email { get; set; }
        public string? Estado { get; set; }
        public string? Producto { get; set; }
        public decimal PrecioUnitario { get; set; }
        public int Cantidad { get; set; }
        public decimal Total { get; set; }
    }
}
```

## 9. Crear la clase "DetallePedidoPdfDoc" en la carpeta Pdf:

<img width="295" height="82" alt="image" src="https://github.com/user-attachments/assets/56b4dffd-972d-46b5-86e7-5340407beaa2" />


**Estrucutra base que debe contener:**

```
using QuestPDF.Infrastructure;
using QuestPDF.Fluent;
using QuestPDF.Helpers;
using QuestPDF.Infrastructure;

namespace migajas_amor.app.Pdf
{
    public class DetallePedidoPdfDoc : IDocument
    {
        private List<DetallePedidoPdf> Model { get; }
        public DetallePedidoPdfDoc(List<DetallePedidoPdf> model)
        {
            Model = model;
        }
        public void Compose(IDocumentContainer container)
        {

        }
    }
}
```

## 10. Crear una consulta SQL de los datos que se desean mostrar, esto debe ser compatible con las columnas del mapeo y la clase creada como DetallePedidoPdf.

**Consulta SQL para la base de datos de MySQL:**

Se puede ejecutar en la base de datos para comprobar la tabla que se mostraría en el pdf.

```
SELECT p.fecha_pedido, c.nombre, c.email, p.estado, pr.nombre AS producto, pr.precio AS precio_unitario, dp.cantidad, dp.total FROM pedidos p JOIN clientes c ON p.cliente_id = c.id JOIN detalle_pedidos dp ON p.id = dp.pedido_id JOIN productos pr ON dp.producto_id = pr.id ORDER BY p.fecha_pedido DESC;
```


**Resultado de la consulta:**


<img width="1706" height="588" alt="image" src="https://github.com/user-attachments/assets/e1c3bc25-9769-4fb6-8d32-9beb9ef6d08c" />


## 11. Crear una función en el controlador de AccesoController:

```
//Acción para generar el PDF de los detalles de pedidos
[HttpGet(Name = "DetallePedidosPdf")]
public IResult DetallePedidosPdf(int n)
{
    string sql = "SELECT p.fecha_pedido, c.nombre, c.email, p.estado, pr.nombre AS producto, pr.precio AS precio_unitario, dp.cantidad, dp.total FROM pedidos p JOIN clientes c ON p.cliente_id = c.id JOIN detalle_pedidos dp ON p.id = dp.pedido_id JOIN productos pr ON dp.producto_id = pr.id ORDER BY p.fecha_pedido DESC;";
    List<DetallePedidoPdf> data = _context.PedidosPdf
        .FromSqlRaw(sql)
        .ToList();
    var document = new DetallePedidoPdfDoc(data);
    var pdfStream = document.GeneratePdf();
    return Results.File(pdfStream, "application/pdf", "detalle_pedidos.pdf");
}
```

## 12. Agregar la importación de QuestPDF al mismo controlador:

```
using QuestPDF.Fluent;
```

