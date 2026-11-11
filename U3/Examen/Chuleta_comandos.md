[UD3_SAD_Chuleta_Comandos_Practica.md](https://github.com/user-attachments/files/34681731/UD3_SAD_Chuleta_Comandos_Practica.md)
# SAD · UD3 — Chuleta rápida de comandos prácticos

> **Uso:** consulta rápida durante el examen.  
> Solo parte práctica: usuarios/grupos, ACL, PAM, contraseñas, `chage`, MFA/SSH, sudo y logs.

---

# 1. USUARIOS Y GRUPOS

```bash
sudo groupadd grupo
```
Crea un grupo.

```bash
sudo useradd -m -s /bin/bash usuario
```
Crea un usuario, su `/home` y le asigna Bash.

```bash
sudo usermod -aG grupo usuario
```
Añade un usuario a un grupo **sin quitarlo de los demás**.

```bash
getent group grupo
```
Muestra un grupo y sus miembros.

```bash
id usuario
```
Muestra UID, GID y grupos del usuario.

```bash
whoami
```
Muestra con qué usuario estás trabajando.

```bash
sudo -iu usuario
```
Cambia a otro usuario con su entorno de login.

```bash
exit
```
Sale del usuario actual y vuelve al anterior.

---

# 2. ARCHIVOS, DIRECTORIOS Y PERMISOS BASE

```bash
sudo mkdir -p directorio
```
Crea un directorio.

```bash
sudo touch archivo
```
Crea un archivo vacío.

```bash
ls -l recurso
```
Muestra permisos, propietario y grupo.

```bash
ls -ld directorio
```
Muestra los permisos del directorio.

```bash
sudo chown usuario:grupo recurso
```
Cambia propietario y grupo.

```bash
sudo chown -R usuario:grupo directorio
```
Lo hace de forma recursiva.

```bash
sudo chmod 700 recurso
```
Solo propietario: `rwx`.

```bash
sudo chmod 600 archivo
```
Solo propietario: `rw-`.

```bash
sudo chmod 755 script
```
Propietario `rwx`; resto `r-x`.

```bash
sudo chmod -R o-rwx directorio
```
Quita todos los permisos al grupo `otros`.

---

# 3. ACL — INSTALACIÓN Y COMPROBACIÓN

```bash
sudo apt update
sudo apt install acl -y
```
Instala las herramientas ACL.

```bash
dpkg -s acl
```
Comprueba si el paquete está instalado.

```bash
which setfacl
which getfacl
```
Comprueba que existen los comandos.

---

# 4. ACL — VER PERMISOS

```bash
getfacl recurso
```
Muestra las ACL de un archivo o directorio.

```bash
getfacl -R directorio
```
Muestra las ACL de forma recursiva.

### Fíjate especialmente en:

```text
mask::
#effective:
```

La `mask` limita los permisos efectivos máximos de usuarios nombrados y grupos ACL.

---

# 5. ACL — DAR PERMISOS

## A un usuario

```bash
sudo setfacl -m u:usuario:rwx recurso
```

Da `rwx` a un usuario concreto.

## A un grupo

```bash
sudo setfacl -m g:grupo:rwx recurso
```

Da `rwx` a un grupo.

## Recursivamente

```bash
sudo setfacl -R -m g:grupo:rwx directorio
```

Aplica la ACL al directorio y a lo que ya contiene.

## ACL por defecto

```bash
sudo setfacl -d -m g:grupo:rwx directorio
```

Los archivos/directorios **nuevos** heredarán esos permisos.

## Eliminar ACL de un usuario

```bash
sudo setfacl -x u:usuario recurso
```

Elimina la entrada ACL de ese usuario.

---

# 6. ACL — DIFERENCIA CLAVE

```text
-R  = aplica a recursos existentes
-d  = herencia para recursos nuevos
```

---

# 7. ACL — COPIAR PERMISOS DE UN DIRECTORIO A OTRO

```bash
getfacl origen | sudo setfacl -R --set-file=- destino
```

Copia las ACL del origen al destino.

---

# 8. ACL — PROBAR PERMISOS COMO OTRO USUARIO

```bash
sudo -u usuario ls directorio
```
Prueba listado.

```bash
sudo -u usuario cat archivo
```
Prueba lectura.

```bash
sudo -u usuario bash -c 'echo "texto" >> archivo'
```
Prueba escritura.

```bash
sudo -u usuario test -r recurso && echo "Puede leer"
```
Comprueba lectura.

```bash
sudo -u usuario test -w recurso && echo "Puede escribir"
```
Comprueba escritura.

```bash
sudo -u usuario test -x recurso && echo "Puede ejecutar/acceder"
```
Comprueba ejecución o acceso a directorio.

---

# 9. ACL — PERMISO `x` EN DIRECTORIOS

En un **directorio**, `x` significa poder **atravesarlo/acceder a su contenido**.

Ejemplo:

```bash
sudo setfacl -m u:alberto:--x /home/planos
```

Permite llegar a un fichero conocido dentro de `planos/` sin dar listado completo.

---

# 10. ACL — EJEMPLOS DE TORRENT S.L.

## `hmi_planta`

```bash
sudo setfacl -m g:operarios:--x /home/hmi_planta
sudo setfacl -m u:nestor:r-x /home/hmi_planta
sudo setfacl -m u:lucia:rwx /home/hmi_planta
```

## `planos/`

```bash
sudo setfacl -m u:victor:rwx /home/planos
sudo setfacl -m g:supervisores:r-x /home/planos
sudo setfacl -m u:alberto:--x,u:oscar:--x /home/planos
```

## Ficheros `.dwg`

```bash
sudo setfacl -m u:victor:rw- /home/planos/*.dwg
sudo setfacl -m g:supervisores:r-- /home/planos/*.dwg
sudo setfacl -m u:alberto:r-- /home/planos/autoclave.dwg
sudo setfacl -m u:oscar:r-- /home/planos/caldera.dwg
```

---

# 11. PAM — INSTALAR `pwquality`

```bash
sudo apt update
sudo apt install libpam-pwquality -y
```

Instala el módulo de calidad de contraseñas.

```bash
dpkg -s libpam-pwquality | grep Status
```

Comprueba que está instalado.

---

# 12. PAM — COMPROBAR QUE ESTÁ ACTIVO

```bash
grep -nE 'pam_pwquality.so|pam_unix.so' /etc/pam.d/common-password
```

`pam_pwquality.so` debe aparecer antes de `pam_unix.so`.

---

# 13. PAM — CONFIGURAR CALIDAD

```bash
sudo nano /etc/security/pwquality.conf
```

Valores usados:

```text
minlen = 12
minclass = 3
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = -1
dictcheck = 1
maxrepeat = 3
enforce_for_root
```

### Significado rápido

```text
minlen        longitud mínima
minclass      nº mínimo de tipos de carácter
dcredit       exige dígito
ucredit       exige mayúscula
lcredit       exige minúscula
ocredit       exige símbolo
dictcheck     evita palabras de diccionario
maxrepeat     limita caracteres repetidos
enforce_for_root  root también cumple la política
```

---

# 14. PAM — COMPROBAR PARÁMETROS

```bash
grep -nE '^[[:space:]]*(minlen|minclass|dcredit|ucredit|lcredit|ocredit|dictcheck|maxrepeat|enforce_for_root)' /etc/security/pwquality.conf
```

Comprueba que los parámetros activos no estén duplicados.

---

# 15. PAM — PROBAR CONTRASEÑA

```bash
sudo passwd usuario
```

Cambia la contraseña.

Si no cumple la política aparecerá:

```text
BAD PASSWORD: ...
```

```bash
sudo passwd -S usuario
```

Muestra el estado de la contraseña.

`P` = contraseña establecida.

---

# 16. `chage` — CADUCIDAD

```bash
sudo chage -m 1 -M 60 -W 7 usuario
```

- `-m 1`: mínimo 1 día antes de volver a cambiar.
- `-M 60`: caduca en 60 días.
- `-W 7`: avisa 7 días antes.

Operarios:

```bash
sudo chage -m 1 -M 90 -W 7 usuario
```

Ver configuración:

```bash
sudo chage -l usuario
```

---

# 17. MFA — INSTALAR GOOGLE AUTHENTICATOR

```bash
sudo apt install libpam-google-authenticator -y
```

Comprobar:

```bash
which google-authenticator
dpkg -s libpam-google-authenticator | grep Status
```

---

# 18. MFA — GENERAR TOTP PARA UN USUARIO

```bash
sudo -iu carlos
google-authenticator
```

Respuestas:

```text
Time-based tokens                 y
Actualizar .google_authenticator y
No reutilizar código             y
Ampliar ventana                  n
Rate limit                       y
```

### NO mostrar ni subir a GitHub

```text
QR
secret key
códigos TOTP
scratch codes
contraseñas
```

---

# 19. MFA — COMPROBAR ARCHIVO

```bash
ls -l ~/.google_authenticator
```

Comprueba que existe sin mostrar su contenido.

---

# 20. MFA — CREAR GRUPO

```bash
getent group mfa || sudo groupadd mfa
sudo usermod -aG mfa carlos
```

Comprobar:

```bash
getent group mfa
id carlos
```

---

# 21. MFA — CONFIGURAR PAM PARA UN GRUPO

Editar:

```bash
sudo nano /etc/pam.d/sshd
```

Debajo de:

```text
@include common-auth
```

añadir:

```text
auth [success=1 default=ignore] pam_succeed_if.so quiet user notingroup mfa
auth required pam_google_authenticator.so
```

Comprobar:

```bash
sudo grep -nE 'common-auth|pam_succeed_if|pam_google_authenticator' /etc/pam.d/sshd
```

---

# 22. MFA — CONFIGURAR SSH

Editar:

```bash
sudo nano /etc/ssh/sshd_config
```

Asegurar:

```text
KbdInteractiveAuthentication yes
UsePAM yes
PasswordAuthentication yes
```

Al final:

```text
Match Group mfa
    AuthenticationMethods keyboard-interactive:pam
```

---

# 23. SSH — VALIDAR ANTES DE REINICIAR

```bash
sudo sshd -t
```

Si **no sale nada**, la sintaxis es válida.

Comprobar configuración:

```bash
sudo sshd -T | grep -E 'usepam|kbdinteractiveauthentication|passwordauthentication|authenticationmethods'
```

---

# 24. SSH — COMPROBAR MÉTODO POR USUARIO

Carlos:

```bash
sudo sshd -T -C user=carlos,host=localhost,addr=127.0.0.1 | grep authenticationmethods
```

Esperado:

```text
authenticationmethods keyboard-interactive:pam
```

Linux:

```bash
sudo sshd -T -C user=linux,host=localhost,addr=127.0.0.1 | grep authenticationmethods
```

Esperado:

```text
authenticationmethods any
```

---

# 25. SSH — REINICIAR

```bash
sudo sshd -t
sudo systemctl restart ssh
sudo systemctl status ssh --no-pager
```

Debe aparecer:

```text
Active: active (running)
```

Si aparece aviso de systemd:

```bash
sudo systemctl daemon-reload
```

---

# 26. SSH — PROBAR MFA DESDE WINDOWS

```powershell
ssh -o PreferredAuthentications=keyboard-interactive carlos@127.0.0.1 -p 2222
```

Debe pedir:

```text
Password:
Verification code:
```

Haz también una prueba con código incorrecto: debe rechazar el acceso.

---

# 27. SUDO — CREAR SCRIPT DE AUDITORÍA

```bash
sudo tee /usr/local/bin/auditoria_planta.sh > /dev/null <<'EOF'
#!/bin/bash
echo "[$(date)] Auditoria de planta ejecutada por $(whoami)"
EOF
```

Dar permisos:

```bash
sudo chmod 755 /usr/local/bin/auditoria_planta.sh
```

Comprobar:

```bash
ls -l /usr/local/bin/auditoria_planta.sh
```

---

# 28. SUDOERS — CREAR REGLAS

```bash
sudo visudo -f /etc/sudoers.d/torrent
```

Reglas:

```text
%ingenieria ALL=(root) /usr/bin/systemctl restart hmi_planta, /usr/bin/systemctl status hmi_planta
%supervisores ALL=(root) NOPASSWD: /usr/local/bin/auditoria_planta.sh
```

No hay regla para `operarios`.

---

# 29. SUDOERS — VALIDAR

```bash
sudo visudo -c
```

Esperado:

```text
parsed OK
```

---

# 30. SUDOERS — ERROR DE PERMISOS

Si aparece:

```text
bad permissions, should be mode 0440
```

Solución:

```bash
sudo chown root:root /etc/sudoers.d/torrent
sudo chmod 440 /etc/sudoers.d/torrent
sudo visudo -c
```

---

# 31. SUDO — VER PERMISOS DE UN USUARIO

```bash
sudo -l -U nestor
```

Debe mostrar `restart/status hmi_planta`.

```bash
sudo -l -U carlos
```

Debe mostrar:

```text
NOPASSWD: /usr/local/bin/auditoria_planta.sh
```

```bash
sudo -l -U alberto
```

Debe indicar que no tiene permisos sudo.

---

# 32. SUDO — PROBAR COMO USUARIO

```bash
sudo -iu alberto
```

Probar:

```bash
sudo systemctl restart hmi_planta
```

Debe denegarse.

Volver:

```bash
exit
```

---

# 33. SUDO — PROBAR `NOPASSWD`

```bash
sudo -iu carlos
sudo -n /usr/local/bin/auditoria_planta.sh
```

Esperado:

```text
Auditoria de planta ejecutada por root
```

`-n` hace que sudo no pregunte contraseña.

---

# 34. LOGS

Según la distribución:

```bash
sudo journalctl -u sudo | tail -n 20
```

o:

```bash
sudo tail -n 20 /var/log/auth.log
```

Buscar un usuario:

```bash
sudo grep -a -i 'alberto' /var/log/auth.log | tail -n 15
```

---

# 35. SI PASA ESTO → MIRA ESTO

## `Permission denied`

```bash
ls -l recurso
getfacl recurso
```

Revisa:

```text
mask::
#effective:
```

## ACL pone `rwx` pero no funciona

La `mask` puede estar limitando los permisos.

## No puedo entrar a un fichero dentro de una carpeta

Comprueba `x` en el directorio padre.

## Los archivos nuevos no heredan ACL

Falta una ACL por defecto:

```bash
sudo setfacl -d -m ...
```

## SSH no arranca después de editar

Antes de reiniciar:

```bash
sudo sshd -t
```

## MFA no funciona

Comprueba:

```bash
ls -l ~/.google_authenticator
id usuario
sudo grep -nE 'common-auth|pam_succeed_if|pam_google_authenticator' /etc/pam.d/sshd
sudo sshd -T | grep -E 'usepam|kbdinteractiveauthentication|passwordauthentication|authenticationmethods'
```

## sudoers da error

```bash
sudo visudo -c
```

Si es por permisos:

```bash
sudo chmod 440 /etc/sudoers.d/torrent
```

## Quiero saber exactamente qué sudo puede ejecutar alguien

```bash
sudo -l -U usuario
```

---

# 36. ORDEN RÁPIDO PARA UN EJERCICIO DE ACL

```text
1. Crear grupos
2. Crear usuarios
3. Añadir usuarios a grupos
4. Crear recursos
5. chown
6. chmod restrictivo
7. setfacl
8. ACL por defecto si hace falta
9. getfacl
10. revisar mask
11. probar usuarios
12. probar también lo que debe fallar
```

---

# 37. ORDEN RÁPIDO PARA PAM + MFA + SUDO

```text
PAM:
instalar → configurar → probar contraseña → chage → comprobar

MFA:
instalar → google-authenticator → grupo mfa → PAM → SSH → sshd -t → reiniciar → probar

SUDO:
visudo → reglas → chmod 440 → visudo -c → sudo -l -U → probar → logs
```

---

# 38. COMANDOS QUE MÁS DEBES RECORDAR

```bash
id usuario
getent group grupo

getfacl recurso
setfacl -m u:usuario:rwx recurso
setfacl -m g:grupo:rwx recurso
setfacl -R -m g:grupo:rwx directorio
setfacl -d -m g:grupo:rwx directorio

sudo passwd usuario
sudo chage -m 1 -M 60 -W 7 usuario
sudo chage -l usuario

google-authenticator

sudo sshd -t
sudo systemctl restart ssh

sudo visudo -f /etc/sudoers.d/torrent
sudo visudo -c
sudo -l -U usuario

sudo tail -n 20 /var/log/auth.log
```

---

## Regla final para el examen

No memorices solo el comando. Piensa siempre:

```text
¿Qué quiero conseguir?
¿Qué comando lo hace?
¿Cómo compruebo que ha funcionado?
¿Qué resultado debería fallar?
```
