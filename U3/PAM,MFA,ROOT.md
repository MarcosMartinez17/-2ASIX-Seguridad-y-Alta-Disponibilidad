## Parte 1 — Contraseñas robustas con PAM

### Paso 1 instalar pwquality

sudo apt update
sudo apt install libpam-pwquality -y

Comprobamos:
dpkg -s libpam-pwquality | grep Status
<img width="563" height="69" alt="image" src="https://github.com/user-attachments/assets/c82c2b1d-42db-497e-984f-33ba90955a0d" />


### Paso 2 Comprobar que PAM utiliza pwquality
grep -nE 'pam_pwquality.so|pam_unix.so' /etc/pam.d/common-password
Esto nos permitirá verificar que pam_pwquality.so aparece antes de pam_unix.so
<img width="964" height="65" alt="image" src="https://github.com/user-attachments/assets/953a4ec1-f459-4331-a175-9fa490fa4a72" />

2.2. Modificar la configuración
sudo nano /etc/security/pwquality.conf
<img width="383" height="219" alt="image" src="https://github.com/user-attachments/assets/ae99e2a8-c84e-48fc-a403-8ba24b876110" />

Explicación:
minlen = 12: establece una longitud mínima de 12 caracteres
minclass = 3: exige al menos tres tipos distintos de caracteres
dcredit, ucredit, lcredit y ocredit: obligan a utilizar números, mayúsculas, minúsculas y símbolos
dictcheck = 1: comprueba que la contraseña no se base en palabras de diccionario
maxrepeat = 3: limita la repetición consecutiva de caracteres
enforce_for_root: aplica los requisitos de calidad también a root
Con esta configuración buscamos evitar que los usuarios establezcan contraseñas débiles

### Paso 3. Comprobar que no hay parámetros duplicados
<img width="957" height="249" alt="image" src="https://github.com/user-attachments/assets/aeac7f79-fe6e-4bf2-b283-c16067feeba8" />
Deben aparecer los nueve parametros solo una vez

### Paso 4 Probar una contraseña débil con Hugo
<img width="665" height="92" alt="image" src="https://github.com/user-attachments/assets/8de0fcd0-ad7e-4005-9aa6-351185f31b88" />
Contraseña probada: 1234

### Paso 5 Establecer una contraseña segura para Hugo
Contra segura: Contraseña_Complicada1@
<img width="850" height="185" alt="image" src="https://github.com/user-attachments/assets/4d60e468-bc43-4f27-a402-7d47a7654ac2" />

### Paso 6 Comprobar la contraseña de Hugo
<img width="390" height="50" alt="image" src="https://github.com/user-attachments/assets/1b5f269c-3434-469d-a480-ec18b399a91e" />
Si aparece P en el estado de la contraseña significa que el usuario tiene una contraseña establecida

### Paso 7 Configurar la caducidad con chage
<img width="651" height="187" alt="image" src="https://github.com/user-attachments/assets/21069929-6d8a-4c75-9ed8-a3546fe0f72d" />
<img width="668" height="166" alt="image" src="https://github.com/user-attachments/assets/fc21b448-d027-4b57-b40d-c441885c726f" />
<img width="653" height="187" alt="image" src="https://github.com/user-attachments/assets/6a3fce8a-df60-4b66-8b54-e811d263c729" />

Explicación
- chage: administra la caducidad de las contraseñas.
- -m 1: obliga a esperar al menos un día entre cambios
- -M 60 o -M 90: establece el número máximo de días de validez
- -W 7: avisa al usuario siete días antes de que caduque


