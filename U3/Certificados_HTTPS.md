## 0.1 Antes de empezar: qué necesita

Opcion B elegimos, IP_SERVIDOR = 192.168.56.100

## Parte 1 — Paso 0: instalar Apache y comprobar HTTP
sudo apt update
sudo apt install -y apache2 openssl curl cron

<img width="950" height="322" alt="image" src="https://github.com/user-attachments/assets/00015335-a3f4-4963-b8f8-d6b2e8c683a9" />

<img width="937" height="268" alt="image" src="https://github.com/user-attachments/assets/1cb54149-f0fe-4a6b-85ec-e563644b271b" />

### Paso 1 — Crear la carpeta de trabajo para la CA

<img width="542" height="192" alt="image" src="https://github.com/user-attachments/assets/04e95c36-3954-498a-8253-7f398b336d84" />

openssl genrsa -aes256 -out private/ca.key 4096
Contraseña: 123456

<img width="546" height="50" alt="image" src="https://github.com/user-attachments/assets/7cbea33b-583b-4cbf-9ed3-ad718663c0c9" />

### Paso 3 — crear el certificado raíz de la CA

<img width="783" height="280" alt="image" src="https://github.com/user-attachments/assets/6433c0e7-e07b-47e2-9607-b05ccd13a1de" />

En el resultado podemos ver que subject e issuer son iguales, lo que confirma que se trata de un certificado raíz autofirmado.

### Paso 4 — Inspeccionar el certificado
<img width="882" height="62" alt="image" src="https://github.com/user-attachments/assets/125fff5c-ef86-4a15-b9e8-7205bf5f3192" />

Eso significa que este certificado puede actuar como Autoridad de Certificación y firmar otros certificados. Es exactamente la comprobación que marca el Paso 4 de la guía.

### Paso 5 — Crear la clave privada del servidor y la CSR

<img width="693" height="138" alt="image" src="https://github.com/user-attachments/assets/eeb1081a-db94-4d18-b926-4d49f999c891" />

Qué estamos haciendo
- srv.key → clave privada del servidor Apache.
- srv.csr → petición para que nuestra CA firme el certificado.
- La CSR contiene la identidad y la clave pública, pero no contiene la clave privada.

### Paso 6 — Crear el fichero SAN

<img width="561" height="103" alt="image" src="https://github.com/user-attachments/assets/f13bf8ad-4119-4225-a1e4-e40210245094" />

El campo SAN indica los nombres e IP por los que el navegador aceptará el certificado. CA:FALSE indica que este certificado es de servidor y no puede firmar otros certificados.

### Paso 7 — Firmar el certificado del servidor con nuestra CA.

<img width="552" height="173" alt="image" src="https://github.com/user-attachments/assets/28dd33b2-258a-414e-8ecc-ecdd7814200c" />

El fichero srv.crt es el certificado del servidor firmado por nuestra CA y tendrá una validez de 365 días.

### Paso 8 — Verificar la cadena de confianza

<img width="891" height="147" alt="image" src="https://github.com/user-attachments/assets/328f43f2-cd83-4e91-a399-165961dac1c0" />

Aquí queremos comprobar tres cosas:
- Subject → debe ser hmi.torrent.local.
- Issuer → debe ser Torrent S.L. CA Raiz.
- SAN → debe mostrar hmi.torrent.local y 192.168.56.100.

### Paso 9 — Instalar el certificado en Apache
<img width="731" height="62" alt="image" src="https://github.com/user-attachments/assets/a2d845d8-dad3-49a9-a799-186f4e05c3f7" />

La clave privada se protege con permisos 600 para que únicamente root pueda leerla.

<img width="940" height="182" alt="image" src="https://github.com/user-attachments/assets/d12c67ae-5760-484b-8177-ad7cdbab35d8" />

<img width="728" height="287" alt="image" src="https://github.com/user-attachments/assets/5411b62c-7dc3-4394-85bd-b50d2ff9792b" />

De esta forma Apache utilizará el certificado que hemos firmado con nuestra propia CA.

<img width="960" height="190" alt="image" src="https://github.com/user-attachments/assets/fe6a1ed4-32f3-4a80-a08b-af661c919542" />

Lo que queremos comprobar es que Apache está escuchando ahora también en el puerto 443, que es el puerto de HTTPS. La guía pide exactamente activar el sitio SSL, validar la configuración, reiniciar Apache y comprobar el puerto 443.

<img width="692" height="223" alt="image" src="https://github.com/user-attachments/assets/19d73914-4094-4468-bb3b-b08de9ae090c" />


### Paso 10 — probar HTTPS desde la propia VM.

Primera prueba: sin confiar en nuestra CA

<img width="691" height="51" alt="image" src="https://github.com/user-attachments/assets/7cdaf613-3ff0-40e7-b120-637eea6fc9c3" />

Ahora le vamos a indicar a curl qué CA debe considerar de confianza para esta conexión.

<img width="762" height="51" alt="image" src="https://github.com/user-attachments/assets/56d84e02-8079-4507-8d08-c68fce9f4ae9" />

### Paso 11 — probarlo en Firefox, ver la advertencia inicial, calcular la huella SHA-256 e importar la CA.

<img width="965" height="95" alt="image" src="https://github.com/user-attachments/assets/37b9ad52-cd56-49cb-8ae8-4ef59ee9562c" />

La huella nos permitirá comprobar que el certificado descargado desde el navegador es exactamente el mismo que hemos generado en la máquina virtual.

comprobar la advertencia en Firefox

<img width="885" height="548" alt="image" src="https://github.com/user-attachments/assets/8decf980-ce57-4c1d-8c65-c28b7d6c1805" />

<img width="761" height="250" alt="image" src="https://github.com/user-attachments/assets/493a968c-a66b-43f0-9f2a-f9e9300689fb" />


### Paso 11 — Importar la CA en Microsoft Edge

<img width="673" height="457" alt="image" src="https://github.com/user-attachments/assets/8f20196d-7431-4235-a5a4-bb93801e101d" />

<img width="521" height="95" alt="image" src="https://github.com/user-attachments/assets/94c5c900-83f3-4b5f-b52c-fe70fdeda562" />

<img width="572" height="236" alt="image" src="https://github.com/user-attachments/assets/c8b6647b-48d6-4864-9675-41920501a064" />

<img width="678" height="271" alt="image" src="https://github.com/user-attachments/assets/5e07abd0-aa60-4017-a383-3d289251872d" />


### Paso 12 — mirar la conexión TLS por dentro.

<img width="870" height="130" alt="image" src="https://github.com/user-attachments/assets/af45d4cd-4f76-4386-838b-d34c05497bb3" />

Eso confirma que:
- el certificado pertenece a hmi.torrent.local;
- está firmado por Torrent S.L. CA Raiz;
- se ha negociado TLS 1.3;
- el cifrado usado es TLS_AES_256_GCM_SHA384;
- Verify return code: 0 (ok) significa que la cadena de confianza se ha validado

### Paso 13 — Guardar una copia de la CA

<img width="732" height="71" alt="image" src="https://github.com/user-attachments/assets/b460831d-64e9-4950-b4e1-e378212c2662" />

La copia contiene todos los archivos de la CA y podrá reutilizarse más adelante.

No debemos subir este archivo a GitHub porque contiene información sensible, incluida la clave privada de la CA.

## 1.3 Preguntas de la Parte 1

### 1. Diferencia entre `ca.key`, `ca.crt`, `srv.key` y `srv.crt`

- `ca.key`: clave privada de la CA. Es secreta.
- `ca.crt`: certificado público de la CA. Se puede compartir.
- `srv.key`: clave privada del servidor. Es secreta.
- `srv.crt`: certificado público del servidor. Se puede compartir.

Las claves privadas nunca deben publicarse ni subirse a GitHub.

### 2. ¿Qué pasa si alguien roba `ca.key`?

Si alguien roba `ca.key` y puede usarla, podría crear certificados falsos firmados como si fueran de Torrent S.L.

Para recuperar la confianza habría que dejar de confiar en esa CA, crear una nueva CA con una nueva clave privada y volver a emitir los certificados.

### 3. ¿Por qué es peligroso descargar `ca.crt` por HTTP sin verificar la huella?

Porque HTTP no cifra ni protege la comunicación.
Un atacante situado en la misma red podría sustituir nuestro certificado por uno falso. Si lo instalamos sin comprobar la huella SHA-256, podríamos terminar confiando en la CA del atacante.

### 4. ¿Es buena idea tener la CA y el servidor web en la misma máquina?

No es lo más seguro.
Si un atacante compromete el servidor web, también podría acceder a la clave privada de la CA.
En un entorno real, la CA debería estar separada y mejor protegida que el servidor web.






