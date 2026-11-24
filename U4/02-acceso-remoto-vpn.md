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

### 0.4 Antes de montar nada: sin VPN no se llega
Desconecta los adaptadores de gestión de todas las máquinas (en VirtualBox, desmarca «Cable conectado»). Si no lo haces, el cliente podría llegar al HMI por esa vía y la prueba no valdría.

Volverás a conectarlos solo para instalar paquetes y copiar ficheros (Partes 1, 2, 4 y 6.3). Antes de la Parte 5, déjalos desconectados otra vez.

Desde el cliente:
ping -c 2 10.0.20.20
Entrega 0. Captura del ping fallando (sin ruta o sin respuesta) y de ip -br a en el cliente.


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







