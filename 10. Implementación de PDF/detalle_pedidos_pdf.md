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


**Estructura base que debe contener la clase:**

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

**Se quitó el campo "apellidos" para evitar problemas en la presentación del documento PDF.**

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

## 13. Código completado para la clase de "DetallePedidoPdfDoc":

```
using QuestPDF.Infrastructure;
using QuestPDF.Fluent;
using QuestPDF.Helpers;
using System;
using System.Collections.Generic;

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
            container.Page(page =>
            {
                page.Size(PageSizes.Letter);
                page.Margin(1, Unit.Centimetre);
                page.PageColor(Colors.White);
                page.DefaultTextStyle(x => x.FontSize(8));

                page.Header()
                    .Text("Detalle de pedidos")
                    .SemiBold().FontSize(22).FontColor(Colors.Blue.Medium);

                page.Content()
                    .Column(column =>
                    {
                        column.Spacing(10);

                        column.Item().Text("Total pedidos: " + Model.Count)
                            .FontSize(10);

                        column.Item().Table(table =>
                        {
                            table.ColumnsDefinition(columns =>
                            {
                                columns.RelativeColumn(1); // FechaPedido
                                columns.RelativeColumn(1); // Nombre
                                columns.RelativeColumn(2); // Email
                                columns.RelativeColumn(1); // Estado
                                columns.RelativeColumn(1); // Producto
                                columns.RelativeColumn(1); // PrecioUnitario
                                columns.RelativeColumn(1); // Cantidad
                                columns.RelativeColumn(1); // Total
                            });

                            // Header
                            table.Header(header =>
                            {
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("FECHA");
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("CLIENTE");
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("CORREO ELECTRÓNICO");
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("ESTADO");
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("PRODUCTO");
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("PRECIO").FontSize(8);
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("CANTIDAD").FontSize(8);
                                header.Cell().Element(CellStyleHeader).AlignCenter().Text("TOTAL").FontSize(8);

                                static IContainer CellStyleHeader(IContainer container) =>
                                    container.DefaultTextStyle(x => x.SemiBold().FontSize(8).FontColor(Colors.White))
                                             .Background(Colors.Blue.Medium)
                                             .PaddingVertical(7)
                                             .PaddingHorizontal(3)
                                             .BorderBottom(1)
                                             .BorderColor(Colors.Grey.Medium);
                            });

                            // Rows
                            foreach (var item in Model)
                            {
                                table.Cell().Element(CellStyleRow).AlignCenter().Text(item.FechaPedido.ToString("yyyy-MM-dd"));
                                table.Cell().Element(CellStyleRow).Text(item.Nombre);
                                table.Cell().Element(CellStyleRow).Text(item.Email);
                                table.Cell().Element(CellStyleRow).Text(item.Estado);
                                table.Cell().Element(CellStyleRow).Text(item.Producto);
                                table.Cell().Element(CellStyleRow).Row(row =>
                                {
                                    row.RelativeItem().Text("$").AlignLeft();
                                    row.RelativeItem().Text(item.PrecioUnitario.ToString("F2")).AlignRight();
                                });
                                table.Cell().Element(CellStyleRow).AlignCenter().Text(item.Cantidad.ToString());
                                table.Cell().Element(CellStyleRow).Row(row =>
                                {
                                    row.RelativeItem().Text("$").AlignLeft();
                                    row.RelativeItem().Text(item.Total.ToString("F2")).AlignRight();
                                });

                                static IContainer CellStyleRow(IContainer container) =>
                                    container.PaddingVertical(2).PaddingHorizontal(3);
                            }
                        });
                    });

                page.Footer()
                    .AlignCenter()
                    .Text(x =>
                    {
                        x.Span("Página ").FontSize(9);
                        x.CurrentPageNumber().FontSize(9);
                    });
            });
        }
    }
}
```

## 14. En la vista de ListPedidos.cshtml en la carpeta "Views/Acceso/", se debe agregar el botón que permitirá ejecutar la función de descarga del PDF:

<img width="330" height="152" alt="image" src="https://github.com/user-attachments/assets/76c56036-f326-48cd-978b-7bcecd499a2a" />


**Código del botón:**

Los estilos aplicados al botón, pueden cambiar en un futuro.

```
<a asp-controller="Acceso" asp-action="DetallePedidosPdf" asp-route-n="3" class="btn btn-success mb-4">
    <i class="bi bi-file-earmark-pdf me-1"></i> Generar Reporte PDF
</a>
```

# ¡Listo!, ya se encuentra implementado un PDF en el proyecto sobre los detalles de los pedidos.
