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

## Puero 23 



   





## Reflexión
(respuestas a las preguntas finales)
