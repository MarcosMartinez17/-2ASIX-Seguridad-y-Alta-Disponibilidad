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

## 1.4 Certificado del cliente (el técnico)

### Paso A - Generar la clave y la peticion
Maquina Cliente
1. Instalar Easy-RSA y OpenVPN
sudo apt update
sudo apt install -y easy-rsa openvpn

2. Preparar Easy-RSA
make-cadir ~/easy-rsa
cd ~/easy-rsa
./easyrsa version

Despues inicializa la PKI:
./easyrsa init-pki

<img width="477" height="90" alt="image" src="https://github.com/user-attachments/assets/b83824a1-9f2b-4684-97d6-51e0adbe6304" />

3. Generar el certificado del tecnico
./easyrsa gen-req tecnico01 nopass

<img width="475" height="50" alt="image" src="https://github.com/user-attachments/assets/8424d0ff-5c25-4306-9c22-984cdf9e0957" />

4. Comprobar los archivos

<img width="617" height="77" alt="image" src="https://github.com/user-attachments/assets/60fc293b-c213-49bd-a523-7568ae340adc" />

En la maquina `Cliente` se ha instalado Easy-RSA y OpenVPN. Despues se ha inicializado la PKI y se ha generado la clave privada `tecnico01.key` junto con la peticion de firma `tecnico01.req`, utilizando `tecnico01` como Common Name. La clave privada permanece en el Cliente y solo se enviara la peticion a `srv-ca` para obtener el certificado firmado.

### Paso B - Enviar tecnico01.req a srv-ca
Maquinas encendidas: Cliente y srv-ca

Maquina: Cliente
En el 1.3 copiamos vpn-gw.req desde dmz-vpn a srv-ca usando: scp ~/easy-rsa/pki/reqs/vpn-gw.req srv-ca@10.0.20.30:/tmp/

Funciono porque ambas maquinas estan conectadas a net-lan
Ahora es diferente: Cliente esta en net-externa y srv-ca en net-lan, por lo que no pueden comunicarse directamente.
Utilizaremos el adaptador de gestion para copiar archivos

La forma mas rapida es conectar temporalmente Cliente a net-lan mediante un tercer adaptador y quitarlo despues de copiar el archivo.

Despues encenemos la maquina le asginamos una ip:
scp ~/easy-rsa/pki/reqs/tecnico01.req srv-ca@10.0.20.30:/tmp/

<img width="776" height="127" alt="image" src="https://github.com/user-attachments/assets/e515bf7c-f963-4d57-968b-4e859a4385e1" />

Maquina: srv-ca, comprobamos el archivo

<img width="523" height="57" alt="image" src="https://github.com/user-attachments/assets/db5ffa76-2a7c-40fa-8bba-b0fca8b9c368" />

Ahora que tenemos el archivo ya quitamos el adaptador que añadimos al cliente

### Paso C · En srv-ca: importar y firmar como client.

1. Importar la peticion
Maquina: srv-ca

<img width="631" height="125" alt="image" src="https://github.com/user-attachments/assets/05a00dc1-69f4-4e39-9f39-2cfb756fe667" />

2. Firmar el certificado

./easyrsa sign-req client tecnico01

3. Comprobar el certificado

<img width="717" height="72" alt="image" src="https://github.com/user-attachments/assets/449ea743-20fa-46e9-8a84-33c5f36ca1a0" />

Se ha transferido la peticion `tecnico01.req` desde Cliente hasta `srv-ca` para solicitar su firma. Para ello se ha utilizado temporalmente una conexion a `net-lan`, que se ha retirado despues de la transferencia para mantener el aislamiento entre redes. Posteriormente, en `srv-ca`, se ha importado la peticion y se ha firmado como certificado de cliente mediante Easy-RSA. El certificado generado es `tecnico01.crt`, mientras que la clave privada `tecnico01.key` permanece exclusivamente en Cliente. La validez de la firma se comprueba utilizando OpenSSL y el certificado de la CA.

### Parte 1.4 - Comprobar el uso del certificado

<img width="810" height="50" alt="image" src="https://github.com/user-attachments/assets/c34cca63-f251-4fe1-be26-f4caaf4677a8" />

Debe aparecer: TLS Web Client Authentication

<img width="852" height="66" alt="image" src="https://github.com/user-attachments/assets/53f67749-b159-4d73-8184-a1a1d002b199" />

### Comprobar los roles
Estos dos primeros deben fallar, porque los certificados se utilizan para el papel contrario.

<img width="837" height="70" alt="image" src="https://github.com/user-attachments/assets/a58b29f8-3135-45b8-912a-3a252eed3412" />

<img width="873" height="63" alt="image" src="https://github.com/user-attachments/assets/ec57ec96-b6de-46ae-bb11-e1bf1c8f7ff8" />

Estos debem funcionar:

<img width="860" height="38" alt="image" src="https://github.com/user-attachments/assets/aacda46c-a34d-4625-a7aa-f24ab32cf0c0" />

<img width="840" height="47" alt="image" src="https://github.com/user-attachments/assets/de486570-da59-4756-9735-71bb859a03d6" />

### 1.5 Lista de revocación (CRL) inicial

La CRL sirve para indicar que certificados han sido revocados y ya no deben aceptarse en la VPN.
Paso 1. Generar la CRL

<img width="575" height="167" alt="image" src="https://github.com/user-attachments/assets/1b055057-8f96-4d03-b026-ff7220f45882" />

LAS CONTRASEÑAS DE LAS CA ES 123456
Paso 2. Comprobar la CRL

<img width="645" height="45" alt="image" src="https://github.com/user-attachments/assets/3f64527e-79e9-49c5-ab11-be556a7de3d1" />

En la maquina `srv-ca` se ha generado la lista de revocacion de certificados (CRL) mediante Easy-RSA. Esta lista permite identificar los certificados que hayan sido revocados para impedir su utilizacion en la VPN. Se ha creado el archivo `pki/crl.pem` y se ha comprobado su fecha de caducidad mediante OpenSSL. La CRL debera renovarse antes de que caduque para evitar problemas con las conexiones VPN.

<img width="473" height="32" alt="image" src="https://github.com/user-attachments/assets/db89c8c4-a90c-46fb-874e-740e2bf1ddbc" />

### 1.6 Qué se copia a cada máquina
Paso 1. Copiar los certificados a dmz-vpn
Maquinas encendidas: srv-ca y dmz-vpn.

Maquina: dmz-vpn:
mkdir -p ~/certificados-vpn

Ahora copia los tres archivos desde srv-ca:

<img width="746" height="148" alt="image" src="https://github.com/user-attachments/assets/e8c60e8e-99df-49e3-9635-9ad7fcf09e8c" />

Comprobar los archivos:

<img width="532" height="95" alt="image" src="https://github.com/user-attachments/assets/ec04881e-ebcd-45db-a50b-f6313ae4cb55" />

Maquina: Cliente:
Poner otra vez el adaptador 3
mkdir -p ~/certificados-vpn

Copia de archivos:

<img width="826" height="97" alt="image" src="https://github.com/user-attachments/assets/026c3a12-cc2f-4e54-9ac8-5d25864e57c4" />

Comprobar los archivos

<img width="522" height="66" alt="image" src="https://github.com/user-attachments/assets/ee0507a6-c9d4-4ee9-aae2-1fccf3c4c3b0" />

### Ultimo paso: apagar la CA
La apagamos porque contiene ca.key, la clave privada de la autoridad de certificacion. Solo volveremos a encenderla cuando necesitemos firmar o revocar certificados.


## Parte 2 · La pasarela dmz-vpn
### Parte 2.1 - Instalar OpenVPN y activar el reenvio de paquetes

sudo apt update
sudo apt install -y openvpn nftables
openvpn --version | head -1

Despues activa el reenvio:

echo "net.ipv4.ip_forward=1" | sudo tee /etc/sysctl.d/99-forward.conf
sudo sysctl --system
sysctl net.ipv4.ip_forward

<img width="416" height="61" alt="image" src="https://github.com/user-attachments/assets/3cdf5899-10a0-4296-b70d-a5de9ea1bb0a" />

En dmz-vpn se ha comprobado la instalacion de OpenVPN y nftables. Despues se ha activado el reenvio de paquetes IPv4 mediante /etc/sysctl.d/99-forward.conf, permitiendo que la pasarela pueda dirigir trafico entre la VPN y la red interna. Se ha comprobado que net.ipv4.ip_forward tiene el valor 1.

### 2.2 - Colocar los certificados de OpenVPN.
Crear la carpeta de OpenVPN
sudo mkdir -p /etc/openvpn/server

Copiar los certificados
sudo cp ~/certificados-vpn/ca.crt /etc/openvpn/server/
sudo cp ~/certificados-vpn/vpn-gw.crt /etc/openvpn/server/
sudo cp ~/certificados-vpn/crl.pem /etc/openvpn/server/

Ahora copia la clave privada que generamos en esta misma maquina:
sudo cp ~/easy-rsa/pki/private/vpn-gw.key /etc/openvpn/server/

Configurar los permisos
sudo chmod 600 /etc/openvpn/server/vpn-gw.key
sudo chmod 644 /etc/openvpn/server/crl.pem

La clave privada solo podra leerla root, mientras que la lista de revocacion podra ser leida por OpenVPN cuando funcione sin privilegios.

<img width="525" height="117" alt="image" src="https://github.com/user-attachments/assets/1621f565-a112-4d9a-9d30-cd9468a06bf0" />

En la maquina dmz-vpn se ha creado la carpeta /etc/openvpn/server/ y se han copiado los certificados necesarios para configurar OpenVPN: ca.crt, vpn-gw.crt, vpn-gw.key y crl.pem. Se han establecido permisos restrictivos para la clave privada, permitiendo su lectura solo a root, mientras que la lista de revocacion queda disponible para que OpenVPN pueda comprobar los certificados revocados. La clave privada vpn-gw.key permanece en la maquina donde se genero.


### Parte 2.3 - Clave de proteccion del canal de control
Vamos a generar ta.key, una clave compartida que OpenVPN utilizara para proteger el canal de control mediante tls-crypt.

Paso 1 - Generar la clave

<img width="648" height="67" alt="image" src="https://github.com/user-attachments/assets/ebb97911-0cd5-416e-84be-e2ab298293f4" />

Paso 2 - Comprobar

<img width="632" height="35" alt="image" src="https://github.com/user-attachments/assets/d75cbee6-71c3-48e7-94ba-eeda94bd3350" />

En la maquina dmz-vpn se ha generado la clave compartida ta.key mediante OpenVPN. Esta clave se utilizara con tls-crypt para cifrar y autenticar el canal de control, protegiendo el intercambio inicial de la conexion VPN. Tambien se han establecido permisos 600 para evitar que otros usuarios puedan leerla. Mas adelante se copiara de forma segura al Cliente, ya que ambos extremos necesitan utilizar la misma clave.

### 2.4 - Configurar el servidor OpenVPN.
En este paso vamos a crear el archivo server.conf, que indica a OpenVPN como debe funcionar la VPN.

sudo nano /etc/openvpn/server/server.conf

<img width="830" height="415" alt="image" src="https://github.com/user-attachments/assets/355e7cf7-903f-4a6c-8507-74c7e934dcf3" />

En dmz-vpn se ha creado el archivo /etc/openvpn/server/server.conf para configurar el servidor OpenVPN. Se ha establecido el puerto UDP 1194 y la red del tunel 10.8.0.0/24. Tambien se ha añadido una ruta hacia la LAN 10.0.20.0/24 para que los clientes VPN puedan acceder a los recursos internos. Se han configurado los certificados, la clave ta.key para proteger el canal de control y la lista de revocacion crl.pem. Ademas, se han establecido los algoritmos de cifrado y la version minima de TLS.

### Parte 2.5 - Arrancar y comprobar OpenVPN
Paso 1 - Iniciar el servidor
sudo systemctl enable --now openvpn-server@server
sudo systemctl restart openvpn-server@server

Paso 2 - Comprobar el estado del servicio

<img width="888" height="87" alt="image" src="https://github.com/user-attachments/assets/cfc83917-c382-470f-b91d-7250e53d56d7" />

Paso 3 - Comprobar la interfaz VPN

<img width="653" height="50" alt="image" src="https://github.com/user-attachments/assets/c9a4dd9f-3dea-4b23-b279-b8b4dc0aef1c" />

Paso 4 - Comprobar el puerto OpenVPN

<img width="872" height="48" alt="image" src="https://github.com/user-attachments/assets/f4ea2198-b65e-4c64-9425-217325f824b5" />

Paso 5 - Comprobar los registros

<img width="860" height="420" alt="image" src="https://github.com/user-attachments/assets/8cc4e43b-087f-45e5-9c7c-c18d2948e945" />

En la maquina dmz-vpn se ha iniciado y habilitado el servicio OpenVPN. Se ha comprobado que el servidor ha creado correctamente la interfaz virtual tun0 con la direccion 10.8.0.1/24 y que esta escuchando conexiones en el puerto UDP 1194. Ademas, se han revisado los registros del servicio, donde aparece el mensaje Initialization Sequence Completed, confirmando que OpenVPN se ha iniciado correctamente y esta preparado para aceptar conexiones VPN.

## Parte 3 · Cortafuegos mínimo en la pasarela
Paso 1 - Crear la configuracion del cortafuegos
sudo nano /etc/nftables.conf

<img width="786" height="540" alt="image" src="https://github.com/user-attachments/assets/bb288306-ff3a-4559-a27a-b90ad04eea4c" />

Comprobar la sintaxis sin aplicar las reglas: sudo nft -c -f /etc/nftables.conf

Paso 2: Activar nftables
Aplicar las reglas: sudo nft -f /etc/nftables.conf
Activar el cortafuegos al arrancar: sudo systemctl enable --now nftables
Comprobar las reglas:sudo nft list ruleset

<img width="947" height="346" alt="image" src="https://github.com/user-attachments/assets/d19bea10-8c75-4b37-b1ed-24eee8b0102b" />

### Parte 3 - Configuracion del cortafuegos

En la maquina dmz-vpn se ha configurado el cortafuegos nftables mediante /etc/nftables.conf. Se ha establecido una politica de bloqueo por defecto para impedir conexiones no autorizadas. Se permite el puerto UDP 1194 para OpenVPN y SSH para la administracion de la pasarela. Desde el tunel VPN solo se permite el acceso por SSH y ping al servidor srv-hmi (10.0.20.20). Finalmente, se han aplicado las reglas y se ha comprobado su configuracion mediante sudo nft list ruleset.

## Parte 4 · El cliente
### 4.1 Colocar los ficheros
Maquina: Cliente

Crear la carpeta:
mkdir -p ~/vpn-torrent
cd ~/vpn-torrent

Copiar los archivos que ya tenemos
cp ~/easy-rsa/pki/private/tecnico01.key .
cp ~/certificados-vpn/ca.crt .
cp ~/certificados-vpn/tecnico01.crt .

Comprobar

<img width="520" height="106" alt="image" src="https://github.com/user-attachments/assets/3f8ff0d6-e565-40bb-a30c-57a61743ffae" />

Todavia faltara ta.key, que copiaremos desde dmz-vpn en el siguiente paso.

Que estamos haciendo?
Estamos reuniendo en una carpeta los archivos que necesita el Cliente para identificarse ante el servidor VPN y comprobar que se conecta a la pasarela correcta

Copiar ta.key al Cliente.
Este archivo se creo en dmz-vpn y debe ser identico en las dos maquinas para que funcione la VPN
Vamos a utilizar SCP. Como dmz-vpn y Cliente comparten net-externa

Paso 2 - Transferir ta.key
Maquina: dmz-vpn
sudo cp /etc/openvpn/server/ta.key ~/ta.key
sudo chown "$USER:$USER" ~/ta.key

Enviar el archivo al Cliente:
scp ~/ta.key vboxuser@203.0.113.50:~/vpn-torrent/ta.key

<img width="910" height="185" alt="image" src="https://github.com/user-attachments/assets/0e011ff1-9549-46c7-b734-5b1258f1e093" />

Comprobar y proteger los archivos
Maquina: Cliente

<img width="547" height="133" alt="image" src="https://github.com/user-attachments/assets/66f7040d-1ab4-4281-ad81-34fbafaf570b" />

En la maquina Cliente se ha creado la carpeta ~/vpn-torrent y se han reunido los archivos necesarios para la conexion VPN: ca.crt, tecnico01.crt, tecnico01.key y ta.key. La clave ta.key se ha transferido de forma segura desde dmz-vpn mediante SCP. Finalmente, se han establecido permisos 600 para proteger las claves privadas y se ha comprobado que todos los archivos estan en la carpeta correcta.

### 4.2 Configuración del cliente
Maquina: Cliente
Crear el archivo cliente.conf

<img width="765" height="295" alt="image" src="https://github.com/user-attachments/assets/ed66636d-e574-4fd3-9a75-f5e704c25410" />

Comprobar:

<img width="532" height="140" alt="image" src="https://github.com/user-attachments/assets/dff20f7e-45e2-4edf-a58f-4ded33bf4f75" />

### Parte 4.3 - Conectar a la VPN
Paso 1 - Preparar las maquinas
Encender estas 3 maquinas:
- Cliente
- dmz-vpn
- srv-hmi
Y desconectar el NAT de gestion

Paso 2 - Conectar a OpenVPN
Maquina: Cliente
cd ~/vpn-torrent
sudo openvpn --config cliente.conf







