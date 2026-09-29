# Ataques de suplantación de identidad

## 1. SMTP spoofing

### Qué es
Un correo de suplantacion de identididad, se hace pasar por alguien de confianza

### Cómo se lleva a cabo
Ingieneria social, se hacen pasar por otros, como tu seguro para que te instales algo o pedirte datos personales
Maneras de mensajeria basicos como gmail o SMT, modifican las cabeceras

### Qué categoría(s) de amenaza compromete
Autenticidad al falsificar un correo

### Ejemplo o caso real
100 millones de dolares facebook un atacante uso para enviar acorre que se hicieron pasar por el proveedor y les pillaron

### Medida de prevención
SPF registro de dns
PKIM ayuda una firma criptografica
DMARC

### Fuente
GRUPO 4


## 2. DNS spoofing

### Qué es
consiste en falsificar la direccion ip, haciendo de que viene algo de una fuente confiable

### Cómo se lleva a cabo
pq toda cabecera tiene ip y destino, entonces el atacante la cambia usando una ip falsa pero que parezca que sea igual

### Qué categoría(s) de amenaza compromete
Autenticidad

### Ejemplo o caso real
Consiguio saber una ip de confianza, la falsifico, monto un paquete cambia el origen y caundo el usuario ve el mensaje acepte y le entre al servidor

### Medida de prevención
Supervidion de firewall
Filtrado de paquetes

### Fuente
Grupo 1

## 3. IP spoofing

### Qué es
es un ciberataque donde se alteran los registros de un servidor o el cache dns paar redirigir a los usuarios hacia paginaa web falsas
### Cómo se lleva a cabo
el atacante introduce datos fslsos en el cache de un servidor dns o intercepta la consulta para devolver una ip fraudalenta
### Qué categoría(s) de amenaza compromete
Conficiealidad
### Ejemplo o caso real
Hackearon una aerolinea que en vez de que salga la pagina web salia una foto de un lagarto
### Medida de prevención
Un servidor DNS SSL
### Fuente
Grupo 2

## 4. Captura de cuentas de usuario y contraseñas

### Qué es
Un tipo de ataque dirigido a la obtención ilícita de nombres de usuario y credenciales de acceso para lograr el control de cuentas legítimas en sistemas informáticos.

### Cómo se lleva a cabo
Se ejecuta principalmente mediante el uso de herramientas de captura de tráfico de red como los sniffers(son herramientas que permiten capturar y analizar los datos que circulan por una red informática.), técnicas de ingeniería social como el phishing(es una técnica de engaño en la que un atacante se hace pasar por una persona o empresa de confianza para conseguir información privada), y la instalación de software malicioso registrador de pulsaciones (keyloggers, es un programa o dispositivo que registra las teclas que una persona pulsa en el teclado.).

### Qué categoría(s) de amenaza compromete
Compromete la categoría de Confidencialidad, cuando se capturan datos durante su transmisión, y la Integridad, porque tendra acceso a modificar los archivos, y la Disponibilidad.

### Ejemplo o caso real
En junio de 2025, hubo un caso que se considera la mayor exposición de datos de la historia, en el que se localizaron 30 conjuntos de datos en internet que sumaban 16.000 millones de registros de nombres de usuario, contraseñas, cookies de sesión y enlaces directos de inicio de sesión. 
Una gran cantidad de empresas de las mas famosas del sector fueron afectadas entre las que se encontraban multinacionales como Netflix, PayPal, Apple, Google, entre muchas otras.
Todo esto se originó mediante un malware instalado en los ordenadores de los usuarios, utilizando ingeniería social para que los propios usuarios lo descarguen e instalen sin darse cuenta.

### Medida de prevención
Utilizar contraseñas seguras y diferentes para cada cuenta, activar la autenticación de dos factores (2FA), evitar acceder a enlaces sospechosos y mantener actualizados el sistema operativo y los programas de seguridad.

### Fuente
INCIBE – Instituto Nacional de Ciberseguridad de España

## Aplicado a Estudio Torrent

De los cuatro, ¿cuál creéis que sería el más plausible contra Estudio Torrent 
(wifi de oficina, ERP online, disco compartido con clientes)? Razonad la respuesta 
en 3-4 líneas, usando lo que habéis aprendido de los cuatro ataques, no solo del vuestro.
