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
<img width="620" height="431" alt="image" src="https://github.com/user-attachments/assets/f88ecbbb-2535-44ba-8410-83ecab5617de" />
