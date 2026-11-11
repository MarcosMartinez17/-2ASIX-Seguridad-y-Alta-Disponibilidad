[UD3_SAD_Como_Resolver_Ejercicios_Examen.md](https://github.com/user-attachments/files/34681761/UD3_SAD_Como_Resolver_Ejercicios_Examen.md)
# SAD · UD3 — Cómo resolver ejercicios prácticos de examen

## Objetivo

Esta guía está pensada para ayudarte a **resolver ejercicios prácticos de la UD3 en un examen**, no solo para memorizar comandos.

Está basada en los tipos de tareas trabajados en tus prácticas y en las guías del profesor:

- creación de usuarios y grupos;
- permisos clásicos;
- ACL;
- ACL por defecto;
- comprobación con `getfacl`;
- políticas de contraseñas con PAM;
- caducidad con `chage`;
- MFA/TOTP con Google Authenticator;
- configuración de SSH;
- privilegios mínimos con `sudo`.

Los ejercicios de esta guía son **inventados para practicar**, pero usan los mismos conceptos y comandos trabajados en clase.

---

# ÍNDICE

1. Cómo pensar un ejercicio práctico
2. Método rápido para ACL
3. Ejercicio 1 — ACL completo
4. Método rápido para PAM y contraseñas
5. Ejercicio 2 — PAM + chage + MFA
6. Método rápido para sudo
7. Ejercicio 3 — sudoers y mínimo privilegio
8. Ejercicio 4 — Detectar errores
9. Ejercicio 5 — Elegir el comando correcto
10. Errores típicos de examen
11. Mini chuleta de razonamiento
12. Checklist final

---

# 1. CÓMO PENSAR UN EJERCICIO PRÁCTICO

Antes de escribir comandos, hazte estas preguntas:

```text
1. ¿Qué usuarios aparecen?
2. ¿Qué grupos aparecen?
3. ¿Quién pertenece a qué grupo?
4. ¿Qué archivos/directorios existen?
5. ¿Quién debe ser propietario?
6. ¿Qué permisos base pongo?
7. ¿Qué usuarios necesitan excepciones?
8. ¿Hay que usar ACL?
9. ¿Hay que usar ACL por defecto?
10. ¿Qué debería funcionar?
11. ¿Qué debería fallar?
12. ¿Cómo voy a comprobarlo?
```

No empieces escribiendo `setfacl` sin haber contestado antes esas preguntas.

---

# 2. MÉTODO RÁPIDO PARA ACL

## Paso mental 1 — separar grupos y usuarios

Ejemplo:

```text
grupo empleados:
ana
luis

grupo responsables:
maria
pedro
```

---

## Paso mental 2 — identificar recursos

Ejemplo:

```text
/home/documentos
/home/documentos/informe.txt
```

---

## Paso mental 3 — traducir permisos

Si el enunciado dice:

```text
empleados pueden leer
responsables pueden leer y escribir
ana no puede acceder
```

Traducimos:

```text
empleados      r--
responsables   rw-
ana            ---
```

Si se trata de una carpeta:

```text
leer/listar    r-x
modificar      rwx
entrar         --x
```

---

## Paso mental 4 — decidir si la regla es de usuario o grupo

Grupo:

```bash
setfacl -m g:grupo:permisos recurso
```

Usuario:

```bash
setfacl -m u:usuario:permisos recurso
```

---

## Paso mental 5 — decidir `-R` o `-d`

```text
-R = aplicar ahora a lo que ya existe
-d = herencia para lo que se cree después
```

---

## Paso mental 6 — comprobar

```bash
getfacl recurso
```

Después probar:

```bash
sudo -u usuario comando
```

---

# 3. EJERCICIO 1 — ACL COMPLETO

## Enunciado inventado

Una empresa tiene estos grupos:

```text
tecnicos:
raul
ines

administracion:
laura
sergio

auditores:
pablo
nuria
```

Debemos crear:

```text
/home/proyectos
/home/proyectos/proyectoA.txt
/home/proyectos/proyectoB.txt
```

Permisos:

```text
tecnicos:
leer y modificar todos los proyectos

administracion:
solo leer

auditores:
sin acceso

pablo:
excepción individual, puede leer proyectoA.txt
```

Además, los nuevos archivos creados en `/home/proyectos` deben permitir:

```text
tecnicos      lectura/escritura
administracion lectura
auditores      nada
```

---

## 3.1. Antes de hacer comandos

Tabla mental:

| Entrada | Directorio | Archivos |
|---|---|---|
| tecnicos | `rwx` | `rw-` |
| administracion | `r-x` | `r--` |
| auditores | `---` | `---` |
| pablo | `--x` | `r--` solo proyectoA |

---

## 3.2. Crear grupos

```bash
sudo groupadd tecnicos
sudo groupadd administracion
sudo groupadd auditores
```

Comprobar:

```bash
getent group tecnicos administracion auditores
```

---

## 3.3. Crear usuarios

```bash
sudo useradd -m -s /bin/bash raul
sudo useradd -m -s /bin/bash ines

sudo useradd -m -s /bin/bash laura
sudo useradd -m -s /bin/bash sergio

sudo useradd -m -s /bin/bash pablo
sudo useradd -m -s /bin/bash nuria
```

---

## 3.4. Añadir usuarios a grupos

```bash
sudo usermod -aG tecnicos raul
sudo usermod -aG tecnicos ines

sudo usermod -aG administracion laura
sudo usermod -aG administracion sergio

sudo usermod -aG auditores pablo
sudo usermod -aG auditores nuria
```

---

## 3.5. Comprobar

```bash
id raul
id laura
id pablo
```

---

## 3.6. Crear recursos

```bash
sudo mkdir -p /home/proyectos
sudo touch /home/proyectos/proyectoA.txt
sudo touch /home/proyectos/proyectoB.txt
```

---

## 3.7. Permisos base restrictivos

```bash
sudo chmod 700 /home/proyectos
sudo chmod 600 /home/proyectos/*.txt
```

Así todo acceso adicional debe concederse explícitamente.

---

## 3.8. ACL del directorio

```bash
sudo setfacl -m g:tecnicos:rwx /home/proyectos
sudo setfacl -m g:administracion:r-x /home/proyectos
sudo setfacl -m g:auditores:--- /home/proyectos
```

Pablo necesita atravesar el directorio para llegar al archivo:

```bash
sudo setfacl -m u:pablo:--x /home/proyectos
```

---

## 3.9. ACL de los archivos

Técnicos:

```bash
sudo setfacl -m g:tecnicos:rw- /home/proyectos/*.txt
```

Administración:

```bash
sudo setfacl -m g:administracion:r-- /home/proyectos/*.txt
```

Auditores:

```bash
sudo setfacl -m g:auditores:--- /home/proyectos/*.txt
```

Excepción:

```bash
sudo setfacl -m u:pablo:r-- /home/proyectos/proyectoA.txt
```

---

## 3.10. ACL por defecto

```bash
sudo setfacl -d -m g:tecnicos:rwx /home/proyectos
sudo setfacl -d -m g:administracion:r-x /home/proyectos
sudo setfacl -d -m g:auditores:--- /home/proyectos
sudo setfacl -d -m o::--- /home/proyectos
```

---

## 3.11. Comprobar

```bash
getfacl /home/proyectos
getfacl /home/proyectos/proyectoA.txt
getfacl /home/proyectos/proyectoB.txt
```

---

## 3.12. Probar técnico

```bash
sudo -u raul cat /home/proyectos/proyectoA.txt
sudo -u raul bash -c 'echo "cambio" >> /home/proyectos/proyectoA.txt'
```

Debe funcionar.

---

## 3.13. Probar administración

```bash
sudo -u laura cat /home/proyectos/proyectoA.txt
```

Debe funcionar.

Escritura:

```bash
sudo -u laura bash -c 'echo "cambio" >> /home/proyectos/proyectoA.txt'
```

Debe fallar.

---

## 3.14. Probar auditor

```bash
sudo -u nuria ls /home/proyectos
```

Debe fallar.

---

## 3.15. Probar excepción de Pablo

```bash
sudo -u pablo cat /home/proyectos/proyectoA.txt
```

Debe funcionar.

```bash
sudo -u pablo cat /home/proyectos/proyectoB.txt
```

Debe fallar.

---

## 3.16. Qué se evalúa realmente

El profesor puede cambiar nombres y permisos, pero el razonamiento es el mismo:

```text
grupo → regla de grupo
excepción → regla individual
carpeta → necesita x
existentes → -R si procede
futuros → -d
comprobar → getfacl
probar → sudo -u
```

---

# 4. MÉTODO RÁPIDO PARA PAM Y CONTRASEÑAS

Piensa en tres capas:

```text
pwquality = calidad
chage     = duración
PAM       = integración
```

---

## 4.1. Si preguntan calidad de contraseña

Instalar:

```bash
sudo apt install libpam-pwquality -y
```

Comprobar:

```bash
grep -nE 'pam_pwquality.so|pam_unix.so' /etc/pam.d/common-password
```

Configurar:

```bash
sudo nano /etc/security/pwquality.conf
```

---

## 4.2. Si preguntan caducidad

```bash
sudo chage -m 1 -M 60 -W 7 usuario
```

Consultar:

```bash
sudo chage -l usuario
```

---

## 4.3. Si preguntan si una contraseña cumple

```bash
sudo passwd usuario
```

---

# 5. EJERCICIO 2 — PAM + CHAGE + MFA

## Enunciado inventado

Se pide:

1. Exigir mínimo 12 caracteres.
2. Exigir mayúscula, minúscula, número y símbolo.
3. Evitar palabras de diccionario.
4. Contraseñas de administradores: máximo 60 días.
5. Contraseñas de empleados: máximo 90 días.
6. Activar MFA por SSH para el grupo `seguridad`.

---

## 5.1. Instalar pwquality

```bash
sudo apt update
sudo apt install libpam-pwquality -y
```

---

## 5.2. Comprobar PAM

```bash
grep -nE 'pam_pwquality.so|pam_unix.so' /etc/pam.d/common-password
```

---

## 5.3. Configurar calidad

```bash
sudo nano /etc/security/pwquality.conf
```

Valores:

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

---

## 5.4. Probar

```bash
sudo passwd usuario
```

Una contraseña débil debe mostrar:

```text
BAD PASSWORD
```

---

## 5.5. Configurar caducidad

Administradores:

```bash
sudo chage -m 1 -M 60 -W 7 admin1
```

Empleados:

```bash
sudo chage -m 1 -M 90 -W 7 empleado1
```

Comprobar:

```bash
sudo chage -l admin1
sudo chage -l empleado1
```

---

## 5.6. Instalar MFA

```bash
sudo apt install libpam-google-authenticator -y
```

---

## 5.7. Crear grupo MFA

```bash
sudo groupadd seguridad
sudo usermod -aG seguridad admin1
```

---

## 5.8. Generar TOTP

```bash
sudo -iu admin1
google-authenticator
```

Respuestas:

```text
y
y
y
n
y
```

---

## 5.9. Configurar PAM SSH

```bash
sudo nano /etc/pam.d/sshd
```

Añadir debajo de `@include common-auth`:

```text
auth [success=1 default=ignore] pam_succeed_if.so quiet user notingroup seguridad
auth required pam_google_authenticator.so
```

---

## 5.10. Configurar SSH

```bash
sudo nano /etc/ssh/sshd_config
```

Asegurar:

```text
KbdInteractiveAuthentication yes
UsePAM yes
PasswordAuthentication yes
```

Y:

```text
Match Group seguridad
    AuthenticationMethods keyboard-interactive:pam
```

---

## 5.11. Validar

```bash
sudo sshd -t
```

Si no muestra nada:

```text
correcto
```

---

## 5.12. Reiniciar

```bash
sudo systemctl restart ssh
```

---

## 5.13. Probar

```powershell
ssh -o PreferredAuthentications=keyboard-interactive admin1@127.0.0.1 -p 2222
```

Debe pedir:

```text
Password:
Verification code:
```

---

# 6. MÉTODO RÁPIDO PARA SUDO

Antes de escribir sudoers:

```text
¿Quién?
¿Como quién?
¿Qué comando exacto?
¿Con o sin contraseña?
```

---

## Sintaxis mental

```text
%grupo ALL=(root) comando
```

Con `NOPASSWD`:

```text
%grupo ALL=(root) NOPASSWD: comando
```

---

# 7. EJERCICIO 3 — SUDOERS Y MÍNIMO PRIVILEGIO

## Enunciado inventado

Se quiere:

```text
grupo soporte:
puede reiniciar apache2

grupo auditoria:
puede ejecutar /usr/local/bin/revisar.sh sin contraseña

grupo usuarios:
sin permisos sudo
```

---

## 7.1. Crear grupos

```bash
sudo groupadd soporte
sudo groupadd auditoria
sudo groupadd usuarios
```

---

## 7.2. Crear script

```bash
sudo tee /usr/local/bin/revisar.sh > /dev/null <<'EOF'
#!/bin/bash
echo "Revision ejecutada por $(whoami)"
EOF
```

Permisos:

```bash
sudo chmod 755 /usr/local/bin/revisar.sh
```

---

## 7.3. Crear sudoers

```bash
sudo visudo -f /etc/sudoers.d/examen
```

Añadir:

```text
%soporte ALL=(root) /usr/bin/systemctl restart apache2, /usr/bin/systemctl status apache2
%auditoria ALL=(root) NOPASSWD: /usr/local/bin/revisar.sh
```

---

## 7.4. Permisos del fichero

```bash
sudo chown root:root /etc/sudoers.d/examen
sudo chmod 440 /etc/sudoers.d/examen
```

---

## 7.5. Validar

```bash
sudo visudo -c
```

Debe mostrar:

```text
parsed OK
```

---

## 7.6. Comprobar privilegios

```bash
sudo -l -U usuario_soporte
sudo -l -U usuario_auditoria
sudo -l -U usuario_normal
```

---

## 7.7. Probar usuario normal

```bash
sudo -iu usuario_normal
sudo systemctl restart apache2
```

Debe denegarse.

---

## 7.8. Probar auditoría

```bash
sudo -iu usuario_auditoria
sudo -n /usr/local/bin/revisar.sh
```

Debe ejecutarse como root sin pedir contraseña.

---

# 8. EJERCICIO 4 — DETECTAR ERRORES

## Caso A

Tenemos:

```text
user:victor:rwx
mask::r-x
```

Pregunta:

¿Por qué Víctor no puede escribir?

### Respuesta

Porque la máscara limita el permiso efectivo a:

```text
r-x
```

---

## Caso B

Alberto tiene:

```text
user:alberto:r-- /home/planos/autoclave.dwg
```

pero no puede leerlo.

Pregunta:

¿Qué puede faltar?

### Respuesta

Permiso `x` sobre el directorio:

```bash
sudo setfacl -m u:alberto:--x /home/planos
```

---

## Caso C

Los archivos nuevos no heredan ACL.

Se usó:

```bash
setfacl -R -m g:grupo:rwx carpeta
```

### Error

`-R` no crea herencia.

### Solución

```bash
setfacl -d -m g:grupo:rwx carpeta
```

---

## Caso D

`visudo -c` muestra:

```text
bad permissions, should be mode 0440
```

### Solución

```bash
sudo chown root:root /etc/sudoers.d/archivo
sudo chmod 440 /etc/sudoers.d/archivo
sudo visudo -c
```

---

## Caso E

Después de editar SSH no sabes si puedes reiniciar.

### Solución

```bash
sudo sshd -t
```

Si no muestra nada:

```text
sintaxis correcta
```

---

## Caso F

Carlos no pide código MFA.

Comprobar:

```bash
id carlos
```

Debe estar en el grupo MFA.

Después:

```bash
sudo grep -nE 'pam_succeed_if|pam_google_authenticator' /etc/pam.d/sshd
```

Y:

```bash
sudo sshd -T -C user=carlos,host=localhost,addr=127.0.0.1 | grep authenticationmethods
```

---

# 9. EJERCICIO 5 — ELEGIR EL COMANDO CORRECTO

## Quiero crear un grupo

```bash
sudo groupadd grupo
```

---

## Quiero crear un usuario con `/home`

```bash
sudo useradd -m -s /bin/bash usuario
```

---

## Quiero añadir un usuario a un grupo

```bash
sudo usermod -aG grupo usuario
```

---

## Quiero ver a qué grupos pertenece

```bash
id usuario
```

---

## Quiero ver una ACL

```bash
getfacl recurso
```

---

## Quiero dar permisos ACL a un usuario

```bash
sudo setfacl -m u:usuario:rwx recurso
```

---

## Quiero dar permisos ACL a un grupo

```bash
sudo setfacl -m g:grupo:rwx recurso
```

---

## Quiero que también se aplique a archivos existentes

```bash
sudo setfacl -R -m ...
```

---

## Quiero que lo hereden archivos nuevos

```bash
sudo setfacl -d -m ...
```

---

## Quiero probar lectura como otro usuario

```bash
sudo -u usuario cat archivo
```

---

## Quiero probar escritura

```bash
sudo -u usuario bash -c 'echo "texto" >> archivo'
```

---

## Quiero saber si puede ejecutar/acceder

```bash
sudo -u usuario test -x recurso
```

---

## Quiero cambiar contraseña

```bash
sudo passwd usuario
```

---

## Quiero cambiar caducidad

```bash
sudo chage -m 1 -M 60 -W 7 usuario
```

---

## Quiero consultar caducidad

```bash
sudo chage -l usuario
```

---

## Quiero generar TOTP

```bash
google-authenticator
```

---

## Quiero validar SSH

```bash
sudo sshd -t
```

---

## Quiero reiniciar SSH

```bash
sudo systemctl restart ssh
```

---

## Quiero editar sudoers

```bash
sudo visudo -f /etc/sudoers.d/archivo
```

---

## Quiero validar sudoers

```bash
sudo visudo -c
```

---

## Quiero ver qué puede ejecutar un usuario con sudo

```bash
sudo -l -U usuario
```

---

# 10. ERRORES TÍPICOS DE EXAMEN

## Error 1 — olvidar comprobar

No basta con:

```bash
setfacl ...
```

Después:

```bash
getfacl ...
```

---

## Error 2 — no probar lo que debe fallar

Un buen ejercicio comprueba:

```text
lo que debe funcionar
y
lo que debe fallar
```

---

## Error 3 — confundir `rwx` de archivo y directorio

Archivo:

```text
r leer
w escribir
x ejecutar
```

Directorio:

```text
r listar
w crear/borrar
x atravesar
```

---

## Error 4 — olvidar `x` en directorios

Muy común.

---

## Error 5 — confundir `-R` con `-d`

```text
-R = existentes
-d = futuros
```

---

## Error 6 — ignorar `mask`

Siempre mirar:

```text
mask::
#effective:
```

---

## Error 7 — editar sudoers con nano directamente

Mejor:

```bash
visudo
```

---

## Error 8 — reiniciar SSH sin validar

Antes:

```bash
sshd -t
```

---

## Error 9 — cerrar la única sesión SSH durante MFA

Mantén una sesión abierta hasta probar desde otra terminal.

---

## Error 10 — publicar secretos MFA

Nunca:

```text
QR
secret
TOTP
scratch codes
contraseñas
```

---

# 11. MINI CHULETA DE RAZONAMIENTO

## ACL

```text
usuario/grupo
↓
recurso
↓
rwx
↓
setfacl
↓
getfacl
↓
probar
```

---

## PAM

```text
instalar
↓
configurar
↓
probar contraseña débil
↓
probar contraseña válida
↓
chage
↓
comprobar
```

---

## MFA

```text
instalar
↓
generar TOTP
↓
grupo
↓
PAM
↓
SSH
↓
sshd -t
↓
reiniciar
↓
probar
```

---

## sudo

```text
qué necesita cada grupo
↓
visudo
↓
regla exacta
↓
0440
↓
visudo -c
↓
sudo -l -U
↓
probar
```

---

# 12. CHECKLIST FINAL PARA EL EXAMEN

## Si es ACL

- [ ] He identificado usuarios.
- [ ] He identificado grupos.
- [ ] He creado usuarios/grupos.
- [ ] He añadido usuarios a grupos.
- [ ] He creado recursos.
- [ ] He puesto propietario y permisos base.
- [ ] He traducido permisos a `rwx`.
- [ ] He usado ACL de grupo o usuario según corresponda.
- [ ] He pensado si necesito `-R`.
- [ ] He pensado si necesito `-d`.
- [ ] He usado `getfacl`.
- [ ] He revisado `mask`.
- [ ] He probado usuarios autorizados.
- [ ] He probado accesos que deben fallar.

## Si es PAM

- [ ] He instalado `libpam-pwquality`.
- [ ] He comprobado PAM.
- [ ] He configurado `pwquality.conf`.
- [ ] He probado contraseña débil.
- [ ] He probado una válida.
- [ ] He configurado `chage`.
- [ ] He comprobado con `chage -l`.

## Si es MFA

- [ ] He instalado Google Authenticator PAM.
- [ ] He generado TOTP como el usuario.
- [ ] He creado el grupo MFA.
- [ ] He configurado PAM.
- [ ] He configurado SSH.
- [ ] He validado con `sshd -t`.
- [ ] He mantenido una sesión abierta.
- [ ] He reiniciado SSH.
- [ ] He probado código correcto.
- [ ] He probado código incorrecto.

## Si es sudo

- [ ] He definido el comando exacto.
- [ ] He usado `visudo`.
- [ ] He puesto solo los privilegios necesarios.
- [ ] He corregido permisos 0440.
- [ ] He usado `visudo -c`.
- [ ] He comprobado con `sudo -l -U`.
- [ ] He probado usuario autorizado.
- [ ] He probado usuario no autorizado.
- [ ] He revisado logs si lo pide.

---

# REGLA FINAL

En cualquier ejercicio práctico piensa siempre:

```text
CONFIGURAR
↓
COMPROBAR
↓
PROBAR
↓
PROBAR TAMBIÉN EL FALLO
```

Si haces esas cuatro cosas, es mucho más difícil dejarte una parte importante del ejercicio.
