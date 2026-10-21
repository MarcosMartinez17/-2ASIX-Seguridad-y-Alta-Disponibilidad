# Práctica de Seguridad — Análisis de Malware

## Parte A. Entendiendo las amenazas

En el Entregable 5 estudiamos los diferentes tipos de amenazas informáticas, como virus, troyanos, ransomware, spyware, phishing y gusanos.

También clasificamos distintos incidentes de seguridad, identificando si las amenazas podían propagarse automáticamente o necesitaban la intervención del usuario.

## Parte B. Preparación de un entorno controlado

El objetivo de esta parte es preparar una máquina virtual segura para analizar archivos sospechosos sin poner en peligro el ordenador principal.

### 1. Configuración de VirtualBox

Apagamos la máquina virtual **SAD-Ubuntu (Torrent-Auditor)** y revisamos su configuración de seguridad.

Comprobamos que:

- No había carpetas compartidas con el ordenador anfitrión.
- El portapapeles compartido estaba deshabilitado.
- La opción de arrastrar y soltar estaba deshabilitada.

Estas medidas reducen el riesgo de transferir archivos maliciosos entre la máquina virtual y el ordenador principal.

### 2. Creación de una instantánea

Creamos una instantánea de la máquina virtual con el nombre:

`limpio-antes-malware`

Esta instantánea permite restaurar la máquina a su estado anterior si se produce algún problema durante el análisis.

### 3. Configuración de red

Comprobamos que la máquina disponía de tres adaptadores:

| Adaptador | Configuración |
|---|---|
| Adaptador 1 | Red interna |
| Adaptador 2 | Solo-anfitrión |
| Adaptador 3 | NAT |

Mantuvimos el adaptador NAT para instalar ClamAV y descargar las bases de datos de virus.

Antes de ejecutar cualquier programa sospechoso, tendremos que desactivar las conexiones de red para evitar posibles comunicaciones con otros equipos.

### 4. Tabla de medidas de aislamiento

| Medida de aislamiento | Estado | Riesgo que evita |
|---|---|---|
| Sin carpetas compartidas ni portapapeles | Hecho | Transferencia de malware al ordenador principal |
| Snapshot previo | Hecho | Permite recuperar el sistema |
| Sin red al ejecutar | Pendiente, Parte F | Propagación del malware y conexiones externas |
| Sin datos personales ni credenciales | Pendiente de comprobar | Robo de información personal |

---

## Parte D. Instalación y configuración de ClamAV

El objetivo es instalar un antivirus en Ubuntu y actualizar sus firmas para detectar posibles amenazas.

### 1. Actualización de repositorios

Primero actualizamos la lista de paquetes disponibles:

```bash
sudo apt update
```

### 2. Instalación de ClamAV

Instalamos el antivirus y su herramienta de actualización:

```bash
sudo apt install clamav clamav-freshclam -y
```

- **ClamAV:** permite analizar archivos y detectar malware.
- **FreshClam:** descarga y actualiza las bases de datos de virus.

### 3. Comprobación de la instalación

Ejecutamos:

```bash
clamscan --version
```

Resultado obtenido:

```text
ClamAV 1.5.4/28159/Tue Oct 20 06:24:12 2026
```

Esto confirmó que ClamAV estaba instalado correctamente.

### 4. Actualización de las firmas

Detuvimos temporalmente el servicio automático:

```bash
sudo systemctl stop clamav-freshclam
```

Después ejecutamos la actualización manual:

```bash
sudo freshclam
```

Finalmente, volvimos a iniciar el servicio:

```bash
sudo systemctl start clamav-freshclam
```

### 5. Comprobación del servicio

Ejecutamos:

```bash
sudo systemctl status clamav-freshclam
```

Obtuvimos:

```text
Active: active (running)
```

Esto confirmó que el servicio de actualización estaba funcionando.

### 6. Comprobación de las bases de datos

Ejecutamos:

```bash
ls -lh /var/lib/clamav/
```

Encontramos las siguientes bases de datos:

| Archivo | Tamaño |
|---|---|
| daily.cvd | 23 MB |
| main.cvd | 85 MB |
| bytecode.cvd | 276 KB |

También comprobamos que los archivos tenían fecha del 20 de octubre de 2026.

Esto confirmó que las bases de datos estaban descargadas.

---

<img width="945" height="582" alt="image" src="https://github.com/user-attachments/assets/7ef0c49e-aa37-4d9a-a482-c1b428d03b5a" />

<img width="876" height="468" alt="image" src="https://github.com/user-attachments/assets/c16ba146-fa2f-43c0-a679-60d778efba21" />

<img width="886" height="290" alt="image" src="https://github.com/user-attachments/assets/385a1111-6e35-4dc1-84b1-05f3d31b1f79" />


## Parte C · El fichero EICAR

Creamos el archivo `eicar.txt` en la máquina virtual SAD-Ubuntu para comprobar el funcionamiento del antivirus.

Verificamos que tenía 68 bytes y calculamos su hash SHA-256:

```bash
sha256sum ~/eicar.txt
```

**SHA-256 obtenido:**
`275a021bbfb6489e54d471899f7db9d1663fc695ec2fe2a2c4538aabf651fd0f`

Después analizamos el archivo con ClamAV:

```bash
clamscan ~/eicar.txt
```

**Resultado:** `Eicar-Test-Signature FOUND`

Finalmente, buscamos el hash en VirusTotal.

| Aspecto | Resultado |
|---|---|
| Motores que detectan EICAR | 66 |
| Motores que no lo detectan | 2 |
| Motores que no pudieron procesarlo | 7 |
| Detección de ClamAV | Correcta |

**Conclusión:** ClamAV detectó correctamente el archivo EICAR. VirusTotal mostró que 66 motores lo identificaron como una amenaza de prueba. EICAR no es un virus real, sino un archivo diseñado para comprobar el funcionamiento de los antivirus.


<img width="900" height="267" alt="image" src="https://github.com/user-attachments/assets/bf677676-7fc6-4fd2-ad6a-4815dea51019" />

<img width="786" height="85" alt="image" src="https://github.com/user-attachments/assets/d6d7aca1-c53c-4d4c-8843-47e2564efa45" />

<img width="896" height="195" alt="image" src="https://github.com/user-attachments/assets/eda81ed9-4580-43df-897a-e3d3c9cb7844" />

<img width="990" height="212" alt="image" src="https://github.com/user-attachments/assets/ecef0064-6b62-4767-a56d-7c15f01cfb93" />

<img width="942" height="402" alt="image" src="https://github.com/user-attachments/assets/2bc75679-a3ce-4db4-a6c5-c856145879c6" />

<img width="1773" height="277" alt="image" src="https://github.com/user-attachments/assets/578e1c00-a514-4051-b75a-5a0b9e9d9db4" />

<img width="1882" height="852" alt="image" src="https://github.com/user-attachments/assets/6aa11936-4d7f-4eff-8bf4-6ff2e5391eaf" />
<img width="1736" height="840" alt="image" src="https://github.com/user-attachments/assets/24ff6abd-e014-46d5-8e85-00870cb8ca14" />
<img width="1917" height="561" alt="image" src="https://github.com/user-attachments/assets/e44b6357-ffc2-4196-8b5b-bfd2702db827" />

 ## SESION 2
# Parte D · Instalar y usar ClamAV

## D1. Instalación

Actualizamos los repositorios e instalamos ClamAV en SAD-Ubuntu con el adaptador NAT activado.

```bash
sudo apt update
sudo apt install -y clamav clamav-freshclam
```

Comprobamos que la instalación se realizó correctamente.

**Versión instalada:** ClamAV 1.5.4.

## D2. Actualización de firmas

Detuvimos temporalmente el servicio automático, actualizamos las firmas y volvimos a iniciarlo.

```bash
sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam
```

Comprobamos la versión y la fecha de la base de datos:

```bash
clamscan --version
```

**Resultado obtenido:**

`ClamAV 1.5.4/28159/Tue Oct 20 06:24:12 2026`

**Fecha de actualización de las firmas:** 20/10/2026.

## D3. Escaneo del fichero EICAR

Analizamos el archivo de prueba EICAR con el siguiente comando:

```bash
clamscan ~/eicar.txt
```

**Resultados:**

| Aspecto | Resultado |
|---|---|
| Nombre de la amenaza | Eicar-Test-Signature |
| Archivos analizados | 1 |
| Archivos infectados | 1 |
| Resultado | FOUND |

## Conclusión

Instalamos ClamAV, actualizamos su base de datos de virus y comprobamos que detectaba correctamente el archivo EICAR.

El antivirus identificó la amenaza de prueba como `Eicar-Test-Signature`, demostrando que el análisis funciona correctamente.

## D4
clamscan -r -i -v ~
<img width="545" height="295" alt="image" src="https://github.com/user-attachments/assets/7922c0b0-9ad4-4c36-9b6d-6354061ac066" />

## D5. Cuarentena en vez de borrar

Paso 1. Crear la carpeta de cuarentena
mkdir -p ~/cuarentena
Paso 2. Escanear y mover los archivos detectados
clamscan -r -i -v --move=$HOME/cuarentena ~
Paso 3. Comprobar la cuarentena
ls -l ~/cuarentena

<img width="620" height="132" alt="image" src="https://github.com/user-attachments/assets/882ab5a2-1c6c-4426-a82f-c75054218406" />

**¿Por qué suele ser mejor mover a cuarentena que borrar directamente? ¿Qué pasaría si ClamAV marcara por error un fichero importante del ERP de Torrent?**

Es mejor mover los archivos a cuarentena porque permite recuperarlos si se detectan por error. Si ClamAV borrara un archivo importante del ERP, podría provocar fallos en el programa o pérdida de información. Con la cuarentena podríamos revisar el archivo y restaurarlo si fuera seguro.

## D6. Detectar rootkits

Instalamos Rkhunter para comprobar si existían posibles rootkits o modificaciones sospechosas en el sistema.

Ejecutamos los siguientes comandos:

```bash
sudo apt install -y rkhunter
sudo rkhunter --update
sudo rkhunter --propupd
sudo rkhunter --check --sk --rwo
```

Para consultar las advertencias utilizamos:

```bash
sudo grep -i "warning" /var/log/rkhunter.log
```

**Resultados:** Rkhunter mostró advertencias relacionadas con el programa `lwp-request`, la instalación de Postfix y dos archivos ocultos del sistema.

Las advertencias de Postfix parecen deberse a su instalación durante la práctica. Los archivos ocultos necesitan una comprobación adicional.

**Conclusión:** Rkhunter ha detectado cambios que deben revisarse, pero no hay pruebas suficientes para afirmar que el sistema esté infectado por un rootkit.

# Parte E · Análisis estático de un archivo sospechoso

Analizamos el archivo `sospechoso.sh` sin ejecutarlo, utilizando los siguientes comandos:

```bash
file sospechoso.sh
sha256sum sospechoso.sh
cat sospechoso.sh
```

El archivo es un script Bash utilizado como simulación didáctica.

## Pregunta 1. ¿Qué crees que hace cada línea?

El script crea una carpeta oculta en `/tmp/.cache_sistema`, guarda un registro con la fecha, hace una copia de sí mismo y abre un servidor web local en el puerto 8081.

También añade una tarea de cron para ejecutarse automáticamente después de reiniciar el sistema.

## Pregunta 2. ¿Crea ficheros? ¿Abre puertos? ¿Modifica algo para sobrevivir a un reinicio?

**Sí, realiza las tres acciones:**

- **Crea ficheros:** genera `estado.log` y `actualizador.sh` dentro de `/tmp/.cache_sistema`.
- **Abre puertos:** inicia un servidor web en el puerto 8081, accesible únicamente desde `127.0.0.1`.
- **Persistencia:** añade una tarea `@reboot` en cron para ejecutar el script cuando se reinicia el sistema.

## Pregunta 3. Si fuera un .exe binario, ¿qué habría que hacer?

Si fuera un archivo `.exe`, no podríamos leer su código directamente con `cat`.

Utilizaríamos herramientas como `file` para identificarlo, `strings` para extraer cadenas de texto y un desensamblador como Ghidra para analizar su funcionamiento sin ejecutarlo.

## Conclusión

El análisis estático nos ha permitido descubrir que el script crea archivos, abre un puerto local y establece un mecanismo de persistencia.

Aunque es una simulación didáctica, reproduce comportamientos que también pueden utilizar programas maliciosos.

<img width="827" height="117" alt="image" src="https://github.com/user-attachments/assets/8f2f2480-c3c1-42be-96e2-9f45edc1e397" />

## Parte F · Análisis dinámico: observar qué hace (CE d)
Importante quitar todos los adaptadores red
<img width="807" height="146" alt="image" src="https://github.com/user-attachments/assets/4dfebfbd-a59d-4bb8-90ec-464e72c7ab45" />

<img width="881" height="328" alt="image" src="https://github.com/user-attachments/assets/eb5a0a06-f046-41db-b1c2-005b98703fcc" />
<img width="867" height="187" alt="image" src="https://github.com/user-attachments/assets/b49db7ea-e681-4b12-8934-74d2feaf52e0" />
<img width="858" height="257" alt="image" src="https://github.com/user-attachments/assets/e2ca0133-6b7c-4cf6-80cc-f2b94cdf2ff3" />

1. Tabla de comportamiento para entregar
Qué hace	Evidencia (comando y salida)	Fase del ataque
Crea ficheros o carpetas	ls -la /tmp/.cache_sistema muestra actualizador.sh y estado.log	Ejecución
Abre algún puerto o proceso	ss -tlnp muestra Python 3 escuchando en 127.0.0.1:8081	Ejecución
Intenta persistir tras reiniciar	crontab -l muestra @reboot /bin/bash /tmp/.cache_sistema/actualizador.sh	Persistencia

Despues de reiniciar la maquina
<img width="815" height="196" alt="image" src="https://github.com/user-attachments/assets/27706c57-c248-478d-a92b-eefc3e9de7f8" />

<img width="765" height="255" alt="image" src="https://github.com/user-attachments/assets/b950ad9f-eab3-4c81-aa20-392191d96ea0" />

# Parte F · Análisis dinámico de un archivo sospechoso

## F1. Preparación del entorno

El objetivo de esta práctica era ejecutar el archivo `sospechoso.sh` en un entorno controlado y observar los cambios que realizaba en el sistema.

Antes de ejecutarlo, desactivamos los tres adaptadores de red de SAD-Ubuntu en VirtualBox para evitar conexiones con otros equipos.

Comprobamos el aislamiento mediante:

```bash
ip -br a
```

**Resultado:** Únicamente aparecía la interfaz local `lo`, con la dirección `127.0.0.1`.

También preparamos una instantánea llamada `antes-de-ejecutar` para poder restaurar la máquina.

## F2. Captura del estado inicial

Antes de ejecutar el programa, guardamos información de los procesos, puertos, tareas programadas y archivos temporales.

```bash
ps aux > ~/antes_procesos.txt
ss -tulpn > ~/antes_puertos.txt
crontab -l > ~/antes_cron.txt 2>&1
ls -la /tmp > ~/antes_tmp.txt
```

Estos archivos nos permitieron comparar el estado del sistema antes y después de ejecutar el programa.

## F3. Ejecución del archivo sospechoso

Ejecutamos el script como usuario normal, sin utilizar permisos de administrador.

```bash
cd ~/Downloads
bash sospechoso.sh
```

El programa terminó sin mostrar errores en la terminal.

## F4. Captura del estado posterior

Después de ejecutar el script, repetimos los comandos de análisis:

```bash
ps aux > ~/despues_procesos.txt
ss -tulpn > ~/despues_puertos.txt
crontab -l > ~/despues_cron.txt 2>&1
ls -la /tmp > ~/despues_tmp.txt
```

Comparamos los resultados utilizando:

```bash
diff ~/antes_procesos.txt ~/despues_procesos.txt
diff ~/antes_puertos.txt ~/despues_puertos.txt
diff ~/antes_cron.txt ~/despues_cron.txt
diff ~/antes_tmp.txt ~/despues_tmp.txt
```

Detectamos cambios en los archivos temporales, los procesos y las tareas programadas.

## F5. Informe de comportamiento

| ¿Qué hace? | Evidencia (comando y salida) | ¿Qué fase del ataque recuerda? |
|---|---|---|
| Crea ficheros o carpetas | `ls -la /tmp/.cache_sistema` mostró `actualizador.sh` y `estado.log`. | Ejecución y modificación del sistema |
| Abre algún puerto o proceso | `ss -tlnp \| grep 8081` mostró Python 3 escuchando en `127.0.0.1:8081`. | Ejecución |
| Intenta persistir tras un reinicio | `crontab -l` mostró `@reboot /bin/bash /tmp/.cache_sistema/actualizador.sh`. | Persistencia |

### Archivos creados

El programa creó una carpeta oculta:

`/tmp/.cache_sistema`

Dentro encontramos:

- `actualizador.sh`: copia del script original.
- `estado.log`: archivo que registra la fecha y la actividad.

### Puerto abierto

El script inició un servidor HTTP mediante Python 3.

**Puerto:** 8081  
**Dirección:** 127.0.0.1  
**Proceso:** python3  
**PID observado:** 3616

El servicio únicamente escuchaba en la propia máquina virtual.

### Persistencia

El programa añadió una tarea de cron:

```bash
@reboot /bin/bash /tmp/.cache_sistema/actualizador.sh
```

Su objetivo era volver a ejecutar el script cada vez que se reiniciara el sistema.

---

## F6. Comprobación de la persistencia

Reiniciamos SAD-Ubuntu para comprobar si el comportamiento se mantenía.

```bash
sudo reboot
```

Después del reinicio ejecutamos:

```bash
ss -tlnp | grep 8081
crontab -l
ls -la /tmp/.cache_sistema
```

### Resultados

| Comprobación | Resultado |
|---|---|
| Puerto 8081 | No aparecía abierto |
| Tarea de cron | Seguía configurada |
| Carpeta oculta | Había desaparecido |
| Persistencia | No consiguió ejecutarse correctamente |

**Conclusión:** El script intentó establecer persistencia mediante cron, pero la carpeta `/tmp/.cache_sistema` desapareció después del reinicio. Por tanto, la tarea programada ya no encontraba el archivo que debía ejecutar.

---

## F7. Erradicación

Eliminamos los restos identificados del programa.

### 1. Eliminar la tarea de cron

Ejecutamos:

```bash
crontab -e
```

Eliminamos la línea:

```bash
@reboot /bin/bash /tmp/.cache_sistema/actualizador.sh
```

Después comprobamos:

```bash
crontab -l
```

**Resultado:** No aparecía ninguna tarea programada.

### 2. Comprobar el puerto

```bash
ss -tlnp | grep 8081
```

**Resultado:** No apareció ningún servicio escuchando en el puerto 8081.

### 3. Comprobar los archivos

```bash
ls -ld /tmp/.cache_sistema
```

**Resultado:**

```text
ls: cannot access '/tmp/.cache_sistema': No such file or directory
```

### 4. Comprobar los procesos

```bash
pgrep -af 'http.server 8081'
```

**Resultado:** No se encontró ningún proceso relacionado.

### Resumen de la erradicación

| Elemento | Estado final |
|---|---|
| Tarea programada | Eliminada |
| Puerto 8081 | Cerrado |
| Carpeta sospechosa | No existe |
| Proceso Python sospechoso | No detectado |

Las comprobaciones confirmaron que los elementos identificados durante el análisis habían dejado de estar activos.

Como medida adicional de seguridad, se previó restaurar la instantánea `antes-de-ejecutar`.

---

## F8. Pregunta final

**Habéis encontrado el comportamiento comparando «antes» y «después». ¿Cuál es el equivalente en un servidor real de Torrent si nadie hizo la foto inicial? ¿Qué relación tiene con el inventario de servicios que hicisteis en la práctica anterior?**

En un servidor real utilizaríamos herramientas de monitorización, registros del sistema y análisis de procesos y puertos para detectar comportamientos sospechosos.

El inventario de servicios de la práctica anterior nos permite conocer qué puertos y programas deberían estar funcionando normalmente.

Si aparece un puerto nuevo, un proceso desconocido o una tarea programada sospechosa, podríamos compararlo con el inventario y detectar posibles amenazas.

## Conclusión final

En esta práctica realizamos un análisis dinámico de un script sospechoso dentro de una máquina virtual aislada.

Comprobamos que el programa creaba archivos ocultos, iniciaba un servidor web local en el puerto 8081 y añadía una tarea de cron para intentar ejecutarse tras un reinicio.

También verificamos que la persistencia no funcionaba correctamente después del reinicio y eliminamos los elementos sospechosos identificados.

La práctica demuestra la importancia de controlar los procesos, los puertos abiertos, los archivos del sistema y las tareas programadas para detectar posibles amenazas informáticas.


## Parte G · Preventivo o paliativo

| Medida | Preventiva | Paliativa |
|---|:---:|:---:|
| Mantener el sistema actualizado | X | |
| ClamAV escaneando a demanda un disco | | X |
| Descargar software solo de repositorios oficiales | X | |
| Copia de seguridad semanal desconectada de la red | | X |
| Cuenta de usuario sin privilegios de administrador | X | |
| rkhunter tras sospechar una intrusión | | X |
| Formación del personal contra el phishing | X | |

**Conclusión:** Las medidas preventivas buscan evitar los ataques, mientras que las paliativas permiten detectar amenazas, limitar sus consecuencias o recuperar la información afectada.



