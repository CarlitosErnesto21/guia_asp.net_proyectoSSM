# Comandos generales de GitBash.

## Agregar a Staging:
```
git add .
```
## Hacer un Commit:
```
git commit -m "AgregarTexto"
```
## Hacer una actualización del repositorio remoto:
```
git push -u origin main
```
## Hacer una actualización del repositorio remoto con un branch:
```
git push -u origin NombreBranch
```
## Actualizar repositorio local con los datos del repositorio remoto:
```
git pull
```
## Comprobar estado del repositorio:
```
git status
```
## Comprobar los commits realizados:
```
git log
```
## Clonar repositorio remoto:
Se le agrega la SSH despues de **clone**.
```
git clone
```
## Crear un branch:
```
git branch NombreBranch
```
## Cambiar de branch:
```
git checkout NombreBranch
```
## Hacer merge:
```
git merge NombreBranch
```
## Eliminar branch:
```
git branch -D NombreBranch
```

# USAR CON PRECAUCIÓN.
## Este comando elimina las modificaciones hechas en nuestro repositorio local y lo actualiza con base al repositorio remoto, sobreescribiendo y evitando problemas de merging. 

## NO ES SEGURO, ya que elimina todos los cambios que no se hayan guardado.
```
git reset --hard HEAD
```
