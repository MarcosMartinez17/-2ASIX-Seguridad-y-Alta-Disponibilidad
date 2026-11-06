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

### Paso 7 

