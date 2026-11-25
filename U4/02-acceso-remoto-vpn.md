## Parte 0 · La red del laboratorio
### 0.1 Adaptadores en VirtualBox
Cada VM conserva el adaptador que ya usabas (NAT o solo anfitrión). Lo llamamos gestión: sirve para instalar paquetes y pasar ficheros. Añade estos adaptadores de tipo Red interna:

VM	Adaptador de red interna
dmz-vpn	Uno en net-externa y otro en net-lan
srv-hmi	Uno en net-lan
srv-ca	Uno en net-lan
Cliente	Uno en net-externa
Los nombres de interfaz (enp0s3, enp0s8…) cambian de una máquina a otra. Averigua los tuyos con:

ip -br a
### 0.2 Direcciones IP
En cada máquina, pon la IP de la tabla en el adaptador que corresponda (sustituye IFACE por el nombre de tu interfaz):

sudo ip addr add 203.0.113.10/24 dev IFACE    # dmz-vpn, interfaz de net-externa
sudo ip addr add 10.0.20.1/24 dev IFACE       # dmz-vpn, interfaz de net-lan
sudo ip addr add 10.0.20.20/24 dev IFACE      # srv-hmi
sudo ip addr add 10.0.20.30/24 dev IFACE      # srv-ca
sudo ip addr add 203.0.113.50/24 dev IFACE    # cliente
sudo ip link set IFACE up
Estas direcciones se pierden al reiniciar. Para que persistan, añádelas a la configuración de red de tu distribución (netplan o /etc/network/interfaces).

Comprueba la conectividad dentro de cada red:

ping -c 2 203.0.113.10    # desde el cliente
ping -c 2 10.0.20.20      # desde dmz-vpn

### 0.3 Ruta de retorno en srv-hmi
Para que srv-hmi sepa cómo contestar a los clientes de la VPN, dile que la red 10.8.0.0/24 está detrás de la pasarela:

sudo ip route add 10.8.0.0/24 via 10.0.20.1




Maquina cliente: Cliente-SAD
usuario: vboxuser contra:vboxuser

<img width="970" height="88" alt="image" src="https://github.com/user-attachments/assets/73693462-6cf3-4082-b260-c73e34199247" />

Maquina dmz-vpn: dmz-vpn_SERVER
usuario: dmz-server contra:vboxuser

<img width="961" height="106" alt="image" src="https://github.com/user-attachments/assets/42ca8127-429f-4759-964f-024a3ded73c6" />

Maquina srv-hmi
usuario: srv-hmi contra: vboxuser

<img width="950" height="205" alt="image" src="https://github.com/user-attachments/assets/c7ad3fba-54e4-4cf8-bd6f-6228362fbcbf" />

Maquina srv-ca
usu srv-ca contra: vboxuser

<img width="948" height="85" alt="image" src="https://github.com/user-attachments/assets/3ffaadc2-5e5a-417d-84e2-dc28bc433715" />

### 0.4 Antes de montar nada: sin VPN no se llega
Desconecta los adaptadores de gestión de todas las máquinas (en VirtualBox, desmarca «Cable conectado»). Si no lo haces, el cliente podría llegar al HMI por esa vía y la prueba no valdría.

Volverás a conectarlos solo para instalar paquetes y copiar ficheros (Partes 1, 2, 4 y 6.3). Antes de la Parte 5, déjalos desconectados otra vez.

Desde el cliente:
ping -c 2 10.0.20.20
Entrega 0. Captura del ping fallando (sin ruta o sin respuesta) y de ip -br a en el cliente.

<img width="622" height="123" alt="Captura de pantalla 2026-11-24 195142" src="https://github.com/user-attachments/assets/9ac14f68-63e7-4809-858b-d7973120b594" />

## Parte 1 · Montar la CA con Easy-RSA y emitir los certificados

### Parte 1.1 - Instalar Easy-RSA y preparar la PKI

Maquina: srv-ca
sudo apt update
sudo apt install -y easy-rsa

Despues crea la carpeta de trabajo:
<img width="367" height="41" alt="image" src="https://github.com/user-attachments/assets/ed8e3a26-46af-4a50-a6d4-d9faa6ea49b0" />

Version:
<img width="616" height="128" alt="image" src="https://github.com/user-attachments/assets/dc08ff46-7fb9-40e8-a68e-8a48040c3e09" />

En la maquina `srv-ca` se ha instalado Easy-RSA, que se utilizara para crear y gestionar la autoridad de certificacion de la VPN. Se ha creado la carpeta de trabajo `~/easy-rsa-ca`, donde se guardaran los ficheros necesarios para la PKI. Finalmente, se ha comprobado la version instalada de Easy-RSA para verificar que cumple con la version minima necesaria para la practica.


./easyrsa init-pki

<img width="536" height="415" alt="image" src="https://github.com/user-attachments/assets/b9e0c2af-aa75-45f1-be77-c1c1fb655626" />

Se ha inicializado la PKI de la autoridad de certificacion en la maquina `srv-ca` mediante Easy-RSA. El comando `./easyrsa init-pki` ha creado la estructura de directorios `pki/`, donde se almacenaran la clave privada de la CA, su certificado, las peticiones de firma, los certificados emitidos y el registro de la autoridad de certificacion.

### 1.2 Crear la CA.

<img width="322" height="115" alt="image" src="https://github.com/user-attachments/assets/95051b6e-9bac-4f85-be08-0d60a24ab2e4" />

Contraseña certificados: 123456
Common Name: Torrent S.L. VPN CA


<img width="912" height="152" alt="image" src="https://github.com/user-attachments/assets/56e9ec44-1b8c-4bdc-ab34-f888d5304064" />


<img width="470" height="245" alt="image" src="https://github.com/user-attachments/assets/99ed7e94-8996-48df-bfd6-77624bf3c5a3" />

En la maquina `srv-ca` se ha creado la autoridad de certificacion mediante Easy-RSA. Durante el proceso se ha generado la clave privada `ca.key`, protegida mediante contrasena, y el certificado publico `ca.crt`. La CA se ha identificado con el Common Name `Torrent S.L. VPN CA`. Posteriormente se ha comprobado el certificado con OpenSSL, verificando que incluye `CA:TRUE` y los usos `Certificate Sign` y `CRL Sign`, lo que confirma que puede firmar certificados y listas de revocacion. La clave privada `ca.key` permanece exclusivamente en `srv-ca` y no se copiara a ninguna otra maquina.


### 1.3 Certificado de la pasarela dmz-vpn
Ahora trabajamos primero en dmz-vpn, porque la clave privada del servidor se tiene que generar ahi y no debe salir de esa maquina.

Paso A - Generar clave y peticion en dmz-vpn
sudo apt update
sudo apt install -y easy-rsa openvpn nftables

make-cadir ~/easy-rsa
cd ~/easy-rsa
./easyrsa init-pki

./easyrsa gen-req vpn-gw nopass
Nombre vpn.torrent.local

ls -l pki/private/vpn-gw.key
ls -l pki/reqs/vpn-gw.req

<img width="618" height="77" alt="image" src="https://github.com/user-attachments/assets/39071ed3-2a2a-4048-a612-972b0e879c12" />

En la maquina `dmz-vpn` se ha instalado Easy-RSA junto con OpenVPN y nftables. Despues se ha inicializado una PKI local y se ha generado la clave privada de la pasarela `vpn-gw.key` junto con la peticion de firma `vpn-gw.req`, utilizando `vpn.torrent.local` como Common Name. La clave privada permanece en `dmz-vpn` y no se copiara a ninguna otra maquina. Unicamente la peticion `vpn-gw.req`, que contiene la clave publica y la identidad solicitada, sera enviada a la autoridad de certificacion para ser firmada.


Paso B: llevar la peticion a srv-ca

IP de srv-ca

<img width="963" height="87" alt="image" src="https://github.com/user-attachments/assets/0da97e0a-1929-47c5-a2f9-f54da68a4fe1" />

Instalamos ssh server ca y despues volvemos a dmz y hacemos scp ~/easy-rsa/pki/reqs/vpn-gw.req srv-ca@10.0.20.30:/tmp/

<img width="667" height="142" alt="image" src="https://github.com/user-attachments/assets/276846be-73af-47ff-93b5-4c64c3c51617" />

Volvemos a maquina ca

<img width="533" height="57" alt="image" src="https://github.com/user-attachments/assets/c3573f11-c88b-4cd2-8c3d-33e9445e4975" />

Ahora toca importarla y firmarla en maquina srv-ca
Importa la peticion: ./easyrsa import-req /tmp/vpn-gw.req vpn-gw

Despues firma el certificado de servidor: ./easyrsa --subject-alt-name="DNS:vpn.torrent.local,IP:203.0.113.10" sign-req server vpn-gw

<img width="956" height="707" alt="image" src="https://github.com/user-attachments/assets/6234743e-9fb7-41f0-9fc0-078bf66cb8dc" />

Verificamos:

<img width="703" height="45" alt="image" src="https://github.com/user-attachments/assets/75f8ad47-a73e-4d18-bf3b-9971a6ff4f61" />

En `srv-ca` se ha importado la peticion `vpn-gw.req` enviada por la pasarela y se ha firmado como certificado de servidor. Durante la firma se ha añadido el nombre `vpn.torrent.local` y la direccion `203.0.113.10` como Subject Alternative Name. Como resultado se ha generado el certificado `pki/issued/vpn-gw.crt`, valido para el papel de servidor. Finalmente se comprobara la cadena de confianza con `openssl verify` para confirmar que el certificado ha sido firmado correctamente por la CA.
