# UD2 · Seguridad activa — Guía de teoría y prácticas para el examen

**Módulo:** Seguridad y Alta Disponibilidad (2.º ASIX)  
**Objetivo:** consultar conceptos y resolver ejercicios similares a los realizados en clase.  
**Fuentes utilizadas:** `UD2 - Seguridad Activa(4).pdf` (35 páginas); `UD02_tumbar_un_servicio_paso_a_paso(1).pdf` (4 páginas); y, dentro de la carpeta **`U2` de la raíz del repositorio**, `entregable2.md`, `lab_fases_1_2.md`, `entregable5.md`, `entregable6.md` y `entregable7.md`. **Se excluye expresamente `U2/examen.md`.**

> **Cómo usar estos apuntes:** la Parte I resume la teoría de las diapositivas; la Parte II describe las prácticas y explica *qué haces, por qué, el resultado esperado y cómo interpretarlo*. Los valores observados en las prácticas pueden variar de una máquina a otra. Los comandos son para **laboratorios propios o autorizados**, no para equipos ajenos.

---

# PARTE I — TEORÍA DE UD2

## 1. Seguridad activa, amenazas y ataques

- **Amenaza:** situación o acción, intencionada o accidental, que puede comprometer la seguridad de un sistema.
- **Ataque:** realización de una amenaza; es cuando esa posibilidad se materializa.
- **Seguridad activa:** controles orientados a impedir, dificultar o detectar accesos y ataques: contraseñas, antivirus, actualizaciones, cortafuegos y controles de acceso.
- **Seguridad paliativa / respuesta:** medidas para descubrir daños, contenerlos y recuperarse: análisis posterior, cuarentena, copias de seguridad y restauración.
- **Confidencialidad:** que personas no autorizadas no lean información.
- **Integridad:** que la información no se modifique de forma indebida.
- **Disponibilidad:** que los servicios y datos estén accesibles a quien los necesita.
- **Autenticidad:** que la identidad o el origen sean verdaderos.

**Tipos de afectación que aparecen en el tema:**

| Situación | Qué hace un atacante | Propiedad afectada principalmente |
|---|---|---|
| Interceptación | Escucha o copia información | Confidencialidad |
| Modificación | Altera información | Integridad |
| Fabricación | Introduce información o identidad falsa | Autenticidad |
| Interrupción | Impide que llegue información o funcione un servicio | Disponibilidad |

**Motivaciones mencionadas:** económicas, ideológicas, diversión y autorrealización. Un ataque combina atacante, herramientas, acceso a vulnerabilidades, proceso, resultados y objetivos (diapositivas 5–6).

## 2. Ataques pasivos y activos

| Ataque pasivo | Ataque activo |
|---|---|
| Observa, escucha o registra sin modificar el tráfico | Cambia mensajes, recursos o disponibilidad |
| Ej.: sniffing y escucha de comunicaciones | Ej.: suplantación, modificación, DoS |
| Difícil de detectar porque no altera los datos | Puede dejar trazas, errores y cambios visibles |
| Medida importante: cifrado | Medidas: autenticación, filtrado, parches, IDS/IPS |

**Ejemplos que debes reconocer:** SMTP, DNS e IP spoofing (suplantación); XSS, inyección SQL, troyanos, gusanos, robo de contraseñas, DoS (una fuente) y DDoS (varias fuentes). No confundas un **escaneo** con una explotación: descubrir un servicio no significa que se haya entrado en él.

## 3. Las cinco fases de un ataque

| Fase del temario | Qué busca | Ejemplo o indicio | Defensa |
|---|---|---|---|
| 1. Reconocimiento (Reconnaissance) | Recopilar información externa | Web pública, WHOIS, RR. SS. | Exponer los mínimos datos, formación |
| 2. Exploración (Scanning) | Conocer IP, hosts, puertos y servicios | Nmap; versiones de programas | Eliminar servicios innecesarios, firewall, IDS |
| 3. Obtener acceso (Gaining Access) | Aprovechar una vulnerabilidad | Contraseña débil o servicio obsoleto | Parches, contraseñas robustas, mínimo privilegio |
| 4. Mantener acceso (Maintaining Access) | Volver a entrar tras salir/reiniciar | Backdoor, rootkit, cron `@reboot` | Vigilar integridad, tareas, cuentas y logs externos |
| 5. Borrar huellas (Covering Tracks) | Ocultar actividad | Modificar o borrar logs | Centralización, auditoría y copias externas de logs |

**Regla fácil de memorizar:** *Buscar datos → buscar servicios → entrar → persistir → ocultarse*.

En un ejercicio que pregunte por la fase, mira qué acción concreta se describe. Ejemplo: `nmap -sV` corresponde a exploración; una tarea `@reboot` que ejecuta un programa corresponde a persistencia.

## 4. Herramientas y contramedidas por fase

- `whois`: información de registro de dominios e IP públicas; ayuda a saber qué datos se exponen.
- `nslookup`: consultas y resolución DNS; permite ver registros publicados.
- `nmap`: descubre puertos, servicios y en ocasiones versiones; sirve para inventariar la superficie expuesta.
- `netstat` / `ss`: muestran *desde el propio sistema* quién está escuchando en qué puerto.
- `iptables`: cortafuegos de Linux para aceptar o descartar tráfico según reglas.
- **IDS**: detecta comportamientos sospechosos, como escaneos; **IPS** puede bloquearlos según configuración.
- **Wireshark / tcpdump**: observan tráfico; pueden apoyar diagnósticos y detección.
- **Sniffer**: herramienta que captura paquetes; su utilización puede ser defensiva o maliciosa.
- **Exploit**: aprovecha un fallo; **backdoor**: entrada oculta; **rootkit**: oculta presencia o modificaciones; **password cracker**: intenta obtener contraseñas.
- **Logs**: evidencias de actividad; conviene guardarlos fuera del servidor comprometible.
- **Hashes de referencia**: permiten detectar modificaciones si se comparan contra un estado previamente confiable.

**Pregunta típica:** ¿qué medidas *reducen lo que ve un atacante* y cuáles *detectan* actividad? Cerrar puertos, limitar información y filtrar ICMP reducen exposición; IDS, auditoría y registros ayudan a detectar y analizar. Ocultar un banner no elimina la vulnerabilidad del servicio.

## 5. Puertos, servicios y filtrado

**Puerto:** número lógico por el que escucha una aplicación de red. **Servicio:** programa que lo utiliza. **Proceso/PID:** instancia que lo atiende. **Protocolo:** TCP o UDP.

- `open`: Nmap ha obtenido evidencia de que el puerto acepta conexiones.
- `closed`: el host responde, pero no hay servicio escuchando.
- `filtered`: un filtro o problema de red impide confirmar si está abierto/cerrado.
- `LISTEN`: en el propio servidor, un proceso está escuchando.
- `0.0.0.0:PUERTO`: escucha por todas las interfaces IPv4, sujeto a controles de red.
- `127.0.0.1:PUERTO`: solo escucha en la interfaz local (loopback).
- **Ping/ICMP** no es lo mismo que TCP 22 o TCP 80: bloquear ping no implica cerrar todos los puertos.

Puertos habituales en estas prácticas: 21 FTP, 22 SSH, 23 Telnet, 25 SMTP, 53 DNS, 80 HTTP, 111 RPC, 139/445 SMB, 513 rlogin, 3306 MySQL, 5432 PostgreSQL, 5900 VNC, 8081 HTTP de laboratorio, 8180 Tomcat. **El puerto identifica un servicio probable, no demuestra por sí solo qué aplicación/versión es.**

## 6. Detener un servicio correctamente (PDF «Tumbar un servicio»)

**La receta del profesor tiene cinco pasos: IDENTIFICA → AVERIGUA → PARA → IMPIDE → COMPRUEBA.**

Hay **dos casos diferentes**:

| Servicio permanente | Servicio a demanda (`inetd`/`xinetd`) |
|---|---|
| El propio proceso suele mantener el puerto | Un “portero” atiende el puerto y arranca el programa al llegar conexión |
| Ejemplo del PDF: `proftpd` en 2121 | Ejemplo del PDF: `login`/rlogin en 513 |
| Busca script en `/etc/init.d/` y enlace en `/etc/rc2.d/` | Busca entrada en `/etc/inetd.conf` o `/etc/xinetd.d/` |
| Detén con `/etc/init.d/proftpd stop` | Deshabilita solo ese servicio en su configuración |
| Evita arranque: `update-rc.d -f proftpd remove` (sistema SysV del ejemplo) | Recarga el gestor `xinetd`, sin apagarlo entero |
| Comprueba antes y después de reiniciar | Comprueba que los otros servicios de xinetd siguen funcionando |

**Muy importante:** no detener `xinetd` entero para cerrar solo Telnet o rlogin: podrías apagar también otros servicios. No confundas `stop` (parar ahora) con `disable` o quitar enlaces de arranque (evitar retorno tras reinicio). El método del PDF es específico del Ubuntu antiguo del laboratorio; en Ubuntu moderno pueden existir unidades `systemd` (`systemctl stop`, `systemctl disable`).

## 7. Malware y sus familias

**Malware:** software que busca infiltrarse, perjudicar o actuar sin autorización. El PDF también enumera términos que **no son malware en sí mismos**, como cookies, bugs, crackers, phishing o spam: hay que distinguir entre código malicioso, debilidades, actores y engaños.

| Término | Idea clave / ejemplo |
|---|---|
| Virus | Infecta otros archivos; necesita ejecución de un huésped |
| Gusano (worm) | Se propaga automáticamente por sistemas/redes |
| Troyano | Se presenta como programa legítimo para que lo ejecuten |
| Ransomware | Cifra o bloquea datos y exige rescate |
| Spyware / keylogger | Espía información o pulsaciones |
| Adware | Muestra publicidad intrusiva |
| Bot/zombi | Equipo controlado remotamente para acciones automatizadas |
| Backdoor | Entrada no autorizada o encubierta |
| Rootkit | Oculta procesos, ficheros o mecanismos de acceso |
| Bomba lógica | Se activa al cumplirse una condición o fecha |
| Bomba fork / rabbit | Saturación por multiplicación de procesos |
| Phishing | Mensaje/web falsa para engañar y obtener datos |
| Pharming | Redirección a web falsa por manipulación de resolución/redirección |
| Hoax | Bulo que se difunde por engaño |
| Exploit | Técnica/código que aprovecha una vulnerabilidad |
| Riskware | Software legítimo cuyo uso o configuración supone un riesgo |

**Diferencia crucial:** un antivirus no detecta necesariamente un phishing basado solo en texto persuasivo. El objetivo puede ser manipular al usuario, no instalar un ejecutable.

## 8. Análisis estático y dinámico

| Análisis estático | Análisis dinámico |
|---|---|
| Inspecciona sin ejecutar | Ejecuta en un entorno aislado y monitoriza |
| Ej.: `file`, `sha256sum`, `cat` para scripts, `strings` para binarios | Ej.: `ps`, `ss`, `crontab -l`, `ls /tmp`, antes/después |
| Ayuda a formular hipótesis | Confirma el comportamiento realmente observable |
| Puede resultar complejo si solo hay binarios | Debe hacerse en sandbox/VM aislada, con instantánea |

**Sandbox:** entorno donde la muestra puede ejecutarse sin afectar al anfitrión ni a otras redes. Una VM por sí sola no garantiza aislamiento: debes comprobar portapapeles, carpetas compartidas, red y permisos.

## 9. Protección contra malware y mantenimiento

- Antivirus **en tiempo real**: analiza durante la actividad y puede bloquear amenazas.
- Antivirus **a demanda**: analiza archivos/discos cuando se pide.
- **Cuarentena:** aparta un fichero sospechoso para revisarlo/recuperarlo; evita borrados irreversibles por falsos positivos.
- **ClamAV:** antivirus del laboratorio; **FreshClam:** actualiza firmas.
- **Rkhunter:** busca indicios de rootkits y cambios sospechosos; los `Warning` no prueban infección.
- **EICAR:** archivo de prueba inofensivo que debe ser detectado como muestra de test.
- Actualizar SO, aplicaciones, firmas; descargar de fuentes confiables; usar cuenta sin privilegios administrativos y realizar copias de seguridad.

## 10. Software actualizado y confianza de las descargas

**Software EOL (fin de soporte):** ya no recibe parches de seguridad habituales. Un firewall o contraseña fuerte reduce riesgo, pero **no sustituye** la migración/actualización.

- **Repositorio APT:** fuente configurada de paquetes y sus actualizaciones.
- **Clave de firma de repositorio:** permite comprobar metadatos firmados cuando está configurada como de confianza.
- **Paquete instalado a mano (`dpkg -i`)**: hay que verificar su procedencia e integridad por otras vías.
- **Hash SHA-256:** huella del contenido; un cambio produce otro hash. Por sí solo no demuestra origen auténtico si la referencia está manipulada.
- **Firma digital GPG:** puede comprobar integridad y origen *si se ha verificado que la clave pública pertenece al firmante auténtico*.
- **`Good signature`**: coincide criptográficamente con esa clave; **aviso de clave no certificada**: todavía no has establecido confianza personal en su titular; **`BAD signature`**: fichero o firma no corresponde.

---

# PARTE II — PRÁCTICAS DE U2 EXPLICADAS PASO A PASO

## Práctica 1 · `entregable2.md`: reconocer riesgos en Estudio Torrent

**Qué se pretendía:** analizar información disponible sobre una empresa ficticia y explicar cómo podría facilitar un ataque, sin necesidad de atacar nada.

**Procedimiento de examen:**

1. **Localizar información pública:** personas y cargos, correo, horarios, ubicación, software utilizado, versiones, datos de dominio, DNS, redes sociales y respuestas automáticas.
2. **Relacionar cada dato con un riesgo:** por ejemplo, saber que Lucía gestiona pagos facilita engaños dirigidos a cambios de cuenta; una foto con un post-it puede revelar información privada.
3. **Proponer medidas específicas:** verificar cambios bancarios por teléfono conocido, limitar publicación de datos, formación, MFA y actualizar software.
4. **Priorizar:** primero los riesgos con impacto serio y explotación plausible.

**Datos concretos recogidos en el entregable:** uso de GestiCloud, web GestWeb 2.4, servidor Ubuntu 8.04, PCs Windows 7, copias en NAS, registro con datos personales, SPF `+all`, ausencia de DKIM/DMARC, fotos con post-it, conexión desde cafetería, vacaciones y responsables de pagos.

| Dato | Riesgo | Defensa adecuada |
|---|---|---|
| `SPF +all` | Política de correo demasiado permisiva | SPF restrictivo bien configurado, DKIM, DMARC |
| Ubuntu 8.04 / Windows 7 | Falta de soporte, vulnerabilidades sin parche | Migrar/actualizar |
| Nombres y funciones | Suplantación dirigida | Comprobar identidad y petición |
| Ausencia por vacaciones | Engaños en momento de menor supervisión | No publicar detalles innecesarios |
| Software y versiones | Investigación de fallos conocidos | Actualización y mínima exposición |

**Si te preguntan por fases:** recopilar datos públicos = reconocimiento. Analizar IP/servicios = exploración. No confundir recopilar información con ejecutar un ataque.

## Práctica 2 · `lab_fases_1_2.md`: auditoría de puertos y contramedidas

**Máquinas del laboratorio:** Torrent-Auditor (Ubuntu que escanea) y Torrent-Vulnerable/Metasploitable (objetivo). En las prácticas aparecían dos IP del mismo objetivo: `192.168.1.101` (eth0) y `192.168.56.2` (eth1). **Para comparar antes/después conviene usar la misma interfaz/IP y las mismas opciones.**

### Paso 1. Comprobar la red

```bash
ip a                  # muestra direcciones IP e interfaces
ip route              # muestra rutas
ip route get 192.168.56.2  # muestra por dónde se intenta llegar
ip neigh show 192.168.56.2 # relación IP-MAC/ARP
```

**Interpretación:** si `ip neigh` muestra `FAILED`, puede haber un problema de enlace/ARP entre VMs. Comprueba en VirtualBox que ambas están en la misma red interna o Host-only y tienen cable conectado. No lo soluciones borrando reglas de firewall a ciegas.

### Paso 2. Medición antes

```bash
sudo nmap -sV 192.168.56.2 -oN antes.txt
```

- `sudo`: privilegios para determinadas técnicas de exploración.
- `nmap`: escáner de puertos.
- `-sV`: trata de identificar los servicios y sus versiones.
- `192.168.56.2`: IP del servidor del laboratorio; cambia si tu VM tiene otra.
- `-oN antes.txt`: guarda la salida normal en un archivo de texto.

**Resultado documentado en la práctica:** 23 puertos TCP abiertos y 977 cerrados entre los 1.000 puertos habituales. Entre otros: FTP 21, SSH 22, Telnet 23, DNS 53, HTTP 80, Samba 139/445, MySQL 3306, PostgreSQL 5432 y Tomcat 8180.

### Paso 3. Identificar quién abre cada puerto, *desde el servidor*

```bash
sudo netstat -tulpn
sudo netstat -tulpn | grep ':21 '
```

- `-t`: TCP; `-u`: UDP; `-l`: escuchando; `-p`: proceso/PID; `-n`: números sin resolver nombres.
- `grep` filtra líneas del puerto. Puede ser `xinetd` quien gestione varios servicios a demanda.

**Tabla de inventario recomendable:** puerto, protocolo, servicio, programa/PID, forma de arranque, ¿se necesita?, medida y comprobación.

### Paso 4A. Servicio permanente (ejemplo del PDF: ProFTPD, puerto 2121)

```bash
sudo netstat -tulpn | grep 2121
ls /etc/init.d/ | grep -i proftpd
ls /etc/rc2.d/ | grep -i proftpd
grep -i servertype /etc/proftpd/proftpd.conf
sudo /etc/init.d/proftpd stop
sudo update-rc.d -f proftpd remove
```

**Qué haces:** identificas que lo atiende su propio demonio; encuentras el script y enlaces de arranque; paras el proceso; desactivas ese arranque en el sistema SysV antiguo. **No basta con `stop`: si no cambias el arranque, al reiniciar puede volver.** En el ejemplo se recupera con `update-rc.d proftpd defaults` y `... start`.

### Paso 4B. Servicio a demanda (ejemplo del PDF: rlogin, puerto 513)

```bash
sudo netstat -tulpn | grep ':513 '
sudo grep -n -w login /etc/inetd.conf /etc/xinetd.conf /etc/xinetd.d/* 2>/dev/null
```

**Qué haces:** buscas dónde se define el servicio. Si depende de `inetd`, se comenta su entrada tras hacer copia del fichero; si depende de un fichero `/etc/xinetd.d/login`, se configura `disable = yes`, se guarda y se recarga `xinetd`.

> No ejecutar automáticamente comandos de sustitución sobre archivos que no hayas inspeccionado: la ruta y sintaxis dependen del caso real. **Nunca apagar `xinetd` completo para cerrar un único servicio.**

```bash
sudo /etc/init.d/xinetd reload
sudo netstat -tulpn | grep ':513 '
```

### Paso 5. Filtrar los servicios manteniendo SSH y HTTP

En la práctica usamos `iptables` para permitir tráfico relacionado, loopback, SSH/HTTP y descartar el resto. **Antes de poner una política DROP, asegúrate de no perder una sesión remota de administración y de tener acceso a la consola de VirtualBox.**

```bash
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
sudo iptables -A INPUT -j DROP
sudo iptables -L INPUT -n --line-numbers
```

**Orden importa:** `iptables` examina reglas por orden; un `ACCEPT` anterior puede hacer que un `DROP` posterior no afecte ese tráfico. `DROP` descarta paquetes; no detiene necesariamente el proceso que escucha en el puerto. Las reglas pueden perderse al reiniciar si no se guardan con el mecanismo apropiado.

### Paso 6. Medir después y comparar

```bash
sudo nmap -sV -Pn 192.168.56.2 -oN despues.txt
diff antes.txt despues.txt
ping -c 3 192.168.56.2
```

- `-Pn`: no depender del descubrimiento ICMP/host previo.
- `diff`: enseña líneas añadidas y eliminadas entre informes.
- `ping -c 3`: envía tres solicitudes ICMP.

**Resultado observado:** quedaron abiertos **22 SSH** y **80 HTTP**, y el resto de los 998 puertos TCP escaneados aparecieron **filtered**. La regla ICMP estaba configurada; la pérdida de respuestas se confirma con `ping` cuando se realiza la comprobación. **Distingue `filtered` de `closed`: no son lo mismo.**

**Advertencia metodológica:** el `antes.txt` del laboratorio se realizó en `192.168.1.101` y el `despues.txt` en `192.168.56.2`. Son interfaces distintas de Metasploitable; para comparación estricta usa la misma IP, rango de puertos y opciones.

**Respuesta para examen:** cerrar servicios innecesarios reduce superficie de ataque; bloquear ping dificulta cierto descubrimiento, pero no hace invisible al host; banners ocultos no equivalen a parches; IDS/logs detectan o registran y no sustituyen la corrección de vulnerabilidades.

## Práctica 3 · `entregable5.md`: clasificar amenazas

**Qué hicimos:** elaborar definiciones de amenazas, categorizar 13 incidentes y decidir si se autorreplican o necesitan intervención humana.

**Método para contestar:**

1. ¿Se propaga por sí solo? → gusano.
2. ¿Cifra datos y exige dinero? → ransomware.
3. ¿Parece programa legítimo, pero hace algo oculto? → troyano.
4. ¿Captura teclas? → keylogger.
5. ¿Anuncios tras instalar app? → adware.
6. ¿Se activa en fecha concreta? → bomba lógica.
7. ¿Orden remota para enviar spam? → bot.
8. ¿Oculta procesos o archivos? → rootkit.
9. ¿Permite acceso secreto? → backdoor.
10. ¿Pide datos con mensaje de falsa entidad? → phishing.
11. ¿Cadena alarmista falsa? → hoax.
12. ¿DNS manipulado para ir a sitio fraudulento? → pharming.
13. ¿Rastrea hábitos sin permiso? → spyware.

**Matiz útil:** “necesita al usuario” depende de cómo se distribuya el ataque, no solo del nombre. Conserva las respuestas de los escenarios concretos de clase, pero razona el caso que te den en el examen.

**Pregunta del entregable:** phishing, hoax y pharming son fraudes/engaños, no necesariamente archivos de malware. Un antivirus tradicional no puede garantizar que detenga un mensaje persuasivo o una redirección falsa: hacen falta formación, validación del origen, filtros de correo, controles DNS y autenticación.

## Práctica 4 · `entregable6.md`: actualizaciones, APT y verificaciones

### A. Comprobar actualización y fin de soporte

```bash
lsb_release -a                  # versión/distribución Ubuntu
uname -r                        # versión del kernel
sudo apt update                 # actualiza índices; NO instala actualizaciones
apt list --upgradable           # paquetes con versión nueva disponible
sudo apt-get -s upgrade | head -30  # simula cambios, sin aplicarlos
```

**Diferencia preguntable:** `apt update` renueva índices; `apt upgrade` aplica actualizaciones compatibles; `-s` simula. Un sistema EOL como Ubuntu 8.04 sigue sin soporte incluso con contraseñas fuertes.

### B. ¿De dónde vienen los paquetes?

```bash
cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/
cat /etc/apt/sources.list.d/ubuntu.sources
ls /etc/apt/trusted.gpg.d/ /usr/share/keyrings/ 2>/dev/null
apt policy openssh-server
apt list --installed 2>/dev/null | grep -i local
sudo dpkg -V
```

- `ubuntu.sources`: puede contener `Types`, `URIs`, `Suites`, `Components`, `Signed-By`.
- `Types`: `deb` paquetes binarios; `URIs`: direcciones; `Suites`: versión y canales; `Components`: secciones; `Signed-By`: clave indicada para verificar el repositorio.
- `apt policy`: versión instalada, candidata y origen según prioridades.
- `local`: pista de instalación manual; en tu práctica apareció **Nessus 10.12.4**.
- `dpkg -V`: verifica ciertas propiedades de archivos instalados respecto a la base de datos local. `5` señala cambio de MD5; `c` identifica configuración; `missing` señala archivo ausente. **Un cambio no es automáticamente malware y una base de datos alterada puede engañar esta comprobación.**

### C. Descargar GNU Hello y verificar integridad/autenticidad

```bash
mkdir -p ~/verif && cd ~/verif
wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz
wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz.sig
wget https://ftp.gnu.org/gnu/gnu-keyring.gpg
sha256sum hello-2.12.1.tar.gz
gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-2.12.1.tar.gz.sig hello-2.12.1.tar.gz
```

**Qué hace:** creas un directorio, descargas archivo, firma separada y conjunto de claves, calculas huella y verificas la firma. En tu práctica la firma fue `Good signature`, firmada por Reuben Thomas. El mensaje de clave no certificada significa que aún debes comprobar independientemente su identidad.

**Experimento de manipulación del entregable:**

```bash
cp hello-2.12.1.tar.gz hello-manipulado.tar.gz
echo 'x' >> hello-manipulado.tar.gz
sha256sum hello-2.12.1.tar.gz hello-manipulado.tar.gz
cp hello-2.12.1.tar.gz.sig hello-manipulado.tar.gz.sig
gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-manipulado.tar.gz.sig hello-manipulado.tar.gz
```

**Resultado esperado:** hash distinto y `BAD signature`. **Truco de examen:** hash compara contenido contra referencia fiable; firma asocia contenido a una clave verificada. Si un atacante cambia tanto archivo como hash de la misma web, no basta confiar en ese hash.

### D. Política de actualizaciones (Torrent)

La propuesta de tu práctica: responsable de mantenimiento, revisión semanal, parches críticos con rapidez y pruebas, copia previa antes de actualizar ERP/impresora, instalar solo de fuentes aprobadas, sustituir sistemas EOL y aislarlos temporalmente hasta la migración. El objetivo es equilibrar **seguridad** y **continuidad del servicio**.

## Práctica 5 · `entregable7.md`: malware, ClamAV, EICAR y sandbox

### B. Preparar un entorno controlado (VirtualBox)

1. Apagar la VM SAD-Ubuntu.
2. Deshabilitar carpetas compartidas, portapapeles bidireccional y arrastrar/soltar.
3. Tomar instantánea `limpio-antes-malware`.
4. Mantener NAT solo para instalar/actualizar firmas; **desconectar interfaces antes de ejecutar la muestra**.
5. No introducir datos reales/credenciales en la máquina de pruebas.

**Por qué:** evita propagación, comunicación exterior, fuga hacia el anfitrión y facilita volver a estado limpio. **Una snapshot no reemplaza aislamiento de red.**

### C. EICAR: comprobar que el antivirus detecta un archivo de prueba

```bash
wc -c ~/eicar.txt
sha256sum ~/eicar.txt
clamscan ~/eicar.txt
```

**Resultados observados:** 68 bytes, SHA-256 `275a021bbfb6489e54d471899f7db9d1663fc695ec2fe2a2c4538aabf651fd0f`, detección `Eicar-Test-Signature FOUND`. Es un archivo de **prueba, no malware real**. En la práctica también se buscó el hash (no el fichero) en VirusTotal.

### D1–D3. Instalar ClamAV y actualizar firmas

```bash
sudo apt update
sudo apt install -y clamav clamav-freshclam
clamscan --version
sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam
sudo systemctl status clamav-freshclam
ls -lh /var/lib/clamav/
clamscan ~/eicar.txt
```

**Para qué sirve:** `clamav` analiza; `freshclam` descarga firmas; se detiene servicio automático antes de usar `freshclam` manual por posible bloqueo. En tu práctica apareció **ClamAV 1.5.4** y bases `daily.cvd`, `main.cvd`, `bytecode.cvd` con fecha 20/10/2026.

### D4–D5. Escaneo recursivo y cuarentena

```bash
clamscan -r -i -v ~                 # examina home, muestra detecciones y detalles
mkdir -p ~/cuarentena              # crea carpeta si falta
clamscan -r -i -v --move="$HOME/cuarentena" ~
ls -l ~/cuarentena
```

- `-r`: recursivo; `-i`: solo infectados/detecciones; `-v`: detallado.
- `--move=...`: mueve lo detectado; **no usar `--remove` sin revisar: el antivirus puede equivocarse**.
- **Resultado de tu práctica:** `eicar.txt` quedó en `~/cuarentena` (68 bytes).

### D6. Rkhunter: avisos de posibles rootkits

```bash
sudo apt install -y rkhunter
sudo rkhunter --update
sudo rkhunter --propupd
sudo rkhunter --check --sk --rwo
sudo grep -i warning /var/log/rkhunter.log
```

- `--update`: intenta actualizar datos de Rkhunter.
- `--propupd`: actualiza la referencia de propiedades/hashes; **solo conviene basarse en ella si el sistema se considera limpio**.
- `--check`: comprobación; `--sk`: sin pausas; `--rwo`: mostrar solo warnings.
- **Avisos observados:** `lwp-request`, usuarios/grupos `postfix` y `postdrop`, archivos ocultos `/etc/resolv.conf.systemd-resolved.bak` y `/etc/.updated`. Postfix se instaló en la práctica; hay explicaciones benignas posibles, pero los archivos ocultos requerían revisión. **Warning ≠ prueba de rootkit.**

### E. Análisis estático del script entregado por el profesor

```bash
cd ~/Downloads
file sospechoso.sh
sha256sum sospechoso.sh
cat sospechoso.sh
```

**Qué significa cada orden:** `file` identifica formato (Bash UTF-8); `sha256sum` fija huella; `cat` permite leer sin ejecutar.

**Qué identificamos en el código:**

| Fragmento/idea | Qué hace |
|---|---|
| `DIR=/tmp/.cache_sistema` | Define ruta de carpeta oculta |
| `mkdir -p "$DIR"` | Crea directorio |
| `echo ... >> "$DIR/estado.log"` | Añade registro con fecha |
| `cp "$0" "$DIR/actualizador.sh"` | Copia el propio script |
| `python3 -m http.server 8081 --bind 127.0.0.1` | Abre web accesible solo localmente |
| `@reboot /bin/bash .../actualizador.sh` | Intenta ejecutar copia tras reiniciar |

**Si fuera `.exe` binario:** usar `file`, `strings`, análisis de metadatos y desensamblador, sin abrirlo como programa. No afirmar comportamientos no evidenciados por el contenido o por la ejecución.

### F. Análisis dinámico: antes, ejecutar, después, comparar

**Aislamiento validado:** `ip -br a` solo mostraba la interfaz `lo` con `127.0.0.1` y `::1`; red externa desconectada. Se preparó instantánea `antes-de-ejecutar`.

**Foto inicial:**

```bash
ps aux > ~/antes_procesos.txt
ss -tulpn > ~/antes_puertos.txt
crontab -l > ~/antes_cron.txt 2>&1
ls -la /tmp > ~/antes_tmp.txt
```

**Ejecución controlada, solo VM aislada y como usuario normal:**

```bash
cd ~/Downloads
bash sospechoso.sh
```

**Foto final y comparación:**

```bash
ps aux > ~/despues_procesos.txt
ss -tulpn > ~/despues_puertos.txt
crontab -l > ~/despues_cron.txt 2>&1
ls -la /tmp > ~/despues_tmp.txt
diff ~/antes_procesos.txt ~/despues_procesos.txt
diff ~/antes_puertos.txt ~/despues_puertos.txt
diff ~/antes_cron.txt ~/despues_cron.txt
diff ~/antes_tmp.txt ~/despues_tmp.txt
```

**Resultados reales documentados:**

| Evidencia | Qué significa | Fase |
|---|---|---|
| Aparece `/tmp/.cache_sistema/estado.log` y `actualizador.sh` | Crea carpeta y ficheros ocultos | Ejecución |
| `ss -tlnp` muestra Python escuchando en `127.0.0.1:8081` | Abre servicio solo local | Ejecución |
| `crontab -l` muestra `@reboot /bin/bash /tmp/.cache_sistema/actualizador.sh` | Intenta reiniciarse | Persistencia |

**Tras reiniciar:** cron seguía programado, pero el puerto 8081 ya no estaba escuchando y `/tmp/.cache_sistema` había desaparecido. La **persistencia se intentó pero no funcionó** por desaparecer la ruta temporal.

**Erradicación verificada:** `crontab -e` para quitar `@reboot`; `crontab -l` vacío; `ss -tlnp | grep 8081` sin respuesta; `ls -ld /tmp/.cache_sistema` indica inexistente; `pgrep -af 'http.server 8081'` sin resultado. Después, restaurar instantánea previa (guardar capturas antes).

**Pregunta de examen:** si no tienes captura “antes” de un servidor real, compara con inventario de servicios autorizado, líneas base, registros centralizados, monitorización y copias de referencia. Sin línea base no puedes afirmar automáticamente que un cambio es malicioso.

### G. Preventivo o paliativo

| Medida | Clasificación principal del ejercicio |
|---|---|
| Sistema actualizado | Preventiva |
| ClamAV escaneando a demanda | Paliativa/detección |
| Instalar desde repositorios oficiales | Preventiva |
| Copia de seguridad desconectada | Paliativa/recuperación |
| Usuario sin privilegios de admin | Preventiva |
| Rkhunter tras sospecha | Paliativa/detección |
| Formación ante phishing | Preventiva |

**Nota:** una misma medida puede desempeñar más de una función; aquí se clasifica según el uso que pide la actividad.

---

# PARTE III — RESOLUCIÓN RÁPIDA DE PROBLEMAS

| Síntoma en el examen | Qué comprobar antes de tocar nada |
|---|---|
| Nmap: `0 hosts up` | IP, interfaces, VirtualBox, enrutamiento, ARP, bloqueos de descubrimiento; considerar `-Pn` |
| `ip neigh ... FAILED` | Conectividad de capa 2: misma red virtual, cable virtual, interfaces UP |
| 22/80 aparecen `filtered` | Red y reglas; no asumir que el proceso está caído |
| Un servicio reaparece tras reiniciar | Script de arranque SysV/systemd, xinetd/inetd, cron, otros gestores |
| `netstat` muestra `xinetd` | El puerto lo gestiona el portero: buscar entrada, no apagar todo xinetd |
| `apt update` no instala nada | Es normal: solo sincroniza índices |
| `dpkg -V` devuelve cambios | Analizar cuáles y si son configuraciones; no es prueba automática de malware |
| `gpg: Good signature` + aviso de confianza | Firma coincide, pero falta validar independencia de la clave |
| Hash cambia tras editar un byte | La integridad ha cambiado; comportamiento esperado |
| ClamAV encuentra EICAR | El motor ha detectado muestra de prueba, no virus real |
| Rkhunter muestra warnings | Revisar causas y comparar contra estado confiable |
| No funciona `@reboot` | Comprobar ruta/script disponible al arranque, cron y logs; `/tmp` puede limpiarse |

---

# PARTE IV — CHULETA DE COMANDOS CON EXPLICACIÓN

| Comando | Para qué sirve |
|---|---|
| `ip a` | IP e interfaces |
| `ip -br a` | Resumen compacto de interfaces |
| `ip route get IP` | Ruta e interfaz hacia IP |
| `ip neigh show IP` | Estado de resolución ARP/vecinos |
| `ping -c 3 IP` | Tres solicitudes ICMP |
| `sudo nmap -sV IP` | Puertos + detección de servicios/versiones |
| `sudo nmap -sV -Pn IP -oN archivo.txt` | Escanear sin descubrimiento previo y guardar resultado |
| `sudo netstat -tulpn` | Sockets/puertos y PID/servicio local |
| `ss -tlnp` | Puertos TCP a la escucha y procesos |
| `sudo iptables -L INPUT -n --line-numbers` | Ver reglas INPUT en orden |
| `diff antes.txt despues.txt` | Comparar textos |
| `lsb_release -a` | Versión del SO |
| `uname -r` | Kernel |
| `sudo apt update` | Actualizar catálogos APT |
| `apt list --upgradable` | Paquetes actualizables |
| `sudo apt-get -s upgrade` | Simular actualización |
| `apt policy paquete` | Origen, versión instalada/candidata |
| `sudo dpkg -V` | Verificar propiedades respecto a base de dpkg |
| `sha256sum archivo` | Hash del contenido |
| `gpg --verify firma archivo` | Comprobar firma con claves disponibles |
| `clamscan --version` | Versión de motor/firmas |
| `sudo freshclam` | Actualizar firmas ClamAV |
| `clamscan -r -i -v ~` | Escaneo recursivo del home |
| `sudo rkhunter --check --sk --rwo` | Buscar avisos de rootkit |
| `file archivo` | Identificar tipo |
| `cat archivo.sh` | Leer script sin ejecutarlo |
| `ps aux` | Listar procesos |
| `crontab -l` | Mostrar tareas cron de usuario |
| `crontab -e` | Editar tareas cron |
| `pgrep -af patron` | Buscar procesos por nombre/argumentos |
| `sudo reboot` | Reiniciar (con preparación) |

---

# PARTE V — PREGUNTAS PARA PRACTICAR (RESPUESTAS INCLUIDAS)

1. **¿En qué se diferencian amenaza y ataque?** Amenaza = posibilidad; ataque = materialización.
2. **¿Qué distingue un ataque pasivo de uno activo?** El pasivo observa sin alterar; el activo cambia datos/servicio/identidades.
3. **¿Qué fase representa Nmap?** Exploración o scanning.
4. **¿Qué fase representa `@reboot`?** Mantener acceso/persistencia.
5. **¿Qué diferencia hay entre puerto filtrado y cerrado?** Cerrado responde sin servicio; filtrado no permite determinar el estado normalmente.
6. **¿Bloquear ping equivale a cerrar SSH?** No, ICMP y TCP son diferentes.
7. **¿Por qué no apagar xinetd entero?** Gestiona varios servicios; apagarlo corta todos.
8. **¿Qué falta después de parar un servicio?** Impedir arranque automático y verificar tras reinicio.
9. **¿Qué hace `-sV` en Nmap?** Intenta identificar servicio y versión.
10. **¿Qué hace `-oN`?** Guarda informe de texto.
11. **¿`apt update` instala parches?** No; actualiza catálogos.
12. **¿Por qué EOL es grave aunque haya buenas contraseñas?** No llegan parches y persisten fallos.
13. **¿Un hash por sí solo prueba autoría?** No, necesita referencia confiable; firma válida y clave autenticada ayudan.
14. **¿Qué pasa al añadir un carácter a un archivo firmado?** Cambia hash y deja de coincidir la firma original.
15. **¿Por qué cuarentena y no borrar?** Permite revisar y recuperar ante falsos positivos.
16. **¿Rkhunter `Warning` confirma un rootkit?** No; requiere investigación.
17. **¿Qué diferencia estático/dinámico?** Sin ejecutar vs ejecutar y observar en sandbox.
18. **¿Por qué el servicio 8081 de tu muestra no era accesible desde la red?** Escuchaba en `127.0.0.1`.
19. **¿Por qué falló la persistencia después del reinicio?** Desapareció el script guardado en `/tmp`.
20. **¿Qué hacer si no hay línea base de un servidor?** Usar inventario autorizado, logs, controles de integridad y referencias históricas.

---

## Orientación final para el examen

Para cualquier ejercicio práctico sigue el mismo esquema: **(1) identificar el objetivo → (2) obtener evidencia con comandos de solo lectura → (3) explicar qué significa → (4) aplicar el cambio mínimo y documentarlo → (5) verificar desde dentro y fuera → (6) repetir tras reiniciar cuando corresponda**. No memorices solo comandos; aprende qué pregunta resuelve cada uno.

**Fuentes y límites:** la organización teórica procede de los dos PDF entregados; la descripción de actividades y resultados procede de los cinco `.md` de `U2`. Algunos matices añadidos para estudiar mejor (como diferenciar `filtered`/`closed`, la limitación de un hash o la conservación de SSH) son aclaraciones explicativas, no resultados adicionales del laboratorio. No se ha utilizado `U2/examen.md` ni material de otras carpetas.

