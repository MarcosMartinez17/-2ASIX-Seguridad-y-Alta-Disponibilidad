# SAD · UD3 — Guía completa de ACL en Linux

## Objetivo

Esta guía explica paso a paso cómo trabajar con **usuarios, grupos, permisos clásicos y ACL en Linux**.

Está pensada para entender bien la parte práctica de la UD3 y poder resolver ejercicios parecidos en examen.

La idea principal es aprender este orden:

```text
1. Crear grupos
2. Crear usuarios
3. Meter usuarios en grupos
4. Crear recursos
5. Poner permisos base
6. Añadir ACL
7. Comprobar ACL
8. Probar con usuarios reales
9. Corregir errores
```

---

# 1. ¿QUÉ PROBLEMA RESUELVEN LAS ACL?

Linux tiene permisos clásicos para:

```text
usuario propietario
grupo propietario
otros
```

Eso muchas veces no es suficiente.

Ejemplo:

```text
archivo: planos.dwg
```

Queremos:

```text
victor       puede leer y escribir
carlos       puede leer
alberto      puede leer solo un fichero concreto
resto        sin acceso
```

Con permisos clásicos sería difícil porque solo tenemos:

```text
propietario
grupo
otros
```

Las **ACL (Access Control Lists)** permiten añadir permisos específicos para:

```text
usuarios concretos
grupos concretos
```

sin tener que cambiar continuamente el propietario o el grupo principal.

---

# 2. PERMISOS `rwx`

Antes de usar ACL hay que entender bien:

```text
r = read
w = write
x = execute
```

## En un archivo

```text
r = leer el contenido
w = modificar el contenido
x = ejecutar el archivo
```

Ejemplo:

```text
rw-
```

significa:

```text
leer
escribir
no ejecutar
```

## En un directorio

En carpetas cambia un poco:

```text
r = ver/listar nombres del contenido
w = crear o borrar elementos
x = entrar/atravesar el directorio
```

El permiso `x` es especialmente importante.

Un usuario puede tener permiso para leer un fichero, pero si no tiene `x` sobre la carpeta donde está, puede no llegar hasta él.

---

# 3. COMPROBAR SI ACL ESTÁ INSTALADO

```bash
dpkg -s acl
```

Si está instalado debería aparecer algo parecido a:

```text
Status: install ok installed
```

Si no está:

```bash
sudo apt update
sudo apt install acl -y
```

También podemos comprobar los comandos:

```bash
which getfacl
which setfacl
```

---

# 4. CREAR GRUPOS

Ejemplo con tres grupos:

```text
operarios
ingenieria
supervisores
```

Creamos:

```bash
sudo groupadd operarios
sudo groupadd ingenieria
sudo groupadd supervisores
```

## Comprobar grupos

```bash
getent group operarios
getent group ingenieria
getent group supervisores
```

O todos juntos:

```bash
getent group operarios ingenieria supervisores
```

### Qué hace `getent`

Consulta la información de usuarios o grupos del sistema.

---

# 5. CREAR USUARIOS

Ejemplo:

```text
operarios:
alberto
oscar
hugo

ingenieria:
nestor
victor
lucia

supervisores:
carlos
mary
john
```

Crear usuarios:

```bash
sudo useradd -m -s /bin/bash alberto
sudo useradd -m -s /bin/bash oscar
sudo useradd -m -s /bin/bash hugo

sudo useradd -m -s /bin/bash nestor
sudo useradd -m -s /bin/bash victor
sudo useradd -m -s /bin/bash lucia

sudo useradd -m -s /bin/bash carlos
sudo useradd -m -s /bin/bash mary
sudo useradd -m -s /bin/bash john
```

---

# 6. EXPLICACIÓN DE `useradd`

Ejemplo:

```bash
sudo useradd -m -s /bin/bash alberto
```

Significa:

```text
useradd          crear usuario
-m               crear /home/alberto
-s /bin/bash     usar Bash como shell
alberto          nombre del usuario
```

---

# 7. COMPROBAR QUE LOS USUARIOS EXISTEN

```bash
id alberto
id victor
id carlos
```

También podemos mirar sus directorios:

```bash
ls -ld /home/alberto
ls -ld /home/victor
ls -ld /home/carlos
```

---

# 8. AÑADIR USUARIOS A GRUPOS

Operarios:

```bash
sudo usermod -aG operarios alberto
sudo usermod -aG operarios oscar
sudo usermod -aG operarios hugo
```

Ingeniería:

```bash
sudo usermod -aG ingenieria nestor
sudo usermod -aG ingenieria victor
sudo usermod -aG ingenieria lucia
```

Supervisores:

```bash
sudo usermod -aG supervisores carlos
sudo usermod -aG supervisores mary
sudo usermod -aG supervisores john
```

---

# 9. EXPLICACIÓN DE `usermod -aG`

```bash
sudo usermod -aG grupo usuario
```

Significa:

```text
usermod     modificar usuario
-a          añadir sin quitar otros grupos
-G          indicar grupo secundario
```

Muy importante:

```text
-aG
```

se suele usar junto.

Si se usa solo `-G`, se pueden reemplazar otros grupos secundarios del usuario.

---

# 10. COMPROBAR PERTENENCIA A GRUPOS

```bash
id alberto
```

Ejemplo esperado:

```text
uid=... alberto gid=... alberto groups=...,operarios
```

También:

```bash
getent group operarios
```

Debe mostrar:

```text
operarios:x:....:alberto,oscar,hugo
```

---

# 11. CREAR RECURSOS

Ejemplo de la práctica:

```text
/home/hmi_planta
/home/planos/
```

Crear:

```bash
cd /home
sudo touch hmi_planta
sudo mkdir -p planos
```

Dentro de `planos`:

```bash
sudo touch /home/planos/autoclave.dwg
sudo touch /home/planos/caldera.dwg
sudo touch /home/planos/destilacion.dwg
```

Comprobar:

```bash
ls -l /home
ls -l /home/planos
```

---

# 12. PROPIETARIOS CON `chown`

Ejemplo:

```bash
sudo chown linux:linux /home/hmi_planta
sudo chown -R linux:linux /home/planos
```

Sintaxis:

```text
chown usuario:grupo recurso
```

Con `-R`:

```text
aplica de forma recursiva
```

---

# 13. PERMISOS BASE CON `chmod`

Antes de usar ACL conviene partir de permisos restrictivos.

Ejemplo:

```bash
sudo chmod 700 /home/hmi_planta
sudo chmod 700 /home/planos
sudo chmod 600 /home/planos/*.dwg
```

Significado:

```text
700 = rwx------
600 = rw-------
```

Así solo el propietario tiene acceso inicialmente.

Después damos permisos especiales mediante ACL.

---

# 14. COMPROBAR PERMISOS CLÁSICOS

```bash
ls -l /home/hmi_planta
ls -ld /home/planos
ls -l /home/planos
```

Ejemplo:

```text
-rwx------ hmi_planta
drwx------ planos
-rw------- autoclave.dwg
```

---

# 15. COMANDO `setfacl`

`setfacl` sirve para crear o modificar ACL.

Forma general:

```bash
sudo setfacl -m tipo:nombre:permisos recurso
```

---

# 16. ACL PARA UN USUARIO

Ejemplo:

```bash
sudo setfacl -m u:victor:rwx /home/planos
```

Significa:

```text
u           usuario
victor      usuario concreto
rwx         permisos
```

---

# 17. ACL PARA UN GRUPO

Ejemplo:

```bash
sudo setfacl -m g:supervisores:r-x /home/planos
```

Significa:

```text
g               grupo
supervisores    nombre grupo
r-x             leer + acceder
```

---

# 18. ACL SOBRE UN ARCHIVO

Ejemplo:

```bash
sudo setfacl -m u:alberto:r-- /home/planos/autoclave.dwg
```

Alberto puede leer ese archivo concreto.

---

# 19. ACL SOBRE VARIOS ARCHIVOS

```bash
sudo setfacl -m u:victor:rw- /home/planos/*.dwg
```

`*.dwg` significa:

```text
todos los archivos que terminan en .dwg
```

---

# 20. ACL RECURSIVA

```bash
sudo setfacl -R -m g:grupoA:rwx /home/compartido01
```

`-R` significa:

```text
aplicar también a todo lo que ya existe dentro
```

Muy útil en carpetas con contenido existente.

---

# 21. DIFERENCIA ENTRE `-R` Y `-d`

Esta diferencia es muy importante.

## `-R`

```bash
sudo setfacl -R -m g:grupo:rwx carpeta
```

Actúa sobre:

```text
carpeta actual
archivos existentes
subcarpetas existentes
```

## `-d`

```bash
sudo setfacl -d -m g:grupo:rwx carpeta
```

Crea una ACL **por defecto**.

Sirve para:

```text
archivos nuevos
directorios nuevos
```

---

# 22. ACL POR DEFECTO

Ejemplo:

```bash
sudo setfacl -d -m u:victor:rwx /home/planos
sudo setfacl -d -m g:supervisores:r-x /home/planos
sudo setfacl -d -m o::--- /home/planos
```

Esto hace que los nuevos recursos dentro de `planos` hereden esas ACL.

---

# 23. PROBAR LA HERENCIA

Crear un fichero nuevo:

```bash
sudo touch /home/planos/prueba.txt
```

Ver ACL:

```bash
getfacl /home/planos/prueba.txt
```

Después borrar:

```bash
sudo rm /home/planos/prueba.txt
```

---

# 24. VER ACL CON `getfacl`

```bash
getfacl /home/planos
```

Ejemplo:

```text
# owner: linux
# group: linux

user::rwx
user:victor:rwx
group::---
group:supervisores:r-x
mask::rwx
other::---
```

---

# 25. CÓMO LEER `getfacl`

Ejemplo:

```text
user::rwx
```

Permisos del propietario.

```text
user:victor:rwx
```

ACL específica de Víctor.

```text
group::---
```

Permisos del grupo propietario.

```text
group:supervisores:r-x
```

ACL específica del grupo supervisores.

```text
mask::rwx
```

Máscara ACL.

```text
other::---
```

Permisos de otros.

---

# 26. LA MÁSCARA `mask`

La máscara limita los permisos efectivos máximos.

Ejemplo:

```text
user:victor:rwx
mask::r-x
```

Aunque Víctor tiene:

```text
rwx
```

sus permisos efectivos serán:

```text
r-x
```

porque la máscara elimina escritura.

---

# 27. `#effective:`

Puede aparecer:

```text
user:victor:rwx          #effective:r-x
```

Esto indica:

```text
ACL solicitada = rwx
permiso efectivo = r-x
```

La causa suele ser la máscara.

---

# 28. CAMBIAR LA MÁSCARA

Si hace falta:

```bash
sudo setfacl -m m::rwx recurso
```

`m` significa:

```text
mask
```

Pero no conviene modificarla sin entender por qué está limitando.

---

# 29. EL PERMISO `x` EN DIRECTORIOS

Ejemplo:

```bash
sudo setfacl -m u:alberto:--x /home/planos
```

Alberto puede:

```text
atravesar /home/planos
```

pero no necesariamente listar todo.

Esto se usa cuando queremos darle acceso solo a un fichero concreto.

---

# 30. EJEMPLO: ALBERTO SOLO LEE AUTOCLAVE

Primero permite atravesar la carpeta:

```bash
sudo setfacl -m u:alberto:--x /home/planos
```

Después:

```bash
sudo setfacl -m u:alberto:r-- /home/planos/autoclave.dwg
```

Ahora puede:

```bash
sudo -u alberto cat /home/planos/autoclave.dwg
```

Pero esto debería fallar:

```bash
sudo -u alberto cat /home/planos/caldera.dwg
```

---

# 31. EJEMPLO: ÓSCAR SOLO LEE CALDERA

```bash
sudo setfacl -m u:oscar:--x /home/planos
sudo setfacl -m u:oscar:r-- /home/planos/caldera.dwg
```

Prueba correcta:

```bash
sudo -u oscar cat /home/planos/caldera.dwg
```

Prueba que debe fallar:

```bash
sudo -u oscar cat /home/planos/autoclave.dwg
```

---

# 32. EJEMPLO: SUPERVISORES PUEDEN LEER PLANOS

Directorio:

```bash
sudo setfacl -m g:supervisores:r-x /home/planos
```

Archivos:

```bash
sudo setfacl -m g:supervisores:r-- /home/planos/*.dwg
```

Probar:

```bash
sudo -u carlos ls /home/planos
sudo -u carlos cat /home/planos/autoclave.dwg
```

---

# 33. EJEMPLO: VÍCTOR PUEDE MODIFICAR PLANOS

Directorio:

```bash
sudo setfacl -m u:victor:rwx /home/planos
```

Ficheros:

```bash
sudo setfacl -m u:victor:rw- /home/planos/*.dwg
```

Probar escritura:

```bash
sudo -u victor bash -c 'echo "prueba" >> /home/planos/autoclave.dwg'
```

Comprobar:

```bash
cat /home/planos/autoclave.dwg
```

---

# 34. ACL DE `hmi_planta`

Ejemplo de la práctica:

```bash
sudo setfacl -m g:operarios:--x /home/hmi_planta
sudo setfacl -m u:nestor:r-x /home/hmi_planta
sudo setfacl -m u:lucia:rwx /home/hmi_planta
```

Significa:

```text
operarios   ejecutar
nestor      leer + ejecutar
lucia       leer + escribir + ejecutar
```

---

# 35. PROBAR PERMISOS CON `sudo -u`

Ejemplo:

```bash
sudo -u alberto ls /home/planos
```

Ejecuta el comando como Alberto.

Leer:

```bash
sudo -u alberto cat /home/planos/autoclave.dwg
```

Escribir:

```bash
sudo -u victor bash -c 'echo "texto" >> /home/planos/autoclave.dwg'
```

---

# 36. PROBAR CON `test`

## Lectura

```bash
sudo -u usuario test -r recurso && echo "Puede leer"
```

## Escritura

```bash
sudo -u usuario test -w recurso && echo "Puede escribir"
```

## Ejecución

```bash
sudo -u usuario test -x recurso && echo "Puede ejecutar"
```

---

# 37. COPIAR ACL DE UNA CARPETA A OTRA

Ejemplo de la práctica guiada:

```bash
getfacl /home/compartido01 | sudo setfacl -R --set-file=- /home/compartido02
```

Proceso:

```text
getfacl             obtiene ACL
|                   pipe
setfacl             aplica ACL
--set-file=-        lee reglas desde entrada estándar
```

---

# 38. EL PIPE `|`

Ejemplo:

```bash
comando1 | comando2
```

La salida de:

```text
comando1
```

se convierte en entrada de:

```text
comando2
```

En ACL:

```bash
getfacl origen | setfacl destino
```

sirve para reutilizar permisos.

---

# 39. ELIMINAR ACL

Eliminar una entrada concreta:

```bash
sudo setfacl -x u:alberto recurso
```

Para grupo:

```bash
sudo setfacl -x g:grupo recurso
```

---

# 40. QUITAR TODAS LAS ACL EXTENDIDAS

```bash
sudo setfacl -b recurso
```

`-b` elimina las ACL extendidas.

Usar con cuidado.

---

# 41. SÍMBOLO `+` EN `ls -l`

Ejemplo:

```text
drwxrwx---+
```

El `+` indica:

```text
existen ACL extendidas
```

---

# 42. EJEMPLO COMPLETO DESDE CERO

Supongamos:

```text
grupoA
usuarioA01
usuarioA02
usuarioA03

grupoB
usuarioB01
usuarioB02
usuarioB03

grupoC
usuarioC01
usuarioC02
usuarioC03
```

Queremos:

```text
grupoA       rwx
grupoB       r-x
grupoC       ---
usuarioC03   r-x
```

---

# 43. PASO 1 — CREAR GRUPOS

```bash
sudo groupadd grupoA
sudo groupadd grupoB
sudo groupadd grupoC
```

---

# 44. PASO 2 — CREAR USUARIOS

```bash
sudo useradd -m -s /bin/bash usuarioA01
sudo useradd -m -s /bin/bash usuarioA02
sudo useradd -m -s /bin/bash usuarioA03

sudo useradd -m -s /bin/bash usuarioB01
sudo useradd -m -s /bin/bash usuarioB02
sudo useradd -m -s /bin/bash usuarioB03

sudo useradd -m -s /bin/bash usuarioC01
sudo useradd -m -s /bin/bash usuarioC02
sudo useradd -m -s /bin/bash usuarioC03
```

---

# 45. PASO 3 — AÑADIR A GRUPOS

```bash
sudo usermod -aG grupoA usuarioA01
sudo usermod -aG grupoA usuarioA02
sudo usermod -aG grupoA usuarioA03

sudo usermod -aG grupoB usuarioB01
sudo usermod -aG grupoB usuarioB02
sudo usermod -aG grupoB usuarioB03

sudo usermod -aG grupoC usuarioC01
sudo usermod -aG grupoC usuarioC02
sudo usermod -aG grupoC usuarioC03
```

---

# 46. PASO 4 — COMPROBAR GRUPOS

```bash
getent group grupoA grupoB grupoC
```

---

# 47. PASO 5 — CREAR RECURSO

```bash
sudo mkdir -p /home/compartido01
sudo touch /home/compartido01/hola.txt
sudo touch /home/compartido01/adios.txt
```

---

# 48. PASO 6 — PERMISOS BASE

```bash
sudo chmod -R o-rwx /home/compartido01
```

---

# 49. PASO 7 — APLICAR ACL

```bash
sudo setfacl -R -m g:grupoA:rwx /home/compartido01
sudo setfacl -R -m g:grupoB:r-x /home/compartido01
sudo setfacl -R -m g:grupoC:--- /home/compartido01
sudo setfacl -R -m u:usuarioC03:r-x /home/compartido01
```

---

# 50. PASO 8 — COMPROBAR

```bash
getfacl /home/compartido01
getfacl /home/compartido01/hola.txt
```

---

# 51. PASO 9 — PROBAR GRUPO A

```bash
sudo -u usuarioA01 ls /home/compartido01
sudo -u usuarioA01 cat /home/compartido01/hola.txt
sudo -u usuarioA01 bash -c 'echo "prueba" >> /home/compartido01/hola.txt'
```

Debe funcionar.

---

# 52. PASO 10 — PROBAR GRUPO B

```bash
sudo -u usuarioB01 ls /home/compartido01
sudo -u usuarioB01 cat /home/compartido01/hola.txt
```

Debe funcionar.

Escritura:

```bash
sudo -u usuarioB01 bash -c 'echo "prueba" >> /home/compartido01/hola.txt'
```

Debe fallar.

---

# 53. PASO 11 — PROBAR GRUPO C

```bash
sudo -u usuarioC01 ls /home/compartido01
```

Debe fallar.

---

# 54. PASO 12 — PROBAR USUARIOC03

```bash
sudo -u usuarioC03 ls /home/compartido01
sudo -u usuarioC03 cat /home/compartido01/hola.txt
```

Debe funcionar.

Escritura:

```bash
sudo -u usuarioC03 bash -c 'echo "prueba" >> /home/compartido01/hola.txt'
```

Debe fallar.

---

# 55. ACL POR DEFECTO EN EL EJEMPLO

Si queremos que los archivos nuevos hereden:

```bash
sudo setfacl -d -m g:grupoA:rwx /home/compartido01
sudo setfacl -d -m g:grupoB:r-x /home/compartido01
sudo setfacl -d -m g:grupoC:--- /home/compartido01
sudo setfacl -d -m u:usuarioC03:r-x /home/compartido01
```

Crear uno nuevo:

```bash
sudo touch /home/compartido01/nuevo.txt
```

Comprobar:

```bash
getfacl /home/compartido01/nuevo.txt
```

---

# 56. ERRORES TÍPICOS

## Error 1 — `Permission denied`

Comprobar:

```bash
ls -l recurso
getfacl recurso
```

---

## Error 2 — La ACL parece correcta pero no funciona

Mirar:

```text
mask::
#effective:
```

---

## Error 3 — Puede leer el fichero pero no entrar a la carpeta

Probablemente falta:

```text
x
```

en algún directorio padre.

---

## Error 4 — Los ficheros nuevos no heredan permisos

Falta:

```bash
setfacl -d ...
```

---

## Error 5 — Se usó `-R` pensando que crea herencia

`-R` solo aplica recursivamente a lo que ya existe.

No sustituye a:

```text
-d
```

---

## Error 6 — Usuario no aparece en el grupo

Comprobar:

```bash
id usuario
getent group grupo
```

Si falta:

```bash
sudo usermod -aG grupo usuario
```

---

## Error 7 — Usar mal los permisos de directorio

Recuerda:

```text
r = listar
w = crear/borrar
x = entrar/atravesar
```

---

# 57. COMANDOS QUE DEBES DOMINAR

```bash
sudo groupadd grupo
sudo useradd -m -s /bin/bash usuario
sudo usermod -aG grupo usuario

id usuario
getent group grupo

sudo chown usuario:grupo recurso
sudo chmod 700 recurso

getfacl recurso
sudo setfacl -m u:usuario:rwx recurso
sudo setfacl -m g:grupo:rwx recurso
sudo setfacl -R -m g:grupo:rwx directorio
sudo setfacl -d -m g:grupo:rwx directorio
sudo setfacl -x u:usuario recurso

sudo -u usuario comando
```

---

# 58. ORDEN MENTAL PARA EL EXAMEN

Si te dan un ejercicio de ACL, piensa:

```text
1. ¿Qué usuarios hay?
2. ¿Qué grupos hay?
3. ¿Quién pertenece a qué grupo?
4. ¿Qué recursos tengo?
5. ¿Quién es propietario?
6. ¿Qué permisos base pongo?
7. ¿Quién necesita r?
8. ¿Quién necesita w?
9. ¿Quién necesita x?
10. ¿Es permiso de grupo o excepción individual?
11. ¿Debo usar -R?
12. ¿Necesito ACL por defecto con -d?
13. ¿Qué debería funcionar?
14. ¿Qué debería fallar?
```

---

# 59. RESUMEN FINAL

## Crear grupo

```bash
sudo groupadd grupo
```

## Crear usuario

```bash
sudo useradd -m -s /bin/bash usuario
```

## Añadir a grupo

```bash
sudo usermod -aG grupo usuario
```

## Ver grupo

```bash
getent group grupo
```

## Ver usuario

```bash
id usuario
```

## Dar ACL a usuario

```bash
sudo setfacl -m u:usuario:rwx recurso
```

## Dar ACL a grupo

```bash
sudo setfacl -m g:grupo:rwx recurso
```

## Recursivo

```bash
sudo setfacl -R -m g:grupo:rwx directorio
```

## Herencia

```bash
sudo setfacl -d -m g:grupo:rwx directorio
```

## Ver ACL

```bash
getfacl recurso
```

## Probar como otro usuario

```bash
sudo -u usuario comando
```

---

# Idea clave

No basta con escribir `setfacl`.

En un ejercicio completo debes saber hacer:

```text
usuarios
→ grupos
→ recursos
→ propietarios
→ chmod
→ ACL
→ mask
→ herencia
→ comprobación
→ pruebas
```

Si sigues ese orden es mucho más difícil dejarte algo.
