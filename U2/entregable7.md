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

SESION 2

