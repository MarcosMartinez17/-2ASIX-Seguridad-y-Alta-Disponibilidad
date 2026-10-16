## UNIDAD 1 

## 1. Seguridad informática

La **seguridad informática** es el conjunto de medidas y controles que protegen los sistemas informáticos y su información para garantizar:

- **Confidencialidad:** que solo accedan a los datos las personas autorizadas.
- **Integridad:** que los datos no se modifiquen sin autorización.
- **Disponibilidad:** que la información y los sistemas estén accesibles cuando se necesiten.

También puede comprender autenticidad, responsabilidad, fiabilidad y no repudio.

### ¿Qué debemos proteger?

- **Hardware:** ordenadores, servidores, procesadores, discos, cables y sistemas de almacenamiento.
- **Software:** sistemas operativos y aplicaciones.
- **Datos:** información almacenada, procesada y transmitida.
- **Otros activos:** personas, infraestructuras y materiales (papel, tinta, soportes físicos).

**La seguridad absoluta no existe.** Se busca detectar problemas y amenazas, utilizar adecuadamente los recursos, reducir pérdidas, recuperarse de incidentes y cumplir los requisitos legales y organizativos.

---

## 2. Tipos de seguridad informática

### 2.1. Seguridad física y lógica

| Tipo | Definición | Ejemplos |
|---|---|---|
| **Física** | Protege el hardware, los equipos y las instalaciones frente a amenazas físicas. | Cerraduras, cámaras, climatización, alarmas y protección contra incendios. |
| **Lógica** | Protege el acceso a los datos, programas y recursos informáticos. | Contraseñas, permisos, cifrado y cortafuegos. |

**Objetivos de la seguridad lógica:** restringir accesos, asegurar el uso correcto de programas y archivos, verificar la integridad en la transmisión y disponer de alternativas ante emergencias.

**Para memorizar:** física = instalaciones y equipos; lógica = acceso a información y programas.

### 2.2. Seguridad activa y pasiva

| Tipo | Definición | Ejemplos incluidos en las diapositivas |
|---|---|---|
| **Activa** | Medidas destinadas a evitar ataques o accesos no deseados. | Antivirus, cortafuegos, contraseñas y SAI. |
| **Pasiva** | Medidas para reducir o paliar los efectos de un ataque ya producido. | Copias de seguridad, particiones lógicas, cifrado y registros (*logs*). |

**Importante para el examen:** las diapositivas incluyen el cifrado también como medida activa y el SAI como activa, aunque determinados mecanismos pueden cumplir varias funciones según el contexto. Para responder según los apuntes, conoce los ejemplos que figuran en sus tablas.

**Diferencia principal:** activa = prevenir; pasiva = reducir las consecuencias.

---

## 3. Principios de la seguridad: CID

Los tres principios principales son **confidencialidad, integridad y disponibilidad** (**CID**, también conocidos como **CIA**).

### 3.1. Confidencialidad (C)

Garantiza que la información no sea divulgada ni consultada por personas sin autorización.

- **Ejemplo:** un empleado no puede consultar las nóminas de otros empleados.
- **Medidas:** contraseñas, control de accesos, cifrado, tarjetas y huellas dactilares.

### 3.2. Integridad (I)

Garantiza que los datos no sean modificados o alterados por personas no autorizadas.

- **Ejemplo:** impedir que alguien cambie las notas de un alumno.
- **Integridad de datos:** los datos mantienen su contenido correcto.
- **Integridad de origen:** se puede confiar en la procedencia de la información (relacionada con la autenticación).
- **Medidas:** controles de permisos, funciones hash y otros mecanismos de comprobación.

### 3.3. Disponibilidad (D)

Garantiza que los sistemas y la información estén disponibles cuando los usuarios autorizados los necesiten.

- **Ejemplo:** evitar que el servidor de un centro educativo quede fuera de servicio.
- **Medidas:** sistemas redundantes, copias de seguridad y SAI (*Sistema de Alimentación Ininterrumpida*).

### Otros conceptos

- **Fiabilidad:** probabilidad de que un sistema se comporte como se espera.
- **Confiabilidad:** capacidad del sistema para mantener la calidad del servicio, conservando la confidencialidad, integridad y disponibilidad según corresponda.

**Regla para memorizar:**

- **C:** ¿Quién puede leerlo?
- **I:** ¿Se ha modificado?
- **D:** ¿Se puede acceder cuando hace falta?

---

## 4. Vulnerabilidades y amenazas

### 4.1. Vulnerabilidad

Una **vulnerabilidad** es una debilidad que compromete la seguridad del sistema y que puede ser aprovechada por una amenaza.

| Origen de la vulnerabilidad | En qué consiste | Ejemplos del temario |
|---|---|---|
| **Diseño** | Problemas presentes desde la planificación o arquitectura. | Protocolos inseguros, políticas de seguridad deficientes. |
| **Implementación** | Problemas al desarrollar o implantar el sistema. | Errores de programación, puertas traseras, descuidos del fabricante. |
| **Uso** | Problemas de configuración o comportamiento de los usuarios. | Configuraciones inadecuadas, desconocimiento, contraseña en un post-it. |

### 4.2. Vulnerabilidad de día cero (*Zero-Day*)

Es una vulnerabilidad desconocida o no corregida por el fabricante, que puede aprovecharse **antes de disponer de un parche**.

- Es especialmente peligrosa porque puede explotarse sin que exista todavía una corrección disponible.
- El temario menciona el CCN-CERT como fuente de información sobre vulnerabilidades.

### 4.3. Amenaza

Una **amenaza** es una circunstancia o acción que puede causar un daño al sistema informático, generalmente aprovechando una vulnerabilidad.

**No confundas:**

- **Vulnerabilidad = debilidad.**
- **Amenaza = lo que puede aprovechar esa debilidad y causar daño.**

**Ejemplo:** contraseña escrita en un post-it (vulnerabilidad de uso); alguien la lee y entra en el sistema (amenaza que afecta a la confidencialidad).

### 4.4. Protección y gestión de vulnerabilidades

Medidas básicas:

1. Aplicar políticas de seguridad.
2. Actualizar sistemas y programas.
3. Realizar copias de seguridad.
4. Utilizar antivirus y cortafuegos.
5. Formar y concienciar a los usuarios.
6. Analizar periódicamente los sistemas, valorar impactos y registrar las correcciones.

**Herramientas mencionadas:** Nessus, Catbird y Conan Mobile.  
**Fuentes de información citadas en las diapositivas:** SecurityFocus, OSVDB, Secunia y CERT/CC.

### 4.5. Ejemplo de clasificación de una vulnerabilidad

| Vulnerabilidad | Origen | Amenaza que la aprovecha | Física/lógica | CID afectada |
|---|---|---|---|---|
| Contraseña en un post-it | Uso | Alguien lee la contraseña. | Física que deriva en lógica. | Confidencialidad. |
| Archivador sin llave | Uso | Robo o consulta de documentos. | Física. | Confidencialidad. |

---

## 5. Amenazas informáticas

### 5.1. Según su origen

| Tipo | Ejemplos |
|---|---|
| **Naturales** | Terremotos, incendios, inundaciones y explosiones. |
| **Agentes externos** | Virus, robos, ataques y disturbios. |
| **Agentes internos** | Errores humanos, empleados descuidados y mal uso de herramientas. |

También se pueden clasificar por su **naturaleza física o lógica** y su **intencionalidad** (accidentes, errores o acciones malintencionadas).

### 5.2. Naturaleza de las amenazas a la información

El temario diferencia el **flujo normal** de cuatro alteraciones: **interceptación, modificación, interrupción y fabricación**.

**Flujo normal:** el mensaje llega del emisor al receptor correctamente, sin intervención indebida; se cumplen confidencialidad, integridad y disponibilidad.

| Tipo | Qué sucede | Propiedad afectada según las diapositivas | Ejemplo |
|---|---|---|---|
| **Interceptación** | Un tercero accede a la información sin alterarla. | **Confidencialidad**. | Escuchar tráfico de red (*sniffing*). |
| **Modificación** | Un tercero accede y altera datos o recursos. | **Integridad** y, en el supuesto descrito, **confidencialidad**. | Cambiar datos de una base de datos. |
| **Interrupción** | Un recurso deja de estar disponible o queda inutilizable. | **Disponibilidad**. | Destruir un disco o provocar una caída del sistema. |
| **Fabricación** | Se crean datos u objetos falsos que simulan ser legítimos. | **Autenticidad**. | Falsificar un documento o un mensaje. |

**Matices de los apuntes:**

- Interceptación: no afecta necesariamente a integridad ni a disponibilidad.
- Modificación: los apuntes mantienen la disponibilidad, pero consideran comprometidas integridad y confidencialidad.
- Interrupción: puede conservar confidencialidad e integridad, pero afecta a disponibilidad.
- Fabricación: puede mantener CID en la información original, pero no garantiza autenticidad.

**Truco:** interceptar = leer; modificar = cambiar; interrumpir = impedir; fabricar = falsificar.

### 5.3. Amenazas físicas y protección

| Amenaza física | Medidas de protección |
|---|---|
| Desastres naturales, incendios, inundaciones | Emplazamiento seguro, materiales ignífugos, sistemas antiincendios y plan de emergencia. |
| Temperatura y humedad | Climatización, sensores y control ambiental. |
| Robos, vandalismo y sabotajes | Cerraduras, cámaras, guardias y control físico de acceso. |
| Apagones, sobretensiones y fallos eléctricos | SAI, generadores, protecciones eléctricas y cableado seguro. |
| Disturbios internos o externos | Vigilancia, control de accesos y planes de contingencia. |

### 5.4. Amenazas lógicas y protección

| Amenaza lógica | Medidas de protección |
|---|---|
| Acceso indebido a datos o programas | Autenticación fuerte, controles de acceso y ACL. |
| Robo o interceptación de información | Criptografía, VPN, HTTPS y SSH. |
| Modificación no autorizada de datos | Permisos, firma digital y registros. |
| Malware: virus, gusanos, troyanos, spyware | Antivirus, antispyware, cortafuegos e IDS/IPS. |
| Ataques de red: DoS, inyección, spoofing | Firewalls, IDS/IPS y políticas de red seguras. |
| Pérdida de disponibilidad | Backups, redundancia y planes de recuperación. |

---

## 6. Ingeniería social

La **ingeniería social** consiste en manipular a las personas para conseguir contraseñas, información, dinero o acceso a sistemas.

Los apuntes destacan la importancia del factor humano en incidentes de seguridad; incluyen estadísticas de INCIBE y del informe Verizon DBIR 2025. Lo esencial para el examen es reconocer **cómo funciona cada técnica**.

### 6.1. Técnicas por comunicación remota

| Técnica | Definición | Ejemplo |
|---|---|---|
| **Phishing** | Correo fraudulento que suplanta a una entidad confiable. | Email falso del banco. |
| **Vishing** | Engaño por llamada telefónica. | Llamada de un falso técnico. |
| **Smishing** | Engaño por SMS. | Mensaje falso de un servicio de paquetería. |
| **Pretexting** | Inventar un pretexto creíble para obtener información. | «Soy de informática y necesito tu clave». |
| **Fraude del CEO / BEC** | Suplantar a un directivo para pedir una acción urgente. | Solicitar una transferencia bancaria. |

### 6.2. Técnicas por presencia física

| Técnica | Definición | Ejemplo |
|---|---|---|
| **Shoulder surfing** | Observar por encima del hombro para obtener datos. | Mirar cómo escriben una contraseña. |
| **Tailgating / piggybacking** | Entrar en una zona restringida siguiendo a alguien autorizado. | Colarse detrás de un empleado. |
| **Basureo / trashing** | Buscar en la basura documentos y soportes sensibles. | Recuperar documentos con datos privados. |

### 6.3. Técnicas de manipulación psicológica

| Técnica | Definición | Ejemplo |
|---|---|---|
| **Baiting (cebo)** | Ofrecer algo que despierte la curiosidad de la víctima. | Dejar un USB infectado. |
| **Quid pro quo** | Ofrecer un beneficio a cambio de datos o acceso. | Supuesta asistencia técnica a cambio de credenciales. |
| **Ingeniería social inversa** | El atacante se presenta como alguien que puede ayudar y consigue que la víctima le contacte. | Técnico falso al que la víctima pide ayuda. |

**Truco:** phishing = email; vishing = voz; smishing = SMS.

---

## 7. Mecanismos básicos de seguridad

Las diapositivas enumeran: **autenticación, no repudio, autorización, administración, auditoría, registro y mantenimiento de la integridad de la información**.

### 7.1. Autenticación

Es el proceso de **verificar la identidad** de un usuario o sistema.

Tres factores:

1. **Algo que sabes:** contraseña o PIN.
2. **Algo que tienes:** tarjeta o dispositivo.
3. **Algo que eres:** huella dactilar u otro dato biométrico.

La autenticación de doble factor combina dos factores diferentes para mejorar la seguridad.

**Contraseñas:** deben ser robustas, largas, variadas y secretas; no deben compartirse ni dejarse visibles. Los apuntes mencionan una política de renovación periódica de **30-45 días**. Es una indicación del material, no una regla universal para todos los sistemas.

### 7.2. No repudio

Es el mecanismo que permite probar que las partes participaron en una comunicación e impide que nieguen su intervención.

- **No repudio en origen:** el emisor no puede negar que envió el mensaje.
- **No repudio en destino:** el receptor no puede negar que lo recibió.

**Ejemplo:** una firma digital puede servir para demostrar la autoría de una comunicación.

### 7.3. Autorización

Determina **qué, cómo y cuándo** puede utilizar los recursos un usuario previamente autenticado.

- Ejemplo: poder leer un documento, pero no modificarlo ni borrarlo.
- **Principio de mínimo privilegio:** dar a cada usuario únicamente los permisos necesarios para su trabajo.

### 7.4. Administración

Gestiona las autorizaciones, usuarios, recursos y reglas de seguridad. Los administradores convierten las políticas en configuraciones concretas.

### 7.5. Auditoría

Examina si los sistemas y mecanismos de seguridad cumplen las políticas establecidas. Puede ser periódica, manual o automatizada.

### 7.6. Registro

Almacena eventos de seguridad en **logs**, por ejemplo, intentos de acceso no autorizado. Los registros deben analizarse para que resulten útiles.

### 7.7. Mantenimiento de la integridad

Son procedimientos que buscan evitar cambios no autorizados en la información y comprobar que los datos llegan sin alteraciones.

**Técnicas citadas:** antivirus, cifrado y funciones hash.

### 7.8. Diferencias fundamentales

| Concepto | Pregunta que responde |
|---|---|
| **Autenticación** | ¿Quién eres? |
| **Autorización** | ¿Qué puedes hacer? |
| **Administración** | ¿Quién configura y gestiona los permisos? |
| **Auditoría** | ¿Se están cumpliendo las medidas de seguridad? |
| **Registro** | ¿Qué ocurrió en el sistema? |
| **No repudio** | ¿Se puede demostrar quién participó? |

### 7.9. Autenticidad frente a autenticación

| Autenticidad | Autenticación |
|---|---|
| Es una propiedad de la información o comunicación. | Es un proceso de comprobación de identidad. |
| Garantiza la procedencia u origen real. | Verifica quién es un usuario o sistema. |
| Ejemplo: certificado digital de un documento. | Ejemplo: introducir usuario y contraseña. |

### 7.10. CIDAN

Los apuntes amplían CID con dos propiedades:

- **C:** Confidencialidad.
- **I:** Integridad.
- **D:** Disponibilidad.
- **A:** Autenticidad.
- **N:** No repudio.

---

## 8. Análisis forense informático

Es un proceso de investigación de incidentes informáticos para reconstruir lo sucedido y preservar las pruebas.

### Las cinco fases (en este orden)

1. **Identificación:** detectar el incidente y determinar su alcance.
2. **Preservación:** aislar y proteger las evidencias para no alterarlas.
3. **Adquisición:** obtener una copia exacta, comprobada mediante un hash.
4. **Análisis:** investigar qué pasó, cómo y cuándo.
5. **Documentación:** elaborar un informe con las conclusiones.

**Hash:** valor usado para comprobar la integridad de los datos; permite verificar que una copia no se ha alterado respecto de la original.

**Para memorizar:** identificar → preservar → adquirir → analizar → documentar.

---

## 9. Modelo PDCA e ISO 27001

El **PDCA** (*Plan–Do–Check–Act*) es un ciclo de mejora continua para la gestión de la seguridad informática. Se relaciona con la norma **ISO 27001** y con un **SGSI** (Sistema de Gestión de Seguridad de la Información).

| Fase | Traducción | Qué se hace | Ejemplo |
|---|---|---|---|
| **PLAN** | Planificar | Definir alcance, objetivos, políticas, procesos y riesgos. | Diseñar una política de contraseñas. |
| **DO** | Hacer | Implantar y aplicar los controles de seguridad. | Configurar la política. |
| **CHECK** | Verificar | Revisar resultados y realizar auditorías. | Comprobar el cumplimiento. |
| **ACT** | Actuar | Aplicar correcciones y mejoras. | Modificar una norma que falla. |

El ciclo **se repite continuamente**, por eso permite mejorar la seguridad con el tiempo.

```text
PLAN (Planificar) → DO (Hacer) → CHECK (Verificar) → ACT (Actuar)
        ↑                                            |
        └────────── MEJORA CONTINUA ──────────────────┘
```

---

## 10. Políticas de seguridad

Una **política de seguridad** es un documento formal que define las directrices, objetivos y criterios de seguridad de la organización.

- Debe reflejar el compromiso de la dirección.
- Ha de difundirse entre los empleados y ser entendida por ellos.
- Debe definir responsabilidades, reglas de acceso y cómo proteger información y recursos.
- Se revisa y actualiza periódicamente.
- Sirve de base para crear **normas** y **procedimientos** concretos.

### 10.1. Qué debe contemplar

- Objetivos y relevancia de la seguridad de la información.
- Compromiso y responsabilidades de la dirección y el personal.
- Clasificación y control de los datos.
- Seguridad del personal.
- Seguridad física y ambiental.
- Gestión de accesos.
- Prevención y detección de malware.
- Gestión de actualizaciones.
- Copias de seguridad y planes de contingencia y recuperación.
- Administración de sistemas y documentación actualizada.

### 10.2. Etapas de elaboración y aplicación

1. **Identificar** necesidades, riesgos y consecuencias posibles.
2. **Establecer** reglas y procedimientos para afrontar los riesgos.
3. **Controlar** vulnerabilidades y detectar deficiencias.
4. **Definir** acciones y personas de contacto ante amenazas.

Además, es importante formar a los empleados para que conozcan y cumplan las reglas.

### 10.3. Política, normas, procedimientos y registros

| Nivel | Responde a | Responsable indicado en la última diapositiva | Ejemplo |
|---|---|---|---|
| **1. Política** | **Qué y por qué.** | Alta dirección; cambia poco. | «Habrá una política de contraseñas». |
| **2. Normas** | **Qué se exige exactamente.** | Responsable de seguridad. | «Contraseñas de mínimo 12 caracteres y caducidad cada 60 días». |
| **3. Procedimientos** | **Cómo hacerlo paso a paso.** | Equipo técnico. | Pasos para forzar el cambio en Active Directory. |
| **4. Registros / guías técnicas** | **Qué evidencia demuestra que se ha hecho.** | Puede generarse automáticamente. | Logs de cambios, checklist de auditoría. |

**Nota:** los valores «12 caracteres/60 días» son el *ejemplo de norma* de la última diapositiva, mientras que «30-45 días» se menciona antes al hablar de políticas de contraseñas. No son dos valores que deban aplicarse simultáneamente; son ejemplos distintos del material.

---

## 11. Prevención, detección y recuperación

Los mecanismos de seguridad también se clasifican según su función:

| Mecanismo | Para qué sirve | Ejemplos |
|---|---|---|
| **Prevención** | Intentar evitar incidentes. | Cifrado, autenticación y control de acceso. |
| **Detección** | Identificar intrusiones y anomalías. | IDS, supervisión y revisión de registros. |
| **Recuperación** | Restablecer el servicio después de un incidente. | Copias de seguridad y planes de recuperación. |

### Mecanismos preventivos que aparecen en el temario

- **Identificación y autenticación:** reconocer y verificar entidades o usuarios.
- **Control de acceso:** decidir qué recursos y operaciones están permitidos.
- **Separación:** aislar objetos con diferentes niveles de seguridad.
- **Seguridad en comunicaciones:** proteger el tráfico mediante tecnologías y protocolos seguros como SSH o Kerberos.

El análisis forense también contribuye a comprender el alcance de los incidentes para evitar que se repitan.

---

## 12. Repaso rápido: 20 conceptos imprescindibles

1. **Seguridad informática:** protección de sistemas, recursos e información.
2. **CID:** confidencialidad, integridad y disponibilidad.
3. **Confidencialidad:** solo acceden los autorizados.
4. **Integridad:** los datos no se alteran indebidamente.
5. **Disponibilidad:** la información puede utilizarse cuando se necesita.
6. **Seguridad física:** protege equipos e instalaciones.
7. **Seguridad lógica:** protege datos y accesos.
8. **Seguridad activa:** intenta prevenir ataques.
9. **Seguridad pasiva:** limita daños tras los ataques.
10. **Vulnerabilidad:** debilidad del sistema.
11. **Amenaza:** circunstancia o acción capaz de causar daño.
12. **Día cero:** vulnerabilidad desconocida o aún sin corrección.
13. **Interceptación:** acceso o lectura no autorizados.
14. **Modificación:** alteración de datos.
15. **Interrupción:** pérdida de disponibilidad.
16. **Fabricación:** creación de datos u objetos falsos.
17. **Ingeniería social:** manipulación de personas.
18. **Autenticación / autorización:** comprobar quién eres / determinar qué puedes hacer.
19. **CIDAN:** CID + autenticidad + no repudio.
20. **PDCA:** planificar → hacer → verificar → actuar.

**Secuencias para memorizar:**

- **Análisis forense:** identificación → preservación → adquisición → análisis → documentación.
- **Política → norma → procedimiento → registro:** qué y por qué → qué se exige → cómo se hace → evidencia.
- **Ingeniería social:** phishing (correo), vishing (llamada), smishing (SMS).

---

## UNIDAD 1 PRACTICA

## 13. Práctica: clasificación de vulnerabilidades detectadas con Nessus (captura 1)
<img width="1508" height="492" alt="image" src="https://github.com/user-attachments/assets/3fc2199d-a341-41a6-810a-31d162a7102f" />

### 13.1. Qué significa cada columna

- **Vulnerabilidad:** el fallo o debilidad que se ha detectado.
- **Severidad:** indica cuánto daño podría causar y ayuda a establecer prioridades de corrección.
- **CVSS (Common Vulnerability Scoring System):** sistema habitual para puntuar la gravedad de una vulnerabilidad entre **0 y 10**. Cuanto mayor sea la puntuación, mayor es la gravedad evaluada según ese sistema.
- **Origen (diseño / implementación / uso):** fase o circunstancia que explica el problema:
  - **Diseño:** el protocolo o la arquitectura ya nació con debilidades.
  - **Implementación:** falla la manera en que se desarrolló o implementó una función.
  - **Uso:** alguien configura, administra o utiliza mal un sistema.
- **Breve descripción:** explicación sencilla de cómo funciona el fallo y de qué permite hacer a un atacante.

**Tramos habituales de CVSS:** 0 = ninguna; 0,1–3,9 = baja; 4,0–6,9 = media; 7,0–8,9 = alta; 9,0–10,0 = crítica. La puntuación describe gravedad técnica, no es por sí sola todo el riesgo de una empresa.
---

## 14. Práctica: cinco niveles de clasificación de la información (captura 2)

<img width="1451" height="688" alt="image" src="https://github.com/user-attachments/assets/07214496-3436-4e80-805f-9724b7e853b6" />

### 14.1. Qué significa cada nivel

| Nivel | Nombre | Explicación sencilla | Ejemplo de uso |
|---|---|---|---|
| **1** | **Uso público** | Información destinada a cualquier persona; se puede publicar. | Catálogo de servicios de la web. |
| **2** | **Difusión limitada** | Información para un grupo concreto, normalmente interno; no está pensada para publicarse, pero su divulgación tendría un impacto pequeño. | Calendario de vacaciones en un correo interno. |
| **3** | **Confidencial** | Información privada o empresarial cuya consulta debe limitarse a personal autorizado. | Facturas de clientes. |
| **4** | **Reservada** | Información especialmente sensible; una filtración podría causar daños graves en privacidad, seguridad o funcionamiento. | Contraseña de ERP, planos privados, informe de vulnerabilidades. |
| **5** | **Secreta** | Información estratégica de máxima sensibilidad en el caso; una filtración puede perjudicar gravemente a la empresa. | Negociación confidencial de venta del estudio. |

**Diferencia entre niveles:** conforme se sube de nivel, la información es más sensible y se requiere un control de acceso más estricto.

---

## 15. Práctica: origen, amenaza, tipo físico/lógico y CID (captura 3)

<img width="1362" height="407" alt="image" src="https://github.com/user-attachments/assets/4984dbbb-cd28-4509-aaaa-e29ed4e41ff7" />


### 15.1. Significado de las columnas

| Columna | Qué hay que escribir |
|---|---|
| **Vulnerabilidad** | La debilidad concreta observada: por ejemplo, «ordenador sin bloquear». |
| **Origen** | **Diseño, implementación o uso**: de dónde procede esa debilidad. |
| **Amenaza que la explota** | La acción que aprovecharía el fallo: leer datos, robar documentos, cambiar archivos, etc. |
| **Física / Lógica** | Si afecta al **entorno físico** (personas, papeles, equipos) o a los **accesos y datos informáticos**; a veces empieza físicamente y termina en un acceso lógico. |
| **C-I-D afectada** | Qué principio queda comprometido: **confidencialidad**, **integridad** o **disponibilidad**. Puede haber más de uno. |

## Resumen – Cómo leer un vector CVSS

## 1. ¿Qué es CVSS?

**CVSS (Common Vulnerability Scoring System)** es un estándar para puntuar la **gravedad de una vulnerabilidad** del 0 al 10. Se utiliza en herramientas como **Nessus**.

| Puntuación | Severidad |
|---|---|
| 9.0 – 10.0 | Crítica |
| 7.0 – 8.9 | Alta |
| 4.0 – 6.9 | Media |
| 0.1 – 3.9 | Baja |
| 0.0 | Informativa / ninguna |

La nota tiene en cuenta dos preguntas:

1. **¿Qué tan fácil es explotar la vulnerabilidad?**
2. **¿Qué tan grave puede ser el daño?**

Un daño muy grande no implica siempre una nota crítica: si es difícil ejecutar el ataque, la puntuación puede ser menor.

---

## 2. Las ocho métricas de CVSS v3

| Sigla | Nombre en inglés | ¿Qué significa? |
|---|---|---|
| **AV** | Attack Vector | Desde dónde se puede atacar. |
| **AC** | Attack Complexity | Qué dificultad tiene el ataque. |
| **PR** | Privileges Required | Qué permisos necesita ya el atacante. |
| **UI** | User Interaction | Si otra persona debe intervenir. |
| **S** | Scope | Si el impacto alcanza otros componentes bajo otra autoridad de seguridad. |
| **C** | Confidentiality | Cuánto afecta a la confidencialidad. |
| **I** | Integrity | Cuánto afecta a la integridad. |
| **A** | Availability | Cuánto afecta a la disponibilidad. |

### AV — Vector de ataque

| Valor | Significado |
|---|---|
| **N** (Network) | Se puede atacar de forma remota, por red. |
| **A** (Adjacent) | Requiere estar en una red adyacente (por ejemplo, misma Wi-Fi o VLAN). |
| **L** (Local) | Requiere acceso local al equipo o una sesión en él. |
| **P** (Physical) | Requiere acceso físico al dispositivo. |

### AC — Complejidad del ataque

- **L (Low):** complejidad baja; no necesita condiciones especiales.
- **H (High):** complejidad alta; requiere condiciones especiales o difíciles.

### PR — Privilegios necesarios

- **N (None):** ningún permiso o cuenta previa.
- **L (Low):** permisos de usuario normal.
- **H (High):** permisos elevados, como administrador.

### UI — Interacción del usuario

- **N (None):** no necesita la acción de otra persona.
- **R (Required):** necesita que una víctima haga algo, como hacer clic o abrir un archivo.

### S — Alcance

- **U (Unchanged):** el impacto se queda bajo la misma autoridad de seguridad del componente vulnerable.
- **C (Changed):** alcanza recursos fuera de esa autoridad de seguridad.

### C, I y A — Impacto en la seguridad

- **C (Confidentiality):** ¿cuánta información puede leer el atacante?
- **I (Integrity):** ¿cuánto puede modificar el atacante?
- **A (Availability):** ¿cuánto puede interrumpir el servicio?

Las tres usan los mismos valores:

| Valor | Significado |
|---|---|
| **N (None)** | Sin impacto. |
| **L (Low)** | Impacto parcial o limitado. |
| **H (High)** | Impacto alto. |

**Ojo:** `C` en `S:C` significa *Changed*, mientras que `C` al comienzo de `C:H` es *Confidentiality*.

---

## 3. Ejemplo resuelto: Samba Badlock

**Vulnerabilidad:** CVE-2016-2118  
**Puntuación indicada en la guía:** **7.5 (Alta)**

```text
CVSS:3.0/AV:N/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:H
```

| Parte | Interpretación |
|---|---|
| `AV:N` | Se ataca por red. |
| `AC:H` | La explotación es compleja; necesita condiciones especiales, como interceptar tráfico. |
| `PR:N` | No se necesitan credenciales previas. |
| `UI:R` | Requiere interacción, por ejemplo una sesión cliente-servidor en curso. |
| `S:U` | El alcance no cambia. |
| `C:H` | Impacto alto en confidencialidad. |
| `I:H` | Impacto alto en integridad. |
| `A:H` | Impacto alto en disponibilidad. |

**¿Por qué no es crítica si C, I y A son altos?**  
Porque `AC:H` y `UI:R` hacen más difícil que el ataque tenga éxito. CVSS valora **tanto el impacto como la explotabilidad**.

---

## 4. Métricas temporales (opcionales)

| Sigla | Qué indica |
|---|---|
| **E** (Exploit Code Maturity) | Si existe un método conocido y funcional para explotar la vulnerabilidad. |
| **RL** (Remediation Level) | Si existe parche, solución temporal o alternativa. |
| **RC** (Report Confidence) | La confianza en el informe sobre la vulnerabilidad. |

Ejemplo de vector temporal:

```text
CVSS:3.0/E:U/RL:O/RC:C
```

**Según la guía:** no necesitas memorizar todos sus valores; basta con reconocer para qué sirven.

---

## 5. Diferencias entre CVSS v2 y v3

| CVSS v2 | CVSS v3 |
|---|---|
| Emplea `Au` para autenticación. | Separa **PR** (privilegios) y **UI** (interacción del usuario). |
| No incluye **Scope**. | Incluye la métrica **S**. |
| Impacto: None / Partial / Complete. | Impacto: None / Low / High. |

Si Nessus muestra puntuaciones v2 y v3 para una misma vulnerabilidad, **no significa que haya un error**: usan métricas diferentes.

---

## 6. Cómo resolver un ejercicio de CVSS

Cuando te den un vector, léelo **en este orden**:

1. **¿Cómo se llega hasta la vulnerabilidad?** → `AV` + `AC`.
2. **¿Qué necesita el atacante o la víctima?** → `PR` + `UI`.
3. **¿Qué se rompe y hasta dónde llega el daño?** → `S` + `C/I/A`.

**Regla para memorizar:** `AV – AC – PR – UI – S – C – I – A`.

---

## 7. Preguntas rápidas de repaso

**1. ¿Qué significa `AV:N`?**  
Ataque por red.

**2. ¿Qué significa `PR:N`?**  
No requiere privilegios o credenciales previas.

**3. ¿Qué representa `UI:R`?**  
El ataque necesita una interacción de otro usuario.

**4. ¿Qué indica `C:H/I:H/A:N`?**  
Impacto alto en confidencialidad e integridad, sin impacto en disponibilidad.

**5. ¿Qué significa `AC:H`?**  
La explotación tiene una complejidad alta.

**6. ¿Qué diferencia existe entre `S:U` y `S:C`?**  
En `S:U` el impacto no cambia de ámbito de seguridad; en `S:C` sí lo hace.

**7. ¿Qué nota es una vulnerabilidad CVSS 5.9?**  
**Media**, porque pertenece al rango 4.0–6.9.

---

## 8. Chuleta final para el examen

- **CVSS:** puntuación de gravedad del **0 al 10**.
- **9.0–10.0:** crítica; **7.0–8.9:** alta; **4.0–6.9:** media; **0.1–3.9:** baja.
- **AV:** desde dónde se ataca.
- **AC:** dificultad del ataque.
- **PR:** privilegios previos necesarios.
- **UI:** interacción necesaria de otra persona.
- **S:** alcance del daño.
- **C/I/A:** impacto en confidencialidad, integridad y disponibilidad.
- **Más impacto no siempre significa nota crítica:** también cuenta la dificultad de explotación.
- **E, RL, RC:** métricas temporales; reconocerlas.

**Lo más importante:** saber interpretar cada parte de un vector CVSS como el ejemplo de Samba Badlock.


## UNIDAD 2 Teoria

## 1. Ataques y contramedidas (diapositivas 3-6)

- **Amenaza:** escenario en el que una acción o un suceso, deliberado o accidental, puede comprometer la seguridad de un sistema.
- **Ataque:** realización o materialización de una amenaza.
- **Contramedida:** medida que se emplea para impedir, detectar o reducir los efectos de un ataque.

### ¿Qué propiedades se pueden atacar?

| Tipo de incidente | ¿Qué ocurre? | Propiedad afectada |
|---|---|---|
| **Interceptación** | Alguien obtiene información sin autorización. | **Confidencialidad** |
| **Modificación** | Alguien cambia información o mensajes. | **Integridad** |
| **Fabricación** | Alguien crea datos o comunicaciones falsos. | **Autenticidad** |
| **Interrupción** | Un recurso o servicio deja de estar disponible. | **Disponibilidad** |

**Motivaciones de los atacantes:** económicas, ideológicas, diversión y autorrealización (demostrar sus capacidades).

---

## 2. Clasificación de los ataques (diapositivas 7-11)

### Ataques pasivos

El atacante **observa, escucha, registra o intercepta** información, pero **no cambia los datos ni el flujo de comunicación**.

- Objetivos: obtener información y analizar el tráfico de red.
- Son difíciles de detectar porque no provocan alteraciones visibles.
- **Contramedida destacada:** cifrado de la información

### Ataques activos

El atacante **modifica, fabrica o interrumpe** mensajes o servicios.

Categorías incluidas en el tema:

1. **Suplantación de identidad:** hacerse pasar por otro usuario, sistema o servicio.
2. **Re-actuación (replay):** volver a transmitir una comunicación previamente capturada.
3. **Modificación de mensajes:** alterar el contenido transmitido.
4. **Degradación o denegación de servicio:** impedir o dificultar que el servicio funcione.

Otras clasificaciones: **internos / externos** (según el origen) y **estructurados / no estructurados** (según la complejidad).

| Ataque pasivo | Ataque activo |
|---|---|
| Observa o captura información. | Cambia mensajes, identidades o recursos. |
| No altera el tráfico. | Puede alterar o interrumpir el tráfico. |
| Difícil de detectar. | Puede provocar efectos visibles. |
| Ejemplo: sniffing. | Ejemplo: modificación de mensajes o DoS. |

### Ataques concretos citados en los apuntes

- Reconocimiento de sistemas y detección de vulnerabilidades.
- Interceptación y modificación de comunicaciones.
- **SMTP spoofing:** suplantación relacionada con el correo electrónico.
- **DNS spoofing:** falsificación de información o respuestas del sistema de nombres DNS.
- **IP spoofing:** falsificación de una dirección IP de origen.
- Robo de cuentas y contraseñas.
- Alteración del tráfico y de las tablas de enrutamiento.
- Conexión no autorizada a equipos.
- Introducción de virus, troyanos y gusanos.
- **XSS** e **inyección SQL**, que la diapositiva enumera junto a ataques con malware.
- Ataques contra sistemas criptográficos; fraudes y extorsiones.
- **DoS:** denegación de servicio.
- **DDoS:** denegación de servicio distribuida (desde múltiples orígenes).

**Aclaración de estudio:** XSS y la inyección SQL son técnicas de ataque a aplicaciones; no son, por sí mismas, tipos de programa malicioso.

---

## 3. Las cinco fases de un ataque informático (diapositivas 12-17)

**Orden que debes memorizar:**

1. **Reconocimiento (Reconnaissance):** recoger información pública o accesible sobre la víctima u organización.
2. **Exploración (Scanning):** investigar sistemas, direcciones IP, nombres de host, puertos y posibles vulnerabilidades.
3. **Obtener acceso (Gaining Access):** aprovechar una debilidad o técnica para entrar al sistema.
4. **Mantener el acceso (Maintaining Access):** asegurar que el atacante pueda volver a entrar, por ejemplo mediante puertas traseras.
5. **Borrar huellas (Covering Tracks):** intentar ocultar la intrusión y eliminar indicios para no ser detectado.

**Truco:** **Investigar → Escanear → Entrar → Permanecer → Ocultarse**.

| Fase | Qué busca el atacante | Ejemplo |
|---|---|---|
| Reconocimiento | Datos sobre el objetivo. | Consultar información pública del dominio. |
| Exploración | Descubrir equipos, puertos y fallos. | Escanear puertos. |
| Acceso | Entrar en el sistema. | Aprovechar un servicio vulnerable. |
| Persistencia | Poder volver a entrar. | Crear una puerta trasera. |
| Borrado | Ocultar su actividad. | Manipular o borrar registros. |

---

## 4. Contramedidas en cada fase (diapositivas 18-24)

### Fases 1 y 2: reconocimiento y exploración

**Técnicas citadas:** ingeniería social, *trashing* (basureo), *sniffing*, mapeadores de red y escáneres de puertos/vulnerabilidades.

**Herramientas para revisar qué información se expone:**

| Herramienta | Para qué sirve |
|---|---|
| `whois` | Consultar datos publicados sobre un dominio o dirección IP. |
| `nslookup` | Consultar información y registros DNS. |
| `nmap` | Explorar hosts, puertos y servicios. |
| Wireshark / TCPDump / WinDump | Observar y analizar paquetes de red. |
| Snort | Detectar tráfico sospechoso mediante un IDS. |

**Medidas:** reducir la información publicada en DNS/WHOIS; filtrar paquetes y ciertas consultas ICMP; configurar cortafuegos y ACL; instalar IDS; revisar tráfico y alertas. Las diapositivas citan `iptables` (Linux), ACL de routers Cisco y herramientas de captura y monitorización.

### Fase 3: obtener acceso

**Riesgos:** vulnerabilidades, contraseñas débiles o robadas, servicios mal configurados, troyanos y puertas traseras.

**Medidas:**

- Actualizar el software.
- Exigir contraseñas robustas.
- Revisar la configuración de los servicios.
- Desactivar servicios innecesarios o peligrosos.
- Configurar routers y cortafuegos.
- Realizar auditorías, filtrar paquetes y revisar los logs.

### Fase 4: mantener acceso o persistencia

**Riesgos:** creación de cuentas privilegiadas, instalación de *backdoors*, *rootkits* o troyanos, y cambios en los logs.

**Medidas:**

- Guardar copias de los logs **fuera del equipo que los genera**.
- Usar funciones **hash** para detectar modificaciones en ficheros de sistema.
- Aplicar filtrado, sistemas IDS y comprobación periódica de registros.
- Monitorizar logs; el PDF menciona Swatch y Logcheck en Ubuntu.

### Fase 5: borrado de huellas

El atacante intenta limpiar registros y ocultar herramientas o señales de intrusión.

**Medidas:** conservar históricos, monitorizar la red y los equipos y controlar el software instalado.

**Conceptos clave:**
- **Log:** registro de sucesos y actividades del sistema.
- **IDS:** sistema de detección de intrusiones.
- **ACL:** lista de control de acceso, permite definir qué tráfico o acceso se autoriza.
- **Hash:** valor que permite comprobar si un archivo ha cambiado.

---

## 5. Herramientas utilizadas en ataques (diapositivas 26-27)

| Herramienta | Qué hace |
|---|---|
| **Escáner de puertos** | Averigua qué puertos y servicios están disponibles. |
| **Sniffer** | Captura paquetes que circulan por una red. |
| **Exploit** | Aprovecha una vulnerabilidad conocida. |
| **Backdoor kit** | Facilita un acceso oculto o una «puerta trasera». |
| **Rootkit** | Oculta componentes o mecanismos de acceso del atacante. |
| **Auto-rooter** | Automatiza fases de exploración, explotación y acceso. |
| **Password cracker** | Trata de averiguar contraseñas. |
| **Herramientas de spoofing** | Facilitan la suplantación de datos de identificación, como una IP. |
| **Herramientas de cifrado del atacante** | Pueden ocultar las comunicaciones de un sistema comprometido. |

También se citan generadores de virus y otro software malicioso.

---

## 6. Malware y entorno sandbox (diapositiva 28)

**Malware:** software creado con el objetivo de infiltrarse o dañar un ordenador o sistema de información sin el consentimiento de su propietario.

El tema cita virus, gusanos, troyanos, puertas traseras y *adware* entre otros.

**Sandbox o caja de arena:** entorno aislado utilizado para analizar programas sospechosos. Su objetivo es que lo que ocurre dentro del entorno de prueba no afecte al sistema exterior.

---

## 7. Análisis estático frente a dinámico (diapositivas 29-30)

| **Análisis estático** | **Análisis dinámico** |
|---|---|
| Estudia el código o ejecutable **sin ejecutarlo**. | **Ejecuta** el programa en un entorno controlado. |
| Busca instrucciones o comportamientos potencialmente maliciosos. | Observa procesos, conexiones y consecuencias de la ejecución. |
| Puede exigir mucha especialización. | Los apuntes lo presentan como más rápido y práctico. |
| No necesita activar la muestra. | Emplea entornos aislados, incluidas máquinas virtuales. |

**Pregunta típica:** ¿qué análisis utiliza máquinas virtuales para observar el comportamiento del malware? **El dinámico.**

---

## 8. Programas antimalware (diapositiva 31)

Se describen dos funciones principales:

1. **Protección en tiempo real:** analizar datos que llegan de la red y bloquear amenazas detectadas.
2. **Detección y eliminación:** analizar el registro de Windows, archivos, memoria y programas ya instalados; mostrar amenazas y permitir eliminarlas.

El antivirus ayuda contra software malicioso, pero no evita automáticamente todos los fraudes o engaños humanos (por ejemplo, entregar voluntariamente una contraseña a un impostor).

---

## 9. Medidas de protección (diapositivas 32-33)

1. Mantener el **sistema operativo y navegador actualizados**.
2. Utilizar **antivirus y cortafuegos** actualizados.
3. Trabajar con una **cuenta de usuario sin privilegios administrativos**; usar administrador solo cuando sea necesario.
4. Desconfiar de programas descargados de Internet y soportes extraíbles de origen desconocido.
5. Evitar software de procedencia incierta, como determinadas descargas por redes P2P.
6. Restringir la ejecución de scripts y tecnologías del navegador según el nivel de confianza de las páginas.
7. Usar **contraseñas robustas** frente a ataques de diccionario.
8. Hacer **copias de seguridad periódicas**.

---

## 10. Clasificación y vocabulario de malware (diapositivas 34-35)

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


**Aclaraciones de apoyo (no desarrolladas en esas diapositivas):**

| Término destacado | Definición breve para estudiar |
|---|---|
| **Virus** | Código malicioso que infecta otros archivos o programas. |
| **Gusano (worm)** | Malware que puede propagarse entre sistemas. |
| **Troyano** | Programa malicioso que se presenta como algo legítimo. |
| **Ransomware** | Malware que bloquea o cifra datos y exige un rescate. |
| **Spyware** | Software que espía y recopila información del usuario. |
| **Keylogger** | Herramienta que registra pulsaciones de teclado. |
| **Adware** | Software asociado a la visualización de publicidad. |
| **Rootkit** | Conjunto de herramientas para ocultar componentes o actividad maliciosa. |
| **Backdoor** | Método oculto para acceder o volver a entrar en un sistema. |
| **Bot** | Programa que realiza tareas automáticas; puede ser usado con fines maliciosos. |
| **Phishing** | Fraude de suplantación para obtener datos; **no es necesariamente malware**. |
| **Spam** | Mensajes no solicitados; **no implica necesariamente malware**. |
| **Hoax o bulo** | Mensaje o información engañosa; **no es por sí mismo un programa malicioso**. |



---





## UNIDAD 2 PRACTICA
<img width="917" height="661" alt="image" src="https://github.com/user-attachments/assets/64e9a634-0787-4b54-991f-56dbe19fd9a1" />
<img width="925" height="651" alt="image" src="https://github.com/user-attachments/assets/a87cb120-b204-4af0-a0d9-7ced1dc32bd1" />
<img width="912" height="652" alt="image" src="https://github.com/user-attachments/assets/a91440c4-0889-4824-a163-4792ff23c90f" />
<img width="937" height="647" alt="image" src="https://github.com/user-attachments/assets/db1d3e9e-35cf-48f8-a593-be55bbe998e4" />
