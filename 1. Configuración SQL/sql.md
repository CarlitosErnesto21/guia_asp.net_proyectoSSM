# Configuración y creación de tablas de MySQL.

## 1. Creando Bases de datos.
```
CREATE DATABASE migajas_amor;
```
## 2. Se indica que se usará la Base de Datos creada.
```
USE migajas_amor;
```
## 3. Se crean las tablas dentro de la base de datos.
Estas tablas son del control de Autenticación y Autorización.
```
CREATE TABLE IF NOT EXISTS Usuarios (
    Id INT AUTO_INCREMENT NOT NULL,
    Login VARCHAR(20) NOT NULL,
    Password VARCHAR(64) NOT NULL,
    PRIMARY KEY (Id)
);
CREATE TABLE IF NOT EXISTS Roles (
    Id INT AUTO_INCREMENT NOT NULL,
    Nombre VARCHAR(30) NOT NULL,
    PRIMARY KEY (Id)
);
CREATE TABLE IF NOT EXISTS RolesAsignados (
    Id INT AUTO_INCREMENT NOT NULL,
    UsuarioId INT NOT NULL,
    RolId INT NOT NULL,
    PRIMARY KEY (Id),
    FOREIGN KEY (UsuarioId) REFERENCES Usuarios(Id),
    FOREIGN KEY (RolId) REFERENCES Roles(Id)
);
```
## 4. Insertando usuarios a la tabla Usuarios.
Realizar los cambios correspondientes.
La contraseña debe ser con el algoritmo de hash criptográfico **SHA-256**.
Lo cual se puede hacer a través de **Notepadd++**
```
insert into Usuarios(Login,Password) values('usuario','contraseña');
```
**Ejemplo:**

<img width="620" height="431" alt="image" src="https://github.com/user-attachments/assets/f88ecbbb-2535-44ba-8410-83ecab5617de" />

## 5. Insertando roles a la tabla Roles.
```
insert into roles(Nombre) values('Administrador');
```
```
insert into roles(Nombre) values('Empleado');
```
## 6. Definiendo a cada usuario un rol.
Al usuario número uno, se le asigna el rol de Administrado (rol 1.)
Se debe realizar el mismo proceso para agregar a otros usuario o incluso el mismo otro rol diferente.
```
INSERT INTO RolesAsignados (UsuarioId, RolId) VALUES (1, 1);
```
## 7. Se creó una nueva tabla Productos.
```
CREATE TABLE productos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT NOT NULL,
    precio DECIMAL(10,2) NOT NULL,
    stock INT NOT NULL,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
## 8. Insertando registros a la tabla Productos.
```
INSERT INTO productos (nombre, descripcion, precio, stock)
VALUES
('Pan francés', 'Panecillo clásico de corteza crujiente y miga suave.', 1.50, 50),
('Baguette', 'Barra de pan largo y delgado, tradicional francesa.', 2.00, 30),
('Pan dulce', 'Pan suave y esponjoso con cubierta de azúcar.', 1.80, 40),
('Concha', 'Pan dulce mexicano con cobertura de vainilla.', 2.20, 25),
('Croissant', 'Pan hojaldrado de mantequilla, típico francés.', 2.50, 20);
```
## 9. Se creó un nueva tabla Proveedores.
```
CREATE TABLE proveedores (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    telefono VARCHAR(8),
    email VARCHAR(150),
    direccion VARCHAR(255)
);
```
## 9. Insertando registros a la tabla Proveedores.
```
INSERT INTO proveedores (nombre, telefono, email, direccion) VALUES
('Panadería San Juan',      '12345678', 'contacto@sanjuan.com',     'Av. Central 123, Ciudad'),
('Distribuidora La Espiga', '87654321', 'ventas@laespiga.com',      'Calle 5 #456, Ciudad'),
('Harinas Selectas',        '23456789', 'info@harinasselectas.com', 'Ruta 2 Km 10, Ciudad'),
('Huevos El Buen Gallo',    '34567890', 'hola@elbuenpollo.com',     'Barrio Norte #321, Ciudad'),
('Lácteos El Rancho',       '45678901', 'contacto@elrancho.com',    'Carretera Vieja 22, Ciudad');
```
