# SAD · UD3 — Guía práctica completa para el examen

## Objetivo

Este documento reúne **solo la parte práctica** de la carpeta `U3` de tu repositorio y la contrasta con las guías del profesor.

Se han revisado estos tres entregables:

1. `ACL_guiado.md`
2. `ACL_entregable.md`
3. `PAM,MFA,ROOT.md`

Y estas tres guías del profesor:

1. **Actividad Guiada ACL** — escenario `grupoA`, `grupoB`, `grupoC`.
2. **Configuración de ACL en Linux — Torrent S.L.** — `hmi_planta` y `planos/`.
3. **Contraseñas, MFA y privilegios justos — Torrent S.L.** — PAM, MFA/TOTP y sudo.

> **Nota:** la tercera guía aparece rotulada como UD5, pero se incluye porque corresponde al tercer entregable que tienes dentro de `U3`.

> **Nota sobre la entrega:** la guía de ACL Torrent S.L. menciona un script `.sh`, pero en clase el profesor os indicó después que documentarais directamente los comandos en GitHub. Para estudiar, aquí se presentan los comandos paso a paso.

---

# ÍNDICE

1. Entorno común y SSH
2. Práctica 1 — ACL guiada
3. Práctica 2 — ACL Torrent S.L.
4. Práctica 3 — Contraseñas robustas con PAM
5. Práctica 3 — MFA/TOTP con Google Authenticator
6. Práctica 3 — Privilegios mínimos con sudo
7. Errores y situaciones típicas
8. Chuleta final de comandos
9. Preguntas prácticas que deberías saber explicar
10. Orden de resolución para el examen

---

# 1. ENTORNO COMÚN Y SSH

Las prácticas se hicieron sobre la máquina **SAD-Ubuntu / Auditor**.

## 1.1. Instalar y activar SSH

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
```

Comprobar:

```bash
sudo systemctl status ssh
```

Resultado esperado:

```text
Active: active (running)
```

### Qué hace cada comando

- `apt update`: actualiza la información de paquetes.
- `apt install openssh-server`: instala el servidor SSH.
- `systemctl enable --now ssh`: inicia SSH y lo habilita al arrancar.
- `systemctl status ssh`: comprueba su estado.

---

## 1.2. Reenvío de puertos en VirtualBox

En el adaptador NAT se utilizó una regla similar a:

| Campo | Valor |
|---|---|
| Protocolo | TCP |
| IP anfitrión | `127.0.0.1` |
| Puerto anfitrión | `2222` |
| Puerto invitado | `22` |

Desde PowerShell:

```powershell
ssh linux@127.0.0.1 -p 2222
```

El puerto `2222` del PC físico se redirige al puerto `22` de Ubuntu.

Si la huella SSH ha cambiado y has comprobado que realmente es tu VM:

```powershell
ssh-keygen -R "[127.0.0.1]:2222"
```

---

# 2. PRÁCTICA 1 — ACL GUIADA

## 2.1. Escenario

Tres grupos:

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

Dos directorios:

```text
/home/compartido01
/home/compartido02
```

Cada directorio contiene:

```text
hola.txt
adios.txt
```

## 2.2. Permisos que se quieren conseguir

| Usuario o grupo | Lectura | Escritura | Listado/acceso |
|---|---:|---:|---:|
| `grupoA` | Sí | Sí | Sí |
| `grupoB` | Sí | No | Sí |
| `grupoC` | No | No | No |
| `usuarioC03` | Sí | No | Sí |

`usuarioC03` es una excepción individual dentro de `grupoC`.

---

## 2.3. Ir a `/home`

```bash
cd /home
pwd
ls -la
```

`pwd` debe mostrar:

```text
/home
```

---

## 2.4. Crear grupos

```bash
sudo groupadd grupoA
sudo groupadd grupoB
sudo groupadd grupoC
```

Comprobar:

```bash
getent group grupoA grupoB grupoC
```

- `groupadd`: crea un grupo.
- `getent group`: consulta los grupos.

---

## 2.5. Crear usuarios

### Grupo A

```bash
sudo useradd -m -s /bin/bash usuarioA01
sudo useradd -m -s /bin/bash usuarioA02
sudo useradd -m -s /bin/bash usuarioA03
```

### Grupo B

```bash
sudo useradd -m -s /bin/bash usuarioB01
sudo useradd -m -s /bin/bash usuarioB02
sudo useradd -m -s /bin/bash usuarioB03
```

### Grupo C

```bash
sudo useradd -m -s /bin/bash usuarioC01
sudo useradd -m -s /bin/bash usuarioC02
sudo useradd -m -s /bin/bash usuarioC03
```

Comprobar:

```bash
ls -ld /home/usuario*
```

### Opciones

- `-m`: crea el directorio personal.
- `-s /bin/bash`: asigna Bash como shell.

---

## 2.6. Añadir usuarios a sus grupos

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

Comprobar:

```bash
getent group grupoA grupoB grupoC
```

### Muy importante

```text
usermod -aG GRUPO USUARIO
```

- `-a`: añade sin eliminar otros grupos secundarios.
- `-G`: indica el grupo secundario.

---

## 2.7. Instalar ACL

```bash
sudo apt update
sudo apt install acl -y
```

Comprobar:

```bash
which setfacl
which getfacl
```

También:

```bash
dpkg -s acl
```

Debes reconocer:

```text
Status: install ok installed
```

---

## 2.8. Crear directorios compartidos

```bash
cd /home
sudo mkdir -p compartido01 compartido02
```

Comprobar:

```bash
ls -ld /home/compartido01 /home/compartido02
```

---

## 2.9. Crear archivos

```bash
sudo touch /home/compartido01/hola.txt /home/compartido01/adios.txt
sudo touch /home/compartido02/hola.txt /home/compartido02/adios.txt
```

Añadir contenido para poder probar lectura:

```bash
echo "Hola desde compartido01" | sudo tee /home/compartido01/hola.txt
echo "Adios desde compartido01" | sudo tee /home/compartido01/adios.txt

echo "Hola desde compartido02" | sudo tee /home/compartido02/hola.txt
echo "Adios desde compartido02" | sudo tee /home/compartido02/adios.txt
```

Comprobar:

```bash
ls -l /home/compartido01
ls -l /home/compartido02
```

---

## 2.10. Permisos base restrictivos

En tu práctica se eliminaron los permisos de `otros`:

```bash
sudo chmod -R o-rwx /home/compartido01 /home/compartido02
```

- `o`: otros.
- `-rwx`: quitar lectura, escritura y ejecución.
- `-R`: recursivo.

Así el acceso se concede de forma explícita mediante ACL.

---

## 2.11. ACL de grupoA

```bash
sudo setfacl -R -m g:grupoA:rwx /home/compartido01
```

Despiece:

```text
setfacl     modifica ACL
-R          recursivo
-m          añadir o modificar
g:grupoA    entrada para grupoA
rwx         leer + escribir + ejecutar/acceder
```

---

## 2.12. ACL de grupoB

```bash
sudo setfacl -R -m g:grupoB:r-x /home/compartido01
```

`grupoB` puede leer y acceder/listar, pero no escribir.

> En un directorio `x` permite atravesarlo. En un archivo normal `x` significa ejecución. La guía utiliza `-R`, por lo que la regla se aplica también a los ficheros existentes.

---

## 2.13. ACL de grupoC

```bash
sudo setfacl -R -m g:grupoC:--- /home/compartido01
```

No recibe ningún permiso.

---

## 2.14. Excepción para usuarioC03

```bash
sudo setfacl -R -m u:usuarioC03:r-x /home/compartido01
```

Aunque pertenece a `grupoC`, la ACL individual le concede lectura y acceso.

### Idea importante

Una ACL permite asignar permisos a un **usuario concreto** independientemente de los permisos de su grupo.

---

## 2.15. Consultar ACL

```bash
getfacl /home/compartido01
getfacl /home/compartido01/hola.txt
```

Debes reconocer entradas parecidas a:

```text
user:usuarioC03:r-x
group:grupoA:rwx
group:grupoB:r-x
group:grupoC:---
mask::rwx
```

---

## 2.16. Máscara ACL

La `mask` limita los permisos efectivos máximos de los usuarios nombrados y grupos de la ACL.

Ejemplo:

```text
user:usuarioC03:rwx
mask::r-x
```

Aunque la entrada indique `rwx`, los permisos efectivos quedan en `r-x`.

---

## 2.17. Replicar ACL en `compartido02`

Método de la guía:

```bash
getfacl /home/compartido01 | sudo setfacl -R --set-file=- /home/compartido02
```

Qué ocurre:

1. `getfacl` obtiene las ACL.
2. `|` envía la salida al siguiente comando.
3. `--set-file=-` hace que `setfacl` lea las reglas desde la entrada estándar.
4. `-R` las aplica recursivamente.

Comprobar:

```bash
getfacl /home/compartido01
getfacl /home/compartido02
getfacl /home/compartido02/hola.txt
getfacl /home/compartido02/adios.txt
```

Alternativa directa:

```bash
sudo setfacl -R -m g:grupoA:rwx,g:grupoB:r-x,g:grupoC:---,u:usuarioC03:r-x /home/compartido02
```

---

## 2.18. Símbolo `+`

```bash
cd /home
ls -ld compartido01 compartido02
```

Ejemplo:

```text
drwxrwx---+ ... compartido01
```

El `+` indica que existen ACL extendidas.

---

## 2.19. Verificar grupoA

```bash
sudo -u usuarioA01 ls /home/compartido01
sudo -u usuarioA01 cat /home/compartido02/adios.txt
```

Probar escritura:

```bash
sudo -u usuarioA01 bash -c 'echo "Modificado por A01" >> /home/compartido02/adios.txt'
cat /home/compartido02/adios.txt
```

Debe poder:

- listar;
- leer;
- escribir.

---

## 2.20. Verificar grupoB

```bash
sudo -u usuarioB01 ls /home/compartido01
sudo -u usuarioB01 cat /home/compartido02/adios.txt
```

Debe poder listar y leer.

Probar escritura:

```bash
sudo -u usuarioB01 bash -c 'echo "intento" >> /home/compartido02/adios.txt'
```

Esperado:

```text
Permission denied
```

---

## 2.21. Verificar grupoC

```bash
sudo -u usuarioC01 ls /home/compartido01
sudo -u usuarioC01 cat /home/compartido01/hola.txt
```

Debe fallar por falta de permisos.

---

## 2.22. Verificar usuarioC03

```bash
sudo -u usuarioC03 ls /home/compartido01
sudo -u usuarioC03 cat /home/compartido01/hola.txt
```

Debe poder leer/listar.

Probar escritura:

```bash
sudo -u usuarioC03 bash -c 'echo "intento" >> /home/compartido01/hola.txt'
```

Debe fallar.

---

## 2.23. Comandos ACL básicos

```bash
getfacl recurso
setfacl -m u:usuario:rwx recurso
setfacl -m g:grupo:rwx recurso
setfacl -R -m g:grupo:rwx directorio
setfacl -d -m g:grupo:rwx directorio
setfacl -x u:usuario recurso
```

### Diferencia fundamental

- `-R`: aplica la ACL a recursos existentes de forma recursiva.
- `-d`: crea una ACL por defecto para que recursos nuevos hereden permisos.

---

# 3. PRÁCTICA 2 — ACL TORRENT S.L.

## 3.1. Recursos

```text
/home/hmi_planta
/home/planos/
├── autoclave.dwg
├── caldera.dwg
└── destilacion.dwg
```

Grupos:

| Grupo | Usuarios |
|---|---|
| `operarios` | alberto, oscar, hugo |
| `ingenieria` | nestor, victor, lucia |
| `supervisores` | carlos, mary, john |

La práctica demuestra que los permisos clásicos propietario/grupo/resto no son suficientes para expresar todas las excepciones.

---

## 3.2. Permisos implementados

### `hmi_planta`

| Entrada | Permiso |
|---|---|
| `operarios` | ejecutar `--x` |
| `nestor` | `r-x` |
| `lucia` | `rwx` |

### `planos/`

| Entrada | Permiso |
|---|---|
| `victor` | `rwx` sobre el directorio |
| `supervisores` | `r-x` sobre el directorio |
| `alberto` | `--x` para atravesar |
| `oscar` | `--x` para atravesar |

### Ficheros

| Entrada | Recurso | Permiso |
|---|---|---|
| `victor` | todos los `.dwg` | `rw-` |
| `supervisores` | todos los `.dwg` | `r--` |
| `alberto` | `autoclave.dwg` | `r--` |
| `oscar` | `caldera.dwg` | `r--` |

---

## 3.3. Comprobar ACL

```bash
cd /home
dpkg -s acl
```

Buscar:

```text
Status: install ok installed
```

---

## 3.4. Crear grupos

```bash
sudo groupadd operarios
sudo groupadd ingenieria
sudo groupadd supervisores
```

Comprobar:

```bash
getent group operarios ingenieria supervisores
```

---

## 3.5. Crear usuarios

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

## 3.6. Asignar grupos

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

Comprobar:

```bash
getent group operarios
getent group ingenieria
getent group supervisores
```

---

## 3.7. Comprobar directorios personales

```bash
cd /home
ls -ld alberto oscar hugo nestor victor lucia carlos mary john
```

---

## 3.8. Crear recursos

```bash
cd /home
sudo touch hmi_planta
sudo mkdir -p planos

sudo touch /home/planos/autoclave.dwg
sudo touch /home/planos/caldera.dwg
sudo touch /home/planos/destilacion.dwg
```

---

## 3.9. Propietarios

En tu práctica se utilizó `linux`:

```bash
sudo chown linux:linux /home/hmi_planta
sudo chown -R linux:linux /home/planos
```

`chown usuario:grupo recurso` cambia propietario y grupo.

---

## 3.10. Permisos base restrictivos

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

Comprobar:

```bash
ls -l /home/hmi_planta
ls -ld /home/planos
ls -l /home/planos
```

---

## 3.11. ACL de `hmi_planta`

```bash
sudo setfacl -m g:operarios:--x /home/hmi_planta
sudo setfacl -m u:nestor:r-x /home/hmi_planta
sudo setfacl -m u:lucia:rwx /home/hmi_planta
```

Comprobar:

```bash
getfacl /home/hmi_planta
```

---

## 3.12. ACL del directorio `planos`

```bash
sudo setfacl -m u:victor:rwx /home/planos
sudo setfacl -m g:supervisores:r-x /home/planos
sudo setfacl -m u:alberto:--x,u:oscar:--x /home/planos
```

### Por qué `--x` para Alberto y Óscar

Para acceder a un fichero dentro de un directorio hay que poder **atravesar** el directorio.

`--x` permite llegar al fichero conocido sin conceder necesariamente el listado completo.

---

## 3.13. ACL sobre los `.dwg`

```bash
sudo setfacl -m u:victor:rw- /home/planos/*.dwg
sudo setfacl -m g:supervisores:r-- /home/planos/*.dwg
```

Excepciones:

```bash
sudo setfacl -m u:alberto:r-- /home/planos/autoclave.dwg
sudo setfacl -m u:oscar:r-- /home/planos/caldera.dwg
```

---

## 3.14. Comprobar las ACL

```bash
getfacl /home/planos
getfacl /home/planos/autoclave.dwg
getfacl /home/planos/caldera.dwg
getfacl /home/planos/destilacion.dwg
```

Fíjate siempre en:

```text
mask::
#effective:
```

---

## 3.15. ACL por defecto

```bash
sudo setfacl -d -m u:victor:rwx /home/planos
sudo setfacl -d -m g:supervisores:r-x /home/planos
sudo setfacl -d -m o::--- /home/planos
```

`-d` crea ACL de herencia para recursos que se creen después.

No cambia por sí solo los archivos antiguos.

---

## 3.16. Probar la herencia

```bash
sudo touch /home/planos/prueba_acl.txt
getfacl /home/planos/prueba_acl.txt
```

Después:

```bash
sudo rm /home/planos/prueba_acl.txt
```

---

## 3.17. Probar `hmi_planta`

Operarios:

```bash
sudo -u alberto test -x /home/hmi_planta && echo "Alberto puede ejecutar"
sudo -u oscar test -x /home/hmi_planta && echo "Oscar puede ejecutar"
sudo -u hugo test -x /home/hmi_planta && echo "Hugo puede ejecutar"
```

Ingeniería:

```bash
sudo -u nestor test -r /home/hmi_planta && echo "Nestor puede leer"
sudo -u nestor test -x /home/hmi_planta && echo "Nestor puede ejecutar"
sudo -u lucia test -w /home/hmi_planta && echo "Lucia puede escribir"
```

### `test`

- `test -r`: comprueba lectura.
- `test -w`: comprueba escritura.
- `test -x`: comprueba ejecución/acceso.

---

## 3.18. Probar `planos/`

Víctor y supervisores:

```bash
sudo -u victor ls /home/planos
sudo -u carlos ls /home/planos
sudo -u mary cat /home/planos/autoclave.dwg
```

Excepciones:

```bash
sudo -u alberto cat /home/planos/autoclave.dwg
sudo -u oscar cat /home/planos/caldera.dwg
```

Pruebas que deben fallar:

```bash
sudo -u alberto cat /home/planos/caldera.dwg
sudo -u oscar cat /home/planos/autoclave.dwg
```

Esperado:

```text
Permission denied
```

---

## 3.19. Verificación final

```bash
getfacl /home/hmi_planta
getfacl /home/planos
getfacl /home/planos/autoclave.dwg
getfacl /home/planos/caldera.dwg
getfacl /home/planos/destilacion.dwg
```

La guía también propone:

```bash
getfacl -R /home/planos
```

---

## 3.20. ACL de acceso vs ACL por defecto

ACL de acceso:

```bash
sudo setfacl -m u:victor:rwx /home/planos
```

Afecta al recurso actual.

ACL por defecto:

```bash
sudo setfacl -d -m g:supervisores:r-x /home/planos
```

Sirve como plantilla para nuevos recursos dentro de un directorio.

---

## 3.21. Preguntas de reflexión de ACL

### ¿Por qué no bastan los permisos clásicos?

Porque solo distinguen propietario, grupo y resto. En este caso hacen falta permisos diferentes para usuarios concretos sobre el mismo recurso.

### ¿Qué hace la máscara?

Limita los permisos efectivos máximos de las entradas ACL correspondientes.

### ¿ACL de acceso vs ACL por defecto?

- Acceso: permisos del recurso actual.
- Por defecto: permisos que heredarán recursos nuevos.

### ¿Qué hacer con un nuevo operario?

Ejemplo:

```bash
sudo useradd -m -s /bin/bash pedro
sudo usermod -aG operarios pedro
id pedro
```

Si la ACL está puesta sobre el grupo `operarios`, el nuevo usuario recibe esos permisos por pertenecer al grupo.

---

# 4. PRÁCTICA 3 — CONTRASEÑAS ROBUSTAS CON PAM

## 4.1. Objetivo

PAM centraliza la autenticación utilizada por programas como:

```text
login
su
passwd
sshd
```

La política de calidad no se configura editando directamente `/etc/shadow`.

---

## 4.2. Instalar `pwquality`

```bash
sudo apt update
sudo apt install libpam-pwquality -y
```

Comprobar:

```bash
dpkg -s libpam-pwquality | grep Status
```

Esperado:

```text
Status: install ok installed
```

---

## 4.3. Comprobar PAM

```bash
grep -nE 'pam_pwquality.so|pam_unix.so' /etc/pam.d/common-password
```

`pam_pwquality.so` debe aparecer antes que `pam_unix.so`.

La guía usa una línea equivalente a:

```text
password requisite pam_pwquality.so retry=3
```

---

## 4.4. Configurar `pwquality.conf`

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

| Parámetro | Función |
|---|---|
| `minlen = 12` | mínimo 12 caracteres |
| `minclass = 3` | mínimo 3 clases de caracteres |
| `dcredit = -1` | exige dígito |
| `ucredit = -1` | exige mayúscula |
| `lcredit = -1` | exige minúscula |
| `ocredit = -1` | exige símbolo |
| `dictcheck = 1` | rechaza palabras de diccionario |
| `maxrepeat = 3` | limita repeticiones |
| `enforce_for_root` | root también cumple la política |

Con los cuatro `*credit = -1` se exige al menos uno de cada tipo indicado.

---

## 4.5. Comprobar duplicados

```bash
grep -nE '^[[:space:]]*(minlen|minclass|dcredit|ucredit|lcredit|ocredit|dictcheck|maxrepeat|enforce_for_root)' /etc/security/pwquality.conf
```

Cada parámetro activo debería aparecer una sola vez.

---

## 4.6. Probar contraseña débil

```bash
sudo passwd hugo
```

La guía propone probar una contraseña débil como:

```text
1234
```

Debe aparecer:

```text
BAD PASSWORD: ...
```

El motivo concreto puede variar.

> No publiques contraseñas reales en GitHub.

---

## 4.7. Establecer contraseña válida

```bash
sudo passwd hugo
```

Después:

```bash
sudo passwd -S hugo
```

Si aparece `P`, el usuario tiene contraseña establecida.

---

## 4.8. Comprobar `enforce_for_root`

```bash
grep -n '^enforce_for_root' /etc/security/pwquality.conf
```

También:

```bash
sudo passwd -S root
```

`enforce_for_root` evita que root se salte la política al establecer contraseñas.

---

## 4.9. Caducidad con `chage`

Opciones:

| Opción | Significado |
|---|---|
| `-m` | días mínimos antes de volver a cambiar |
| `-M` | días máximos de validez |
| `-W` | días de aviso |
| `-l` | listar política |

---

## 4.10. Ingeniería y supervisores: 60 días

Forma de la guía:

```bash
for u in nestor victor lucia carlos mary john; do
    sudo chage -m 1 -M 60 -W 7 $u
done
```

Equivalente individual:

```bash
sudo chage -m 1 -M 60 -W 7 nestor
sudo chage -m 1 -M 60 -W 7 victor
sudo chage -m 1 -M 60 -W 7 lucia
sudo chage -m 1 -M 60 -W 7 carlos
sudo chage -m 1 -M 60 -W 7 mary
sudo chage -m 1 -M 60 -W 7 john
```

---

## 4.11. Operarios: 90 días

```bash
for u in alberto oscar hugo; do
    sudo chage -m 1 -M 90 -W 7 $u
done
```

O individualmente:

```bash
sudo chage -m 1 -M 90 -W 7 alberto
sudo chage -m 1 -M 90 -W 7 oscar
sudo chage -m 1 -M 90 -W 7 hugo
```

---

## 4.12. Comprobar `chage`

```bash
sudo chage -l nestor
sudo chage -l carlos
sudo chage -l hugo
```

Esperado:

- Néstor: mínimo 1, máximo 60, aviso 7.
- Carlos: mínimo 1, máximo 60, aviso 7.
- Hugo: mínimo 1, máximo 90, aviso 7.

---

# 5. PRÁCTICA 3 — MFA/TOTP CON GOOGLE AUTHENTICATOR

## 5.1. Objetivo

El acceso SSH protegido por MFA debe pedir:

1. contraseña;
2. código temporal TOTP.

TOTP genera un código de 6 dígitos que cambia aproximadamente cada 30 segundos.

---

## 5.2. Instalar Google Authenticator PAM

```bash
sudo apt install libpam-google-authenticator -y
```

Comprobar:

```bash
which google-authenticator
dpkg -s libpam-google-authenticator | grep Status
```

---

## 5.3. Generar el secreto como el usuario

Ejemplo:

```bash
sudo -iu carlos
google-authenticator
```

La configuración se guarda en:

```text
~/.google_authenticator
```

---

## 5.4. Respuestas del asistente

| Pregunta | Respuesta |
|---|---|
| Tokens time-based | `y` |
| Actualizar `~/.google_authenticator` | `y` |
| Impedir reutilización | `y` |
| Ampliar ventana | `n` |
| Rate limit | `y` |

El asistente muestra:

- QR;
- secreto Base32;
- 5 scratch codes.

### Nunca publicar

- QR;
- clave secreta;
- códigos TOTP;
- scratch codes;
- contraseñas.

Si un secreto se publica en una captura, debe regenerarse.

---

## 5.5. Comprobar fichero

Sin mostrar su contenido:

```bash
ls -l ~/.google_authenticator
```

Volver:

```bash
exit
whoami
```

---

## 5.6. Crear grupo MFA

```bash
getent group mfa || sudo groupadd mfa
sudo usermod -aG mfa carlos
```

Comprobar:

```bash
getent group mfa
id carlos
id linux
```

Carlos debe pertenecer a `mfa` y Linux no.

---

## 5.7. Copias de seguridad

```bash
sudo cp -a /etc/pam.d/sshd /etc/pam.d/sshd.bak-ud3
sudo cp -a /etc/ssh/sshd_config /etc/ssh/sshd_config.bak-ud3
```

La idea importante es hacer una copia antes de modificar la autenticación.

---

## 5.8. Ver configuración SSH inicial

```bash
sudo sshd -T | grep -E 'usepam|kbdinteractiveauthentication|passwordauthentication|authenticationmethods'
```

Antes de MFA se observó algo equivalente a:

```text
usepam yes
passwordauthentication yes
kbdinteractiveauthentication no
authenticationmethods any
```

---

## 5.9. Configurar PAM para el grupo MFA

Abrir:

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

Interpretación:

1. `common-auth` comprueba la autenticación normal.
2. `pam_succeed_if` mira si el usuario no pertenece a `mfa`.
3. Si no pertenece, `success=1` salta la siguiente regla.
4. Si pertenece, se ejecuta `pam_google_authenticator.so`.

Comprobar:

```bash
sudo grep -nE 'common-auth|pam_succeed_if|pam_google_authenticator' /etc/pam.d/sshd
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

En versiones antiguas puede aparecer `ChallengeResponseAuthentication`.

---

## 5.11. Aplicar MFA al grupo

Al final de `sshd_config`:

```text
Match Group mfa
    AuthenticationMethods keyboard-interactive:pam
```

---

## 5.12. Validar antes de reiniciar

```bash
sudo sshd -t
```

Si no muestra nada, la sintaxis es válida.

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

## 5.13. Reiniciar SSH

Mantén abierta la sesión SSH actual.

```bash
sudo sshd -t
sudo systemctl restart ssh
sudo systemctl status ssh --no-pager
```

Debe aparecer:

```text
Active: active (running)
```

Si systemd pide recargar unidades:

```bash
sudo systemctl daemon-reload
```

---

## 5.14. Verificar desde otra terminal

En PowerShell:

```powershell
ssh -o PreferredAuthentications=keyboard-interactive carlos@127.0.0.1 -p 2222
```

Debe pedir:

```text
Password:
Verification code:
```

Con ambos correctos debe entrar como Carlos.

---

## 5.15. Probar código incorrecto

Repite el acceso e introduce un código TOTP incorrecto.

La autenticación debe rechazarse.

Esto demuestra que la contraseña por sí sola no es suficiente.

---

## 5.16. Pérdida del móvil

Los `scratch codes` son códigos de emergencia de un solo uso.

Si el usuario pierde móvil y códigos:

1. el administrador verifica su identidad;
2. invalida/regenera el segundo factor de ese usuario;
3. se genera un nuevo `~/.google_authenticator`;
4. se registra el nuevo dispositivo.

No hay que desactivar MFA para todos.

---

# 6. PRÁCTICA 3 — PRIVILEGIOS JUSTOS CON SUDO

## 6.1. Objetivo

Aplicar mínimo privilegio:

| Grupo | Privilegio |
|---|---|
| `ingenieria` | reiniciar/consultar `hmi_planta` |
| `supervisores` | ejecutar auditoría sin contraseña |
| `operarios` | ningún sudo adicional |

---

## 6.2. Comprobar grupos

```bash
whoami
getent group ingenieria supervisores operarios
```

Esperado:

```text
ingenieria: nestor,victor,lucia
supervisores: carlos,mary,john
operarios: alberto,oscar,hugo
```

---

## 6.3. Comprobar `hmi_planta`

```bash
ls -l /home/hmi_planta
systemctl list-unit-files 'hmi_planta.service'
```

En tu máquina existía `/home/hmi_planta`, pero no una unidad real `hmi_planta.service`.

### Importante

La regla sudo puede ser correcta aunque el servicio no exista.

La autorización se comprueba con:

```bash
sudo -l -U nestor
```

---

## 6.4. Crear script de auditoría

La guía del profesor sí pide este script concreto:

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

Esperado:

```text
-rwxr-xr-x 1 root root ... /usr/local/bin/auditoria_planta.sh
```

Probar:

```bash
/usr/local/bin/auditoria_planta.sh
```

Sin sudo, `whoami` mostrará el usuario que lo ejecuta.

---

## 6.5. Crear reglas sudo

```bash
sudo visudo -f /etc/sudoers.d/torrent
```

### Por qué `visudo`

- valida sintaxis;
- evita dejar sudo roto;
- se recomienda un fichero en `/etc/sudoers.d/`.

---

## 6.6. Reglas utilizadas

```text
%ingenieria ALL=(root) /usr/bin/systemctl restart hmi_planta, /usr/bin/systemctl status hmi_planta
%supervisores ALL=(root) NOPASSWD: /usr/local/bin/auditoria_planta.sh
```

`operarios` no tiene entrada.

### Interpretación

- `%ingenieria`: `%` significa grupo.
- `ALL=(root)`: puede ejecutar esos comandos como root.
- `NOPASSWD`: ese comando concreto no pide contraseña sudo.

---

## 6.7. Validar sudoers

```bash
sudo visudo -c
```

Debe indicar:

```text
parsed OK
```

Ver archivo:

```bash
sudo cat /etc/sudoers.d/torrent
```

---

## 6.8. Error `mode 0440`

En la práctica apareció:

```text
/etc/sudoers.d/torrent: bad permissions, should be mode 0440
```

Corregir:

```bash
sudo chown root:root /etc/sudoers.d/torrent
sudo chmod 440 /etc/sudoers.d/torrent
```

Comprobar:

```bash
ls -l /etc/sudoers.d/torrent
sudo visudo -c
```

Esperado:

```text
-r--r----- 1 root root ... /etc/sudoers.d/torrent
/etc/sudoers.d/torrent: parsed OK
```

---

## 6.9. Ver privilegios de Néstor

```bash
sudo -l -U nestor
```

Debe mostrar:

```text
(root) /usr/bin/systemctl restart hmi_planta, /usr/bin/systemctl status hmi_planta
```

---

## 6.10. Ver privilegios de Carlos

```bash
sudo -l -U carlos
```

Debe mostrar:

```text
(root) NOPASSWD: /usr/local/bin/auditoria_planta.sh
```

---

## 6.11. Ver privilegios de Alberto

```bash
sudo -l -U alberto
```

Debe indicar que no está autorizado a usar sudo.

---

## 6.12. Probar como Alberto

```bash
sudo -iu alberto
whoami
```

Intento:

```bash
sudo systemctl restart hmi_planta
```

Debe denegarse.

En tu práctica también se usó:

```bash
sudo -n systemctl restart hmi_planta
```

`-n` = modo no interactivo; si necesita preguntar una contraseña, falla en lugar de preguntarla.

Volver:

```bash
exit
whoami
```

---

## 6.13. Consultar logs de sudo

Según la distribución:

```bash
sudo journalctl -u sudo | tail -n 20
```

o:

```bash
sudo tail -n 20 /var/log/auth.log
```

En tu Ubuntu se utilizó `auth.log`.

Buscar Alberto:

```bash
sudo grep -a -i 'alberto' /var/log/auth.log | tail -n 15
```

- `-a`: tratar como texto.
- `-i`: ignorar mayúsculas/minúsculas.

---

## 6.14. Probar Carlos con `NOPASSWD`

```bash
sudo -iu carlos
whoami
```

Ejecutar:

```bash
sudo -n /usr/local/bin/auditoria_planta.sh
```

Esperado:

```text
Auditoria de planta ejecutada por root
```

Aparece `root` porque sudo ejecuta el script como root.

Volver:

```bash
exit
whoami
```

---

## 6.15. ACL y sudo no son lo mismo

ACL controla permisos sobre recursos:

```text
leer / escribir / ejecutar / acceder
```

Sudo controla comandos administrativos que se pueden ejecutar con privilegios.

Ejemplo:

- ACL puede permitir a Víctor modificar planos.
- sudo puede permitir a ingeniería administrar únicamente `hmi_planta`.

---

# 7. ERRORES Y SITUACIONES TÍPICAS

## 7.1. `Permission denied`

Revisar:

```bash
ls -l recurso
getfacl recurso
```

Fíjate en:

```text
mask::
#effective:
```

---

## 7.2. ACL dice `rwx` pero no puede escribir

Ejemplo:

```text
user:lucia:rwx
mask::r-x
```

La máscara limita el permiso efectivo a `r-x`.

---

## 7.3. Puede leer un fichero pero no llegar a él

Puede faltar `x` en el directorio padre.

Por eso Alberto y Óscar tenían `--x` sobre `/home/planos`.

---

## 7.4. `-R` vs `-d`

```text
-R = aplicar recursivamente a recursos existentes
-d = ACL por defecto para herencia futura
```

---

## 7.5. Símbolo `+`

```text
drwxrwx---+
```

Indica ACL extendidas.

---

## 7.6. `BAD PASSWORD`

`pam_pwquality` ha rechazado la contraseña.

Puede ser por:

- longitud;
- falta de mayúscula;
- falta de dígito;
- palabra de diccionario;
- repetición;
- etc.

---

## 7.7. Google Authenticator falla

Comprobar:

- secreto/QR actual;
- hora del móvil y servidor;
- existencia de `~/.google_authenticator`;
- reglas PAM;
- configuración SSH;
- pertenencia al grupo `mfa`.

Nunca publiques el secreto para diagnosticarlo.

---

## 7.8. `sshd -t` no muestra nada

Es correcto: la sintaxis es válida.

Si muestra error, no reinicies SSH.

---

## 7.9. Mantener sesión abierta durante MFA

Mientras modificas MFA:

1. mantén una sesión administrativa abierta;
2. prueba la nueva configuración desde otra terminal;
3. solo cierra la primera cuando confirmes que funciona.

---

## 7.10. `bad permissions, should be mode 0440`

```bash
sudo chown root:root /etc/sudoers.d/torrent
sudo chmod 440 /etc/sudoers.d/torrent
sudo visudo -c
```

---

## 7.11. `sudo -n` dice que necesita contraseña

`-n` impide preguntar interactivamente.

Para comprobar de verdad qué puede ejecutar un usuario:

```bash
sudo -l -U usuario
```

---

## 7.12. `hmi_planta.service` no existe

Distingue entre:

- tener una regla sudo válida;
- tener realmente instalado/configurado ese servicio.

La autorización puede verificarse con:

```bash
sudo -l -U nestor
```

---

# 8. CHULETA FINAL DE COMANDOS

## Usuarios y grupos

```bash
sudo groupadd grupo
sudo useradd -m -s /bin/bash usuario
sudo usermod -aG grupo usuario
getent group grupo
id usuario
```

## Propietario y permisos

```bash
sudo chown usuario:grupo recurso
sudo chown -R usuario:grupo directorio
sudo chmod 700 recurso
sudo chmod 600 fichero
sudo chmod 755 script
ls -l recurso
```

## ACL

```bash
dpkg -s acl
getfacl recurso
getfacl -R directorio
sudo setfacl -m u:usuario:rwx recurso
sudo setfacl -m g:grupo:rwx recurso
sudo setfacl -R -m g:grupo:rwx directorio
sudo setfacl -d -m g:grupo:rwx directorio
sudo setfacl -x u:usuario recurso
```

## Copiar ACL

```bash
getfacl origen | sudo setfacl -R --set-file=- destino
```

## Probar otro usuario

```bash
sudo -u usuario comando
sudo -iu usuario
su -l usuario
```

## `test`

```bash
test -r recurso
test -w recurso
test -x recurso
```

## PAM

```bash
sudo apt install libpam-pwquality -y
grep -nE 'pam_pwquality.so|pam_unix.so' /etc/pam.d/common-password
sudo nano /etc/security/pwquality.conf
sudo passwd usuario
sudo passwd -S usuario
```

## `chage`

```bash
sudo chage -m 1 -M 60 -W 7 usuario
sudo chage -m 1 -M 90 -W 7 usuario
sudo chage -l usuario
```

## Google Authenticator

```bash
sudo apt install libpam-google-authenticator -y
sudo -iu carlos
google-authenticator
ls -l ~/.google_authenticator
```

## PAM MFA por grupo

```text
@include common-auth
auth [success=1 default=ignore] pam_succeed_if.so quiet user notingroup mfa
auth required pam_google_authenticator.so
```

## SSH MFA

```text
KbdInteractiveAuthentication yes
UsePAM yes
PasswordAuthentication yes

Match Group mfa
    AuthenticationMethods keyboard-interactive:pam
```

Comprobar:

```bash
sudo sshd -t
sudo sshd -T | grep -E 'usepam|kbdinteractiveauthentication|passwordauthentication|authenticationmethods'
sudo systemctl restart ssh
sudo systemctl status ssh --no-pager
```

## Sudoers

```bash
sudo visudo -f /etc/sudoers.d/torrent
sudo visudo -c
sudo chown root:root /etc/sudoers.d/torrent
sudo chmod 440 /etc/sudoers.d/torrent
```

Reglas:

```text
%ingenieria ALL=(root) /usr/bin/systemctl restart hmi_planta, /usr/bin/systemctl status hmi_planta
%supervisores ALL=(root) NOPASSWD: /usr/local/bin/auditoria_planta.sh
```

Comprobar:

```bash
sudo -l -U nestor
sudo -l -U carlos
sudo -l -U alberto
```

## Logs

```bash
sudo journalctl -u sudo | tail -n 20
sudo tail -n 20 /var/log/auth.log
sudo grep -a -i 'alberto' /var/log/auth.log | tail -n 15
```

---

# 9. PREGUNTAS PRÁCTICAS IMPORTANTES

## ¿Qué diferencia hay entre `chmod` y ACL?

`chmod` gestiona propietario, grupo y resto.

ACL permite añadir permisos específicos para usuarios y grupos concretos.

## ¿Qué diferencia hay entre `setfacl -R` y `setfacl -d`?

- `-R`: recursos existentes.
- `-d`: herencia de recursos nuevos.

## ¿Para qué sirve `getfacl`?

Para consultar ACL, máscara y permisos efectivos.

## ¿Por qué `x` es importante en un directorio?

Permite atravesarlo y acceder a sus contenidos.

## ¿Qué hace la máscara?

Limita permisos efectivos máximos de entradas ACL.

## ¿Qué significa `+` en `ls -l`?

Que existen ACL extendidas.

## ¿Qué diferencia hay entre `pwquality` y `chage`?

- `pwquality`: calidad de una contraseña nueva.
- `chage`: caducidad y avisos.

## ¿Qué hace `enforce_for_root`?

Obliga a root a cumplir también la política de calidad.

## ¿Qué es TOTP?

Código temporal basado en un secreto compartido y la hora.

## ¿Qué son los scratch codes?

Códigos de emergencia de un solo uso.

## ¿Para qué usamos `pam_succeed_if`?

Para aplicar Google Authenticator solo a usuarios del grupo `mfa`.

## ¿Por qué ejecutar `sshd -t`?

Para validar la configuración antes de reiniciar SSH.

## ¿Por qué mantener una sesión SSH abierta?

Para poder corregir la máquina si la nueva autenticación falla.

## ¿Por qué usar `visudo`?

Porque valida la sintaxis de sudoers.

## ¿Qué significa `%ingenieria`?

El `%` indica que la regla es para un grupo.

## ¿Qué significa `NOPASSWD`?

Que el comando concreto se puede ejecutar mediante sudo sin pedir contraseña.

## ¿Por qué operarios no aparece en sudoers?

Porque no necesita privilegios administrativos: principio de mínimo privilegio.

## ¿ACL sustituye a sudo?

No.

ACL controla acceso a archivos y directorios.

Sudo controla ejecución de comandos administrativos.

## ¿Qué riesgo cubre MFA aunque la contraseña sea fuerte?

El uso de una contraseña que haya sido robada o filtrada.

---

# 10. ORDEN PARA RESOLVER UN EJERCICIO DE ACL

1. Identificar usuarios y grupos.
2. Crear grupos.
3. Crear usuarios.
4. Añadir usuarios a grupos.
5. Crear recursos.
6. Establecer propietario/grupo con `chown`.
7. Aplicar permisos base restrictivos con `chmod`.
8. Traducir requisitos a `r`, `w`, `x`.
9. Aplicar ACL con `setfacl -m`.
10. Usar `-R` si se necesita aplicar a recursos existentes.
11. Usar `-d` si se necesita herencia.
12. Comprobar con `getfacl`.
13. Revisar `mask`.
14. Probar usuarios autorizados.
15. Probar operaciones que deben fallar.

---

# 11. ORDEN PARA PAM + MFA + SUDO

## PAM

1. Instalar `libpam-pwquality`.
2. Comprobar `pam_pwquality.so`.
3. Configurar `pwquality.conf`.
4. Probar contraseña débil.
5. Probar contraseña válida.
6. Configurar `chage`.
7. Comprobar con `chage -l`.

## MFA

1. Instalar `libpam-google-authenticator`.
2. Ejecutar `google-authenticator` como el usuario.
3. Guardar QR/secreto/scratch codes de forma privada.
4. Crear grupo `mfa`.
5. Configurar PAM.
6. Configurar SSH.
7. Ejecutar `sshd -t`.
8. Mantener una sesión abierta.
9. Reiniciar SSH.
10. Probar desde otra terminal.
11. Probar código correcto.
12. Probar código incorrecto.

## Sudo

1. Decidir exactamente qué comando necesita cada grupo.
2. Crear reglas con `visudo`.
3. Corregir permisos `0440` si es necesario.
4. Ejecutar `visudo -c`.
5. Ver permisos con `sudo -l -U`.
6. Probar usuario autorizado.
7. Probar usuario no autorizado.
8. Consultar logs.

---

# 12. PREGUNTAS DE REFLEXIÓN DE LAS GUÍAS, RESUMIDAS

## ACL 1 — ¿Por qué no bastan los permisos clásicos?

Porque solo permiten diferenciar propietario, un grupo y resto. Las ACL permiten permisos distintos para usuarios y grupos concretos.

## ACL 2 — ¿Qué hace la máscara?

Limita los permisos efectivos máximos de las entradas ACL.

## ACL 3 — Acceso vs por defecto

- ACL de acceso: afecta al recurso actual.
- ACL por defecto: se hereda en recursos nuevos.

## ACL 4 — Nuevo operario

Crear usuario, añadirlo a `operarios` y comprobar con `id`. Si la regla está sobre el grupo, hereda esos permisos.

## MFA 1 — ¿Qué cubre MFA?

Protege frente al uso de contraseñas robadas o filtradas porque exige un segundo factor.

## MFA 2 — Caducidad muy frecuente

Puede provocar contraseñas previsibles, anotadas o reutilizadas.

## MFA 3 — ¿Por qué hacen falta ACL y sudo?

ACL controla acceso a archivos; sudo controla comandos administrativos.

## MFA 4 — Pérdida del móvil

Verificar identidad, invalidar el segundo factor antiguo y registrar uno nuevo solo para ese usuario.

---

# FIN

Para el examen no memorices solo los comandos: intenta saber **qué problema resuelve cada uno, qué resultado esperas y cómo comprobar que ha funcionado**.
