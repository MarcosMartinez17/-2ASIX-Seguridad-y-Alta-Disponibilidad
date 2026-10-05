
metasploitebale

iface eth0 inet static
    address 192.168.1.101
    netmask 255.255.255.0
    gateway 192.168.1.1
auto eth1
iface eth1 inet static
    address 192.168.56.2
    netmask 255.255.255.0
    gateway 192.168.56.1


linux:

### Configuración de Red Actual (`ip a`)

| Interfaz | Dirección IP / Máscara | Descripción |
| :--- | :--- | :--- |
| **lo** | `127.0.0.1/8`[cite: 3] | Interfaz de bucle local (*localhost*)[cite: 3]. |
| **enp0s3** | `192.168.1.100/24`[cite: 3] | Interfaz de red principal (IP estática de prácticas)[cite: 3]. |
| **enp0s8** | `10.0.3.15/24`[cite: 3] | Interfaz secundaria (IP dinámica / NAT)[cite: 3]. |
