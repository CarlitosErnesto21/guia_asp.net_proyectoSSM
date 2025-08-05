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

## 5. 
