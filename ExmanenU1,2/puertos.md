# UD2 · Guía de examen: cerrar puertos de Metasploitable permanentemente

> **Alcance:** material de consulta para el laboratorio de Metasploitable. Basado en `U2/lab_fases_1_2.md` y en el documento del profesor **«Tumbar un servicio: la receta»**. Los PID, direcciones IP y resultados son ejemplos de esa práctica; compruébalos en la máquina del examen. **No se trata de bloquear un puerto con un cortafuegos, sino de detener su servicio y evitar su arranque al reiniciar.**

## 0. Objetivo y precauciones

En la práctica debían permanecer accesibles **22/tcp (SSH)** y **80/tcp (HTTP)**. El resto de servicios innecesarios debían desactivarse. Trabaja solo sobre las máquinas de la práctica, crea una **instantánea de VirtualBox** antes de modificar nada y conserva el acceso de consola por si se interrumpe SSH. No detengas `xinetd` entero: podría gestionar varios servicios.

**Regla que debes memorizar:** **IDENTIFICA → AVERIGUA → PARA → IMPIDE QUE ARRANQUE → COMPRUEBA**, primero desde dentro, luego desde fuera y finalmente **tras reiniciar**.

## 1. Cómo descubrir puertos abiertos

### Paso 1. Identifica la IP de Metasploitable

En **Metasploitable**:

```bash
ip a
```

En nuestra práctica, la interfaz `eth1` tenía `192.168.56.2/24` y Torrent-Auditor usaba la red `192.168.56.0/24`. Si cambia la IP, usa la nueva en los comandos siguientes.

### Paso 2. Escanea desde Torrent-Auditor

```bash
sudo nmap -sV -Pn 192.168.56.2
sudo nmap -sV -Pn 192.168.56.2 -oN antes.txt
```

- `-sV`: intenta identificar el servicio y su versión.
- `-Pn`: omite el descubrimiento previo de hosts; **no** evita los filtros de puertos.
- `-oN antes.txt`: guarda un informe normal para compararlo al final.

Interpreta el estado:

| Nmap | Significado |
|---|---|
| `open` | Hay un servicio accesible que acepta conexiones. |
| `closed` | El puerto es alcanzable, pero no hay servicio aceptando conexiones. |
| `filtered` | Algún filtro impide determinar su estado con certeza. No demuestra que el servicio se haya detenido. |

### Paso 3. Consulta el dueño del puerto en Metasploitable

```bash
sudo netstat -tulpn
```

| Letra | Significado |
|---|---|
| `-t` | TCP |
| `-u` | UDP |
| `-l` | Solo puertos a la escucha |
| `-p` | PID y nombre del proceso |
| `-n` | Números, sin resolver nombres |

Para filtrar un solo puerto:

```bash
sudo netstat -tulpn | grep ':25 '
```

Ejemplos **registrados en la práctica**: `21` pertenecía a `xinetd`, `25` a `master` (Postfix), `513` a `xinetd` y `1099` a `rmiregistry`. No elijas el método de cierre solo por el número del puerto: **mira siempre el proceso propietario y la configuración de arranque**.

## 2. Caso A — Servicio permanente: Postfix (25/tcp)

**Qué es:** SMTP, correo. En la práctica el servicio estaba gestionado por sus propios scripts de arranque (SysV init).

### 1) Identificar

En Metasploitable:

```bash
sudo netstat -tulpn | grep ':25 '
```

Debe aparecer un proceso `master` asociado a Postfix si está activo. En Torrent-Auditor:

```bash
sudo nmap -sV -Pn -p 25 192.168.56.2
```

### 2) Averiguar cómo arranca

```bash
ls /etc/init.d/ | grep -i postfix
ls /etc/rc2.d/ | grep -i postfix
```

La práctica registra el script `postfix` y un enlace de arranque como `S20postfix`. `init.d` contiene el script de gestión y `rc2.d` los enlaces del nivel de ejecución 2.

### 3) Parar ahora

```bash
sudo /etc/init.d/postfix stop
sudo netstat -tulpn | grep ':25 '
```

**Esperado:** el segundo comando no muestra un proceso escuchando. Eso **solo** demuestra que ahora está parado.

### 4) Impedir su inicio al reiniciar

```bash
sudo update-rc.d -f postfix remove
ls /etc/rc2.d/ | grep -i postfix
```

En el Ubuntu antiguo de la práctica, `update-rc.d -f postfix remove` elimina los enlaces de inicio del servicio en SysV. Comprueba que ya no aparece el enlace `S20postfix`; si existe otro mecanismo de arranque, habrá que investigarlo.

### 5) Comprobar fuera y después de reiniciar

En Torrent-Auditor:

```bash
sudo nmap -sV -Pn -p 25 192.168.56.2
```

Resultado esperado, si no hay otro filtrado: `25/tcp closed smtp`.

Reinicia **Metasploitable**:

```bash
sudo reboot
```

Tras arrancar, comprueba en Metasploitable y Torrent-Auditor:

```bash
sudo netstat -tulpn | grep ':25 '
# Este segundo comando, en Torrent-Auditor:
sudo nmap -Pn -p 25 192.168.56.2
```

**Idea de examen:** `stop` lo para **ahora**; `update-rc.d ... remove` evita el arranque normal mediante esos enlaces; reiniciar y comprobar demuestra el resultado.

## 3. Caso B — Servicio a demanda con xinetd: vsftpd (21/tcp)

**Qué es:** servidor FTP. La práctica registra que el puerto `21` lo escuchaba `4490/xinetd`, aunque Nmap identificaba `vsftpd 2.3.4`. Por eso detener otro FTP, como ProFTPD, **no** cerraba el 21.

### 1) Identificar

En Metasploitable:

```bash
sudo netstat -tulpn | grep ':21 '
```

En Torrent-Auditor:

```bash
sudo nmap -sV -Pn -p 21 192.168.56.2
```

### 2) Averiguar quién lo configura

```bash
ls /etc/xinetd.d/
cat /etc/xinetd.d/vsftpd
grep -n 'disable' /etc/xinetd.d/vsftpd
```

La práctica encontró un archivo `vsftpd` en `/etc/xinetd.d/`. Si indica `disable = no`, está habilitado.

### 3) Hacer una copia antes de modificar

```bash
sudo cp /etc/xinetd.d/vsftpd /root/vsftpd.xinetd.bak
```

### 4) Desactivar el servicio de forma persistente

```bash
sudo nano /etc/xinetd.d/vsftpd
```

Cambia **dentro de la sección del servicio**:

```text
disable = no
```

por:

```text
disable = yes
```

Guardar en Nano: **Ctrl+O**, Enter; salir: **Ctrl+X**. Si la opción no existe, consúltalo antes de añadirla en el bloque correcto.

### 5) Aplicar el cambio, sin parar todo xinetd

```bash
sudo /etc/init.d/xinetd reload
```

Si el puerto no cambia, revisa el archivo y, si procede, utiliza `restart` del gestor sabiendo que puede afectar momentáneamente a otros servicios:

```bash
sudo /etc/init.d/xinetd restart
```

### 6) Comprobar ahora y tras reiniciar

```bash
sudo netstat -tulpn | grep ':21 '
# Desde Torrent-Auditor:
sudo nmap -sV -Pn -p 21 192.168.56.2
```

Resultado esperado, sin filtrado adicional: `21/tcp closed ftp`. Reinicia Metasploitable y vuelve a ejecutar ambos comandos; verifica también que sigue configurado `disable = yes`.

**Idea de examen:** cuando el dueño del puerto es `xinetd`, no mates el gestor. Deshabilita **solo la definición del servicio** y recarga el gestor.

## 4. Caso C — Definición en inetd.conf: rlogin (513/tcp)

**Qué es:** acceso remoto antiguo. En la práctica lo gestionaba `xinetd`, pero el servicio `login` estaba definido en `/etc/inetd.conf`. El documento del profesor presenta esta forma de desactivación.

### 1) Identificar

```bash
sudo netstat -tulpn | grep ':513 '
# Desde Torrent-Auditor:
sudo nmap -sV -Pn -p 513 192.168.56.2
```

### 2) Buscar su definición

```bash
sudo grep -n -w login /etc/inetd.conf /etc/xinetd.conf /etc/xinetd.d/* 2>/dev/null
```

Confirma que la línea corresponde **al servicio `login` del puerto 513**, no a cualquier aparición de la palabra.

### 3) Respaldar la configuración

```bash
sudo cp /etc/inetd.conf /root/inetd.conf.bak
```

### 4) Comentar solo la entrada correspondiente

```bash
sudo nano /etc/inetd.conf
```

Antes (ejemplo esquemático):

```text
login stream tcp nowait root ...
```

Después:

```text
#login stream tcp nowait root ...
```

Guardar: **Ctrl+O**, Enter; salir: **Ctrl+X**.

En el repositorio también aparece esta alternativa automática, **solo si la línea comienza exactamente por `login`**:

```bash
sudo sed -i 's/^login/#login/' /etc/inetd.conf
```

### 5) Recargar el gestor que realmente escucha

En nuestra práctica era xinetd:

```bash
sudo /etc/init.d/xinetd reload
```

En otras máquinas puede gestionar esta configuración `inetd`, y habrá que recargar ese servicio en lugar de asumir que siempre es xinetd.

### 6) Comprobar ahora y después del reinicio

```bash
sudo netstat -tulpn | grep ':513 '
# Desde Torrent-Auditor:
sudo nmap -Pn -p 513 192.168.56.2
```

Si no hay filtrado adicional debería aparecer `513/tcp closed login`. Reinicia Metasploitable y repite ambas pruebas; verifica que la línea permanece comentada.

**Idea de examen:** servicio definido en `inetd.conf` → comentar su entrada → recargar **el gestor real** → comprobar antes y después de reiniciar.

## 5. Caso D — Proceso independiente: rmiregistry (1099/tcp)

**Qué es:** Java RMI Registry. La práctica muestra `4626/rmiregistry`, pero **no documenta un origen de arranque definitivo**. En este caso no existe un único comando de desactivación permanente confirmado por el material.

### 1) Identificar

```bash
sudo netstat -tulpn | grep ':1099 '
ps aux | grep '[r]miregistry'
```

Anota el **PID real del momento**; no reutilices `4626` sin comprobarlo.

### 2) Investigar el arranque

```bash
ls /etc/init.d/
cat /etc/rc.local
crontab -l
sudo crontab -l
sudo grep -Ri 'rmiregistry' /etc/init.d/ /etc/rc.local /etc/cron* 2>/dev/null
```

**Qué buscamos:** un script de inicio, una línea en `rc.local`, una tarea `@reboot` o alguna aplicación que inicie `rmiregistry`.

### 3) Detener el PID comprobado

Si el PID **actual** fuera `4626`:

```bash
sudo kill 4626
sudo netstat -tulpn | grep ':1099 '
```

`kill` solicita terminar el proceso. No asegura que permanezca detenido después de reiniciar.

### 4) Desactivar el origen encontrado

| Origen confirmado | Acción necesaria |
|---|---|
| Script SysV en `/etc/init.d/` | Parar y quitar enlaces de arranque del **servicio concreto** |
| Línea en `/etc/rc.local` | Hacer copia del archivo y comentar solo la orden responsable |
| `crontab` de usuario o de root | Eliminar solo la tarea que lanza el proceso |
| Otra aplicación | Localizar su configuración y desactivar ahí el lanzamiento |

**No inventes una orden de desactivación si aún no sabes cómo se inicia.** En esta parte tu repositorio documenta una investigación, no una solución permanente verificada.

### 5) Comprobar después de reiniciar

```bash
sudo reboot
# En Metasploitable, tras arrancar:
sudo netstat -tulpn | grep ':1099 '
# En Torrent-Auditor:
sudo nmap -Pn -p 1099 192.168.56.2
```

Si vuelve a aparecer, queda otro mecanismo de arranque por localizar.

## 6. No confundir cierre de servicio con firewall

En otra parte de la práctica configuramos `iptables` para permitir los puertos 22 y 80 y **filtrar** el resto. Nmap terminó mostrando 2 puertos `open` y 998 `filtered`.

- **Cerrar el servicio:** dejar de escuchar en el puerto y eliminar/deshabilitar el mecanismo que lo inicia.
- **Filtrar el tráfico con `iptables`:** impedir conexiones de red, aunque el programa pueda seguir ejecutándose.
- Una regla de `iptables` creada en ejecución **puede perderse al reiniciar si no se guarda**. No sustituye los pasos de deshabilitar el servicio.

Para la práctica del profesor, comprueba dentro con `netstat`, fuera con `nmap` **y tras reiniciar**. Si el puerto aparece `filtered`, investiga las reglas de firewall antes de afirmar que el servicio fue detenido.

## 7. Chuleta final para localizar rápido en el examen

| Caso | Cómo identificar | Cómo parar ahora | Cómo evitar su arranque | Qué comprobar |
|---|---|---|---|---|
| **Permanente (25, Postfix)** | `netstat` → `master`; `init.d` / `rc2.d` | `/etc/init.d/postfix stop` | `update-rc.d -f postfix remove` | `netstat`, `nmap`, reinicio |
| **xinetd (21, vsftpd)** | `netstat` → `xinetd`; `/etc/xinetd.d/vsftpd` | Poner `disable = yes` y recargar | La configuración guardada persiste | `netstat`, `nmap`, reinicio |
| **inetd.conf (513, login)** | Buscar línea de `login` en `inetd.conf` | Comentar la línea y recargar gestor | La línea comentada persiste | `netstat`, `nmap`, reinicio |
| **Proceso independiente (1099)** | `netstat`, `ps`; buscar autoarranque | `kill` al PID actual | Depende de si arranca por init, cron, rc.local u otro | `netstat`, `nmap`, reinicio |

### Pregunta de examen resuelta: cerrar el puerto 25 y evitar que se reabra

1. `sudo netstat -tulpn | grep ':25 '` → localizo el dueño (`master`, Postfix).
2. `ls /etc/init.d/ | grep postfix` y `ls /etc/rc2.d/ | grep postfix` → averiguo cómo se inicia.
3. `sudo /etc/init.d/postfix stop` → lo detengo ahora.
4. `sudo update-rc.d -f postfix remove` → elimino los enlaces de arranque SysV.
5. `sudo netstat -tulpn | grep ':25 '` y desde Torrent-Auditor `sudo nmap -Pn -p 25 192.168.56.2` → compruebo el cierre.
6. Reinicio Metasploitable y repito ambas comprobaciones → verifico que sigue cerrado.

**Frase clave:** «Cerrar un puerto de forma permanente exige identificar su servicio, detenerlo, deshabilitar su mecanismo de arranque y comprobar que no reaparece después del reinicio».

---

**Fuentes de clase:** repositorio `U2/lab_fases_1_2.md` y PDF `UD02_tumbar_un_servicio_paso_a_paso(1).pdf`. Los apartados en los que se dice *investigar*, *si existe* o *según el gestor* señalan pasos de diagnóstico cuya solución concreta depende de la configuración de cada máquina.
