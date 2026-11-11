# U3 -- Práctica: CA propia y Let's Encrypt con DuckDNS y acme.sh

## 1. Objetivo

En esta práctica configuramos HTTPS en Apache utilizando dos métodos:

1.  Crear una Autoridad de Certificación (CA) propia llamada **Torrent
    S.L. CA Raiz**, emitir un certificado para el servidor e instalar la
    CA en Windows para que el navegador confíe en ella.
2.  Obtener un certificado público de **Let's Encrypt** para un dominio
    DuckDNS mediante `acme.sh`, instalarlo en Apache y comprobar que la
    renovación automática queda configurada.

> **Importante:** los nombres de dominio, direcciones IP y rutas que
> aparecen aquí corresponden al laboratorio realizado. Si se repite en
> otra máquina, hay que adaptar los valores a su configuración.

## 2. Preparación y precauciones

Antes de empezar:

-   Tener Apache instalado y funcionando.
-   Tener acceso a la máquina Linux mediante terminal o SSH.
-   Tener permisos de administrador (`sudo`).
-   Para la segunda parte, disponer de un dominio DuckDNS y de su token.
-   No publicar claves privadas ni tokens.

En esta documentación se utilizan estos nombres de archivo:

  Archivo     Contenido
  ----------- --------------------------------------------------
  `ca.key`    Clave privada de nuestra CA. Secreta.
  `ca.crt`    Certificado público de nuestra CA.
  `srv.key`   Clave privada del servidor. Secreta.
  `srv.csr`   Solicitud de firma del servidor.
  `srv.crt`   Certificado del servidor firmado por nuestra CA.

No se debe subir a GitHub `ca.key`, `srv.key`, claves privadas de Let's
Encrypt, tokens de DuckDNS ni copias de seguridad que contengan esos
archivos.

------------------------------------------------------------------------

# PARTE 1. Crear una CA propia y configurar HTTPS

## 3. Crear la estructura de trabajo

Entramos en la máquina Linux y creamos el directorio de trabajo:

``` bash
mkdir -p ~/ca
cd ~/ca
```

Comprobamos que estamos en el directorio correcto:

``` bash
pwd
```

**¿Qué hacemos y para qué?**

Creamos una carpeta para guardar los archivos de la autoridad de
certificación y del servidor. Así mantenemos el material de la práctica
ordenado y separado del resto de archivos del sistema.

## 4. Crear la clave privada de la CA

Generamos la clave privada de nuestra autoridad:

``` bash
openssl genrsa -out ca.key 4096
```

Comprobamos que se ha creado:

``` bash
ls -l ca.key
```

**¿Qué hace el comando?**

Genera una clave RSA de 4096 bits y la guarda en `ca.key`. Esta clave es
el elemento más importante de nuestra CA, porque se utilizará para
firmar certificados.

**Seguridad:** no mostrar el contenido de esta clave ni copiarla a
GitHub.

## 5. Crear el certificado de la CA

Creamos un certificado raíz autofirmado:

``` bash
openssl req -x509 -new -nodes -key ca.key -sha256 -days 365 -out ca.crt
```

Durante el proceso se solicitan datos de identificación. En el campo de
nombre común (*Common Name*) utilizamos:

``` text
Torrent S.L. CA Raiz
```

Comprobamos el certificado:

``` bash
openssl x509 -in ca.crt -noout -subject -issuer -dates
```

**¿Qué hace el comando?**

-   `req -x509`: genera directamente un certificado X.509.
-   `-key ca.key`: utiliza la clave privada de nuestra CA.
-   `-sha256`: utiliza SHA-256 para la firma.
-   `-days 365`: establece una validez de 365 días.
-   `-out ca.crt`: guarda el certificado público.

Es autofirmado porque la propia CA firma su certificado raíz.

## 6. Crear la clave privada del servidor

Generamos la clave privada que utilizará Apache:

``` bash
openssl genrsa -out srv.key 2048
```

Comprobamos que existe:

``` bash
ls -l srv.key
```

**¿Para qué sirve?**

Esta clave pertenece al servidor web. No es la misma que la clave de la
CA. Apache la utilizará junto con el certificado del servidor para
ofrecer HTTPS.

## 7. Crear la solicitud CSR del servidor

Generamos la solicitud de firma:

``` bash
openssl req -new -key srv.key -out srv.csr
```

En los datos solicitados introducimos la información del servidor. El
nombre común debe corresponder al nombre con el que se accederá al
servidor en esta parte de la práctica.

Comprobamos la solicitud:

``` bash
openssl req -in srv.csr -noout -subject
```

**¿Qué hace?**

La CSR contiene los datos de identidad y la clave pública del servidor.
Se entrega a la CA para que firme el certificado. La clave privada
`srv.key` permanece en el servidor.

## 8. Firmar el certificado del servidor con nuestra CA

Utilizamos la CA para emitir el certificado:

``` bash
openssl x509 -req -in srv.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out srv.crt -days 365 -sha256
```

Comprobamos el certificado emitido:

``` bash
openssl x509 -in srv.crt -noout -subject -issuer -dates
```

**¿Qué conseguimos?**

La CA firma la solicitud del servidor y genera `srv.crt`. El certificado
queda relacionado con nuestra CA. Los clientes podrán verificarlo si
confían en `ca.crt`.

## 9. Preparar los certificados para Apache

Creamos un directorio para guardar los archivos utilizados por Apache:

``` bash
sudo mkdir -p /etc/ssl/torrent-ca
```

Copiamos el certificado y la clave del servidor:

``` bash
sudo cp srv.crt /etc/ssl/torrent-ca/
sudo cp srv.key /etc/ssl/torrent-ca/
```

Copiamos también el certificado público de la CA si lo necesitamos para
las comprobaciones:

``` bash
sudo cp ca.crt /etc/ssl/torrent-ca/
```

Protegemos la clave privada del servidor:

``` bash
sudo chmod 600 /etc/ssl/torrent-ca/srv.key
```

**Importante:** Apache necesita leer la clave privada. Los permisos
deben ser restrictivos, pero también compatibles con el usuario y la
configuración de Apache del sistema.

## 10. Activar SSL en Apache

Activamos el módulo SSL:

``` bash
sudo a2enmod ssl
```

Activamos el sitio HTTPS predeterminado si todavía no está habilitado:

``` bash
sudo a2ensite default-ssl
```

Editamos la configuración:

``` bash
sudo nano /etc/apache2/sites-available/default-ssl.conf
```

Buscamos las directivas del certificado y las dejamos apuntando a
nuestros archivos:

``` apache
SSLCertificateFile /etc/ssl/torrent-ca/srv.crt
SSLCertificateKeyFile /etc/ssl/torrent-ca/srv.key
```

Guardamos el archivo y salimos del editor.

Comprobamos la sintaxis:

``` bash
sudo apache2ctl configtest
```

El resultado esperado es:

``` text
Syntax OK
```

Recargamos Apache:

``` bash
sudo systemctl reload apache2
```

Comprobamos su estado:

``` bash
sudo systemctl is-active apache2
```

El resultado esperado es:

``` text
active
```

**¿Por qué hacemos estas comprobaciones?**

Antes de recargar el servicio comprobamos que la configuración no
contiene errores. Después verificamos que Apache sigue funcionando.

## 11. Instalar la CA propia en Windows

Para que Edge confíe en el certificado firmado por nuestra CA, Windows
debe confiar en el certificado público `ca.crt`.

Primero copiamos `ca.crt` a Windows mediante un método seguro. No
copiamos `ca.key`.

En Windows:

1.  Abrimos el menú Inicio.
2.  Buscamos `certmgr.msc`.
3.  Entramos en **Entidades de certificación raíz de confianza**.
4.  Abrimos la carpeta **Certificados**.
5.  Pulsamos con el botón derecho y seleccionamos **Todas las tareas →
    Importar**.
6.  Seleccionamos el archivo `ca.crt`.
7.  Lo instalamos en el almacén de entidades raíz de confianza.
8.  Finalizamos el asistente.

Cerramos y volvemos a abrir el navegador si fuera necesario y accedemos
al sitio mediante HTTPS utilizando el nombre configurado para el
servidor.

**¿Qué estamos haciendo?**

Estamos indicando a Windows que confíe en nuestra CA. A partir de ese
momento, el navegador puede validar los certificados emitidos por ella,
siempre que sean correctos y correspondan al sitio visitado.

## 12. Comprobar el certificado con OpenSSL

Podemos comprobar el certificado del servidor indicando expresamente
nuestra CA:

``` bash
openssl verify -CAfile ca.crt srv.crt
```

El resultado esperado es:

``` text
srv.crt: OK
```

También se puede comprobar una conexión TLS contra el servidor,
indicando la CA de confianza:

``` bash
openssl s_client -connect DIRECCION_DEL_SERVIDOR:443 -servername NOMBRE_DEL_SERVIDOR -CAfile ca.crt
```

Hay que sustituir `DIRECCION_DEL_SERVIDOR` y `NOMBRE_DEL_SERVIDOR` por
los valores del laboratorio.

Buscamos un resultado equivalente a:

``` text
Verify return code: 0 (ok)
```

**¿Por qué indicamos `-CAfile`?**

Porque nuestra CA es privada y no está incluida por defecto en los
almacenes públicos de confianza de OpenSSL. Al indicar `ca.crt`, le
decimos qué autoridad debe utilizar para verificar la firma.

## 13. Crear una copia de seguridad

En la práctica se creó una copia de seguridad del directorio de la CA:

``` bash
tar -czf ~/ca-torrent-backup.tar.gz -C ~ ca
```

Comprobamos que existe:

``` bash
ls -lh ~/ca-torrent-backup.tar.gz
```

**Precaución:** esta copia contiene la clave privada de la CA. No se
debe subir a GitHub ni compartir. Hay que guardarla en un lugar
protegido.

------------------------------------------------------------------------

# PARTE 2. Let's Encrypt con DuckDNS y acme.sh

## 14. Preparar el dominio DuckDNS

Creamos o utilizamos un dominio en DuckDNS. En nuestro laboratorio
usamos:

``` text
torrent-marcos.duckdns.org
```

El dominio debe apuntar a la dirección IP que corresponda según la
configuración de la práctica.

En DuckDNS obtenemos el token necesario para actualizar el registro DNS.

**Seguridad:** el token es secreto. No lo pongas en capturas públicas,
documentos de GitHub ni comandos que vayas a compartir sin ocultarlo.

## 15. Entrar como administrador

En esta parte instalamos y ejecutamos `acme.sh` como `root`:

``` bash
sudo -i
```

Comprobamos el usuario:

``` bash
whoami
```

El resultado esperado es:

``` text
root
```

**¿Por qué usamos root?**

La herramienta debe instalar certificados en rutas del sistema y
recargar Apache. Al instalarla como `root`, sus archivos de
configuración y su tarea programada quedan asociados a ese usuario.

## 16. Descargar y revisar el instalador de acme.sh

Descargamos el instalador:

``` bash
curl -o get-acme.sh https://get.acme.sh
```

Podemos revisar su contenido antes de ejecutarlo:

``` bash
less get-acme.sh
```

Para salir de `less`, pulsamos `q`.

Instalamos la herramienta indicando un correo electrónico válido:

``` bash
sh get-acme.sh email=TU_CORREO
```

Sustituimos `TU_CORREO` por el correo utilizado en la práctica.

Comprobamos que se ha instalado:

``` bash
ls -la /root/.acme.sh/
```

**¿Qué hace?**

Instala el cliente ACME en el directorio del usuario `root` y configura
una tarea programada para gestionar las renovaciones.

## 17. Seleccionar Let's Encrypt como autoridad predeterminada

Ejecutamos:

``` bash
/root/.acme.sh/acme.sh --set-default-ca --server letsencrypt
```

Comprobamos que el comando confirma el cambio a Let's Encrypt.

**¿Para qué sirve?**

Indica a acme.sh que utilice Let's Encrypt como autoridad de
certificación predeterminada para las siguientes solicitudes.

## 18. Configurar el token de DuckDNS

Exportamos el token en la terminal:

``` bash
export DuckDNS_Token="TU_TOKEN_DUCKDNS"
```

Sustituimos el texto por el token real.

**Importante:** no guardes el token real en este documento. Si se
necesita documentar el comando, deja el marcador `TU_TOKEN_DUCKDNS`.

La variable de entorno permite que acme.sh utilice el token durante la
validación DNS-01.

## 19. Solicitar el certificado mediante DNS-01

Solicitamos el certificado para nuestro dominio:

``` bash
/root/.acme.sh/acme.sh --issue --dns dns_duckdns -d torrent-marcos.duckdns.org
```

Esperamos a que termine la validación. Si todo funciona, acme.sh muestra
un mensaje similar a:

``` text
Cert success
```

**¿Qué ocurre?**

1.  acme.sh solicita el certificado a Let's Encrypt.
2.  Utiliza el token de DuckDNS para crear el registro DNS TXT de
    validación.
3.  Let's Encrypt consulta el registro y comprueba que controlamos el
    dominio.
4.  Si la comprobación es correcta, emite el certificado.

Los archivos generados se guardan dentro del directorio de acme.sh
correspondiente al dominio.

Podemos consultar el directorio:

``` bash
ls -la /root/.acme.sh/torrent-marcos.duckdns.org_ecc/
```

Entre los archivos aparecen el certificado, la clave privada, el
certificado de la CA intermedia y la cadena completa.

## 20. Preparar las rutas de instalación para Apache

Creamos el directorio donde Apache utilizará los archivos:

``` bash
mkdir -p /etc/ssl/torrent-le
```

Instalamos el certificado y la clave privada:

``` bash
/root/.acme.sh/acme.sh --install-cert -d torrent-marcos.duckdns.org \
    --key-file /etc/ssl/torrent-le/privkey.pem \
    --fullchain-file /etc/ssl/torrent-le/fullchain.pem \
    --reloadcmd "systemctl reload apache2"
```

Protegemos la clave privada:

``` bash
chmod 600 /etc/ssl/torrent-le/privkey.pem
```

Comprobamos los archivos:

``` bash
ls -l /etc/ssl/torrent-le/
```

**¿Qué hace la instalación?**

Copia los archivos a las rutas que utilizará Apache. También registra el
comando que debe ejecutarse después de renovar e instalar el
certificado: recargar Apache para que utilice el certificado
actualizado.

## 21. Cambiar la configuración SSL de Apache

Antes de modificar el archivo, guardamos una copia de seguridad de la
configuración actual:

``` bash
cp /etc/apache2/sites-available/default-ssl.conf /root/default-ssl.conf.ca-propia
```

Editamos el sitio HTTPS:

``` bash
nano /etc/apache2/sites-available/default-ssl.conf
```

Buscamos las directivas del certificado y las cambiamos para que queden
así:

``` apache
SSLCertificateFile /etc/ssl/torrent-le/fullchain.pem
SSLCertificateKeyFile /etc/ssl/torrent-le/privkey.pem
```

Guardamos los cambios.

Comprobamos la sintaxis:

``` bash
apache2ctl configtest
```

El resultado esperado es:

``` text
Syntax OK
```

Recargamos Apache:

``` bash
systemctl reload apache2
```

Comprobamos el servicio:

``` bash
systemctl is-active apache2
```

El resultado esperado es:

``` text
active
```

## 22. Comprobar HTTPS con curl

Ejecutamos:

``` bash
curl -vI https://torrent-marcos.duckdns.org
```

Para mostrar solo las líneas más importantes podemos utilizar:

``` bash
curl -vI https://torrent-marcos.duckdns.org 2>&1 | grep -iE 'subject:|issuer:|expire|verify|HTTP/'
```

En nuestra comprobación se obtuvo:

-   El nombre del certificado correspondía a
    `torrent-marcos.duckdns.org`.
-   El emisor pertenecía a Let's Encrypt.
-   La verificación del certificado fue correcta.
-   El servidor respondió con `HTTP/1.1 200 OK`.

**¿Qué demuestra?**

Que se puede establecer una conexión HTTPS, que el certificado
corresponde al dominio y que el cliente confía en la cadena de
certificación presentada.

## 23. Comprobar la cadena de confianza con OpenSSL

Ejecutamos:

``` bash
openssl s_client -connect torrent-marcos.duckdns.org:443 -servername torrent-marcos.duckdns.org
```

Al final de la salida buscamos:

``` text
Verification: OK
Verify return code: 0 (ok)
```

**¿Qué comprobamos?**

OpenSSL muestra información del certificado y permite comprobar si la
cadena de confianza se valida correctamente. En nuestro caso, el
certificado del dominio estaba encadenado a una autoridad intermedia de
Let's Encrypt y la verificación terminó correctamente.

Si aparece `Connection refused`, no significa necesariamente que el
certificado esté mal. Puede indicar que el servidor no está escuchando
en el puerto 443 o que hay un problema de red, dirección IP, interfaz o
cortafuegos. Primero hay que comprobar que Apache está activo y que la
máquina tiene la dirección IP esperada.

## 24. Comprobar la renovación automática

Como instalamos acme.sh como `root`, debemos consultar el cron de ese
usuario.

Si no estamos como root, entramos:

``` bash
sudo -i
```

Consultamos las tareas programadas:

``` bash
crontab -l
```

En nuestro laboratorio aparecía una tarea de acme.sh parecida a esta:

``` cron
26 2,8,14,20 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
```

La hora y los minutos pueden ser diferentes en otra instalación.

Consultamos los certificados gestionados por acme.sh:

``` bash
/root/.acme.sh/acme.sh --list
```

Comprobamos que aparece `torrent-marcos.duckdns.org` y que Let's Encrypt
figura como CA.

**¿Qué significa?**

La tarea cron ejecuta periódicamente acme.sh. La herramienta revisa si
algún certificado necesita renovarse y, cuando corresponde, realiza el
proceso. Al instalar el certificado configuramos también la recarga de
Apache después de la instalación.

No es necesario renovar manualmente cada pocos días. Aun así, conviene
revisar periódicamente el estado de la tarea y la fecha de caducidad.

------------------------------------------------------------------------

# PARTE 3. Comparación y preguntas

## 25. Tabla comparativa

  -----------------------------------------------------------------------
  Característica          CA propia               Let's Encrypt
  ----------------------- ----------------------- -----------------------
  Emisor                  Torrent S.L. CA Raiz    Let's Encrypt

  Confianza del navegador Hay que instalar la CA  Confianza habitual de
                          propia                  los navegadores

  Validez utilizada       365 días en nuestra     Aproximadamente 90 días
                          práctica                

  Renovación              Manual                  Automática con acme.sh
                                                  y cron

  Validación              Firma realizada por     Validación DNS-01 con
                          nuestra CA              DuckDNS

  Uso recomendado         Servicios internos y    Servicios asociados a
                          redes privadas          dominios públicos

  Internet                Puede funcionar en una  Se necesita para emitir
                          red interna sin         y renovar
                          Internet una vez        
                          configurada             
  -----------------------------------------------------------------------

## 26. Preguntas y respuestas

### 1. ¿Por qué Edge confía en el certificado de Let's Encrypt y no en el de Torrent S.L.?

Edge confía en Let's Encrypt porque su cadena de certificación llega a
una autoridad raíz que Windows reconoce como de confianza. En cambio,
nuestra CA Torrent S.L. es privada y no viene instalada por defecto.
Para que Edge confíe en sus certificados tuvimos que importar `ca.crt`
en el almacén de autoridades raíz de confianza de Windows.

### 2. ¿Por qué los certificados de Let's Encrypt tienen una duración corta?

Tienen una validez aproximada de 90 días para reducir el tiempo durante
el que un certificado o una clave comprometidos podrían seguir
utilizándose. Por eso es importante tener configurada la renovación
automática y comprobar que funciona.

### 3. ¿Cuándo utilizaríamos una CA propia y cuándo Let's Encrypt?

Utilizaríamos una CA propia para servicios internos de una empresa,
laboratorios, equipos o conexiones VPN en los que queremos controlar
quién emite los certificados. Utilizaríamos Let's Encrypt para una
página web asociada a un dominio y accesible mediante una validación
pública del dominio.

### 4. ¿HTTPS evita todos los ataques?

No. HTTPS cifra la comunicación, ayuda a detectar modificaciones y
permite autenticar el servidor. Pero no evita que una persona cree una
página de phishing con un certificado válido, ni corrige
vulnerabilidades del servidor o del propio sitio web.

### 5. ¿Montaríamos una CA propia en una empresa de tres personas?

Normalmente no sería necesario si solo se quiere proteger una página web
pública, porque Let's Encrypt ofrece certificados gratuitos y permite
automatizar su renovación. Sí podría tener sentido si la empresa
necesita emitir certificados para servicios internos, dispositivos,
usuarios o conexiones VPN.

## 27. Conclusión

En la práctica hemos configurado HTTPS de dos maneras. Primero hemos
creado una CA propia, hemos firmado el certificado del servidor y hemos
instalado el certificado público de nuestra CA en Windows para que Edge
confiase en él. Después hemos solicitado un certificado de Let's Encrypt
utilizando DuckDNS y acme.sh, lo hemos instalado en Apache y hemos
comprobado la conexión y la renovación automática.

La práctica nos ha permitido ver que una CA propia ofrece control sobre
los certificados de una organización, mientras que Let's Encrypt
facilita el uso de certificados reconocidos públicamente. En los dos
casos es fundamental proteger las claves privadas y verificar que el
certificado y su cadena de confianza son correctos.

## 28. Archivos y datos que no se deben publicar

Antes de subir la documentación a GitHub, comprobar que no se incluyen:

-   `ca.key`
-   `srv.key`
-   `privkey.pem`
-   El token real de DuckDNS.
-   La carpeta privada de acme.sh.
-   `ca-torrent-backup.tar.gz`, porque contiene material privado.

Sí se puede documentar el nombre de los archivos, los comandos con
marcadores de posición, las salidas de verificación y los certificados
públicos cuando sea apropiado para la práctica.
