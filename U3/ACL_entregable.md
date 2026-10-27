## Tarea 1

### 1. Comprobación de ACL
```bash
cd /home
dpkg -s acl
```

<img width="613" height="395" alt="image" src="https://github.com/user-attachments/assets/c8165795-e498-4275-9874-9b3258b5df7b" />
Status: install ok installed

### 2. Creación de grupos

```bash
sudo groupadd operarios
sudo groupadd ingenieria
sudo groupadd supervisores
```

<img width="677" height="186" alt="image" src="https://github.com/user-attachments/assets/76b1e392-24d1-4e82-ba77-5bc4f58c2588" />

### 3. Creación de usuarios

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
<img width="580" height="230" alt="image" src="https://github.com/user-attachments/assets/522acf88-4640-45c6-b7cb-9a5c50a70f73" />

### 4. Asignación de usuarios a sus grupos


```bash
sudo usermod -aG operarios alberto
sudo usermod -aG operarios oscar
sudo usermod -aG operarios hugo

sudo usermod -aG ingenieria nestor
sudo usermod -aG ingenieria victor
sudo usermod -aG ingenieria lucia

sudo usermod -aG supervisores carlos
sudo usermod -aG supervisores mary
sudo usermod -aG supervisores john
```
<img width="565" height="323" alt="image" src="https://github.com/user-attachments/assets/717ee4ef-6540-4c2d-9b03-6e0a9e202f87" />

### 5. Comprobación de los directorios personales

```bash
cd /home
ls -ld alberto oscar hugo nestor victor lucia carlos mary john
```
<img width="592" height="247" alt="image" src="https://github.com/user-attachments/assets/d7e20c9a-e432-4fcd-87ce-f50c573e23be" />

## Tarea 2

### 1. Creación de los recursos

Creamos el archivo hmi_planta y el directorio planos, que contiene los tres ficheros

```bash
cd /home
sudo touch hmi_planta
sudo mkdir -p planos

sudo touch /home/planos/autoclave.dwg
sudo touch /home/planos/caldera.dwg
sudo touch /home/planos/destilacion.dwg
```

### 2. Configuración de propietarios

Establecemos como propietario de los recursos al usuario administrador

```bash
sudo chown linux:linux /home/hmi_planta
sudo chown -R linux:linux /home/planos
```

El comando chown permite modificar el propietario y el grupo asociado a un archivo o directorio

### 3. Configuración de permisos iniciales

```bash
sudo chmod 700 /home/hmi_planta
sudo chmod 700 /home/planos
sudo chmod 600 /home/planos/*.dwg
```

Utilizamos chmod para establecer permisos restrictivos antes de aplicar las ACL.

- 700: concede lectura, escritura y ejecución únicamente al propietario.
- 600: concede lectura y escritura únicamente al propietario.

Con esta configuración, ningún otro usuario tiene acceso inicialmente a los recursos.

### 4. Verificación

```bash
ls -l /home/hmi_planta
ls -ld /home/planos
ls -l /home/planos
```
<img width="603" height="665" alt="image" src="https://github.com/user-attachments/assets/5a69145b-36fc-48eb-8c5c-d3bcfe3a7176" />


## Tarea 3 

### 1. Configurar las ACL de hmi_planta

Utilizamos setfacl para establecer los permisos especiales de acceso

```bash
sudo setfacl -m g:operarios:--x /home/hmi_planta
sudo setfacl -m u:nestor:r-x /home/hmi_planta
sudo setfacl -m u:lucia:rwx /home/hmi_planta
```

**Explicación de los comandos:**

- `setfacl -m`: añade o modifica entradas de una lista de control de acceso.
- `g:operarios:--x`: concede al grupo operarios permiso de ejecución.
- `u:nestor:r-x`: concede a Néstor permisos de lectura y ejecución.
- `u:lucia:rwx`: concede a Lucía permisos de lectura, escritura y ejecución.

Estas reglas permiten configurar permisos diferentes para cada usuario o grupo sobre un mismo archivo.

### 2. Verificar los permisos

```bash
getfacl /home/hmi_planta
```

Utilizamos `getfacl` para consultar las ACL del recurso y comprobar que coinciden con las indicadas en el diagrama.
<img width="670" height="352" alt="image" src="https://github.com/user-attachments/assets/28cb738b-22a0-4f36-9c57-0b130f069bc7" />


<img width="790" height="87" alt="image" src="https://github.com/user-attachments/assets/8017b910-5a24-4f55-b720-88430418d4f1" />

### 3. Configuración de ACL en la carpeta planos

Configuramos los permisos de acceso a la carpeta planos siguiendo el esquema del enunciado.

```bash
sudo setfacl -m u:victor:rwx /home/planos
sudo setfacl -m g:supervisores:r-x /home/planos
sudo setfacl -m u:alberto:--x,u:oscar:--x /home/planos
```

Víctor dispone de permisos de lectura, escritura y acceso al directorio. Los supervisores pueden consultar y listar el contenido sin modificarlo.

Alberto y Óscar tienen permiso de acceso al directorio, necesario para poder abrir los archivos concretos que se les autorizan.

### 4. Configuración de ACL sobre los archivos

```bash
sudo setfacl -m u:victor:rw- /home/planos/*.dwg
sudo setfacl -m g:supervisores:r-- /home/planos/*.dwg
```

Concedemos a Víctor permisos de lectura y escritura sobre todos los planos, mientras que los supervisores únicamente pueden leerlos.

Añadimos las excepciones individuales:

```bash
sudo setfacl -m u:alberto:r-- /home/planos/autoclave.dwg
sudo setfacl -m u:oscar:r-- /home/planos/caldera.dwg
```

Estas reglas permiten que Alberto consulte autoclave.dw y Óscar consulte caldera.dwg, sin concederles acceso a los demás documentos.


<img width="530" height="275" alt="image" src="https://github.com/user-attachments/assets/ce76258d-e514-46aa-bceb-248d21816c28" />

### 5. Verificación de las ACL

```bash
getfacl /home/planos
getfacl /home/planos/autoclave.dwg
getfacl /home/planos/caldera.dwg
getfacl /home/planos/destilacion.dwg
```

Comprobamos que los permisos de cada recurso coinciden con el diagrama del profesor.

<img width="558" height="256" alt="image" src="https://github.com/user-attachments/assets/5b897fcc-39f1-4e63-9875-75e310dea5f8" />
<img width="512" height="240" alt="image" src="https://github.com/user-attachments/assets/4ff11937-f2a2-4782-9c79-4a4a4ff95c34" />
<img width="553" height="222" alt="image" src="https://github.com/user-attachments/assets/67033e4f-fd00-4170-b17a-680563cc8bf4" />


### 6. Configuración de ACL por defecto

Configuramos las ACL por defecto de la carpeta planos para que los archivos y subdirectorios nuevos hereden los permisos generales que hemos establecido.

```bash
sudo setfacl -d -m u:victor:rwx /home/planos
sudo setfacl -d -m g:supervisores:r-x /home/planos
sudo setfacl -d -m o::--- /home/planos
```

El parámetro -d permite establecer las ACL por defecto de un directorio.

De esta manera, Víctor y los supervisores dispondrán de entradas ACL en los nuevos archivos. Las excepciones individuales de Alberto y Óscar se mantienen únicamente sobre los archivos concretos autorizados.

Comprobamos los permisos:

```bash
getfacl /home/planos
```

Creamos un archivo de prueba para verificar la herencia:

```bash
sudo touch /home/planos/prueba_acl.txt
getfacl /home/planos/prueba_acl.txt
```

Con`getfacl verificamos que el nuevo archivo ha heredado las entradas ACL correspondientes.

<img width="627" height="483" alt="image" src="https://github.com/user-attachments/assets/01f05bdf-3d9b-4831-b57e-5f38abb9c4a8" />
<img width="572" height="248" alt="image" src="https://github.com/user-attachments/assets/06927afe-2419-4885-8cc6-fa41e132ba12" />

## Tarea 4


### 1. Comprobación de hmi_planta

Utilizamos sudo -u para comprobar los permisos de los usuarios sin necesidad de iniciar una sesión independiente para cada uno.

```bash
sudo -u alberto test -x /home/hmi_planta && echo "Alberto puede ejecutar"
sudo -u oscar test -x /home/hmi_planta && echo "Oscar puede ejecutar"
sudo -u hugo test -x /home/hmi_planta && echo "Hugo puede ejecutar"
```

El comando test -x comprueba si un usuario tiene permiso de ejecución sobre un archivo.

También verificamos los permisos individuales de ingeniería:

```bash
sudo -u nestor test -r /home/hmi_planta && echo "Nestor puede leer"
sudo -u nestor test -x /home/hmi_planta && echo "Nestor puede ejecutar"
sudo -u lucia test -w /home/hmi_planta && echo "Lucia puede escribir"
```
<img width="887" height="243" alt="image" src="https://github.com/user-attachments/assets/620bc4bb-4066-4fdf-b0ef-bf8244f08ca2" />

### 2. Comprobación de planos

Verificamos que Víctor y supervisores tienen acceso:

```bash
sudo -u victor ls /home/planos
sudo -u carlos ls /home/planos
sudo -u mary cat /home/planos/autoclave.dwg
```
<img width="557" height="102" alt="image" src="https://github.com/user-attachments/assets/6a19b585-92e0-447e-af86-00bc08162395" />

Comprobamos las excepciones individuales de Alberto y Óscar:

```bash
sudo -u alberto cat /home/planos/autoclave.dwg
sudo -u oscar cat /home/planos/caldera.dwg
```
<img width="651" height="43" alt="image" src="https://github.com/user-attachments/assets/8535b722-1e96-4218-83ee-c635d4952e35" />

Por último, comprobamos que no pueden acceder a archivos no autorizados:

```bash
sudo -u alberto cat /home/planos/caldera.dwg
sudo -u oscar cat /home/planos/autoclave.dwg
```
Estos últimos comandos deben mostrar `Permission denied`.
<img width="636" height="82" alt="image" src="https://github.com/user-attachments/assets/7fb0c66a-bf55-447e-a5dd-9eaed2f35351" />

### 3. Verificación final de las ACL

getfacl /home/hmi_planta
<img width="496" height="242" alt="image" src="https://github.com/user-attachments/assets/ef2eb684-d9c0-4a1c-baf0-5fc10fda4cdc" />

getfacl /home/planos
<img width="506" height="381" alt="image" src="https://github.com/user-attachments/assets/88279fe5-0a24-4315-9ef6-32c025cb9ae9" />

getfacl /home/planos/autoclave.dwg
<img width="535" height="245" alt="image" src="https://github.com/user-attachments/assets/367d2a7c-8da4-40a9-ba9b-30e6ecf57516" />

getfacl /home/planos/caldera.dwg
<img width="517" height="245" alt="image" src="https://github.com/user-attachments/assets/b8bbfa00-4781-4ee0-b27d-b530598a8ec5" />

getfacl /home/planos/destilacion.dwg
<img width="560" height="225" alt="image" src="https://github.com/user-attachments/assets/df6d37ab-7ee0-4ee7-ac90-c9ce132863c0" />


## Preguntas de reflexión
## 6. Preguntas de reflexión

### 1. 
Los permisos clásicos de Unix solo permiten asignar permisos al propietario, a un grupo y al resto de usuarios. 
Por eso no son suficientes cuando necesitamos dar permisos diferentes a varios usuarios y grupos sobre el mismo archivo.

Por ejemplo, en el archivo hmi_planta necesitamos que los operarios puedan ejecutarlo, que Néstor pueda leerlo y ejecutarlo y que Lucía tenga permisos de lectura, escritura y ejecución
Con los permisos tradicionales no podemos configurar todas estas diferencias directamente. Por eso utilizamos las ACL, que permiten establecer permisos específicos para cada usuario o grupo.

### 2.
La máscara (mask) establece los permisos máximos efectivos que pueden tener los usuarios y grupos incluidos en las entradas ACL, excepto el propietario del archivo.
Por ejemplo, si configuramos:

user:lucia:rwx
mask::r-x

Aunque Lucía tenga asignados los permisos rwx, realmente solo podrá leer y ejecutar porque la máscara elimina el permiso de escritura
Por tanto los permisos efectivos son los que resultan de combinar la entrada ACL con la máscara

### 3.

Una ACL de acceso establece los permisos que tienen los usuarios y grupos sobre un archivo o directorio existente
Por ejemplo:
sudo setfacl -m u:victor:rwx /home/planos

Este comando establece los permisos de Víctor sobre el directorio planos
Una ACL por defecto se configura únicamente en directorios y sirve para que los nuevos archivos y subdirectorios hereden una configuración de permisos cuando se crean
Por ejemplo:
sudo setfacl -d -m g:supervisores:r-x /home/planos


Con esta regla los nuevos recursos creados dentro de planos heredarán una entrada ACL para los supervisores

En resumen:
- ACL de acceso: controla los permisos actuales sobre un recurso
- ACL por defecto:establece los permisos que heredarán los recursos nuevos dentro de un directorio

### 4.

Primero tendríamos que crear el nuevo usuario en Linux.
Por ejemplo si se incorpora un trabajador llamado Pedro:

sudo useradd -m -s /bin/bash pedro
Después lo añadiríamos al grupo operarios

sudo usermod -aG operarios pedro

Por último, comprobaríamos que pertenece al grupo correcto:

id pedro
No sería necesario volver a configurar las ACL de hmi_planta, porque ya hemos concedido al grupo operarios el permiso de ejecución:

sudo setfacl -m g:operarios:--x /home/hmi_planta
Al pertenecer al grupo, Pedro recibiría automáticamente esos mismos permisos de grupo.

Para verificarlo
sudo -u pedro test -x /home/hmi_planta && echo "Pedro tiene permiso de ejecución"



