Parte A · ¿Está actualizado el sistema?
Trabajad en Torrent-Auditor.

A1. Anotad qué versión de sistema operativo y kernel tenéis:

lsb_release -a
uname -r
<img width="435" height="223" alt="image" src="https://github.com/user-attachments/assets/a664c876-3676-4486-9edb-baf7443bba04" />

A2. Pedid al sistema la lista de paquetes disponibles y qué se puede actualizar:

sudo apt update
apt list --upgradable
<img width="896" height="438" alt="image" src="https://github.com/user-attachments/assets/688d19ba-b7e2-4c51-8692-d03ffbeb3844" />

Anotad: ¿cuántos paquetes tienen actualización pendiente?
<img width="817" height="71" alt="image" src="https://github.com/user-attachments/assets/7b5a9e61-e1b6-40b4-b5e2-28066715cac1" />


A3. Simulad la actualización sin aplicarla:

sudo apt-get -s upgrade | head -30
La opción -s es "simular". Fijaos en que dice qué instalaría, pero no toca nada.
<img width="885" height="708" alt="image" src="https://github.com/user-attachments/assets/49b5aa8f-5aca-4d69-8982-35a27711e96f" />

A4. ¿Y si el sistema ya no recibe parches? Buscad en https://endoflife.date la fecha de fin de soporte de:

La versión de Ubuntu/Debian de vuestro Torrent-Auditor.
Ubuntu 8.04, la que tiene Torrent-Vulnerable (Metasploitable2).
Rellenad:

Sistema	Fecha de fin de soporte	¿Recibe parches hoy?
Torrent-Auditor: 31/05/2029	Sí	
Torrent-Vulnerable (Ubuntu 8.04): Según endoflife.date, Ubuntu 8.04 dejó de recibir soporte el 9 de mayo de 2013 en su edición Server
		
Pregunta A: En la oferta de empleo de Torrent (dossier de la UD02) decían que trabajaban con "Ubuntu 8.04". Aunque pusieran todas las contraseñas bien, ¿qué problema sigue sin tener solución?

Aunque todas las contraseñas sean seguras, Ubuntu 8.04 ya no recibe actualizaciones ni parches de seguridad oficiales desde 2013. Esto significa que las vulnerabilidades descubiertas posteriormente pueden seguir sin corregirse y los atacantes podrían 
aprovecharlas para acceder al sistema. La solución es migrar a una versión que todavía tenga soporte.

## Parte B · ¿De dónde vienen los programas instalados?
B1. Mirad de qué repositorios se descarga vuestro sistema:

cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/
Investiga el fichero donde están configurados los repositorios y comenta el significado de cada una de las líneas y su contenido.
<img width="785" height="160" alt="image" src="https://github.com/user-attachments/assets/774a7989-a25d-4391-bbcf-d17d751bdedb" />
<img width="666" height="142" alt="image" src="https://github.com/user-attachments/assets/e0963780-f754-48b3-8055-90018c1c953a" />

¿Qué hace? Muestra los repositorios desde los que Ubuntu descarga programas y actualizaciones.
<img width="762" height="325" alt="image" src="https://github.com/user-attachments/assets/a1fb377a-5705-4010-85e4-5059c8d078cb" />

El archivo `/etc/apt/sources.list.d/ubuntu.sources` contiene los repositorios de Ubuntu.

- **Types:** indica que se descargan paquetes compilados (`deb`).
- **URIs:** dirección del servidor desde donde se descargan los paquetes.
- **Suites:** indica la versión de Ubuntu y los tipos de actualizaciones (`noble`, `noble-updates`, `noble-backports` y `noble-security`).
- **Components:** clasifica el software en `main`, `restricted`, `universe` y `multiverse`.
- **Signed-By:** indica la clave utilizada para comprobar que los repositorios están firmados correctamente.

El sistema utiliza un repositorio principal y otro de actualizaciones de seguridad.

B2. apt solo instala paquetes firmados por claves en las que confía. Mirad dónde están esas claves:

ls /etc/apt/trusted.gpg.d/ /usr/share/keyrings/ 2>/dev/null
Nessus, por ejemplo, no viene de los repositorios de Ubuntu, lo descargasteis de Tenable. Si lo instalasteis con dpkg -i, apt no comprobó ninguna firma, la confianza la pusisteis vosotros.
<img width="786" height="363" alt="image" src="https://github.com/user-attachments/assets/20a1ba05-641d-4206-a665-f55a7dcc3419" />
Este comando muestra las claves que pueden utilizarse para verificar la autenticidad de los repositorios



Reflexiona: 
¿qué pasaría si un atacante introduce una clave falsa en /usr/share/keyrings/? 

Podría conseguir que se aceptasen paquetes maliciosos si esa clave se configura como confiable para un repositorio. 
Guardarla en /usr/share/keyrings/ por sí solo no basta.

¿y si intercepta o reemplaza la clave oficial de Ubuntu ubuntu-archive-keyrings.gpg?
odría comprometer la seguridad de las actualizaciones, permitiendo aceptar paquetes falsificados si el atacante también controla el repositorio y firma su contenido con la clave correspondiente.

Por eso es importante proteger las claves oficiales y verificar los paquetes instalados.

B3. Información de un paquete concreto. Elegid openssh-server o curl:

apt policy openssh-server
Anotad: versión instalada, versión candidata, repositorio de origen y el significado de la tabla de versiones.
<img width="895" height="307" alt="image" src="https://github.com/user-attachments/assets/6bffd3c1-e446-4fff-9587-068f6172dea6" />

B4. Buscad paquetes instalados que no proceden de ningún repositorio (instalados a mano):

apt list --installed 2>/dev/null | grep -i local
Cada línea que salga merece una pregunta: ¿quién lo instaló, de dónde, y quién lo verificó?
<img width="932" height="252" alt="image" src="https://github.com/user-attachments/assets/3c982c3f-f347-4d18-85a0-8b12b088ca25" />

He ejecutado el comando y he encontrado un paquete instalado localmente:

- **Paquete:** Nessus
- **Versión:** 10.12.4
- **Origen:** descargado de la página oficial de Tenable.
- **Instalación:** fuera de los repositorios oficiales de Ubuntu.

Al instalarlo manualmente, APT no verificó su firma mediante los repositorios configurados. Por eso es 
importante comprobar que el archivo procede de una fuente oficial y que no ha sido manipulado.


B5. Comprobad si algún fichero de un paquete ha cambiado desde que se instaló:

sudo dpkg -V
Si no sale nada, todo coincide con lo instalado.
Si salen líneas, la primera columna indica qué cambió (5 = el hash MD5 es distinto). Los ficheros de configuración (c) cambian de forma normal. Un binario (/usr/bin/...) modificado es una alarma.

¿Qué hace? Comprueba si los archivos de los paquetes instalados han cambiado respecto a la información registrada por dpkg.
- Si no aparece nada, no se han detectado diferencias.
- Si aparecen líneas con 5, significa que el hash MD5 no coincide.
- Si aparece una c, identifica un archivo de configuración, que puede haberse modificado legítimamente.
<img width="665" height="195" alt="image" src="https://github.com/user-attachments/assets/13f1904d-c4b6-4781-9c29-1a3e2f2a19f4" />

Pregunta B: dpkg -V ¿detectaría un rootkit que además hubiera modificado la base de datos de dpkg? ¿Por qué conviene comparar con una copia guardada fuera del sistema? (Pista: recordad la fase 4 de la intrusión.)
He ejecutado `sudo dpkg -V` y se han detectado varios archivos modificados y otros que faltan.

Los archivos pertenecen principalmente a Nessus, aunque también aparece modificado `cloud-init.service`.

El símbolo `5` indica que el hash MD5 del archivo no coincide con el registrado por dpkg, mientras que `missing` indica que falta un archivo.

Estos cambios no significan necesariamente que exista malware, pero conviene revisarlos.
