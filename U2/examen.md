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

## 16. Práctica: clasificación de vulnerabilidades detectadas con Nessus (captura 1)
<img width="1508" height="492" alt="image" src="https://github.com/user-attachments/assets/3fc2199d-a341-41a6-810a-31d162a7102f" />

### 16.1. Qué significa cada columna

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

## 17. Práctica: cinco niveles de clasificación de la información (captura 2)

<img width="1451" height="688" alt="image" src="https://github.com/user-attachments/assets/07214496-3436-4e80-805f-9724b7e853b6" />

> **Origen:** segunda captura, del caso «Estudio Torrent». Es una **clasificación interna de documentos según su sensibilidad**. Estos cinco niveles pertenecen al ejercicio mostrado; no hay que confundirlos con las puntuaciones CVSS ni presentarlos como una escala universal obligatoria para todas las empresas.

### 17.1. Qué significa cada nivel

| Nivel | Nombre | Explicación sencilla | Ejemplo de uso |
|---|---|---|---|
| **1** | **Uso público** | Información destinada a cualquier persona; se puede publicar. | Catálogo de servicios de la web. |
| **2** | **Difusión limitada** | Información para un grupo concreto, normalmente interno; no está pensada para publicarse, pero su divulgación tendría un impacto pequeño. | Calendario de vacaciones en un correo interno. |
| **3** | **Confidencial** | Información privada o empresarial cuya consulta debe limitarse a personal autorizado. | Facturas de clientes. |
| **4** | **Reservada** | Información especialmente sensible; una filtración podría causar daños graves en privacidad, seguridad o funcionamiento. | Contraseña de ERP, planos privados, informe de vulnerabilidades. |
| **5** | **Secreta** | Información estratégica de máxima sensibilidad en el caso; una filtración puede perjudicar gravemente a la empresa. | Negociación confidencial de venta del estudio. |

**Diferencia entre niveles:** conforme se sube de nivel, la información es más sensible y se requiere un control de acceso más estricto.

### 17.2. Explicación de los siete ejemplos de Estudio Torrent

1. **Catálogo de servicios de la web → 1, Uso público.** Es la lista de servicios de la empresa que se anuncia para captar clientes; que otros la vean es normal.
2. **Correo interno sobre el calendario de vacaciones → 2, Difusión limitada.** Es información de organización interna; no debería difundirse a cualquiera, aunque normalmente no es tan delicada como una factura o una contraseña.
3. **Facturas emitidas a clientes → 3, Confidencial.** Contienen datos de personas o empresas, importes y relaciones comerciales; solo debe acceder personal autorizado.
4. **Contraseña de acceso al ERP → 4, Reservada.** Es una credencial de entrada a un sistema esencial de gestión y su robo puede facilitar accesos indebidos.
5. **Planos de la vivienda de un cliente en el disco compartido → 4, Reservada.** Pueden revelar detalles privados de una propiedad; la filtración puede afectar a la intimidad y a la seguridad del cliente.
6. **Informe de auditoría de Nessus → 4, Reservada.** Identifica fallos y sistemas vulnerables; un atacante podría utilizar esa información para preparar intrusiones.
7. **Conversación de los socios sobre una oferta de compra del estudio → 5, Secreta.** Se refiere a una decisión estratégica cuya divulgación podría perjudicar seriamente la negociación.

### 17.3. Términos de esta actividad

- **ERP (Enterprise Resource Planning):** programa de gestión que integra procesos como facturación, compras, ventas y recursos de la empresa.
- **Credencial:** dato o elemento que sirve para demostrar identidad o acceder, como una contraseña.
- **Factura:** documento que registra una operación económica y puede incluir datos de clientes e importes.
- **Disco compartido:** almacenamiento al que acceden varias personas por red. **Compartido no significa que todos deban tener permiso para todo.**
- **Informe de auditoría de vulnerabilidades:** documento que reúne fallos detectados, su gravedad y, normalmente, recomendaciones para corregirlos.
- **Secreto comercial:** información no pública cuyo valor para la empresa depende, en parte, de mantenerla reservada.

**Cómo acertar en el examen:** pregunta «¿quién necesita conocer esto?» y «¿qué pasaría si se filtrase?». Cuanto mayor sea el perjuicio potencial, mayor nivel de protección debes justificar.

---

## 18. Práctica: origen, amenaza, tipo físico/lógico y CID (captura 3)

<img width="1362" height="407" alt="image" src="https://github.com/user-attachments/assets/4984dbbb-cd28-4509-aaaa-e29ed4e41ff7" />


> **Origen:** tercera captura y actividad relacionada con la página 21 del temario. La captura está cortada; aquí se explican **todas las filas visibles**, sin inventar las que no aparecen.

### 18.1. Significado de las columnas

| Columna | Qué hay que escribir |
|---|---|
| **Vulnerabilidad** | La debilidad concreta observada: por ejemplo, «ordenador sin bloquear». |
| **Origen** | **Diseño, implementación o uso**: de dónde procede esa debilidad. |
| **Amenaza que la explota** | La acción que aprovecharía el fallo: leer datos, robar documentos, cambiar archivos, etc. |
| **Física / Lógica** | Si afecta al **entorno físico** (personas, papeles, equipos) o a los **accesos y datos informáticos**; a veces empieza físicamente y termina en un acceso lógico. |
| **C-I-D afectada** | Qué principio queda comprometido: **confidencialidad**, **integridad** o **disponibilidad**. Puede haber más de uno. |

### 18.2. Filas visibles, explicadas una por una

| Vulnerabilidad | ¿Qué es o qué sucede? | Origen | Amenaza | Física/lógica | CID afectada |
|---|---|---|---|---|---|
| **Contraseña en un post-it** | La clave está escrita en un papel visible. | **Uso:** mala práctica del usuario. | Alguien lee la clave y la utiliza. | **Física → lógica:** lee el papel y luego puede entrar al sistema. | **Confidencialidad**, porque la clave y posiblemente la información quedan expuestas. |
| **Ordenadores sin bloquear** | Se deja una sesión abierta al ausentarse. | **Uso:** no se bloquea el equipo. | Alguien aprovecha la sesión abierta para acceder o modificar datos. | **Física → lógica:** se acerca al ordenador y utiliza la sesión. | **Confidencialidad e integridad** si puede leer o modificar. |
| **Pantallas visibles** | Personas ajenas pueden ver los datos mostrados en el monitor. | **Uso:** ubicación o utilización inadecuada. | Un tercero observa información privada (*shoulder surfing*). | **Física:** observación directa de pantalla. | **Confidencialidad**. |
| **Documentos encima de las mesas** | Papeles con información interna quedan accesibles. | **Uso:** falta de custodia de documentos. | Alguien lee o se lleva papeles. | **Física**. | **Confidencialidad**; también podrían afectarse otros principios si fueran alterados o destruidos. |
| **Estanterías con documentos** | Documentos almacenados a la vista o al alcance de personas no autorizadas. | **Uso:** almacenamiento sin protección suficiente. | Una persona consulta o sustrae documentos. | **Física**. | **Confidencialidad**. |

### 18.3. Conceptos adicionales para no confundirse

- **Post-it:** nota adhesiva; el problema no es el papel, sino tener en él una contraseña accesible.
- **Equipo bloqueado:** requiere volver a autenticarse antes de continuar. Cerrar una sesión o bloquearla reduce el riesgo de uso por terceros.
- **Shoulder surfing:** mirar una pantalla o un teclado ajeno para obtener información.
- **Confidencialidad:** se rompe cuando alguien **consulta o conoce información sin permiso**.
- **Integridad:** se rompe cuando alguien **modifica los datos sin autorización**.
- **Disponibilidad:** se rompe cuando no puedes **usar el sistema o acceder a la información cuando lo necesitas**.

### 18.4. Método para resolver ejercicios de clasificación

1. **Lee la situación:** ¿qué está mal hecho o qué es inseguro?
2. **Indica el origen:** diseño (se concibió mal), implementación (se construyó mal) o uso (se utiliza/configura mal).
3. **Especifica la amenaza:** no escribas simplemente «hacker»; explica la acción concreta, como «un visitante lee una contraseña».
4. **Indica si la amenaza es física o lógica:** una intrusión presencial que luego deriva en acceso informático puede ser **física → lógica**.
5. **Marca CID:** lectura sin permiso → C; modificación → I; caída o pérdida de acceso → D.
6. **Justifica con una frase:** «Afecta a la confidencialidad porque permite conocer información privada».

### 18.5. Ejemplos extra de entrenamiento (creados para practicar)

| Situación | Origen probable | Tipo | Principio afectado |
|---|---|---|---|
| El firewall está desactivado por descuido. | Uso. | Lógica. | Puede comprometer C, I y/o D, según el ataque. |
| Un empleado modifica una factura sin permiso. | No se puede establecer solo con esa frase: puede ser un abuso de permisos o un fallo de control. | Lógica. | Integridad. |
| Se corta la electricidad y un servidor se apaga. | No se conoce la vulnerabilidad concreta: faltaría evaluar el sistema de alimentación. | Física. | Disponibilidad. |
| Se permite el acceso a un protocolo de red diseñado sin autenticación adecuada. | Diseño. | Lógica. | Autenticidad y, según el caso, confidencialidad. |

---



## UNIDAD 2 PRACTICA
<img width="917" height="661" alt="image" src="https://github.com/user-attachments/assets/64e9a634-0787-4b54-991f-56dbe19fd9a1" />
<img width="925" height="651" alt="image" src="https://github.com/user-attachments/assets/a87cb120-b204-4af0-a0d9-7ced1dc32bd1" />
<img width="912" height="652" alt="image" src="https://github.com/user-attachments/assets/a91440c4-0889-4824-a163-4792ff23c90f" />
<img width="937" height="647" alt="image" src="https://github.com/user-attachments/assets/db1d3e9e-35cf-48f8-a593-be55bbe998e4" />
