<img width="731" height="45" alt="image" src="https://github.com/user-attachments/assets/d1419a45-fb5b-45b4-8d91-c42cbc566138" />


## Parte 1 — Contraseñas robustas con PAM

### Paso 1 instalar pwquality

sudo apt update
sudo apt install libpam-pwquality -y

Comprobamos:
dpkg -s libpam-pwquality | grep Status

<img width="563" height="69" alt="image" src="https://github.com/user-attachments/assets/c82c2b1d-42db-497e-984f-33ba90955a0d" />


### Paso 2 Comprobar que PAM utiliza pwquality
grep -nE 'pam_pwquality.so|pam_unix.so' /etc/pam.d/common-password
Esto nos permitirá verificar que pam_pwquality.so aparece antes de pam_unix.so

<img width="964" height="65" alt="image" src="https://github.com/user-attachments/assets/953a4ec1-f459-4331-a175-9fa490fa4a72" />

2.2. Modificar la configuración
sudo nano /etc/security/pwquality.conf

<img width="383" height="219" alt="image" src="https://github.com/user-attachments/assets/ae99e2a8-c84e-48fc-a403-8ba24b876110" />

Explicación:
minlen = 12: establece una longitud mínima de 12 caracteres
minclass = 3: exige al menos tres tipos distintos de caracteres
dcredit, ucredit, lcredit y ocredit: obligan a utilizar números, mayúsculas, minúsculas y símbolos
dictcheck = 1: comprueba que la contraseña no se base en palabras de diccionario
maxrepeat = 3: limita la repetición consecutiva de caracteres
enforce_for_root: aplica los requisitos de calidad también a root
Con esta configuración buscamos evitar que los usuarios establezcan contraseñas débiles

### Paso 3. Comprobar que no hay parámetros duplicados

<img width="957" height="249" alt="image" src="https://github.com/user-attachments/assets/aeac7f79-fe6e-4bf2-b283-c16067feeba8" />

Deben aparecer los nueve parametros solo una vez

### Paso 4 Probar una contraseña débil con Hugo

<img width="665" height="92" alt="image" src="https://github.com/user-attachments/assets/8de0fcd0-ad7e-4005-9aa6-351185f31b88" />

Contraseña probada: 1234

### Paso 5 Establecer una contraseña segura para Hugo
Contra segura: Contraseña_Complicada1@

<img width="850" height="185" alt="image" src="https://github.com/user-attachments/assets/4d60e468-bc43-4f27-a402-7d47a7654ac2" />

### Paso 6 Comprobar la contraseña de Hugo

<img width="390" height="50" alt="image" src="https://github.com/user-attachments/assets/1b5f269c-3434-469d-a480-ec18b399a91e" />

Si aparece P en el estado de la contraseña significa que el usuario tiene una contraseña establecida

### Paso 7 Configurar la caducidad con chage
<img width="651" height="187" alt="image" src="https://github.com/user-attachments/assets/21069929-6d8a-4c75-9ed8-a3546fe0f72d" />

<img width="668" height="166" alt="image" src="https://github.com/user-attachments/assets/fc21b448-d027-4b57-b40d-c441885c726f" />

<img width="653" height="187" alt="image" src="https://github.com/user-attachments/assets/6a3fce8a-df60-4b66-8b54-e811d263c729" />

Explicación
- chage: administra la caducidad de las contraseñas.
- -m 1: obliga a esperar al menos un día entre cambios
- -M 60 o -M 90: establece el número máximo de días de validez
- -W 7: avisa al usuario siete días antes de que caduque

### Paso 8. Comprobar la política de contraseñas de root

<img width="391" height="92" alt="image" src="https://github.com/user-attachments/assets/966c6d04-24dd-479b-a020-bdde4f37679b" />

Después comprobamos que tenemos activado el parámetro enforce_for_root
grep -n '^enforce_for_root' /etc/security/pwquality.conf
Este parámetro hace que las contraseñas establecidas por root también tengan que cumplir los requisitos de calidad

<img width="731" height="45" alt="image" src="https://github.com/user-attachments/assets/8122daf6-b09e-4983-9a83-209e449a66ea" />

## Parte 2 — Configuración de MFA con Google Au

<img width="392" height="70" alt="image" src="https://github.com/user-attachments/assets/d2b6af9d-2fb2-4887-a746-0ca39d14a1a1" />

<img width="898" height="947" alt="image" src="https://github.com/user-attachments/assets/9bd5512c-d84b-4be6-89f3-68038f2fe572" />

Este comando inicia el asistente de configuración de autenticación TOTP.

<img width="752" height="187" alt="image" src="https://github.com/user-attachments/assets/a861ed60-2486-4ae2-b33a-39585da85397" />



## Parte 2 — MFA en SSH

<img width="757" height="110" alt="image" src="https://github.com/user-attachments/assets/29540460-1d98-4c3a-a2fd-0df7ff98dc2d" />

Con esta comprobación preparamos la activación del doble factor sin perder el acceso remoto a la máquina virtual.

<img width="981" height="161" alt="image" src="https://github.com/user-attachments/assets/4436e067-e7fe-45b1-9f94-acb579d1cf3e" />

Vamos a preparar la configuración para exigir contraseña + código temporal a los usuarios que pertenezcan al grupo mfa, sin quitarle el acceso al administrador linux.
Crear el grupo MFA y añadir a Carlos

<img width="552" height="110" alt="image" src="https://github.com/user-attachments/assets/eb572c40-0acc-49b7-8ed1-bed3a8be7096" />

Hacer copias de seguridad de PAM y SSH
Revisar dónde tendremos que modificar SSH:

<img width="961" height="147" alt="image" src="https://github.com/user-attachments/assets/3254c63d-5cd3-4f46-857b-e569780366d4" />


Revisar PAM y revisar el grupo MFA

<img width="826" height="128" alt="image" src="https://github.com/user-attachments/assets/5ef6a5ec-dbb5-4937-bd73-64ed85956dd9" />

Configurar PAM para el google au:

<img width="953" height="932" alt="image" src="https://github.com/user-attachments/assets/32ffda31-b8d7-424c-8fc3-451ba9111fc1" />

<img width="723" height="88" alt="image" src="https://github.com/user-attachments/assets/1283e1ab-ac32-4847-8224-e37444291170" />

La primera línea comprueba si el usuario pertenece al grupo mfa. Si no pertenece, omite la comprobación siguiente
La segunda línea utiliza Google Authenticator para solicitar el código temporal a los usuarios del grupo

Comprobamos la configuración:

<img width="952" height="88" alt="image" src="https://github.com/user-attachments/assets/757e8a32-6f62-4498-adf3-6726de492ac3" />

Configurar SSH:
sudo nano /etc/ssh/sshd_config, KbdInteractiveAuthentication no lo cambiamos a yes.

<img width="717" height="73" alt="image" src="https://github.com/user-attachments/assets/c24f788e-beb8-4eff-acfe-86ec1b0e96a3" />

Activamos la autenticación interactiva:
KbdInteractiveAuthentication yes
UsePAM yes
PasswordAuthentication yes

También añadimos una condición para exigir MFA únicamente a los miembros del grupo mfa

<img width="512" height="82" alt="image" src="https://github.com/user-attachments/assets/0ca89577-f506-4316-8f45-785105b7bc63" />

Explicación:
- KbdInteractiveAuthentication yes: permite utilizar la autenticación interactiva de PAM.
- UsePAM yes: activa la integración de SSH con PAM.
- PasswordAuthentication yes: mantiene disponible la autenticación por contraseña para los usuarios que no pertenecen al grupo MFA.
- Match Group mfa: aplica la configuración específica a los miembros del grupo.
- AuthenticationMethods keyboard-interactive:pam: obliga a esos usuarios a autenticarse mediante PAM, donde se comprueban la contraseña y el código temporal.

Comprobar la configuración del grupo MFA:

<img width="572" height="290" alt="image" src="https://github.com/user-attachments/assets/ab5af318-1fc9-4940-bce6-f33f1ea66f4e" />

<img width="911" height="1017" alt="image" src="https://github.com/user-attachments/assets/741b084b-150c-4859-91ea-4fc335e84772" />

Activar MFA en SSH

Después de configurar PAM y SSH, comprobamos que el servicio se inicia correctamente.

<img width="960" height="512" alt="image" src="https://github.com/user-attachments/assets/f04ae417-a352-4921-96ee-dd62de1c81c4" />

<img width="881" height="463" alt="image" src="https://github.com/user-attachments/assets/74a3b95a-cbb1-434d-97bc-b30646c267f3" />

El objetivo es comprobar que Carlos debe introducir su contraseña y el código temporal de Google Authenticator para acceder al sistema.

3. Prueba a escribir un código incorrecto y comprueba que la conexión se rechaza.:
   Carlos contra: Contraseña_Complicada1@
   
   <img width="580" height="95" alt="image" src="https://github.com/user-attachments/assets/38952374-3aa1-4065-a18c-26d4205f51c9" />


### 2.6 A quién se lo exigimos?

<img width="788" height="132" alt="image" src="https://github.com/user-attachments/assets/1c394556-b0d7-410c-9945-4288c0cb5adb" />

Explicación:
- getent group mfa: comprueba que existe el grupo MFA.
- id carlos: comprueba que Carlos pertenece a supervisores y mfa.
- id linux: comprueba que el administrador no pertenece al grupo mfa.

Paso 2. Comprobar la configuración de PAM

<img width="966" height="92" alt="image" src="https://github.com/user-attachments/assets/0cd3e73a-da03-46fd-b14f-a974d3257ccd" />

¿Qué significa?
- common-auth: comprueba la contraseña.
- pam_succeed_if.so: comprueba la pertenencia al grupo mfa.
- pam_google_authenticator.so: exige el código temporal a los usuarios del grupo.

Paso 3. Comprobar SSH para los dos usuarios

<img width="957" height="111" alt="image" src="https://github.com/user-attachments/assets/f7b0e7a2-cafd-4742-9f5d-28626dab0581" />

La primera línea corresponde a Carlos y la segunda a Linux.
Esto demuestra que Carlos debe pasar por la autenticación interactiva de PAM, mientras que Linux conserva su configuración habitual


## PARTE 3 Privilegios justos con sudo

Paso 1. Comprobar los usuarios y el servicio

<img width="450" height="142" alt="image" src="https://github.com/user-attachments/assets/9c0de8d9-0249-4b5c-8f69-c4021d971732" />

Paso 1.2. Comprobar el recurso hmi_planta

<img width="558" height="142" alt="image" src="https://github.com/user-attachments/assets/4214de71-3630-4820-9703-39dd61deefcc" />

Paso 2. Crear el archivo de auditoría
2.1. Crear el archivo

<img width="783" height="90" alt="image" src="https://github.com/user-attachments/assets/e81858d3-fa7f-46bc-842a-566b8ca84275" />

Explicación:
- sudo tee: crea el archivo con permisos de administrador.
- #!/bin/bash: indica que el archivo se ejecuta mediante Bash.
- date: muestra la fecha y hora.
- whoami: muestra el usuario que ejecuta el archivo.
- EOF: marca el final del contenido que estamos escribiendo.

2.2. Dar permisos de ejecución

<img width="723" height="77" alt="image" src="https://github.com/user-attachments/assets/77231bf8-a55c-4d33-bf66-e0ecd11b0f99" />

El permiso 755 significa que el propietario puede leer, escribir y ejecutar el archivo, mientras que los demás pueden leerlo y ejecutarlo, pero no modificarlo.

2.3. Comprobar su funcionamiento

<img width="690" height="63" alt="image" src="https://github.com/user-attachments/assets/dac046fa-1f8c-4604-9c6a-a52d9429f839" />


Paso 3. Crear las reglas de sudo
3.1. Abrir el archivo sudoers
sudo visudo -f /etc/sudoers.d/torrent

Explicación:
- visudo: permite editar reglas de sudo y comprueba su sintaxis.
- -f: indica el archivo que queremos editar.
- /etc/sudoers.d/torrent: será nuestro archivo de permisos específico para la empresa.

3.2. Añadir las reglas

<img width="962" height="107" alt="image" src="https://github.com/user-attachments/assets/98b0bc41-6b9e-4e65-87f1-f8b5146160bb" />

%ingenieria: se aplica a todos los miembros del grupo ingeniería.
ALL=(root): permite ejecutar los comandos indicados como root.
/usr/bin/systemctl: limita los comandos autorizados a las operaciones especificadas.
%supervisores: se aplica al grupo supervisores.
NOPASSWD: permite ejecutar el comando de auditoría sin introducir contraseña.

Paso 3. Comprobar que las reglas son válidas

<img width="920" height="170" alt="image" src="https://github.com/user-attachments/assets/cbabeac7-6ceb-48ee-bbe9-269550819d7f" />

Paso 4. Corregir los permisos del archivo sudoers
4.1. Cambiar los permisos a 0440

<img width="610" height="67" alt="image" src="https://github.com/user-attachments/assets/ce7b7f75-38d3-40dc-9261-b34cc23732b7" />

chown root:root: establece a root como propietario y grupo del archivo
chmod 440: permite que el propietario y el grupo lean el archivo, pero nadie pueda modificarlo mediante permisos normales

4.2. Comprobar los permisos

<img width="608" height="147" alt="image" src="https://github.com/user-attachments/assets/43337b96-d863-4039-8563-2e7b39bf208d" />


Paso 5. Comprobar los privilegios de los tres grupos

sudo -l: muestra los comandos que tiene permitidos un usuario.
-U nestor: consulta los permisos del usuario Néstor.

<img width="930" height="167" alt="image" src="https://github.com/user-attachments/assets/acf39ade-c23c-4cb9-8415-392d54cc03f9" />

5.2. Comprobar al usuario Carlos (supervisores)

<img width="946" height="170" alt="image" src="https://github.com/user-attachments/assets/1e5c574c-048d-49b4-9aed-2ab46d5c58b6" />

Esto significa que Carlos puede ejecutar el archivo de auditoría como administrador sin introducir la contraseña de sudo.

5.3. Comprobar al usuario Alberto (operarios)

<img width="538" height="53" alt="image" src="https://github.com/user-attachments/assets/70cdbb3d-9611-43f1-b831-d239eef3d775" />

Alberto no tendrá autorizaciones administrativas adicionales.

Paso 6. Comprobar que Alberto no puede utilizar sudo
6.1. Cambiar al usuario Alberto
6.2. Intentar reiniciar el servicio

<img width="563" height="88" alt="image" src="https://github.com/user-attachments/assets/400adb48-1616-4fbc-b483-f865bf049622" />

Paso 7. Comprobar el registro del intento de sudo

<img width="963" height="726" alt="image" src="https://github.com/user-attachments/assets/76960f67-4792-4b36-a817-99bbba7e8ccd" />

tail -n 20: muestra las últimas 20 líneas del registro.
/var/log/auth.log: contiene eventos relacionados con autenticación y uso de sudo, cuando Ubuntu tiene habilitado ese registro.
journalctl: permite consultar los registros almacenados por systemd.
_COMM=sudo: filtra los eventos del proceso sudo.

Paso 7.1. Buscar los registros de Alberto

<img width="690" height="51" alt="image" src="https://github.com/user-attachments/assets/2c221125-c254-4ce4-b8f9-6e28bb0709c3" />

Explicación:
- grep -i 'alberto': busca las entradas relacionadas con Alberto.
- tail -n 15: muestra las últimas 15 coincidencias.

Paso 8. Comprobar que Carlos puede ejecutar la auditoría sin contraseña
8.1. Entrar como Carlos

<img width="351" height="65" alt="image" src="https://github.com/user-attachments/assets/7ee269d9-f7ac-4e1f-8291-8e3dac129b2b" />

8.2. Ejecutar la auditoría sin contraseña

<img width="673" height="50" alt="image" src="https://github.com/user-attachments/assets/8fcd29e7-a523-40a2-b181-9df26426f6ab" />


1. ¿Qué riesgo cubre el MFA que una contraseña fuerte no cubre?

Aunque una contraseña sea muy larga y difícil de adivinar, puede ser robada mediante phishing, malware o una filtración de datos.
El MFA añade una segunda comprobación de identidad. Aunque un atacante consiga la contraseña, también necesitaría el código temporal generado por Google Authenticator para acceder.

2. ¿Qué recomienda el NIST sobre la caducidad de contraseñas?

El NIST recomienda no obligar a cambiar las contraseñas periódicamente sin motivo. Hacerlo demasiado a menudo puede provocar que los usuarios utilicen contraseñas fáciles o las apunten en papel.

3. ¿Por qué necesitamos ACL aunque tengamos sudo?

Porque las ACL controlan el acceso a archivos y carpetas, mientras que sudo controla los permisos administrativos.
Por ejemplo, Víctor puede modificar los planos mediante ACL y gestionar el servicio hmi_planta mediante sudo.

4. ¿Qué ocurre si un usuario pierde su móvil y los códigos de recuperación?

El administrador debe comprobar su identidad, invalidar el segundo factor anterior y configurar uno nuevo.
Así recupera el acceso sin desactivar MFA para los demás usuarios.











   













