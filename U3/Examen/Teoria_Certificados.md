[U3_Teoria_Certificados_HTTPS.md](https://github.com/user-attachments/files/34681706/U3_Teoria_Certificados_HTTPS.md)
# U3 -- Certificados digitales y HTTPS: teoría

## 1. Introducción

En esta práctica hemos trabajado con certificados digitales para
proteger las comunicaciones entre un navegador y un servidor web
mediante HTTPS.

HTTPS es la versión segura de HTTP. Utiliza TLS para cifrar la
información que viaja entre el cliente y el servidor. De esta forma,
otras personas que puedan interceptar el tráfico de red no pueden leer
fácilmente los datos intercambiados ni modificarlos sin que se detecte.

Además del cifrado, los certificados digitales permiten comprobar la
identidad del servidor al que nos estamos conectando.

En la práctica se han utilizado dos formas de conseguir certificados:

-   Crear una Autoridad de Certificación (CA) propia y utilizarla para
    firmar el certificado del servidor.
-   Obtener un certificado gratuito de Let's Encrypt utilizando un
    dominio DuckDNS y la herramienta acme.sh.

## 2. Conceptos básicos

### 2.1. HTTP y HTTPS

HTTP es el protocolo utilizado normalmente para acceder a páginas web.
Su problema es que, por sí solo, no cifra la comunicación.

HTTPS añade una capa de seguridad mediante TLS. Sus objetivos
principales son:

-   **Confidencialidad:** los datos se cifran para que no puedan ser
    leídos fácilmente durante el tránsito.
-   **Integridad:** permite detectar modificaciones de los datos durante
    la comunicación.
-   **Autenticación:** permite comprobar que el servidor presenta un
    certificado válido para el dominio al que nos conectamos.

HTTPS protege la comunicación, pero no garantiza que una página sea
honesta ni que el servidor esté libre de malware o vulnerabilidades.

### 2.2. Cifrado simétrico y asimétrico

El cifrado simétrico utiliza una misma clave para cifrar y descifrar la
información. Es rápido y se utiliza para proteger los datos durante la
sesión HTTPS.

El cifrado asimétrico utiliza dos claves relacionadas:

-   **Clave privada:** debe mantenerse en secreto. Su propietario la
    utiliza para demostrar que controla la identidad asociada y para
    operaciones criptográficas.
-   **Clave pública:** se puede compartir. Forma parte del certificado y
    permite realizar operaciones de verificación o establecer mecanismos
    criptográficos.

En una conexión TLS moderna se utilizan mecanismos de clave pública para
autenticar el servidor y establecer secretos de sesión. Después, la
comunicación se cifra principalmente mediante algoritmos simétricos.

### 2.3. Certificado digital

Un certificado digital es un documento electrónico que relaciona una
identidad (por ejemplo, un nombre de dominio) con una clave pública.

Entre sus datos puede incluir:

-   Nombre del dominio o identidad del titular.
-   Clave pública.
-   Entidad que ha emitido y firmado el certificado.
-   Fechas de inicio y fin de validez.
-   Número de serie y otros datos técnicos.
-   Firma digital de la entidad emisora.

El certificado es público. La clave privada correspondiente no debe
compartirse.

### 2.4. Autoridad de Certificación (CA)

Una Autoridad de Certificación, también llamada CA, es una entidad que
firma certificados digitales después de realizar las comprobaciones que
correspondan.

Los sistemas operativos y navegadores incluyen una lista de autoridades
en las que confían. Cuando un servidor presenta un certificado, el
navegador comprueba, entre otras cosas, que la cadena de firmas llegue a
una autoridad de confianza y que el certificado sea válido para el
dominio.

Una empresa también puede crear su propia CA para servicios internos. En
ese caso, los equipos que deban confiar en sus certificados tienen que
instalar o recibir de forma segura el certificado público de esa CA.

## 3. Crear una CA propia

En la primera parte de la práctica creamos una CA llamada **Torrent S.L.
CA Raiz**.

La CA dispone de su propio par de claves y de un certificado:

  -----------------------------------------------------------------------
  Archivo                             Función
  ----------------------------------- -----------------------------------
  `ca.key`                            Clave privada de la CA. Debe
                                      permanecer secreta y protegida.

  `ca.crt`                            Certificado público de la CA. Se
                                      instala en los equipos que deben
                                      confiar en ella.

  `srv.key`                           Clave privada del servidor web. No
                                      se debe compartir.

  `srv.csr`                           Solicitud de firma de certificado
                                      generada por el servidor.

  `srv.crt`                           Certificado del servidor firmado
                                      por nuestra CA.
  -----------------------------------------------------------------------

### 3.1. Solicitud de firma (CSR)

Una CSR (*Certificate Signing Request*) es una solicitud que contiene la
clave pública del servidor y los datos de identidad que se quieren
incluir en el certificado.

El servidor genera su clave privada y, a partir de ella, crea la CSR. La
clave privada no se envía a la CA.

La CA revisa la solicitud y firma el certificado. Así se obtiene un
certificado de servidor que puede validarse mediante el certificado
público de nuestra CA.

### 3.2. Firma del certificado

La CA utiliza su clave privada para firmar el certificado del servidor.
Los clientes pueden comprobar esa firma utilizando el certificado
público de la CA.

Si alguien consigue la clave privada de la CA (`ca.key`), podría emitir
certificados falsos que aparentasen estar firmados por nuestra entidad.
Por eso es especialmente importante protegerla y mantener copias de
seguridad seguras.

### 3.3. Confianza del navegador

Un certificado firmado por una CA propia no es reconocido
automáticamente por los navegadores. Para que Windows y Edge confíen en
él, instalamos el certificado público `ca.crt` en el almacén de
**Entidades de certificación raíz de confianza**.

La instalación debe hacerse por un canal seguro. Si descargamos el
certificado de una CA sin comprobar su huella digital, un atacante
podría sustituirlo por otro y engañarnos.

## 4. HTTPS en Apache con una CA propia

Apache puede ofrecer conexiones HTTPS cuando tiene habilitado el módulo
SSL y dispone de un certificado de servidor y de su clave privada.

En la configuración del sitio HTTPS se indican, entre otros parámetros:

-   `SSLCertificateFile`: ruta del certificado del servidor.
-   `SSLCertificateKeyFile`: ruta de la clave privada correspondiente.

Después de modificar la configuración hay que comprobar su sintaxis y
recargar Apache.

Para verificar el certificado se puede utilizar OpenSSL. Si indicamos el
certificado público de nuestra CA mediante `-CAfile`, OpenSSL puede
validar la cadena aunque esa CA no forme parte de las autoridades
públicas del sistema.

Un resultado `Verify return code: 0 (ok)` indica que la verificación se
ha completado correctamente con las autoridades de confianza indicadas.

## 5. Let's Encrypt

Let's Encrypt es una Autoridad de Certificación pública y gratuita. Sus
certificados son reconocidos por los navegadores actuales porque su
cadena de confianza llega a autoridades incluidas en los almacenes de
confianza habituales.

Los certificados de Let's Encrypt tienen una validez corta, normalmente
de unos 90 días. Esto reduce el tiempo durante el que un certificado o
una clave comprometidos podrían seguir utilizándose y hace importante
automatizar la renovación.

A diferencia de una CA privada creada para una empresa, no necesitamos
instalar manualmente el certificado raíz de Let's Encrypt en los equipos
normales.

## 6. DuckDNS y los nombres de dominio

DuckDNS es un servicio de DNS dinámico que permite disponer de un nombre
de dominio, por ejemplo:

`torrent-marcos.duckdns.org`

El nombre se asocia a una dirección IP. En la práctica lo utilizamos
para identificar el servidor mediante un nombre de dominio y solicitar
un certificado para ese nombre.

El dominio debe resolver correctamente para que los mecanismos de
validación puedan comprobar que controlamos el nombre solicitado.

El token de DuckDNS permite actualizar o validar el dominio. Es un dato
secreto: no debe publicarse en GitHub ni compartirse.

## 7. Validación DNS-01

Let's Encrypt necesita comprobar que quien solicita el certificado
controla el dominio.

En la validación **DNS-01**, el cliente ACME crea un registro TXT
especial en la zona DNS del dominio. Let's Encrypt consulta ese registro
y, si el contenido es correcto, considera validado el control del
dominio.

Esta validación es útil cuando el servidor no está publicado
directamente en Internet mediante el puerto 80. La validación DNS-01 no
significa que cualquier persona de Internet pueda acceder al servidor
web: la validación del dominio y la accesibilidad del servicio web son
cuestiones distintas.

## 8. ACME y acme.sh

ACME es el protocolo utilizado para automatizar la solicitud,
validación, emisión y renovación de certificados.

`acme.sh` es un cliente ACME que permite realizar estas operaciones
desde la terminal. En nuestra práctica lo instalamos como usuario
`root`, por lo que sus archivos y tareas programadas quedan asociados a
ese usuario.

Funciones principales:

-   Solicitar un certificado a Let's Encrypt.
-   Realizar la validación DNS-01 mediante DuckDNS.
-   Guardar los certificados y claves generados.
-   Instalar el certificado en las rutas utilizadas por Apache.
-   Ejecutar automáticamente las renovaciones mediante una tarea
    programada (*cron*).

La clave privada del certificado debe protegerse con permisos adecuados.
En la práctica se establecieron permisos `600` para el archivo de clave
privada.

## 9. Cadena de confianza

Cuando un navegador recibe un certificado, no se limita a comprobar que
exista. También verifica la cadena de confianza:

1.  El servidor presenta su certificado.
2.  El certificado está firmado por una autoridad intermedia.
3.  La autoridad intermedia está vinculada a una autoridad raíz de
    confianza.
4.  El navegador comprueba las firmas, las fechas y que el nombre del
    dominio coincida.

Si todo es correcto, el navegador muestra la conexión como segura. En
OpenSSL, un resultado `Verify return code: 0 (ok)` indica que la
verificación de la cadena ha sido satisfactoria.

## 10. Renovación automática

Como los certificados de Let's Encrypt caducan aproximadamente a los 90
días, es necesario renovarlos antes de que expiren.

`acme.sh` instala una tarea programada en el usuario con el que se
ejecutó la instalación. En nuestro caso se instaló como `root`, por lo
que debemos consultar su cron y ejecutar los comandos de gestión desde
ese usuario.

Al instalar el certificado para Apache también se configuró un comando
de recarga. Cuando el certificado se renueva y se instala, Apache se
recarga para utilizar los nuevos archivos.

La renovación automática no elimina la necesidad de comprobar
periódicamente que el dominio, el token DNS, la tarea cron y Apache
siguen funcionando.

## 11. Comparación: CA propia y Let's Encrypt

  -----------------------------------------------------------------------
  Característica          CA propia               Let's Encrypt
  ----------------------- ----------------------- -----------------------
  Emisor                  Nuestra organización    Autoridad pública Let's
                                                  Encrypt

  Confianza inicial del   No, hay que instalar la Sí, normalmente está
  navegador               CA propia               reconocida por los
                                                  navegadores

  Uso habitual            Servicios internos,     Sitios web con un
                          laboratorios, VPN y     dominio validable
                          equipos propios         

  Validez                 La que se establezca al Normalmente unos 90
                          emitir el certificado   días

  Renovación              La gestionamos nosotros Se puede automatizar
                                                  con acme.sh

  Validación              La realiza nuestra CA   Let's Encrypt comprueba
                                                  el control del dominio

  Dependencia de Internet Puede funcionar en una  Se necesita conexión
                          red interna sin         para emitir y renovar
                          Internet una vez        
                          desplegada              
  -----------------------------------------------------------------------

## 12. Seguridad y buenas prácticas

-   No compartir nunca las claves privadas (`ca.key`, `srv.key` o la
    clave privada de Let's Encrypt).
-   No subir claves privadas, tokens ni copias de seguridad que los
    contengan a GitHub.
-   Proteger las claves con permisos restrictivos.
-   Guardar la clave privada de la CA en un lugar seguro y separado del
    servidor web siempre que sea posible.
-   Comprobar la huella del certificado de la CA antes de instalarlo en
    otros equipos.
-   Renovar los certificados antes de que caduquen.
-   Revisar que Apache utiliza las rutas correctas y que su
    configuración no contiene errores.
-   Eliminar del almacén de confianza los certificados de CA de
    laboratorio cuando ya no se necesiten, si procede.

Si se compromete la clave privada de una CA, hay que dejar de confiar en
ella, retirar su certificado de los equipos afectados, crear una nueva
CA y emitir de nuevo los certificados necesarios.

## 13. Conclusión

En esta práctica hemos aprendido que HTTPS necesita certificados
digitales para autenticar al servidor y proteger la comunicación. Hemos
comprobado dos métodos diferentes: una CA propia, que permite controlar
los certificados de una organización, y Let's Encrypt, que ofrece
certificados públicos reconocidos por los navegadores.

La CA propia es útil para redes internas y entornos controlados. Let's
Encrypt es una solución práctica para servicios asociados a un dominio,
especialmente cuando se automatiza la renovación. En ambos casos,
proteger las claves privadas y comprobar correctamente los certificados
es fundamental.
