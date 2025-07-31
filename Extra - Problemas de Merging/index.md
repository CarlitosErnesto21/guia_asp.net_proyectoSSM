# Esta guía es para solucionar el problema de Merging.

## Al momento de actualizar el proyecto sino permite, se debe realizar lo siguiente:
**Comando de GitBash para Actualizar.**
```
git pull
```

## - Verificar que el proyecto de ASP.NET (aplicación de VisualStudio2022) esté cerrada.
## - Si el problema persiste, despues de hacer git pull
**Hacer lo siguiente:**

## 1. Comprobar el estado del repositorio local:
Por si hay archivos, es necesario agregar a staging
```
git status
```
## 2. Agregar a Staging:
```
git add .
```
## 3. Hacer un commit:
```
git commit -m "Corrigiendo errores"
```
## 4. Ahora si se puede actualizar el repositorio con:
```
git pull
```
# Si el problema persiste hacer lo siguiente, ya que existe un main|MERGING

## - Ubicarse en la carpeta del proyecto:

<img width="264" height="313" alt="image" src="https://github.com/user-attachments/assets/cd93cb54-2098-4cdd-b453-e761c5e5392d" />

## - Entrar a los archivos del proyecto:

<img width="827" height="127" alt="image" src="https://github.com/user-attachments/assets/57fcded1-936b-4785-96e7-514e1a158302" />

## - Eliminar la carpet bin y obj:

<img width="770" height="52" alt="image" src="https://github.com/user-attachments/assets/b1fae835-ea17-43f3-aa00-ff22a03f8782" />

<img width="763" height="48" alt="image" src="https://github.com/user-attachments/assets/8c2d80c9-8c87-4e64-a0e3-02f1057b96b3" />

# A continuación realizar el paso 2, 3 y 4.
```
git add .
git commit -m "Corrigiendo errores"
git pull
```
# _PROBLEMA SOLUCIONADO_
