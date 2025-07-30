# Configuración y creación de tablas de MySQL.

## Creando Bases de datos.
```
CREATE DATABASE migajas_amor;
```
## Se indica que se usará la Base de Datos creada.
```
USE migajas_amor;
```
## Se crean las tablas dentro de la base de datos.
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
