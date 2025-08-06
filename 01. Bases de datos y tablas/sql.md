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
## 10. Se creó la tabla Pedidos.
```
CREATE TABLE pedidos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id INT,
    cantidad INT,
    fecha_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    estado ENUM('pendiente','en preparación','entregado','cancelado') DEFAULT 'pendiente',
    direccion_entrega VARCHAR(255),
    telefono VARCHAR(20),
    comentarios VARCHAR(255),
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
);
```
## 11. Insertando registros a la tabla Pedidos.
```
INSERT INTO pedidos (cliente_id, cantidad, estado, direccion_entrega, telefono, comentarios) VALUES
(1, 2, 'pendiente', 'Calle 1 #123, Ciudad', '12345678', 'Pedido inicial de Juan'),
(1, 1, 'entregado', 'Calle 1 #123, Ciudad', '12345678', 'Pedido repetido'),
(2, 3, 'en preparación', 'Avenida 2 #456, Ciudad', '87654321', ''),
(2, 1, 'pendiente', 'Avenida 2 #456, Ciudad', '87654321', 'Pedido express'),
(3, 2, 'entregado', 'Boulevard 3 #789, Ciudad', '23456789', ''),
(4, 2, 'pendiente', 'Calle 4 #321, Ciudad', '34567890', 'Cliente nuevo'),
(5, 1, 'pendiente', 'Avenida 5 #654, Ciudad', '45678901', ''),
(6, 4, 'entregado', 'Calle 11 #321, Ciudad', '19283746', 'Pedido por cumpleaños'),
(7, 3, 'pendiente', 'Avenida 12 #654, Ciudad', '28374619', ''),
(8, 2, 'en preparación', 'Boulevard 13 #987, Ciudad', '37461928', ''),
(9, 2, 'pendiente', 'Calle 14 #432, Ciudad', '46192837', 'Pedido familiar'),
(10, 5, 'pendiente', 'Avenida 15 #765, Ciudad', '56192837', ''),
(11, 2, 'entregado', 'Calle 16 #210, Ciudad', '61928374', ''),
(12, 1, 'pendiente', 'Calle 17 #543, Ciudad', '72837461', 'Pedido único'),
(13, 3, 'en preparación', 'Calle 18 #876, Ciudad', '81928374', ''),
(14, 2, 'cancelado', 'Boulevard 19 #654, Ciudad', '91827364', 'Cancelado por cliente');
```
## 12. Se creo la tabla Clientes.
```
CREATE TABLE clientes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    apellido VARCHAR(50),
    telefono VARCHAR(8),
    email VARCHAR(150),
    direccion VARCHAR(255),
    fecha_registro TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```
## 13. Insertando registros a la tabla Clientes.
```
INSERT INTO clientes (nombre, apellido, telefono, email, direccion)
VALUES
('Juan', 'Pérez', '12345678', 'juan.perez@email.com', 'Calle 1 #123, Ciudad'),
('María', 'García', '87654321', 'maria.garcia@email.com', 'Avenida 2 #456, Ciudad'),
('Luis', 'Ramírez', '23456789', 'luis.ramirez@email.com', 'Boulevard 3 #789, Ciudad'),
('Ana', 'López', '34567890', 'ana.lopez@email.com', 'Calle 4 #321, Ciudad'),
('Carlos', 'Fernández', '45678901', 'carlos.fernandez@email.com', 'Avenida 5 #654, Ciudad');
```

**Insertando nuevos registros a la tabla clientes:**

```
INSERT INTO clientes (nombre, apellido, telefono, email, direccion) VALUES
('Fernando', 'Santos', '19283746', 'fernando.santos@email.com', 'Calle 11 #321, Ciudad'),
('Isabel', 'Muñoz', '28374619', 'isabel.munoz@email.com', 'Avenida 12 #654, Ciudad'),
('Ricardo', 'Vega', '37461928', 'ricardo.vega@email.com', 'Boulevard 13 #987, Ciudad'),
('Patricia', 'Herrera', '46192837', 'patricia.herrera@email.com', 'Calle 14 #432, Ciudad'),
('Javier', 'Navarro', '56192837', 'javier.navarro@email.com', 'Avenida 15 #765, Ciudad'),
('Andrea', 'Castillo', '61928374', 'andrea.castillo@email.com', 'Boulevard 16 #210, Ciudad'),
('Roberto', 'Silva', '72837461', 'roberto.silva@email.com', 'Calle 17 #543, Ciudad'),
('Carmen', 'Delgado', '81928374', 'carmen.delgado@email.com', 'Avenida 18 #876, Ciudad'),
('Gustavo', 'Ríos', '91827364', 'gustavo.rios@email.com', 'Boulevard 19 #654, Ciudad'),
('Paula', 'Mendoza', '10293847', 'paula.mendoza@email.com', 'Calle 20 #789, Ciudad');
```

## 14. Se creó la tabla Detalle_Pedidos.
```
CREATE TABLE detalle_pedidos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    pedido_id INT NOT NULL,
    producto_id INT NOT NULL,
    cantidad INT,
    total DECIMAL(10,2),
    FOREIGN KEY (pedido_id) REFERENCES pedidos(id),
    FOREIGN KEY (producto_id) REFERENCES productos(id)
);
```
## 15. Insertando registros a la tabla Detalle_Pedidos.
```
INSERT INTO detalle_pedidos (pedido_id, producto_id, cantidad, total) VALUES
-- Pedido 1 tiene 2 productos
(1, 1, 1, 1.50),         -- Pan francés (1 x $1.50)
(1, 3, 1, 1.80),         -- Pan dulce (1 x $1.80)

-- Pedido 2 tiene 1 producto
(2, 2, 1, 2.00),         -- Baguette (1 x $2.00)

-- Pedido 3 tiene 2 productos
(3, 4, 2, 4.40),         -- Concha (2 x $2.20)
(3, 5, 1, 2.50),         -- Croissant (1 x $2.50)

-- Pedido 4 tiene 1 producto
(4, 1, 1, 1.50),         -- Pan francés (1 x $1.50)

-- Pedido 5 tiene 1 producto
(5, 3, 3, 5.40),         -- Pan dulce (3 x $1.80)

-- Pedido 6 tiene 2 productos
(6, 2, 2, 4.00),         -- Baguette (2 x $2.00)
(6, 4, 1, 2.20),         -- Concha (1 x $2.20)

-- Pedido 7 tiene 1 producto
(7, 5, 2, 5.00),         -- Croissant (2 x $2.50)

-- Pedido 8 tiene 1 producto
(8, 1, 2, 3.00),         -- Pan francés (2 x $1.50)

-- Pedido 9 tiene 2 productos
(9, 3, 1, 1.80),         -- Pan dulce (1 x $1.80)
(9, 4, 1, 2.20),         -- Concha (1 x $2.20)

-- Pedido 10 tiene 1 producto
(10, 2, 5, 10.00),       -- Baguette (5 x $2.00)

-- Pedido 11 tiene 1 producto
(11, 5, 1, 2.50),        -- Croissant (1 x $2.50)

-- Pedido 12 tiene 1 producto
(12, 1, 2, 3.00),        -- Pan francés (2 x $1.50)

-- Pedido 13 tiene 1 producto
(13, 3, 2, 3.60),        -- Pan dulce (2 x $1.80)

-- Pedido 14 tiene 2 productos
(14, 4, 2, 4.40),        -- Concha (2 x $2.20)
(14, 2, 1, 2.00);        -- Baguette (1 x $2.00)
```

# Actualizar el contexto del proyecto con relación a la base de datos.

```
Scaffold-DbContext "Server=localhost;Database=migajas_amor;User=root;" MySql.EntityFrameworkCore -OutputDir Models -Force
```
