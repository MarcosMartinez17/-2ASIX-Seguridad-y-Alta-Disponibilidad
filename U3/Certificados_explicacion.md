# Práctica: Certificados digitales y HTTPS

## Introducción

En esta práctica hemos aprendido cómo funcionan los certificados digitales y cómo podemos proteger una página web mediante HTTPS.

El objetivo principal ha sido entender cómo se establece una conexión segura entre un servidor web y un cliente, cómo se comprueba que un servidor es realmente quien dice ser y cómo podemos conseguir que los navegadores confíen en sus certificados.

Para ello hemos trabajado con dos métodos diferentes:

- En la primera parte hemos creado nuestra propia Autoridad de Certificación (CA), que nos permite generar y firmar certificados.
- En la segunda parte hemos utilizado Let's Encrypt, una autoridad de certificación pública que permite conseguir certificados reconocidos por los navegadores.

También hemos utilizado Apache, OpenSSL, DuckDNS y acme.sh para realizar las diferentes configuraciones.

---

# PARTE 1. Crear nuestra propia Autoridad de Certificación

## 1. ¿Qué es una Autoridad de Certificación?

Una Autoridad de Certificación, también conocida como CA, es una entidad que se encarga de emitir y firmar certificados digitales.

Estos certificados sirven para demostrar que un servidor o una página web es quien dice ser.

En nuestro caso hemos creado una CA propia llamada Torrent S.L. CA Raiz.

La hemos creado nosotros porque queríamos aprender cómo funciona el proceso de emisión de certificados sin depender de una empresa externa.

## 2. Crear las claves y certificados

Lo primero que hicimos fue preparar los archivos necesarios para nuestra CA y para el servidor web.

Para entenderlo hay que diferenciar cuatro archivos:

- **ca.key:** es la clave privada de nuestra CA. Sirve para firmar certificados y es el archivo más importante que tenemos que proteger.
- **ca.crt:** es el certificado público de nuestra CA. Sirve para que otros equipos puedan reconocerla y confiar en los certificados que firma.
- **srv.key:** es la clave privada del servidor web. Se utiliza para demostrar que el servidor tiene la clave correspondiente a su certificado.
- **srv.crt:** es el certificado público del servidor. Contiene su identidad y está firmado por nuestra CA.

Las claves privadas no se deben compartir porque, si alguien las consigue, podría suplantar nuestra identidad o la del servidor.

Los certificados públicos sí se pueden compartir porque sirven para comprobar la identidad y la firma.

## 3. ¿Qué es una CSR y para qué sirve?

Durante la práctica también hemos trabajado con una CSR, que significa Certificate Signing Request.

Es una solicitud de firma de certificado que genera el servidor.

En ella se incluyen los datos de identificación del servidor y su clave pública.

La CSR se envía a nuestra CA para que compruebe la información y firme el certificado.

Es importante porque permite que la CA firme un certificado sin necesitar conocer ni recibir la clave privada del servidor.

## 4. Firmar el certificado del servidor

Una vez creada nuestra CA, utilizamos su clave privada para firmar la solicitud del servidor.

De esta forma conseguimos que el certificado del servidor esté respaldado por nuestra propia autoridad de certificación.

Esto permite que los clientes que confíen en nuestra CA también puedan confiar en los certificados que ha firmado.

## 5. Configurar HTTPS en Apache

Después configuramos Apache para que utilizara nuestro certificado y su correspondiente clave privada.

Apache es el servidor web que se encarga de mostrar las páginas web cuando un usuario accede a ellas.

La configuración de HTTPS permite que la comunicación entre el navegador y el servidor esté cifrada.

Utilizamos las directivas:

- SSLCertificateFile: indica dónde está el certificado del servidor.
- SSLCertificateKeyFile: indica dónde está la clave privada del servidor.

También comprobamos que la configuración fuera correcta antes de recargar Apache.

Esto es importante porque un error en los archivos o en las rutas podría impedir que el servidor arrancase correctamente.

## 6. Confiar en nuestra CA desde el navegador

Aunque el certificado del servidor estaba correctamente firmado, el navegador todavía no confiaba en él.

Esto ocurre porque nuestra CA es privada y no está incluida entre las autoridades de certificación que los navegadores reconocen de forma predeterminada.

Por eso tuvimos que importar el certificado público ca.crt en el almacén de autoridades de certificación raíz de confianza de Windows.

Una vez importado, el navegador ya podía reconocer nuestra CA y aceptar los certificados que había firmado.

## 7. Comprobar la conexión con OpenSSL

Utilizamos OpenSSL para comprobar que la conexión HTTPS funcionaba correctamente.

También analizamos la información del certificado, su emisor, el tipo de cifrado y el protocolo TLS utilizado.

Una de las comprobaciones más importantes fue realizar la conexión indicando nuestra CA mediante el parámetro -CAfile.

Cuando lo hicimos, obtuvimos:

Verify return code: 0 (ok)

Esto significa que el certificado se había validado correctamente.

Después repetimos la prueba sin indicar nuestra CA y apareció un error de verificación.

Esto nos permitió comprobar que el sistema necesitaba conocer nuestra CA para confiar en el certificado.

## 8. Copia de seguridad de la CA

Al terminar la primera parte creamos una copia de seguridad de nuestra carpeta de la CA utilizando tar.

Lo hicimos porque contiene los archivos necesarios para poder seguir utilizando nuestra autoridad de certificación más adelante.

Es especialmente importante conservar la clave privada ca.key, pero también protegerla correctamente.

La copia de seguridad no se debe publicar en GitHub porque contiene información privada.

## 9. Riesgos de seguridad de una CA propia

Durante esta parte también aprendimos algunos riesgos:

- Si alguien roba ca.key, puede utilizarla para firmar certificados falsos.
- Si descargamos ca.crt mediante HTTP sin comprobar su huella, alguien podría sustituirlo por otro certificado.
- No es recomendable guardar la CA y el servidor web en la misma máquina porque, si alguien consigue acceder al servidor, también podría intentar robar la clave privada de la CA.

Por eso, en una empresa real, la CA debe estar especialmente protegida y, preferiblemente, separada de los servidores que utilizan sus certificados.

---

# PARTE 2. Utilizar Let's Encrypt con DuckDNS y acme.sh

## 1. ¿Qué es Let's Encrypt?

En la segunda parte hemos utilizado Let's Encrypt para conseguir un certificado digital reconocido públicamente.

La diferencia respecto a la primera parte es que ya no somos nosotros quienes firmamos el certificado, sino una autoridad de certificación externa en la que los navegadores ya confían.

Esto significa que no necesitamos importar manualmente ningún certificado raíz en Windows.

Let's Encrypt permite obtener certificados gratuitos para utilizar HTTPS en páginas web públicas.

## 2. Crear un dominio con DuckDNS

Primero creamos un dominio gratuito en DuckDNS:

torrent-marcos.duckdns.org

DuckDNS es un servicio que permite disponer de un nombre de dominio y asociarlo a una dirección IP.

Lo utilizamos porque Let's Encrypt necesita comprobar que tenemos el control del dominio para poder emitir el certificado.

En nuestro caso configuramos el dominio para que apuntase a la dirección IP de nuestro servidor.

Después comprobamos que el dominio se resolvía correctamente desde Ubuntu.

## 3. Instalar acme.sh

A continuación instalamos acme.sh.

Es una herramienta que permite solicitar, instalar y renovar certificados digitales de forma automática.

La utilizamos porque facilita mucho el trabajo con Let's Encrypt y evita tener que realizar manualmente todos los pasos de renovación.

Durante la instalación también se creó una tarea automática en cron.

Cron es un servicio de Linux que permite programar tareas para que se ejecuten automáticamente en determinados momentos.

En nuestro caso se utiliza para comprobar los certificados y gestionar sus renovaciones.

## 4. Seleccionar Let's Encrypt como autoridad de certificación

Después configuramos acme.sh para que utilizase Let's Encrypt como autoridad de certificación predeterminada.

Esto es necesario para asegurarnos de que las solicitudes de certificados se realizan utilizando la autoridad que queremos.

A partir de ese momento, acme.sh utiliza Let's Encrypt para gestionar nuestros certificados.

## 5. Solicitar el certificado mediante DNS-01

Este fue uno de los pasos más importantes de la segunda parte.

Utilizamos el método DNS-01 para demostrar a Let's Encrypt que controlamos nuestro dominio.

Para ello utilizamos el token que nos proporciona DuckDNS.

Guardamos el token en una variable de entorno para que acme.sh pudiera utilizarlo sin tener que escribirlo directamente en el comando de solicitud.

Después ejecutamos acme.sh para solicitar el certificado de nuestro dominio.

Durante el proceso, acme.sh creó un registro TXT en DuckDNS.

Let's Encrypt comprobó ese registro para verificar que teníamos el control del dominio.

Una vez realizada la comprobación correctamente, eliminó el registro temporal y emitió nuestro certificado.

Este método es especialmente útil porque permite validar el dominio sin tener que exponer directamente el servidor web a Internet para realizar la comprobación.

## 6. Instalar el certificado en Apache

Cuando conseguimos el certificado, tuvimos que instalarlo en una carpeta para que Apache pudiera utilizarlo.

Creamos el directorio /etc/ssl/torrent-le y copiamos allí los archivos necesarios mediante acme.sh.

Los dos archivos principales son:

- privkey.pem: contiene la clave privada del servidor.
- fullchain.pem: contiene el certificado del servidor junto con los certificados intermedios necesarios para formar la cadena de confianza.

También configuramos un comando para que Apache se recargase automáticamente cuando se instalase una renovación del certificado.

Por último, protegimos la clave privada utilizando permisos 600.

Esto significa que solamente el usuario propietario, en nuestro caso root, puede leer y modificar ese archivo.

## 7. Configurar Apache para utilizar Let's Encrypt

Después modificamos la configuración SSL de Apache.

Anteriormente utilizábamos el certificado que habíamos creado con nuestra CA propia.

Ahora cambiamos las rutas para que Apache utilizase el certificado de Let's Encrypt.

De esta forma conseguimos que nuestra página web utilizase un certificado reconocido públicamente.

Antes de aplicar los cambios ejecutamos apache2ctl configtest.

Este comando sirve para comprobar que la configuración de Apache no contiene errores.

Cuando obtuvimos Syntax OK, recargamos el servicio y comprobamos que seguía activo.

## 8. Comprobar el certificado de Let's Encrypt

Una vez configurado Apache, comprobamos que nuestro dominio funcionaba correctamente mediante HTTPS.

Utilizamos curl para consultar el servidor y revisar la información del certificado.

Comprobamos que:

- El certificado pertenecía a nuestro dominio.
- El emisor era Let's Encrypt.
- La verificación del certificado era correcta.
- El servidor respondía con HTTP 200 OK.

Esto significa que la conexión HTTPS funcionaba y que el certificado era reconocido por el sistema.

## 9. Comprobar la cadena de confianza

Después utilizamos OpenSSL para analizar la cadena de certificados.

Una cadena de confianza es el conjunto de certificados que permite comprobar que el certificado del servidor está respaldado por una autoridad de certificación reconocida.

En nuestro caso comprobamos que el certificado del dominio estaba emitido por una autoridad intermedia de Let's Encrypt y que la cadena podía validarse hasta una raíz de confianza de ISRG.

El resultado importante fue:

Verify return code: 0 (ok)

Esto significa que OpenSSL pudo verificar correctamente la cadena de confianza.

También comprobamos que las raíces de ISRG estaban instaladas en el sistema.

## 10. Comprobar la renovación automática

El último paso técnico fue comprobar que la renovación automática estaba configurada.

Para ello revisamos la tarea programada de cron y utilizamos acme.sh --list para consultar los certificados gestionados.

Comprobamos que aparecía nuestro dominio y que Let's Encrypt era la autoridad utilizada.

También pudimos ver la fecha de creación y la fecha prevista para la renovación.

Esto es importante porque los certificados de Let's Encrypt tienen una duración limitada y deben renovarse periódicamente.

Gracias a acme.sh y cron, este proceso se puede realizar automáticamente sin tener que repetir todos los pasos manualmente.

---

# COMPARACIÓN ENTRE NUESTRA CA Y LET'S ENCRYPT

Después de realizar las dos partes podemos entender mejor sus diferencias.

Nuestra CA propia nos permite controlar completamente la emisión de certificados y es útil para redes internas, laboratorios o empresas que necesitan gestionar sus propios servicios.

Sin embargo, los navegadores no confían automáticamente en ella. Tenemos que instalar su certificado raíz en los equipos que quieran utilizarla.

Por otro lado, Let's Encrypt es una autoridad de certificación pública que ya está reconocida por los navegadores.

Esto hace que sea mucho más cómoda para páginas web públicas, ya que los usuarios no tienen que instalar nada.

La principal diferencia es que con nuestra CA gestionamos nosotros la confianza, mientras que con Let's Encrypt confiamos en una autoridad externa reconocida.

---

# CONCLUSIÓN FINAL

Con esta práctica hemos aprendido cómo funcionan los certificados digitales y por qué son importantes para proteger las conexiones HTTPS.

En la primera parte creamos nuestra propia autoridad de certificación, generamos y firmamos certificados y configuramos Apache para utilizar HTTPS. También aprendimos que los navegadores necesitan confiar en nuestra CA para aceptar sus certificados.

En la segunda parte utilizamos DuckDNS, Let's Encrypt y acme.sh para conseguir un certificado público y configurar su instalación y renovación automática.

Lo más importante que hemos aprendido es que HTTPS no consiste solamente en cifrar la conexión, sino que también permite comprobar la identidad del servidor al que nos estamos conectando.

También hemos visto la importancia de proteger las claves privadas, mantener las autoridades de certificación seguras y automatizar las renovaciones para evitar que los certificados caduquen.

En definitiva, ahora sabemos crear y gestionar certificados digitales tanto para entornos privados como para páginas web que necesitan certificados reconocidos públicamente.
