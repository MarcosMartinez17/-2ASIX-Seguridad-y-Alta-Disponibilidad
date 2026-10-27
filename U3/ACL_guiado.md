# SAD — Actividad Guiada: Permisos ACL en Linux

## 1. Objetivo de la práctica

El objetivo de esta práctica es aprender a gestionar permisos avanzados de archivos y directorios en Linux mediante las **ACL (Access Control Lists)**.

Las ACL permiten asignar permisos específicos a distintos usuarios y grupos sobre un mismo recurso.

Vamos a crear:

- 3 grupos: `grupoA`, `grupoB` y `grupoC`.
- 9 usuarios: tres para cada grupo.
- 2 directorios compartidos: `compartido01` y `compartido02`.
- 2 archivos en cada directorio: `hola.txt` y `adios.txt`.
- Reglas ACL con distintos permisos para cada grupo.
- Una excepción de permisos para `usuarioC03`.

### Permisos solicitados

| Usuario o grupo | Lectura | Escritura | Listado/acceso |
|---|---|---|---|
| grupoA | Sí | Sí | Sí |
| grupoB | Sí | No | Sí |
| grupoC | No | No | No |
| usuarioC03 | Sí | No | Sí |

**Máquina utilizada:** SAD-Ubuntu (Auditor).

---

## 2. Conexión SSH

Para trabajar cómodamente desde Windows, instalamos y activamos el servidor SSH en Ubuntu.

### Comandos en SAD-Ubuntu

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
```

Comprobamos su estado:

```bash
sudo systemctl status ssh
```

El resultado debe mostrar:

```text
Active: active (running)
```

### Conexión desde Windows

En VirtualBox configuramos una regla de reenvío de puertos sobre el adaptador NAT:

| Campo | Valor |
|---|---|
| Protocolo | TCP |
| IP anfitrión | 127.0.0.1 |
| Puerto anfitrión | 2222 |
| IP invitado | Vacío |
| Puerto invitado | 22 |

Desde PowerShell:

```powershell
ssh linux@127.0.0.1 -p 2222
```

Si el servidor SSH se ha reinstalado y ha cambiado su clave, podemos eliminar la entrada antigua de Windows, previa comprobación de que se trata de nuestra máquina:

```powershell
ssh-keygen -R "[127.0.0.1]:2222"
```

Después volvemos a conectarnos y verificamos la nueva clave.

### Explicación

SSH permite administrar Ubuntu de forma remota mediante una conexión cifrada.

El puerto `2222` de Windows se redirige al puerto `22` de la máquina virtual.

---

## 3. Acceder al directorio /home

```bash
cd /home
pwd
ls -la
```

### Explicación

- `cd /home`: nos sitúa en el directorio donde crearemos los recursos compartidos.
- `pwd`: muestra el directorio actual.
- `ls -la`: muestra los archivos, directorios y permisos.

El resultado de `pwd` debe ser:

```text
/home
```

---

## 4. Crear los grupos

```bash
sudo groupadd grupoA
sudo groupadd grupoB
sudo groupadd grupoC
```

Comprobación:

```bash
getent group grupoA grupoB grupoC
```

### Explicación

Utilizamos `groupadd` para crear los tres grupos que formarán parte de la actividad.

Los grupos permiten asignar permisos a varios usuarios simultáneamente.

Si los grupos ya existen, no es necesario volver a crearlos.

---

## 5. Crear los usuarios

### Usuarios del grupo A

```bash
sudo useradd -m -s /bin/bash usuarioA01
sudo useradd -m -s /bin/bash usuarioA02
sudo useradd -m -s /bin/bash usuarioA03
```

### Usuarios del grupo B

```bash
sudo useradd -m -s /bin/bash usuarioB01
sudo useradd -m -s /bin/bash usuarioB02
sudo useradd -m -s /bin/bash usuarioB03
```

### Usuarios del grupo C

```bash
sudo useradd -m -s /bin/bash usuarioC01
sudo useradd -m -s /bin/bash usuarioC02
sudo useradd -m -s /bin/bash usuarioC03
```

Comprobación:

```bash
ls -ld /home/usuario*
```

### Explicación

- `useradd`: crea un nuevo usuario.
- `-m`: crea su directorio personal en `/home`.
- `-s /bin/bash`: establece Bash como intérprete de comandos.

En total hemos creado nueve usuarios.

---

## 6. Añadir los usuarios a sus grupos

### Grupo A

```bash
sudo usermod -aG grupoA usuarioA01
sudo usermod -aG grupoA usuarioA02
sudo usermod -aG grupoA usuarioA03
```

### Grupo B

```bash
sudo usermod -aG grupoB usuarioB01
sudo usermod -aG grupoB usuarioB02
sudo usermod -aG grupoB usuarioB03
```

### Grupo C

```bash
sudo usermod -aG grupoC usuarioC01
sudo usermod -aG grupoC usuarioC02
sudo usermod -aG grupoC usuarioC03
```

Comprobación:

```bash
getent group grupoA grupoB grupoC
```

### Explicación

Con `usermod -aG` añadimos cada usuario a su grupo secundario correspondiente.

- `-a`: añade sin eliminar otros grupos secundarios.
- `-G`: indica el grupo al que queremos añadirlo.

La estructura queda organizada de la siguiente forma:

```text
grupoA
├── usuarioA01
├── usuarioA02
└── usuarioA03

grupoB
├── usuarioB01
├── usuarioB02
└── usuarioB03

grupoC
├── usuarioC01
├── usuarioC02
└── usuarioC03
```

---

## 7. Instalar las herramientas ACL

```bash
sudo apt update
sudo apt install acl -y
```

Comprobación:

```bash
which setfacl
which getfacl
```

### Explicación

Instalamos el paquete `acl`, que proporciona las herramientas necesarias para gestionar las listas de control de acceso.

- `setfacl`: establece o modifica permisos ACL.
- `getfacl`: consulta los permisos ACL.

---

## 8. Crear los directorios compartidos

```bash
cd /home
sudo mkdir -p compartido01 compartido02
```

Comprobación:

```bash
ls -ld /home/compartido01 /home/compartido02
```

### Explicación

El comando `mkdir` permite crear directorios.

Creamos dos recursos compartidos:

```text
/home/compartido01
/home/compartido02
```

Sobre estos directorios aplicaremos las reglas ACL.

---

## 9. Crear los archivos compartidos

```bash
sudo touch /home/compartido01/hola.txt /home/compartido01/adios.txt
sudo touch /home/compartido02/hola.txt /home/compartido02/adios.txt
```

Añadimos contenido de ejemplo:

```bash
echo "Hola desde compartido01" | sudo tee /home/compartido01/hola.txt
echo "Adios desde compartido01" | sudo tee /home/compartido01/adios.txt
```

```bash
echo "Hola desde compartido02" | sudo tee /home/compartido02/hola.txt
echo "Adios desde compartido02" | sudo tee /home/compartido02/adios.txt
```

Comprobación:

```bash
ls -l /home/compartido01
ls -l /home/compartido02
```

### Explicación

- `touch`: crea archivos vacíos.
- `echo`: genera texto.
- `tee`: introduce ese texto en un archivo.

La estructura de los recursos compartidos queda así:

```text
/home/
├── compartido01/
│   ├── hola.txt
│   └── adios.txt
└── compartido02/
    ├── hola.txt
    └── adios.txt
```

---

## 10. Preparar los permisos básicos

Antes de aplicar las ACL, eliminamos los permisos de la categoría `otros` para evitar que los usuarios accedan mediante permisos generales.

```bash
sudo chmod -R o-rwx /home/compartido01 /home/compartido02
```

### Explicación

- `chmod`: modifica los permisos tradicionales.
- `-R`: aplica el cambio recursivamente.
- `o-rwx`: elimina lectura, escritura y ejecución para otros usuarios.

Este paso complementa la configuración del PDF y permite comprobar correctamente que los usuarios del grupo C no pueden acceder por los permisos generales.

---

## 11. Configurar permisos ACL para el grupo A

```bash
sudo setfacl -R -m g:grupoA:rwx /home/compartido01
```

### Explicación

Asignamos permisos completos al grupo A.

- `setfacl`: modifica permisos ACL.
- `-R`: aplica las reglas recursivamente.
- `-m`: modifica o añade una entrada.
- `g:grupoA`: indica el grupo destinatario.
- `rwx`: lectura, escritura y ejecución.

Los usuarios del grupo A podrán leer, modificar y acceder al contenido compartido.

---

## 12. Configurar permisos ACL para el grupo B

```bash
sudo setfacl -R -m g:grupoB:r-x /home/compartido01
```

### Explicación

Asignamos al grupo B los permisos `r-x`.

- `r`: permite leer.
- `-`: indica que no hay permiso de escritura.
- `x`: permite entrar en los directorios.

Por tanto, los miembros del grupo B podrán consultar y listar archivos, pero no modificarlos.

---

## 13. Denegar permisos al grupo C

```bash
sudo setfacl -R -m g:grupoC:--- /home/compartido01
```

### Explicación

Asignamos al grupo C los permisos `---`, es decir, ninguno.

Sus usuarios no podrán leer, escribir ni acceder al contenido por pertenecer a este grupo.

Posteriormente configuraremos una excepción individual.

---

## 14. Crear una excepción para usuarioC03

```bash
sudo setfacl -R -m u:usuarioC03:r-x /home/compartido01
```

### Explicación

Aunque `usuarioC03` pertenece al grupo C, le concedemos permisos individuales de lectura y acceso.

- `u:usuarioC03`: establece una regla para un usuario concreto.
- `r-x`: permite leer y acceder, pero no escribir.

Esto demuestra una de las principales ventajas de las ACL: **poder asignar permisos específicos a un usuario independientemente de los permisos generales de su grupo**.

---

## 15. Consultar las ACL del primer directorio

```bash
getfacl /home/compartido01
getfacl /home/compartido01/hola.txt
```

### Explicación

Utilizamos `getfacl` para visualizar los permisos asignados.

Debemos encontrar entradas equivalentes a:

```text
user:usuarioC03:r-x
group:grupoA:rwx
group:grupoB:r-x
group:grupoC:---
```

También comprobamos la entrada `mask`, que determina los permisos efectivos máximos de las entradas ACL correspondientes.

---

## 16. Replicar los permisos en compartido02

Siguiendo el método del profesor, copiamos las ACL del primer directorio al segundo:

```bash
getfacl /home/compartido01 | sudo setfacl -R --set-file=- /home/compartido02
```

Comprobamos los directorios:

```bash
getfacl /home/compartido01
getfacl /home/compartido02
```

### Explicación

- `getfacl`: obtiene la configuración de permisos del primer directorio.
- `|`: envía el resultado del primer comando al segundo.
- `setfacl --set-file=-`: aplica las entradas recibidas.
- `-R`: actúa recursivamente.

Así reutilizamos las ACL ya configuradas sin tener que escribir todas las reglas de nuevo.

También comprobamos que los archivos del segundo directorio reciben las ACL esperadas:

```bash
getfacl /home/compartido02/hola.txt
getfacl /home/compartido02/adios.txt
```

Si alguna entrada no coincide, podemos aplicar directamente las reglas ACL necesarias sobre `compartido02`:

```bash
sudo setfacl -R -m g:grupoA:rwx,g:grupoB:r-x,g:grupoC:---,u:usuarioC03:r-x /home/compartido02
```

---

## 17. Comprobar que los directorios tienen ACL

```bash
cd /home
ls -ld compartido01 compartido02
```

Comprobamos también los archivos:

```bash
ls -l compartido01
ls -l compartido02
```

### Explicación

Al ejecutar `ls -l`, los archivos y directorios con ACL extendidas muestran normalmente un símbolo `+` al final de sus permisos.

Por ejemplo:

```text
drwxrwx---+ ... compartido01
```

El símbolo `+` indica que el archivo o directorio dispone de permisos ACL adicionales.

---

## 18. Comprobar los permisos del grupo A

Utilizamos `usuarioA01` para comprobar los permisos del grupo A.

### Lectura y listado

```bash
sudo -u usuarioA01 ls /home/compartido01
sudo -u usuarioA01 cat /home/compartido02/adios.txt
```

### Escritura

```bash
sudo -u usuarioA01 bash -c 'echo "Modificado por A01" >> /home/compartido02/adios.txt'
cat /home/compartido02/adios.txt
```

### Resultado esperado

El usuario puede:

- Listar el contenido.
- Leer los archivos.
- Modificar su contenido.

### Explicación

`usuarioA01` pertenece al grupo A, que tiene permisos `rwx`.

Por eso puede realizar las tres operaciones sin problemas.

---

## 19. Comprobar los permisos del grupo B

Utilizamos `usuarioB01`.

### Lectura y listado

```bash
sudo -u usuarioB01 ls /home/compartido01
sudo -u usuarioB01 cat /home/compartido02/adios.txt
