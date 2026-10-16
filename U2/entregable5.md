## Parte A · Entendiendo las amenazas

| Tipo | Breve descripción |
|---|---|
| **Adware** | Programa que muestra publicidad no deseada en el ordenador. |
| **Backdoor** | Puerta trasera que permite acceder a un sistema sin autorización. |
| **Bomba Fork** | Ataque que crea muchos procesos hasta saturar o bloquear el ordenador. |
| **Bots** | Programas automáticos que pueden ser controlados por un atacante para realizar acciones maliciosas. |
| **Bug** | Error en un programa que puede provocar fallos o problemas de seguridad. |
| **Caballo de Troya** | Programa malicioso que se hace pasar por uno legítimo para engañar al usuario. |
| **Cookies** | Archivos que guardan información de la navegación y pueden utilizarse para rastrear al usuario. |
| **Crackers** | Personas que vulneran sistemas informáticos para acceder sin permiso, robar información o causar daños. |
| **Cryptovirus** | Malware que cifra los archivos del usuario para impedir que pueda acceder a ellos. |
| **Ransomware** | Malware que bloquea archivos o sistemas y exige un rescate para recuperarlos. |
| **Exploits** | Programas o técnicas que aprovechan vulnerabilidades de seguridad. |
| **Hijacker** | Programa que modifica la configuración del navegador y redirige a páginas no deseadas. |
| **Hoax** | Mensaje falso que busca engañar o alarmar al usuario. |
| **Keystroke** | Programa espía que registra las teclas pulsadas para conseguir contraseñas y datos personales. |
| **Ladilla virtual** | Malware asociado a descargas o aplicaciones de contenido para adultos. |
| **Leapfrog** | Programa malicioso que roba datos de acceso y contactos para utilizarlos o propagarse. |
| **Parásito informático** | Programa malicioso que se une a otros archivos y se propaga cuando se ejecutan. |
| **Pharming** | Ataque que redirige al usuario a páginas falsas para robar información. |
| **Phishing** | Engaño mediante correos, mensajes o páginas falsas para conseguir contraseñas y datos personales. |
| **Rabbit** | Multiplica procesos sin fin hasta agotar los recursos del sistema y bloquearlo. |
| **Riskware** | Software legítimo con vulnerabilidades que pueden suponer un riesgo de seguridad. |
| **Rootkit** | Oculta su presencia y otorga acceso y control de administrador al atacante. |
| **Scumware** | Programas molestos o de dudosa utilidad instalados sin consentimiento claro. |
| **Spam** | Mensajes masivos y no solicitados (correo basura). |
| **Spyware** | Programa espía que recopila y roba información confidencial en secreto. |
| **Pop-ups** | Ventanas emergentes intrusivas usadas para publicidad no deseada o engaños. |
| **Worms** | Gusanos que se autorreplican y propagan rápidamente por la red de forma autónoma. |
| **bomba logica** | fragmento de código malicioso oculto dentro de un programa, sistema operativo o red, que permanece inactivo hasta que se cumple una condición preprogramada específica para ejecutar un daño |

 ## Parte B · Clasificar amenazas

 | # | Incidente en Torrent | Tipo | ¿Se replica solo? | ¿Necesita al usuario? |
| :---: | :--- | :--- | :---: | :---: |
| **1** | Un socio abre `factura_marzo.pdf.exe`; poco después todo el disco compartido está cifrado y aparece un mensaje pidiendo 800 € | **Troyano, Ransomware** | No | Sí |
| **2** | Un "optimizador gratuito" de PC que, además, envía todo lo que se teclea a un servidor externo | **Keylogger** | No | Sí |
| **3** | Sin que nadie haga nada, un programa salta de portátil en portátil a través de la wifi del estudio | **Gusano** | Sí | No |
| **4** | Al abrir ciertos ejecutables, éstos infectan a su vez a otros ficheros del equipo | **Virus** | No | Sí |
| **5** | Tras instalar un editor de PDF gratuito, aparecen ventanas de publicidad constantemente | **Adware** | No | Sí |
| **6** | Un antiguo colaborador dejó un código que borrará los planos el 31 de diciembre | **Bomba lógica** | No | No |
| **7** | El ordenador del estudio está enviando spam sin que nadie lo sepa, bajo las órdenes de un servidor externo | **Bot/zombi** | No | No |
| **8** | Un conjunto de programas modificados que esconde procesos y ficheros del intruso dentro del propio sistema | **Rootkit** | No | No |
| **9** | Un servicio escuchando en un puerto que permite entrar al equipo sin contraseña | **Backdoor** | No | No |
| **10** | Correo: "Tu banco necesita que confirmes tus datos en este enlace" | **Phishing** | No | Sí |
| **11** | Mensaje: "Reenvía esto a 10 contactos o tu cuenta se cerrará" | **Hoax (bulo)** | No | Sí |
| **12** | Una web idéntica a la del ERP a la que se llega porque el DNS ha sido manipulado | **Pharming** | No | Sí |
| **13** | Un programa que registra sin permiso los hábitos de navegación y los vende | **Spyware** | No | No |

Pregunta A: ¿Cuáles de estos incidentes no son "malware" propiamente dicho, sino fraude o ingeniería social? ¿Por qué un antivirus no los detendría?

Incidente 10 (Phishing): Intento de suplantación de identidad mediante correo engañoso.
Incidente 11 (Hoax / Bulo): Mensaje engañoso en cadena para causar alarma o desinformación, sin código malicioso.
Incidente 12 (Pharming): Redirección a una web falsa para engañar al usuario y obtener sus credenciales.

¿Por qué no los detiene un antivirus?
Un antivirus tradicional está diseñado para detectar código ejecutable o archivos maliciosos en el sistema local.

Estas tres amenazas no infectan el equipo con programas dañinos, sino que explotan la confianza y manipulación del usuario (ingeniería social) o alteran la resolución de nombres en la red. Dado que es el propio usuario quien accede, introduce sus datos o reenvía el mensaje de forma voluntaria, el antivirus no identifica ninguna ejecución de software dañino que deba bloquear.


