# UD1 — Seguridad Informática · Guía de estudio y prácticas

**Módulo:** Seguridad y Alta Disponibilidad · 2.º ASIX  
**Material utilizado:** `UD01_Seguridad_Informática(2).pdf` (64 páginas), `CVSS_guia_lectura (1).pdf` (11 páginas) y **exclusivamente** los cuatro archivos de `SAD/UD1` del repositorio facilitado.

> **Cómo usar estos apuntes en el examen:** busca primero la palabra clave de la pregunta (CIA, vulnerabilidad, amenaza, Nessus, CVSS, etc.). En las prácticas encontrarás qué se hizo, por qué, cómo se comprueba y cómo interpretar el resultado. Los comandos explicativos adicionales están marcados como tales para no confundirlos con comandos documentados en el repositorio.

---

# PARTE 1. RESUMEN DE LA TEORÍA

## 1. ¿Qué es la seguridad informática?

Es el conjunto de medidas y controles para **proteger sistemas, equipos, programas y datos**. Busca preservar **confidencialidad, integridad y disponibilidad** y puede incluir autenticidad y no repudio.

**¿Qué se protege?** Hardware (equipos, discos, cables), software (sistemas operativos, programas), datos (información almacenada o transmitida) y también personas e instalaciones.

**La seguridad absoluta no existe:** se intenta reducir el riesgo, detectar problemas, limitar pérdidas y garantizar la recuperación.

### Tipos de seguridad

| Tipo | Qué protege o cuándo actúa | Ejemplos |
|---|---|---|
| **Física** | Equipos, instalaciones y acceso físico | Cerraduras, cámaras, extintores, control de temperatura |
| **Lógica** | Sistemas, programas, datos y accesos digitales | Contraseñas, permisos, firewall, cifrado |
| **Activa** | Intenta impedir ataques o accesos indebidos | Cortafuegos, autenticación, antivirus |
| **Pasiva** | Reduce efectos y facilita recuperación | Copias de seguridad, recuperación, registros |

**Importante:** son dos clasificaciones distintas. Un mecanismo físico puede ser preventivo o de recuperación; el material también ofrece el SAI entre ejemplos de protección activa. Algunos controles pueden cumplir varias funciones según su uso.

## 2. Principios CIA / CID

| Principio | Pregunta esencial | Ejemplo de fallo | Protección típica |
|---|---|---|---|
| **C: Confidencialidad** | ¿Solo ven los datos quienes deben? | Robo de contraseña, lectura de planos privados | Permisos, contraseñas, cifrado |
| **I: Integridad** | ¿Los datos permanecen correctos y sin cambios no autorizados? | Modificación de una factura | Hash, permisos, firma digital |
| **A/D: Disponibilidad** | ¿Pueden acceder al servicio los usuarios autorizados cuando lo necesitan? | Caída de ERP, apagón, disco destruido | SAI, redundancia, copias y recuperación |

**Ejercicio típico:** «Un intruso lee planos sin modificarlos» → confidencialidad. «Altera importes de facturas» → integridad. «Tumba el servidor de la empresa» → disponibilidad.

### Otros conceptos

- **Autenticidad:** confianza en que el emisor o el origen son reales.
- **Autenticación:** proceso que verifica la identidad de alguien.
- **No repudio:** permite aportar pruebas de envío o recepción para que las partes no puedan negar su participación.
- El PDF utiliza también el acrónimo **CIDAN**: confidencialidad, integridad, disponibilidad, autenticidad y no repudio.

## 3. Activo, amenaza, vulnerabilidad y riesgo

- **Activo:** lo que queremos proteger (una base de datos, portátil o información de clientes).
- **Vulnerabilidad:** debilidad explotable o que compromete la seguridad.
- **Amenaza:** circunstancia o acción capaz de producir daños.
- **Riesgo:** posibilidad de que una amenaza aproveche una vulnerabilidad y cause consecuencias; esta definición es una aclaración de estudio.

**Ejemplo:** contraseña escrita en un post-it (vulnerabilidad) → una persona la lee (amenaza) → entra al ERP y consulta datos de clientes (daño sobre confidencialidad).

### Origen de las vulnerabilidades

| Origen | Explicación | Ejemplo |
|---|---|---|
| **Diseño** | El sistema o protocolo se concibió con una debilidad | Protocolo inseguro |
| **Implementación** | Error al desarrollar o fabricar | Fallo de programación, puerta trasera |
| **Uso** | Mala configuración o utilización | Contraseña visible, servicio innecesario abierto |

**Vulnerabilidad de día cero (zero-day):** debilidad todavía no corregida o desconocida para quien debe solucionarla; puede explotarse antes de que exista un parche disponible.

## 4. Amenazas: clasificación

- **Físicas:** robo de equipos, incendio, inundación, apagón, sabotaje.
- **Lógicas:** malware, acceso indebido, modificación de datos, ataques de red.
- **Origen:** naturales, agentes externos y agentes internos.
- **Intencionalidad:** accidentes, errores y acciones malintencionadas.

### Qué ocurre con la información

| Situación | Qué sucede | Propiedad afectada |
|---|---|---|
| **Flujo normal** | El mensaje llega como debe | Ninguna |
| **Interceptación** | Un tercero lee sin autorización | Confidencialidad |
| **Modificación** | Se alteran datos o mensajes | Integridad (y puede afectar confidencialidad) |
| **Interrupción** | El servicio o recurso deja de estar disponible | Disponibilidad |
| **Fabricación** | Se crean datos u objetos falsos | Autenticidad |

Ejemplos: escuchar tráfico = interceptación; cambiar notas = modificación; bloquear una web = interrupción; crear un mensaje falso que suplanta al emisor = fabricación.

## 5. Ingeniería social

Ataca principalmente a las personas y a su confianza.

| Técnica | Qué hace | Ejemplo |
|---|---|---|
| **Phishing** | Engaño por correo o sitio web | Mensaje falso del banco |
| **Smishing** | Phishing mediante SMS | SMS falso de entrega |
| **Vishing** | Engaño mediante llamada | Falso técnico de soporte |
| **Pretexting** | Inventa una historia creíble | «Soy de informática, dime la contraseña» |
| **Fraude del CEO / BEC** | Suplanta a un directivo | Ordena transferencia urgente |
| **Shoulder surfing** | Espía por encima del hombro | Mira un PIN al teclearlo |
| **Tailgating** | Entra siguiendo a un autorizado | Se cuela tras un empleado |
| **Trashing** | Busca información en basura | Recupera documentos desechados |
| **Baiting** | Usa un cebo | USB abandonado |
| **Quid pro quo** | Ofrece algo a cambio de datos | Ayuda técnica falsa |
| **Ingeniería social inversa** | Logra que la víctima busque la ayuda del atacante | Falso solucionador de problemas |

**Prevención:** formación, verificar identidades, no divulgar contraseñas, confirmar solicitudes económicas por un canal alternativo.

## 6. Mecanismos básicos de seguridad

| Mecanismo | Para qué sirve |
|---|---|
| **Autenticación** | Comprobar quién eres |
| **Autorización** | Decidir qué puedes hacer después de identificarte |
| **Administración** | Gestionar usuarios, permisos y reglas |
| **Auditoría** | Revisar si las medidas funcionan y se cumplen |
| **Registro / logs** | Guardar evidencia de eventos y posibles intentos de intrusión |
| **Integridad de la información** | Comprobar que los datos no se han alterado |
| **No repudio** | Acreditar la participación en una comunicación |

**Tres factores de autenticación:** algo que sabes (contraseña), algo que tienes (tarjeta) y algo que eres (huella). Usar varios aumenta la seguridad.

**Principio de mínimo privilegio:** cada persona solo debe tener los permisos imprescindibles para su trabajo.

**Hash:** huella digital calculada a partir de un archivo o texto; sirve para comprobar cambios (comparando valores). No cifra ni permite recuperar el contenido original.

## 7. Mecanismos de protección y políticas

- **Prevención:** evitar que se materialice el incidente (acceso controlado, cifrado, protocolos seguros).
- **Detección:** descubrir actividad sospechosa (IDS, registros, auditoría).
- **Recuperación:** restaurar el servicio (copias y planes de recuperación).

### Política, norma, procedimiento y registro

| Nivel | Qué responde | Ejemplo |
|---|---|---|
| **Política** | Qué quiere protegerse y por qué | «La empresa protegerá las contraseñas» |
| **Norma** | Requisito concreto | «Mínimo 12 caracteres» |
| **Procedimiento** | Cómo aplicarlo, paso a paso | Instrucciones para cambiar una contraseña |
| **Registro** | Prueba de que se hizo | Log de cambios, checklist |

Una **política de seguridad** es el documento que fija directrices, responsabilidades, controles y respuesta ante riesgos. Debe comunicarse y revisarse.

### Modelo PDCA (ISO 27001)

1. **Plan**: analizar riesgos y planificar políticas.
2. **Do**: aplicar medidas.
3. **Check**: comprobar y auditar resultados.
4. **Act**: corregir deficiencias y mejorar.

Se repite continuamente: es un ciclo de mejora.

## 8. Vulnerabilidades, Nessus y CVSS

**Nessus** es un escáner de vulnerabilidades: examina servicios y configuraciones para detectar problemas potenciales. Un hallazgo no demuestra por sí solo que alguien haya explotado el fallo.

**CVE:** identificador de una vulnerabilidad conocida (por ejemplo, `CVE-2016-2118`). **CVSS:** estándar para puntuar la severidad de una vulnerabilidad en una escala de 0 a 10.

### Rangos CVSS (versión explicada en la guía del profesor)

| Nota | Severidad |
|---|---|
| 9.0–10.0 | Crítica |
| 7.0–8.9 | Alta |
| 4.0–6.9 | Media |
| 0.1–3.9 | Baja |
| 0.0 | Informativa / ninguna |

### Leer un vector CVSS v3.x

Ejemplo de la guía:

`CVSS:3.0/AV:N/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:H`

| Métrica | Opciones | Qué pregunta |
|---|---|---|
| **AV** (vector de ataque) | N: red; A: red adyacente; L: local; P: físico | ¿Desde dónde ataca? |
| **AC** (complejidad) | L: baja; H: alta | ¿Qué dificultad tiene? |
| **PR** (privilegios) | N: ninguno; L: bajos; H: altos | ¿Necesita una cuenta? |
| **UI** (interacción) | N: no; R: sí | ¿Debe hacer algo la víctima? |
| **S** (alcance) | U: sin cambios; C: cambiado | ¿El impacto sale del componente vulnerable? |
| **C** (confidencialidad) | N: ninguno; L: bajo; H: alto | ¿Qué puede leer? |
| **I** (integridad) | N, L, H | ¿Qué puede modificar? |
| **A** (disponibilidad) | N, L, H | ¿Cuánto puede interrumpir? |

**Cómo resolver en examen:**

1. Localiza `AV` y `AC`: cómo llega el atacante y qué dificultad tiene.
2. Localiza `PR` y `UI`: qué necesita antes de atacar.
3. Mira `S`, `C`, `I` y `A`: alcance y daño.
4. Consulta la puntuación dada: una vulnerabilidad con gran impacto puede no ser crítica si explotarla es difícil.

**Ejemplo de la guía:** Samba Badlock (`CVE-2016-2118`), vector `AV:N/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:H`; la guía indica **7.5, alta**. El impacto es alto, pero la complejidad es alta y precisa interacción.

**Métricas temporales de la guía:** `E` (madurez de explotación), `RL` (nivel de remedio), `RC` (confianza del informe). Basta reconocerlas, según indica ese PDF.

**CVSS v2 frente a v3:** en v2 no aparecen `PR`, `UI` y `S` tal como se usan en v3.

---

# PARTE 2. PRÁCTICAS DE `SAD/UD1` EXPLICADAS PASO A PASO

## Práctica 1 — Buscar vulnerabilidades en Estudio Torrent (`actividad1.md`)

### Objetivo

Detectar riesgos en una oficina y completar una tabla que relaciona **vulnerabilidad**, **origen**, **amenaza**, **tipo físico/lógico** y **principio CIA afectado**.

### Cómo hacer cada fila

1. **Observa el problema:** por ejemplo, un ordenador desbloqueado.
2. **Identifica la vulnerabilidad:** cualquiera podría utilizarlo.
3. **Clasifica el origen:** en este caso, **uso**, porque el usuario no bloqueó la sesión.
4. **Piensa en la amenaza:** alguien entra, lee o modifica información.
5. **Decide físico/lógico:** hay acceso físico a un equipo y posible acceso lógico a la información.
6. **Elige CIA:** confidencialidad si lee; integridad si modifica.

### Ejemplos presentes en tu entrega

| Situación | Origen indicado | Amenaza | Tipo | CIA afectada |
|---|---|---|---|---|
| Contraseña en post-it | Uso | Alguien la lee | Física → lógica | C |
| Ordenador desbloqueado | Uso | Acceso no autorizado | Física → lógica | C, I |
| Documentos en impresora | Uso | Robo o consulta | Física | C |
| Portátil sin protección | Uso | Robo o acceso | Física | C, D |
| Riesgo de incendio | Diseño | Destrucción de equipos | Física | I, D |
| Cables accesibles | Diseño | Desconexión o manipulación | Física | I, D |

**Truco:** no confundas *vulnerabilidad* (condición débil) con *amenaza* (lo que la aprovecha).

**Si te ponen un caso nuevo:** justifica con una frase: «Es una vulnerabilidad de uso porque __; la amenaza es __; se compromete __ porque __».

## Práctica 2 — Clasificación de la información (`UD1_Actividad2.md`)

### Objetivo

Clasificar documentos de la empresa por **confidencialidad** y justificar quién puede verlos y qué pasaría si se divulgasen.

### Cinco niveles empleados en tu actividad

| Nivel | Etiqueta | Criterio de examen | Ejemplo de tu entrega |
|---|---|---|---|
| **1** | Uso público | Se puede publicar | Catálogo de servicios y tarifas |
| **2** | Difusión limitada | Circulación interna con perjuicio reducido | Calendario de vacaciones entre socios |
| **3** | Confidencial | Datos sensibles accesibles a un grupo reducido | Facturas de clientes |
| **4** | Reservada | Difusión provocaría un perjuicio grave | Contraseña ERP, planos, informe Nessus |
| **5** | Secreta | Información estratégica extremadamente sensible | Negociación de venta de la empresa |

### Paso a paso para decidir el nivel

1. Pregunta: «¿Está pensado para que lo vea cualquiera?» → nivel 1.
2. Si es interno, pregunta: «¿El daño de una filtración sería pequeño?» → nivel 2.
3. ¿Contiene datos personales, comerciales o financieros sensibles? → nivel 3 o superior.
4. ¿Permite acceder a sistemas o expone riesgos graves de seguridad? → nivel 4.
5. ¿Podría comprometer decisiones estratégicas críticas? → nivel 5.

**Atención:** no hay un nivel único universal para todo documento; estos son los niveles y las elecciones utilizadas en tu actividad. En el examen justifica el contexto.

## Práctica 3 — Red privada y auditoría Nessus (`UD1-practica-nessus.md`)

### Qué se hizo

Montaste un **laboratorio con dos máquinas virtuales**:

- **Ubuntu:** máquina desde la que se utiliza Nessus para examinar la seguridad.
- **Metasploitable:** sistema intencionadamente vulnerable, usado como objetivo de pruebas.

El repositorio documenta una red de laboratorio con **`192.168.56.10` (Ubuntu)** y **`192.168.56.20` (Metasploitable)**. Estas son las direcciones **de aquella práctica documentada**, no valores fijos de todas tus máquinas ni necesariamente los actuales.

### Paso 1 — Crear la red privada en VirtualBox

- Configurar un segundo adaptador de red en ambas VM.
- Conectarlas a la **misma red virtual privada**.
- Objetivo: permitir que la auditora contacte con la máquina vulnerable sin exponer la práctica a Internet.

**Qué ocurre si no se ven:** revisar que ambas están en la misma red virtual, tienen IP compatibles y que las interfaces están activas.

### Paso 2 — Configurar direcciones IP

- Asignar a cada máquina una IP distinta de la misma subred.
- En el informe: `192.168.56.10` y `192.168.56.20`.
- **Por qué:** dos equipos deben tener direcciones únicas para comunicarse.

**Comandos de diagnóstico complementarios** (no están detallados en el Markdown de Nessus, pero son útiles para un ejercicio parecido):

```bash
ip a                      # Ver interfaces e IP
ip -br a                  # Vista resumida
ip route                   # Ver rutas
ping -c 3 192.168.56.20   # Probar comunicación con el objetivo del informe
```

`ping` envía mensajes ICMP; recibir respuesta indica conectividad básica. No garantiza que todos los servicios estén disponibles. Si el ping está bloqueado, la máquina aún puede estar operativa.

### Paso 3 — Realizar el análisis con Nessus

1. Desde Ubuntu, acceder a la herramienta **Nessus** preparada en el laboratorio.
2. Crear/seleccionar el análisis del objetivo autorizado.
3. Definir la IP de **Metasploitable** como destino.
4. Iniciar el escaneo y esperar resultados.
5. Revisar hallazgos, nivel de severidad, puntuación CVSS y descripción.
6. Proponer medidas de mitigación, priorizando los hallazgos más graves.

> El repositorio cuenta qué se hizo y resume los resultados, **pero no especifica la instalación de Nessus ni los menús exactos de su interfaz**. Estos seis pasos son una guía explicativa del flujo, no una transcripción de capturas de la práctica.

### Paso 4 — Interpretar el informe real del repositorio

| Severidad | Hallazgos documentados |
|---|---:|
| Crítica | 9 |
| Alta | 3 |
| Media | 30 |
| Baja | 8 |
| Informativa | 140 |
| **Total** | **190** |

**Qué significa:** había numerosas detecciones que requerían investigación. Los hallazgos informativos suelen describir servicios/configuración y no implican necesariamente vulnerabilidades explotables.

### Paso 5 — Distinguir diseño, implementación y uso

Tu informe clasifica tres ejemplos:

| Hallazgo del informe | Origen anotado | Idea de la solución |
|---|---|---|
| SSL Version 2 and 3 Protocol Detection | Diseño | Deshabilitar SSLv2/SSLv3 y adoptar protocolos actuales |
| SSL DROWN Attack Vulnerability | Implementación | Corregir o actualizar software afectado |
| SSL Anonymous Cipher Suites Supported | Uso | Deshabilitar suites anónimas o configuración insegura |

**Nota para evitar confusiones en el examen:** tu entrega escribe «Baja 5.9» para la tercera fila; **según la tabla CVSS del PDF, 5.9 pertenece a Media (4.0–6.9)**. Al estudiar, conserva el dato de la entrega, pero no memorices esa asociación incorrecta.

### Paso 6 — Priorizar y justificar soluciones

1. **Reducir exposición:** cerrar servicios y protocolos inseguros que no se necesiten.
2. **Actualizar:** instalar parches/versiones mantenidas.
3. **Corregir configuración:** eliminar accesos anónimos y opciones débiles.
4. **Comprobar de nuevo:** repetir el escaneo autorizado para verificar si desaparecen los hallazgos.

**Respuesta modelo:** «No pondría Metasploitable en producción porque el informe registra varias vulnerabilidades críticas. Antes de conectarlo a una red real habría que actualizar, deshabilitar protocolos obsoletos y revisar configuraciones».

## Práctica 4 — Interpretar CVSS (guía PDF)

### Ejercicio resuelto

Vector: `AV:N/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:H`

| Campo | Explicación en lenguaje normal |
|---|---|
| `AV:N` | Puede atacarse desde la red |
| `AC:H` | Requiere condiciones difíciles |
| `PR:N` | No necesita credenciales previas |
| `UI:R` | Necesita intervención o una acción de la víctima |
| `S:U` | El impacto queda dentro del ámbito del componente vulnerable |
| `C:H` | Gran pérdida de confidencialidad |
| `I:H` | Gran impacto en integridad |
| `A:H` | Gran impacto en disponibilidad |

**Conclusión de la guía:** severidad **7.5 (alta)** para el ejemplo Samba Badlock. No es crítica pese al impacto alto, por la complejidad y la interacción requerida.

### Cómo afrontar un vector nuevo

1. Subraya todas las siglas.
2. Traduce cada valor según la tabla teórica.
3. Resume en una frase si se ataca por red o requiere acceso local, si precisa credenciales o interacción y qué daños tendría.
4. No intentes calcular la puntuación solo «a ojo»: usa la nota proporcionada o una calculadora CVSS si el ejercicio pide la nota exacta.

## Archivo `fichero.md`

En la carpeta `SAD/UD1` solo contiene `# Prueba`; no aporta temario técnico adicional.

---

# PARTE 3. COMANDOS ÚTILES Y ERRORES HABITUALES

> **Estos comandos son ayudas de repaso:** algunos no aparecen en la práctica Nessus escrita, pero sirven para interpretar situaciones de laboratorio similares.

| Comando | Qué hace | Qué debes mirar |
|---|---|---|
| `ip a` | Muestra interfaces y direcciones | IP, nombre de la interfaz y estado UP |
| `ip -br a` | Resumen de las interfaces | Direcciones asignadas |
| `ip route` | Rutas de red | Por dónde sale el tráfico |
| `ping -c 3 IP` | Tres pruebas de conectividad ICMP | Respuestas, pérdida de paquetes |
| `ss -tulpn` | Puertos y sockets locales | Servicios en escucha, puerto y proceso |
| `nmap -sV IP` | Identifica puertos y versiones detectables | `open`, `closed`, `filtered` |
| `nmap -sV -Pn IP` | Escanea sin depender del descubrimiento habitual por ping | Útil si la detección de host está bloqueada |
| `sha256sum archivo` | Calcula una huella SHA-256 | Compararla antes/después |
| `file archivo` | Identifica el tipo de archivo | Texto, script, binario, etc. |
| `diff antes.txt despues.txt` | Muestra diferencias de dos informes | Qué apareció, desapareció o cambió |

**Diferencia clave:** `open` = un servicio responde en ese puerto; `closed` = no hay servicio escuchando; `filtered` = el escáner no puede determinarlo por filtrado o falta de respuesta. **Nessus busca vulnerabilidades; Nmap se orienta al descubrimiento de hosts, puertos y servicios.**

### Errores típicos de examen

- **Confundir amenaza y vulnerabilidad.** El post-it con clave es la debilidad; quien lo lee y se aprovecha es la amenaza.
- **Confundir CIA.** Leer = C, modificar = I, interrumpir = D/A.
- **Decir que un puerto cerrado significa que el software se ha desinstalado.** No necesariamente: puede estar filtrado por un firewall.
- **Tomar «hallazgo de Nessus» como «ataque confirmado».** Nessus identifica problemas potenciales, no prueba necesariamente una intrusión.
- **Creer que un hash es cifrado.** Un hash comprueba integridad, no oculta reversiblemente el contenido.
- **Confundir autenticación y autorización.** La primera identifica; la segunda determina permisos.
- **Pensar que el CVSS es solo el daño.** También tiene en cuenta condiciones de explotación.

---

# PARTE 4. PREGUNTAS MODELO DE EXAMEN Y RESPUESTAS

1. **¿Qué es seguridad informática?** Medidas para proteger activos preservando confidencialidad, integridad y disponibilidad.
2. **¿Seguridad física o lógica?** Física protege instalaciones/equipos; lógica controla acceso y protección digital.
3. **¿Qué es vulnerabilidad?** Debilidad que puede comprometer la seguridad.
4. **¿Qué diferencia hay entre amenaza y vulnerabilidad?** La amenaza es el posible causante del daño; la vulnerabilidad es la debilidad que aprovecha.
5. **¿Qué tipo de vulnerabilidad es una contraseña escrita?** De uso; amenaza de lectura/robo; afecta a confidencialidad.
6. **¿Qué es zero-day?** Vulnerabilidad sin corrección disponible en el momento relevante, explotable antes del parche.
7. **¿Qué es ingeniería social?** Manipular a personas para obtener acceso o información.
8. **¿Diferencia entre phishing, smishing y vishing?** Correo/web engañosos, SMS y llamadas.
9. **¿Autenticación o autorización?** Comprobar identidad frente a decidir permisos.
10. **¿Para qué sirve una copia de seguridad?** Recuperar información o servicio tras pérdida/incidente.
11. **¿Qué hace Nessus?** Escanear y reportar vulnerabilidades potenciales, con gravedad y detalles de mitigación.
12. **¿Qué es CVSS?** Sistema de puntuación de gravedad de vulnerabilidades.
13. **¿Qué quiere decir `AV:N`?** Ataque posible a través de una red.
14. **¿Qué quiere decir `PR:N`?** No hacen falta privilegios previos.
15. **¿Qué implica `UI:R`?** Se necesita interacción de otra persona.
16. **¿Qué es PDCA?** Planificar, hacer, verificar, actuar para mejorar continuamente.
17. **¿Por qué una contraseña del ERP es nivel 4 en tu actividad?** Porque su filtración puede dar acceso muy sensible al negocio.
18. **¿Qué significa que Nessus detecte muchas vulnerabilidades críticas?** Que hay problemas de alta gravedad que necesitan priorización y validación, sin implicar por sí solos que hayan sido explotados.
19. **¿Cómo compruebas si dos VM están en la misma red?** Revisar configuración del adaptador en VirtualBox, IP/subred, estado de interfaz y conectividad.
20. **¿Qué propondrías ante SSLv2/SSLv3?** Deshabilitarlos, emplear protocolos seguros actualizados y volver a verificar.

---

# HOJA RÁPIDA — PARA CONSULTAR EN 30 SEGUNDOS

- **C** = nadie no autorizado lee. **I** = nadie modifica indebidamente. **D/A** = el servicio funciona cuando se necesita.
- **Vulnerabilidad** = debilidad. **Amenaza** = lo que puede causar daño. **Riesgo** = posibilidad/consecuencia.
- **Diseño** = cómo se concibió. **Implementación** = cómo se programó. **Uso** = cómo se configuró o utilizó.
- **Física** = equipos/instalaciones. **Lógica** = sistemas/datos/accesos.
- **Prevención** = evitar. **Detección** = descubrir. **Recuperación** = restaurar.
- **Autenticación** = quién eres. **Autorización** = qué puedes hacer.
- **PDCA** = Plan → Do → Check → Act.
- **Nessus** = vulnerabilidades. **CVSS** = gravedad. **CVE** = identificador.
- **CVSS:** N/A/L/P en AV; L/H en AC; N/L/H en PR; N/R en UI; U/C en S; N/L/H en C/I/A.
- **Nota CVSS:** 0 informativa; 0.1–3.9 baja; 4–6.9 media; 7–8.9 alta; 9–10 crítica.
- **Nessus documentado:** 9 críticas, 3 altas, 30 medias, 8 bajas y 140 informativas.
- **Red documentada:** Ubuntu `192.168.56.10`; Metasploitable `192.168.56.20` (solo el laboratorio de ese informe).

---

**Nota de alcance:** Esta guía procede exclusivamente de los dos PDF y `SAD/UD1`. Las aclaraciones adicionales se identifican como ejemplos o comandos complementarios. No se han incorporado prácticas de otras carpetas ni unidades.
