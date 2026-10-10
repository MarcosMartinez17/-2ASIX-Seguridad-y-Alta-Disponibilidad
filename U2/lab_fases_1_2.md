# Laboratorio fases 1 y 2 — Estudio Torrent

## A1. whois y nslookup
No es necesario

## B. Antes y después
(inventario de servicios, contramedidas aplicadas, la tabla antes/después)

 ## B2. Medición ANTES
LOGIN PUTTY: 192.168.56.2 / vt100
sudo nmap -sV 192.168.1.101 -oN antes.txt

Puertos abiertos: Hay un total de 23 puertos abiertos (977 puertos cerrados).   
Servicios y versiones destacados:21/tcp (FTP): 
vsftpd 2.3.4   
22/tcp (SSH): OpenSSH 4.7p1   
53/tcp (DNS): ISC BIND 9.4.2   
80/tcp (HTTP): Apache httpd 2.2.8  
139/tcp y 445/tcp (NetBIOS/SMB): 
Samba smbd 3.X - 4.X   
3306/tcp (MySQL) 
5432/tcp (PostgreSQL 8.3.0 - 8.3.7), 
8180/tcp (Apache Tomcat)  
Otros servicios: Telnet, SMTP, RPC, NFS, VNC, IRC  
Respuesta del ping: Si responde, el host está activo  

## B3. Contramedidas (en Torrent-Vulnerable, con sudo)
Contramedida 1 — Inventariar y apagar servicios innecesarios
Listado de puertos y servicios en escucha

| Protocolo | Dirección Local | Puerto | Estado | PID / Programa |
| :--- | :--- | :--- | :--- | :--- |
| **TCP** | 0.0.0.0 | 21 | LISTEN | 4490/xinetd (ftp) |
| **TCP** | 0.0.0.0 | 22 | LISTEN | 4113/sshd |
| **TCP6**| ::: | 22 | LISTEN | 4113/sshd |
| **TCP** | 0.0.0.0 | 23 | LISTEN | 4490/xinetd (telnet) |
| **TCP** | 0.0.0.0 | 25 | LISTEN | 4465/master (smtp) |
| **TCP** | Multi-IP | 53 | LISTEN | 4091/named (DNS) |
| **TCP6**| ::: | 53 | LISTEN | 4091/named (DNS) |
| **TCP** | 0.0.0.0 | 80 | LISTEN | 4607/apache2 |
| **TCP** | 0.0.0.0 | 111 | LISTEN | 3718/portmap |
| **TCP** | 0.0.0.0 | 139 | LISTEN | 4474/smbd |
| **TCP** | 0.0.0.0 | 445 | LISTEN | 4474/smbd |
| **TCP** | 0.0.0.0 | 512 | LISTEN | 4490/xinetd (exec) |
| **TCP** | 0.0.0.0 | 513 | LISTEN | 4490/xinetd (login) |
| **TCP** | 0.0.0.0 | 514 | LISTEN | 4490/xinetd (shell) |
| **TCP** | 0.0.0.0 | 1099 | LISTEN | 4626/rmiregistry |
| **TCP** | 0.0.0.0 | 1524 | LISTEN | 4490/xinetd (bindshell) |
| **TCP** | 0.0.0.0 | 2049 | LISTEN | - (nfs) |
| **TCP6**| ::: | 2121 | LISTEN | 4533/proftpd |
| **TCP** | 0.0.0.0 | 3306 | LISTEN | 4231/mysqld |
| **TCP6**| ::: | 3632 | LISTEN | 4336/distccd |
| **TCP6**| ::: | 5432 | LISTEN | 4310/postgres |
| **TCP** | 0.0.0.0 | 5900 | LISTEN | 4646/Xtightvnc |
| **TCP** | 0.0.0.0 | 6000 | LISTEN | 4646/Xtightvnc |
| **TCP** | 0.0.0.0 | 6667 | LISTEN | 4645/unrealircd |
| **TCP** | 0.0.0.0 | 6697 | LISTEN | 4645/unrealircd |
| **TCP** | 0.0.0.0 | 8009 | LISTEN | 4589/jsvc (ajp13) |
| **TCP** | 0.0.0.0 | 8180 | LISTEN | 4589/jsvc (Tomcat) |
| **TCP** | 0.0.0.0 | 8787 | LISTEN | 4630/ruby |
| **UDP** | 0.0.0.0 | 69 | LISTEN | 4490/xinetd (tftp) |
| **UDP** | 0.0.0.0 | 111 | LISTEN | 3718/portmap |
| **UDP** | Multi-IP | 137, 138 | LISTEN | 4472/nmbd (NetBIOS) |
| **UDP** | Multi-IP | 53 | LISTEN | 4091/named (DNS) |

Contramedida 1 — Inventariar y apagar servicios innecesarios
solo tengo que dejar el puerto 80 y el 22, los demas tengo que cerrarlos, 
pero tengo que saber donde esta el servicio, saber si se arranca cuando se inicia el sistema, 
si es el xinte o el inet y ir cerrando para que no se vuelva a abrir

sudo netstat -tulpn | grep ':21'
sudo nmap -sV -p 21 <IP_DE_LA_MAQUINA>
 El comando netstat muestra que el puerto 21 está a la escucha (LISTEN), pero su dueño no es un programa de FTP propio, sino el portero 4490/xinetd (es un servicio a demanda). Nmap detecta el servicio como ftp (vsftpd).   

## Puerto 21, 23, 25, 53, 111, 139 y 445, 512, 513 y 514, 1099, 1524, 2049, 2121, 3306, 3632, 5432, 5900 y 6000, 6667 y 6697, 8009 y 8180, 8787,  69, 137 y 138.


## Puerto 21
1. IDENTIFICA

msfadmin@metasploitable:~$ sudo netstat -tulpn | grep ':21'
msfadmin@metasploitable:~$ sudo nmap -sV -p 21 192.168.1.101

 El comando netstat muestra que el puerto 21 está a la escucha (LISTEN), pero su dueño no es un programa de FTP propio, sino el portero 4490/xinetd (es un servicio a demanda). Nmap detecta el servicio como ftp (vsftpd).   

Paso 2: AVERIGUA
msfadmin@metasploitable:~$ ls /etc/init.d/ | grep -i ftp
proftpd
msfadmin@metasploitable:~$ ls /etc/rc2.d/ | grep -i ftp
S50proftpd
msfadmin@metasploitable:~$ grep -i servertype /etc/proftpd/proftpd.conf
ServerType                      standalone

Paso 3: Pararlo
msfadmin@metasploitable:~$ sudo /etc/init.d/proftpd stop
 * Stopping ftp server proftpd
   ...done.
msfadmin@metasploitable:~$ sudo netstat -tulpn | grep 21
tcp        0      0 0.0.0.0:21              0.0.0.0:*               LISTEN      4490/xinetd

el resultado de tu netstat aunque has parado el servicio proftpd, el puerto 21 sigue abierto porque lo tiene el portero 4490/xinetd

Paso 4: Impedir que vuelva:
msfadmin@metasploitable:~$ ls /etc/xinetd.d/
chargen  daytime  discard  echo  time  vsftpd
msfadmin@metasploitable:~$ sudo sed -i 's/disable.*/disable = yes/' /etc/xinetd.d/vsftpd
sudo /etc/init.d/xinetd reload

Paso 5: comprobar
msfadmin@metasploitable:~$ sudo nmap -sV -p 21 192.168.1.101
Starting Nmap 4.53 ( http://insecure.org ) at 2026-10-05 14:27 EDT
Interesting ports on 192.168.1.101:
PORT   STATE  SERVICE VERSION
21/tcp closed ftp


## Puero 25 
1- Identifica
msfadmin@metasploitable:~$ sudo netstat -tulpn | grep ':25'
tcp        0      0 0.0.0.0:25              0.0.0.0:*               LISTEN      4530/master  
msfadmin@metasploitable:~$ sudo nmap -p 25 192.168.1.101
Starting Nmap 4.53 ( http://insecure.org ) at 2026-10-06 12:36 EDT
Interesting ports on 192.168.1.101:
PORT   STATE SERVICE
25/tcp open  smtp

2- Averigua
msfadmin@metasploitable:~$ ls /etc/init.d/ | grep -i postfix
postfix
msfadmin@metasploitable:~$ ls /etc/rc2.d/ | grep -i postfix
S20postfix

3- Pararlo
msfadmin@metasploitable:~$ sudo /etc/init.d/postfix stop
 * Stopping Postfix Mail Transport Agent postfix
   ...done.
msfadmin@metasploitable:~$ sudo netstat -tulpn | grep ':25'

4- impedir
msfadmin@metasploitable:~$ sudo update-rc.d -f postfix remove
 Removing any system startup links for /etc/init.d/postfix ...
   /etc/rc0.d/K20postfix
   /etc/rc1.d/K20postfix
   /etc/rc2.d/S20postfix
   /etc/rc3.d/S20postfix
   /etc/rc4.d/S20postfix
   /etc/rc5.d/S20postfix
   /etc/rc6.d/K20postfix
msfadmin@metasploitable:~$ ls /etc/rc2.d/ | grep -i postfix

5- Verificar 
msfadmin@metasploitable:~$ sudo nmap -p 25 192.168.1.101
Starting Nmap 4.53 ( http://insecure.org ) at 2026-10-06 12:40 EDT
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 0 undergoing Host Discovery
Parallel DNS resolution of 1 host. Timing: About 0.00% done

## Puerto 53

1- Identifica
msfadmin@metasploitable:~$ sudo netstat -tulpn | grep ':53'
tcp        0      0 192.168.56.2:53         0.0.0.0:*               LISTEN      4156/named   
tcp        0      0 192.168.1.101:53        0.0.0.0:*               LISTEN      4156/named   
tcp        0      0 127.0.0.1:53            0.0.0.0:*               LISTEN      4156/named   
tcp6       0      0 :::53                   :::*                    LISTEN      4156/named   
udp        0      0 192.168.56.2:53         0.0.0.0:*                           4156/named   
udp        0      0 192.168.1.101:53        0.0.0.0:*                           4156/named   
udp        0      0 127.0.0.1:53            0.0.0.0:*                           4156/named   
udp6       0      0 :::53                   :::*                                4156/named   
msfadmin@metasploitable:~$ sudo nmap -sV -p 53 192.168.1.101

Starting Nmap 4.53 ( http://insecure.org ) at 2026-10-06 12:42 EDT
Interesting ports on 192.168.1.101:
PORT   STATE SERVICE VERSION
53/tcp open  domain

2- Averigua
msfadmin@metasploitable:~$ ls /etc/init.d/ | grep -i bind
bind9
msfadmin@metasploitable:~$ ls /etc/rc2.d/ | grep -i bind
S15bind9

3- Pararlo
msfadmin@metasploitable:~$ sudo /etc/init.d/bind9 stop
 * Stopping domain name service... bind
   ...done.
msfadmin@metasploitable:~$ sudo netstat -tulpn | grep ':53'

4- impedir
msfadmin@metasploitable:~$ sudo update-rc.d -f bind9 remove
 Removing any system startup links for /etc/init.d/bind9 ...
   /etc/rc0.d/K85bind9
   /etc/rc1.d/K85bind9
   /etc/rc2.d/S15bind9
   /etc/rc3.d/S15bind9
   /etc/rc4.d/S15bind9
   /etc/rc5.d/S15bind9
   /etc/rc6.d/K85bind9
msfadmin@metasploitable:~$ ls /etc/rc2.d/ | grep -i bind

5- Verificar 
msfadmin@metasploitable:~$ sudo nmap -sV -p 53 192.168.1.101

Starting Nmap 4.53 ( http://insecure.org ) at 2026-10-06 12:45 EDT
Interesting ports on 192.168.1.101:
PORT   STATE  SERVICE VERSION
53/tcp closed domain

Service detection performed. Please report any incorrect results at http://insecure.org/nmap/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.126 seconds
msfadmin@metasploitable:~$ sudo reboot

## Puerto 111

1- Identifica
sudo netstat -tulpn | grep ':111'
<img width="942" height="150" alt="image" src="https://github.com/user-attachments/assets/b23cfcb1-abc6-46fb-8de7-64a574f513d6" />

sudo nmap -sV -p 111 192.168.1.101
<img width="436" height="52" alt="image" src="https://github.com/user-attachments/assets/846aaf81-831d-40fc-9085-eb4e67f19db4" />

2- Averigua
ls /etc/init.d/ | grep -i portmap
ls /etc/rc2.d/ | grep -i portmap
<img width="678" height="120" alt="image" src="https://github.com/user-attachments/assets/72b4fe4f-4d64-4a02-9ff1-8108980655ad" />

3- Pararlo
sudo /etc/init.d/portmap stop
<img width="648" height="58" alt="image" src="https://github.com/user-attachments/assets/62f4a346-4067-4007-ae69-6e711a8b7f05" />
ya no aparece

4- impedir
sudo update-rc.d -f portmap remove
ls /etc/rc2.d/ | grep -i portmap
<img width="675" height="242" alt="image" src="https://github.com/user-attachments/assets/86065464-de3c-40b1-ad31-867b7e952dae" />

5- Verificar 
<img width="726" height="127" alt="image" src="https://github.com/user-attachments/assets/92fb8a23-25ea-4aa9-ae59-5d2555a93f02" />
sudo reboot
## Puerto 139 y 445 

1- Identifica
sudo netstat -tulpn | grep -E ':139|:445'
<img width="927" height="93" alt="image" src="https://github.com/user-attachments/assets/68fddf24-6f69-4b67-a5c9-e683dac55b5e" />
2- Averigua
ls /etc/init.d/ | grep -i samba
ls /etc/rc2.d/ | grep -i samba
<img width="647" height="88" alt="image" src="https://github.com/user-attachments/assets/80a44b9c-d1fe-42ad-b9b4-fb9807f454ff" />

3- Pararlo
sudo /etc/init.d/samba stop
sudo netstat -tulpn | grep -E ':139|:445'
<img width="725" height="70" alt="image" src="https://github.com/user-attachments/assets/be05478a-fa7c-43e5-a614-4eae378d05ae" />

4- impedir
sudo update-rc.d -f samba remove
ls /etc/rc2.d/ | grep -i samba
<img width="643" height="202" alt="image" src="https://github.com/user-attachments/assets/eb390589-f873-4cee-88b4-8cb5fe5c188f" />

5- Verificar 
sudo netstat -tulpn | grep -E ':139|:445'
sudo nmap -sV -p 139,445 192.168.1.101
sudo reboot
<img width="743" height="167" alt="image" src="https://github.com/user-attachments/assets/200929ef-e4fe-4382-89b8-0da6d5c78c6d" />

## Puerto 512 513 514
1- Identifica
sudo netstat -tulpn | grep -E ':512|:513|:514'
<img width="962" height="141" alt="image" src="https://github.com/user-attachments/assets/c8032586-278c-417d-83f7-4e92fe9c3779" />
sudo nmap -sV -p 512,513,514 192.168.1.101
<img width="776" height="210" alt="image" src="https://github.com/user-attachments/assets/4aba8a98-9c4e-4c14-9779-fdfd5669a231" />
2- Averigua
ls -l /etc/init.d/xinetd
cat /etc/xinetd.conf
<img width="812" height="342" alt="image" src="https://github.com/user-attachments/assets/052420c3-7390-4c15-ae76-eeb800293fab" />
3- Pararlo
sudo nano /etc/xinetd.conf
Busca las secciones de exec, login y shell, y pon disable = yes
<img width="973" height="488" alt="image" src="https://github.com/user-attachments/assets/7b413afd-e668-4727-84ea-a4b49d2437be" />

<img width="590" height="242" alt="image" src="https://github.com/user-attachments/assets/9ef8cd1e-1760-42a3-9046-960b4af348eb" />
sudo grep -E 'exec|login|shell' /etc/inetd.conf /etc/xinetd.conf 2>/dev/null
<img width="951" height="190" alt="image" src="https://github.com/user-attachments/assets/abd356df-b28d-461f-8263-b8160d88ea3a" />

Encontramos su ubicacion real:
sudo sed -i 's/^shell/#shell/' /etc/inetd.conf
sudo sed -i 's/^login/#login/' /etc/inetd.conf
sudo sed -i 's/^exec/#exec/' /etc/inetd.conf


4- impedir
<img width="961" height="388" alt="image" src="https://github.com/user-attachments/assets/4af47ca5-bcce-46a1-a60c-6f893a9ffcb8" />

5- Verificar 
<img width="767" height="65" alt="image" src="https://github.com/user-attachments/assets/2e9ab9fb-f47c-41c5-9c2a-f6da685c42de" />


## Puertos 1099 (rmiregistry) y 1524 (bindshell)
1- Identifica
sudo netstat -tulpn | grep -E ':1099|:1524'
sudo nmap -sV -p 1099,1524 192.168.1.101
<img width="943" height="243" alt="image" src="https://github.com/user-attachments/assets/40b38013-0923-4778-9356-34c36229d005" />

2- Averigua

3- Pararlo

4- impedir
sudo grep 1524 /etc/inetd.conf /etc/xinetd.conf 2>/dev/null
ls /etc/rc2.d/ | grep -i metasploit

para este servicio, el puerto 1099 (RMI Registry) suele levantarse asociado a alguna aplicación de Java o de forma manual.
Para matarlo directamente y comprobar que desaparece
sudo kill -9 4600
sudo netstat -tulpn | grep ':1099'
<img width="737" height="71" alt="image" src="https://github.com/user-attachments/assets/7045fbce-5e42-43f4-a34b-52dc5ab4ad99" />


5- Verificar 
sudo netstat -tulpn | grep -E ':1099|:1524'
sudo nmap -sV -p 1099,1524 192.168.1.101
<img width="747" height="178" alt="image" src="https://github.com/user-attachments/assets/6553ea63-1495-47d6-909d-b8563cc1683f" />

<img width="966" height="212" alt="image" src="https://github.com/user-attachments/assets/74b0df4f-0582-4153-8492-fc05693c5f72" />


 ## Contramedida 2 — Bloquear el ping (obligatoria
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
Este comando bloquea las solicitudes de ping que llegan a Metasploitable.

2. Comprobar que se ha aplicado
<img width="970" height="152" alt="image" src="https://github.com/user-attachments/assets/fa3ade65-3fed-40d9-a5f4-68bc8b42d04a" />

tORRENT-AUDITOR
<img width="735" height="256" alt="image" src="https://github.com/user-attachments/assets/55c7374d-1ff9-447b-9fb0-a02ffe739dc2" />

<img width="775" height="162" alt="image" src="https://github.com/user-attachments/assets/6776e26e-7d1c-45f6-abef-6fe29fce6942" />


## B4. Medición DESPUÉS

sudo nmap -sV -Pn 192.168.56.2 -oN despues.txt
He añadido -Pn porque hemos bloqueado el ping. Así Nmap intentará escanear los puertos aunque Metasploitable no responda al descubrimiento de hosts.

<img width="842" height="368" alt="image" src="https://github.com/user-attachments/assets/6e519600-4e78-4e67-a923-07429a2f348b" />

diff
<img width="890" height="817" alt="image" src="https://github.com/user-attachments/assets/827e9307-8e27-4b19-95df-d8e840ac7f21" />
<img width="908" height="415" alt="image" src="https://github.com/user-attachments/assets/fb9fd7f8-b5a9-4c7a-849f-ba62c5afa0fb" />

TABLA COMPARATIVA — CONTRAMEDIDAS EN METASPLOITABLE

| Aspecto | Antes | Contramedida aplicada | Después |
|:--|:--|:--|:--|
| **Puertos abiertos** | 23 puertos abiertos | Reglas de `iptables` que permiten únicamente TCP 22 y 80 y bloquean el resto del tráfico entrante | **2 puertos abiertos:** 22 (SSH) y 80 (HTTP). Los otros 998 puertos TCP analizados aparecen filtrados. |
| **Servicios/versiones visibles** | 23 servicios detectados, entre ellos FTP, Telnet, Samba, MySQL, SSH y HTTP | Filtrado de conexiones entrantes a los servicios no autorizados | **Solo SSH y HTTP:** OpenSSH 4.7p1 y Apache httpd 2.2.8 |
| **Respuesta al ping** | No comprobada en el escaneo inicial | Bloqueo ICMP con iptables | No responde (100% de pérdida) |






## Reflexión
(respuestas a las preguntas finales)
