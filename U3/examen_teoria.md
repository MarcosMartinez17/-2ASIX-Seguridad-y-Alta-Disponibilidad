# UD3 — Seguridad lógica, criptografía y control de acceso

**Seguridad y Alta Disponibilidad (SAD) · 2.º ASIX**  
**Apuntes exclusivamente de teoría para preparar el examen**

## Material de referencia

Estos apuntes están elaborados a partir de los dos documentos de teoría del profesor:

- **SAD UD3 — PARTE 1.pdf** (9 páginas): seguridad lógica, identificación, autenticación, ACL y políticas de contraseñas.
- **SAD · UD3 PARTE 2.pdf** (26 páginas): criptografía, cifrado, hash, PKI, certificados, firma digital y SSL/TLS.

**No se incluyen actividades, entregables, ejercicios resueltos, prácticas de GitHub ni procedimientos de laboratorio.** Las referencias a páginas indican dónde aparece cada concepto en las diapositivas. Se mantiene el enfoque del material docente; las cifras y clasificaciones históricas se identifican como «según el PDF» cuando conviene.

---

# PARTE 1 — SEGURIDAD LÓGICA Y CONTROL DE ACCESO

## 1. Seguridad lógica (Parte 1, página 4)

La **seguridad lógica** es el conjunto de procedimientos y mecanismos que impiden el acceso no autorizado a la **información**, tanto cuando está almacenada como cuando se transmite.

- La **seguridad física** protege edificios, instalaciones y equipos.
- La **seguridad lógica** protege el acceso a los datos, los programas y su utilización.
- Son **complementarias**: proteger físicamente el servidor no basta si cualquiera puede leer sus archivos.

### Objetivos de la seguridad lógica

1. **Restringir el acceso** a programas y archivos.
2. **Evitar cambios sin supervisión** en la información.
3. Asegurar el **uso correcto de los datos y de los procedimientos**.
4. Garantizar que la información solo la reciba su **destinatario autorizado**.
5. Comprobar que la información recibida sea **idéntica a la enviada**.
6. Disponer de **sistemas alternativos de transmisión** cuando sean necesarios.

**Regla esencial del tema:** «Todo lo que no está permitido debe estar prohibido».

Este criterio significa que los permisos deben concederse de manera explícita: un usuario no debería acceder a recursos solo porque nadie haya configurado una prohibición.

### Diferencia que debes recordar

| Seguridad física | Seguridad lógica |
|---|---|
| Evita accesos o daños materiales | Evita accesos o modificaciones digitales no autorizados |
| Protege equipos e instalaciones | Protege datos, programas y comunicaciones |
| Por ejemplo, control de entrada al CPD | Por ejemplo, autenticación, permisos y cifrado |

## 2. Identificación y autenticación (Parte 1, página 5)

El control de acceso comienza comprobando **quién intenta acceder**.

- **Identificación:** el usuario declara quién es. Habitualmente proporciona su nombre de usuario.
- **Autenticación:** el sistema comprueba que esa identidad sea auténtica, por ejemplo pidiendo una contraseña.
- **Autorización:** determina a qué recursos puede acceder el usuario ya identificado y autenticado. Este concepto ayuda a entender la finalidad de los permisos.

**Orden lógico:** identificarse → autenticarse → comprobar los permisos de acceso.

### Tres factores de autenticación

| Tipo de factor | Qué significa | Ejemplo del temario |
|---|---|---|
| **Algo que sabes** | Un secreto que recuerdas | Contraseña o PIN |
| **Algo que tienes** | Un objeto o dispositivo que posees | Tarjeta de acceso |
| **Algo que eres** | Una característica biométrica | Huella dactilar |

Los tres factores son categorías distintas de evidencia para comprobar la identidad de un usuario. Saber una contraseña, poseer una tarjeta y utilizar una huella no son el mismo tipo de comprobación.

## 3. Ataques contra el control de acceso (Parte 1, páginas 5 y 9)

Los ataques contra contraseñas intentan descubrir una clave válida y acceder a una cuenta.

### Ataque de fuerza bruta

Consiste en **probar combinaciones posibles de caracteres** hasta encontrar la contraseña. Cuanto mayor es el espacio de búsqueda, más costoso resulta probar todas las posibilidades.

- Su dificultad depende de la **longitud** de la contraseña y del **conjunto de caracteres** usado.
- Una contraseña larga y poco predecible aumenta la cantidad de combinaciones posibles.
- Limitar el número de intentos reduce la efectividad de los ataques de prueba y error contra un inicio de sesión.

### Ataque por diccionario

Consiste en probar **palabras conocidas, contraseñas habituales o listas preparadas**.

- Suele ser eficaz contra claves basadas en palabras o expresiones corrientes.
- No necesita recorrer todas las combinaciones posibles.
- Se dificulta evitando contraseñas comunes y aplicando límites de intentos.

### Análisis estadístico

Aprovecha **patrones frecuentes** en las contraseñas y en la forma en que las personas las construyen. Por ejemplo, ciertas estructuras son más predecibles que otras.

### Comparación esencial

| Aspecto | Diccionario | Fuerza bruta |
|---|---|---|
| Qué prueba | Palabras o claves habituales | Combinaciones del conjunto de caracteres |
| Ventaja para el atacante | Prioriza claves frecuentes | No depende de que la contraseña sea una palabra |
| Principal dificultad | Claves largas y no predecibles | Gran número de combinaciones y tiempo de cálculo |
| Defensa destacada | Evitar claves comunes y limitar intentos | Longitud, complejidad y límite de intentos |

**No confundas:** un ataque de diccionario selecciona candidatos probables; el de fuerza bruta busca sistemáticamente combinaciones.

## 4. Listas de control de acceso: ACL (Parte 1, páginas 6 y 7)

**ACL** significa *Access Control List* o **Lista de Control de Acceso**.

Una ACL permite definir permisos **para usuarios o grupos concretos**, más allá de las tres categorías tradicionales de Unix: propietario, grupo y resto.

### 4.1. Permisos tradicionales de Linux

| Categoría | Significado |
|---|---|
| `u` (*user*) | Usuario propietario |
| `g` (*group*) | Grupo propietario |
| `o` (*others*) | Resto de usuarios |

| Permiso | En un archivo | En un directorio |
|---|---|---|
| `r` (*read*) | Leer el contenido | Consultar la lista de nombres |
| `w` (*write*) | Modificar su contenido | Modificar entradas si también se puede acceder al directorio |
| `x` (*execute*) | Ejecutar el archivo cuando corresponda | Entrar o atravesar el directorio |

**Representación habitual:** `rwx` indica lectura, escritura y ejecución; `r-x` indica lectura y ejecución sin escritura; `---` indica ausencia de permisos.

**Cálculo numérico de los permisos:** `r = 4`, `w = 2`, `x = 1`.

| Número | Permisos | Interpretación |
|---|---|---|
| 7 | `rwx` | Todos |
| 6 | `rw-` | Lectura y escritura |
| 5 | `r-x` | Lectura y ejecución |
| 4 | `r--` | Solo lectura |
| 3 | `-wx` | Escritura y ejecución |
| 2 | `-w-` | Solo escritura |
| 1 | `--x` | Solo ejecución |
| 0 | `---` | Ninguno |

Los permisos tradicionales permiten asignar un conjunto a cada una de las tres categorías, pero no resuelven fácilmente situaciones con **varios usuarios y grupos que necesitan accesos distintos sobre un mismo recurso**.

### 4.2. ¿Qué aportan las ACL?

Con una ACL se pueden establecer, en el mismo archivo o directorio:

- Entradas para **usuarios concretos**.
- Entradas para **grupos concretos**.
- Una **máscara de permisos** que limita los permisos efectivos de determinadas entradas.

Esto permite aplicar permisos más precisos sin tener que reunir necesariamente a todos los usuarios en un único grupo.

### 4.3. ACL de acceso y ACL por defecto

| Tipo | Función | ¿Dónde se aplica? |
|---|---|---|
| **ACL de acceso** | Controla los permisos actuales sobre un recurso | Archivos y directorios |
| **ACL por defecto** | Define las entradas que pueden heredar recursos creados dentro de una carpeta | Directorios |

**Idea clave:** una ACL por defecto está pensada para la **herencia** de permisos en nuevos archivos y subdirectorios. No cambia por sí misma los archivos que ya existían.

### 4.4. Máscara ACL

La **máscara** (`mask`) establece un límite máximo de permisos efectivos para determinadas entradas de la ACL: usuarios nombrados, grupo propietario y grupos nombrados.

Ejemplo conceptual:

```text
user:usuario:rwx
mask::r-x
```

Aunque la entrada indique `rwx`, el usuario tendrá efectivamente `r-x` porque la máscara no permite escritura.

**Regla para memorizar:** permisos efectivos = permisos de la entrada **limitados por la máscara**.

La máscara no se aplica de la misma manera a la entrada del **propietario** (`user::`) ni a la de **otros** (`other::`).

### 4.5. Requisitos de las ACL según el profesor

1. Núcleo del sistema con soporte de **atributos extendidos y ACL**.
2. Sistema de archivos compatible y montado con el soporte necesario.
3. Utilidades de usuario instaladas, como las del paquete **`acl`**.

### 4.6. Herramientas y opciones que aparecen en la teoría

| Elemento | Para qué sirve |
|---|---|
| **`getfacl`** | Consultar las entradas ACL de un archivo o directorio |
| **`setfacl`** | Crear, modificar o eliminar entradas ACL |
| **`setfacl -m`** | Añadir o modificar entradas |
| **`setfacl -d`** | Trabajar con ACL por defecto en directorios |
| **`setfacl -x`** | Eliminar una entrada concreta |

Se estudian como **conceptos y herramientas del temario**, sin desarrollar procedimientos prácticos en estos apuntes.

## 5. Políticas de contraseñas (Parte 1, página 8)

Una **política de contraseñas** reúne los criterios que deben cumplir las claves para proteger las cuentas de usuario.

### Recomendaciones recogidas en las diapositivas

- Longitud mínima de **8 caracteres**; el material indica que es preferible **14 o más**.
- Combinar letras mayúsculas, minúsculas, números y símbolos.
- Evitar la repetición y las estructuras demasiado predecibles.
- No utilizar el **nombre de inicio de sesión** como contraseña.
- Evitar palabras que aparezcan en diccionarios.
- Usar **contraseñas diferentes** en servicios o entornos distintos.
- No permitir **contraseñas vacías**.
- No comunicar la contraseña a otras personas.
- Mantener un **histórico de contraseñas** para impedir la reutilización, según la política.
- Formar a los usuarios en la protección de sus credenciales.
- El PDF incluye también la **caducidad y el cambio periódico** entre las medidas de su política.

**Para el examen:** recuerda estas recomendaciones tal como aparecen en el material del profesor. La indicación de caducidad se reproduce como contenido de la diapositiva, no como una valoración general de todas las políticas actuales.

---

# PARTE 2 — CRIPTOGRAFÍA Y CERTIFICADOS DIGITALES

## 6. Criptología, criptografía y criptoanálisis (Parte 2, página 3)

La **criptología** comprende dos campos:

**Criptología = criptografía + criptoanálisis.**

- **Criptografía:** técnicas que transforman un mensaje legible en uno ilegible mediante algoritmos y claves para protegerlo frente a personas no autorizadas.
- **Criptoanálisis:** estudio de métodos para descubrir el mensaje original o la clave sin conocer el secreto utilizado.

### Vocabulario imprescindible

| Concepto | Significado |
|---|---|
| **Texto claro** | Mensaje legible sin cifrar |
| **Texto cifrado o criptograma** | Resultado del cifrado |
| **Clave** | Dato secreto o parte del par criptográfico que interviene en el algoritmo |
| **Cifrar** | Transformar el texto claro en texto cifrado |
| **Descifrar** | Recuperar el texto claro con el procedimiento y la clave adecuados |
| **Criptosistema** | Conjunto de elementos y reglas que hacen posible el cifrado y el descifrado |

### Los cinco conjuntos de un criptosistema

1. Mensajes **sin cifrar**.
2. Mensajes **cifrados**.
3. **Claves** posibles.
4. Transformaciones de **cifrado**.
5. Transformaciones de **descifrado**.

La condición fundamental es poder recuperar el mensaje original después de cifrarlo y descifrarlo con las claves que correspondan al sistema utilizado.

**Idea fundamental de las diapositivas:** la seguridad debe depender de la **clave**, no de mantener oculto el algoritmo.

## 7. Cómo se compromete un criptosistema (Parte 2, página 4)

El **criptoanálisis** busca recuperar información o claves sin conocer de antemano la clave adecuada.

| Técnica | Qué intenta hacer |
|---|---|
| **Fuerza bruta** | Probar muchas claves hasta descubrir una válida |
| **Texto claro escogido** | Estudiar resultados de cifrar mensajes elegidos y buscar relaciones con otros cifrados |
| **Análisis lineal** | Buscar relaciones matemáticas entre texto claro y texto cifrado |
| **Análisis diferencial** | Observar cómo cambia el cifrado cuando los textos originales presentan pequeñas diferencias |
| **Obtener la privada desde la pública** | Intentar resolver los problemas matemáticos de un sistema asimétrico |

**Diferencia importante:** ocultar el nombre del algoritmo no constituye por sí mismo el tipo de criptoanálisis descrito en el PDF, porque se supone que el algoritmo puede ser conocido.

## 8. Criptografía simétrica y asimétrica (Parte 2, páginas 5 y 6)

### 8.1. Cifrado simétrico o de clave secreta

Se utiliza **la misma clave secreta para cifrar y descifrar**. Emisor y receptor necesitan conocerla.

**Ventajas:**

- Operaciones **rápidas** y eficientes.
- Adecuado para **grandes cantidades de datos**.
- Funcionamiento relativamente sencillo.

**Inconvenientes:**

- Hay que **distribuir la clave secreta** de forma segura.
- Puede resultar difícil administrar muchas claves diferentes.
- Las parejas de participantes necesitan disponer de secretos adecuados.

### 8.2. Cifrado asimétrico o de clave pública

Utiliza un **par de claves diferentes pero relacionadas**:

- **Clave pública:** se puede distribuir.
- **Clave privada:** debe permanecer protegida por su propietario.

Para **proteger la confidencialidad**, se cifra para el destinatario utilizando su clave pública y el destinatario recupera el mensaje con su clave privada, según el modelo que explica el temario.

La criptografía asimétrica permite también la **firma digital**, mediante la clave privada del firmante y la verificación con su clave pública.

**Inconveniente destacado:** los cálculos asimétricos son más costosos que el cifrado simétrico.

### 8.3. Comparación general

| Característica | Simétrica | Asimétrica |
|---|---|---|
| Número de claves | Una clave secreta compartida | Par de claves pública y privada |
| Rapidez | **Alta** | **Menor** |
| Volumen de datos | Muy adecuada para grandes volúmenes | No suele usarse para cifrar directamente grandes volúmenes |
| Problema destacado | Repartir la clave secreta | Confiar en que la pública pertenece a quien dice ser |
| Aplicaciones del temario | Cifrado de datos | Confidencialidad, autenticación, firma y gestión de claves |

**Idea clave:** la criptografía moderna suele combinar ambos métodos, formando un **sistema híbrido**.

## 9. Algoritmos simétricos (Parte 2, página 7)

| Algoritmo | Clave indicada en el PDF | Características principales del temario |
|---|---|---|
| **DES** | **56 bits** | Estándar histórico de 1977; cifrado en bloque; seguridad insuficiente actualmente |
| **3DES / TDES** | **112 bits** en la variante presentada | Emplea DES tres veces; fue habitual en banca y está en desuso |
| **AES** | **128, 192 o 256 bits** | Sustituyó a DES; deriva de Rijndael, diseñado por Daemen y Rijmen |
| **IDEA** | **128 bits** | Algoritmo histórico empleado en protección de correo, como PGP |

**Para memorizar:**

- **DES → 56 bits.**
- **3DES → triple aplicación de DES.**
- **AES → 128 / 192 / 256 bits.**
- **IDEA → 128 bits.**

### Confusión y difusión

Los dos conceptos aparecen juntos en la diapositiva:

- **Confusión:** relación compleja entre la **clave** y el texto cifrado. El material destaca que pequeños cambios de clave pueden alterar notablemente el resultado.
- **Difusión:** propagar la influencia de los cambios del **texto claro** a muchas partes del texto cifrado.

El PDF ilustra ambos con cambios aproximados del 50 % en el resultado cifrado.

**No confundas:** en la presentación, confusión se relaciona con la clave; difusión se relaciona con la propagación de cambios del mensaje.

## 10. Cifrado en bloque y cifrado en flujo (Parte 2, página 8)

Son dos maneras de aplicar el cifrado simétrico.

### Cifrado en bloque

- Divide la información en **bloques de tamaño fijo**.
- Cada bloque pasa por transformaciones de cifrado.
- La diapositiva explica operaciones iniciales, rondas, transformaciones finales y expansión de clave.
- Dependiendo del modo de funcionamiento, puede relacionar unos bloques con otros.

### Cifrado en flujo

- Trabaja con un **flujo o secuencia cifrante** que se combina con los datos.
- La secuencia se genera a partir de una clave o semilla.
- Resulta adecuado para procesar información de forma continua o por ráfagas.
- El profesor cita los algoritmos históricos **RC4** y **SEAL**.

| Bloque | Flujo |
|---|---|
| Procesa bloques de bits | Procesa datos mediante una secuencia cifrante |
| Repite operaciones sobre cada bloque | Combina los datos con el flujo generado |
| Ejemplo: familia de cifrados de bloque como AES | Ejemplos del PDF: RC4, SEAL |

**Punto común:** ambos son métodos simétricos y necesitan gestionar de forma segura las claves. El temario también advierte del peligro de **reutilizar el mismo flujo cifrante**.

## 11. Funciones hash (Parte 2, página 9)

Una **función hash criptográfica** convierte un mensaje de longitud variable en un valor o **resumen (*digest*) de longitud fija**.

Características explicadas en la presentación:

- Produce un resumen representativo del mensaje.
- Su diseño pretende que resulte **muy difícil reconstruir el mensaje original** a partir del resumen.
- Un cambio pequeño en el mensaje puede producir un resumen muy diferente.
- Es útil para **comprobar la integridad** y en los procesos de **firma digital**.

### Hash frente a cifrado

| Función hash | Cifrado |
|---|---|
| Obtiene un resumen | Obtiene un texto cifrado |
| No está pensada para deshacerse y recuperar el mensaje original | Permite descifrar con la clave adecuada |
| Uso destacado: integridad | Uso destacado: confidencialidad |

### Algoritmos citados en el material

**MD4, MD5, RIPEMD-160 y SHA-1.** La diapositiva los presenta como ejemplos históricos de funciones hash. No hay que interpretar esta lista como una recomendación de seguridad actual para nuevos sistemas.

**Concepto de examen:** si dos resúmenes calculados con el mismo algoritmo sobre el mismo archivo difieren, es una señal de que el contenido no coincide.

## 12. Criptografía clásica y esteganografía (Parte 2, páginas 10 y 16)

### Sustitución

Consiste en **reemplazar unos símbolos por otros** siguiendo una equivalencia.

### Cifrado César

Es un caso clásico de sustitución. Cada letra se reemplaza por otra situada un número fijo de posiciones más adelante en el alfabeto.

- El desplazamiento es la **clave**.
- El profesor cita el desplazamiento clásico de **3 posiciones**.
- Es fácil de romper con suficiente texto por su simplicidad.

### Permutación o transposición

No sustituye necesariamente los símbolos, sino que **cambia su orden** siguiendo una regla. El material menciona su disposición en una tabla y el intercambio de filas y columnas.

### Esteganografía

Busca **ocultar la existencia de una información** dentro de otro soporte o mensaje; el PDF menciona las **imágenes digitales**.

### Diferencias para memorizar

| Método | Qué se transforma |
|---|---|
| **Sustitución** | Unos símbolos se sustituyen por otros |
| **César** | Las letras se desplazan un número fijo de posiciones |
| **Permutación** | Se reorganiza el orden de los símbolos |
| **Esteganografía** | Se esconde la presencia del mensaje |

**Criptografía y esteganografía no significan lo mismo:** cifrar oculta el contenido; esteganografía intenta ocultar que el mensaje existe.

### Referencias históricas que aparecen en la presentación

- **Escítala espartana** y método de **César** como criptografía clásica.
- **Enigma** y **Lorenz**, sistemas estudiados durante la Segunda Guerra Mundial.
- **Alan Turing** y **Bletchley Park** como referentes de la criptografía moderna.
- Avance de los sistemas de **clave pública** en la década de 1970.

## 13. Claves públicas y privadas: confidencialidad y autenticación (Parte 2, páginas 11 y 12)

En la presentación se sitúa el desarrollo de los sistemas de clave pública en **1976**, con **Diffie y Hellman**.

### Confidencialidad

Si una persona quiere enviar información confidencial a otra:

1. Utiliza la **clave pública del destinatario** para proteger el mensaje.
2. El destinatario recupera el contenido con **su clave privada**.

**Para memorizar:** confidencialidad → **pública del receptor / privada del receptor**.

### Autenticación y firma

Para demostrar quién ha firmado:

1. El emisor utiliza **su clave privada** para firmar.
2. Los demás comprueban la firma mediante **la clave pública del emisor**.

El profesor lo representa de forma simplificada como «cifrar con la privada» y «comprobar con la pública». En el apartado de firma digital se detalla la intervención del **hash**.

**Para memorizar:** firma → **privada del emisor / pública del emisor**.

### Confidencialidad y autenticación a la vez

Se pueden combinar los mecanismos para que:

- Solo el destinatario autorizado pueda acceder al contenido.
- El receptor pueda comprobar la identidad del firmante y la integridad del mensaje.

## 14. Algoritmos asimétricos (Parte 2, página 13)

| Algoritmo | Características según el temario |
|---|---|
| **RSA** | Desarrollado por Rivest, Shamir y Adleman (1978). Basado en propiedades matemáticas de números primos grandes; utilizado para operaciones de clave pública y firma digital |
| **DSS** | *Digital Signature Standard*. Estándar orientado a **firma digital** |
| **ECC** | Criptografía de **curvas elípticas**; ofrece seguridad elevada con claves relativamente más cortas |

### Comparación de longitudes

El PDF incluye como ejemplos históricos claves **RSA de 1024–2048 bits** y curvas elípticas de menor longitud. Son cifras de la diapositiva, **no una recomendación actual de configuración**.

**Lo fundamental para el examen:** identificar cuáles son algoritmos **simétricos** (AES, DES, 3DES, IDEA) y cuáles pertenecen a la criptografía **asimétrica** (RSA, DSS, ECC).

## 15. Sistemas híbridos: PGP y GPG (Parte 2, página 14)

El **cifrado híbrido** combina:

- **Criptografía simétrica** para cifrar el contenido de forma rápida.
- **Criptografía asimétrica** para proteger o intercambiar la clave de sesión.

### PGP

**PGP** significa *Pretty Good Privacy*. La presentación lo relaciona con **Phil Zimmermann** y el año **1991**.

Esquema simplificado que explica el profesor:

1. Preparar o comprimir el mensaje.
2. Generar una **clave de sesión**.
3. Cifrar el contenido con esa clave simétrica.
4. Proteger la clave de sesión con la **clave pública del destinatario**.
5. Permitir que el destinatario recupere la clave de sesión y descifre el contenido.

### GPG

**GPG** significa *GNU Privacy Guard*.

- Es una herramienta de **software libre**.
- Es compatible con el estándar **OpenPGP**.
- Se utiliza para cifrado y firma en entornos como GNU/Linux.

**Idea clave:** PGP y GPG aprovechan la eficiencia del cifrado simétrico y las ventajas de gestión de claves del cifrado asimétrico.

## 16. Identificación digital, firma electrónica y certificado (Parte 2, página 15)

Son tres conceptos relacionados, pero diferentes.

| Concepto | Qué es |
|---|---|
| **Identificación digital** | Datos que permiten distinguir a una persona o entidad en el entorno digital |
| **Firma electrónica** | Mecanismo que permite comprobar el origen de un documento y proteger su integridad |
| **Certificado digital** | Documento que vincula una **clave pública** con su **titular** |

El certificado puede ser **emitido, renovado y revocado**.

**Diferencia esencial:** disponer de una clave pública no basta para saber a quién pertenece; el certificado permite establecer una relación de confianza con su propietario.

## 17. Infraestructura de clave pública: PKI (Parte 2, páginas 17 y 18)

**PKI** significa *Public Key Infrastructure*, en español **Infraestructura de Clave Pública**.

Es el conjunto de **herramientas, programas, equipos, procesos y procedimientos** que permiten crear, gestionar, almacenar, distribuir y revocar certificados digitales.

### El problema que resuelve

Una clave pública puede difundirse libremente, pero eso plantea una pregunta: **¿cómo sabemos que pertenece realmente a la persona, organización o servidor que afirma ser su titular?**

Las **autoridades de certificación (CA o AC)** ayudan a establecer esa confianza mediante certificados firmados digitalmente.

### Componentes de una PKI

| Componente | Función |
|---|---|
| **Autoridad de Certificación (CA / AC)** | Gestiona, firma y emite certificados |
| **Autoridad de Registro (RA)** | Comprueba y autoriza la asociación entre identidad y clave pública |
| **Titular del certificado** | Persona, organización o servidor al que pertenece |
| **Parte utilizadora** | Comprueba certificados y firmas para decidir si confía en ellos |
| **Repositorio** | Almacena y distribuye certificados y datos sobre su estado |
| **Autoridad de Validación** | Proporciona información sobre la validez del certificado; el PDF la considera opcional |

La diapositiva habla de **cinco componentes principales** y muestra además la **autoridad de validación** como elemento opcional.

**Para memorizar:** CA **emite y firma**, RA **verifica la identidad**, repositorio **almacena y distribuye**, parte utilizadora **comprueba**, titular **posee el certificado**.

## 18. Clases de certificados y niveles de validación (Parte 2, página 19)

El material presenta dos clasificaciones diferentes.

### Clases de verificación de identidad

| Clase | Verificación indicada en la diapositiva |
|---|---|
| **Clase 1** | Comprobación sencilla de nombre y correo electrónico |
| **Clase 2** | Verificación más completa de la identidad |
| **Clase 3** | Mayor exigencia; la presentación menciona verificación presencial |

### Validación de certificados web

| Sigla | Nombre | Qué comprueba |
|---|---|---|
| **DV** | *Domain Validation* | Control del dominio |
| **OV** | *Organization Validation* | Control del dominio y datos de la organización |
| **EV** | *Extended Validation* | Comprobación más exhaustiva de la organización |

**No confundas:** las clases 1, 2 y 3 y las siglas DV, OV y EV son **dos clasificaciones diferentes** en las diapositivas.

## 19. Contenido y estados de un certificado digital (Parte 2, página 20)

### Información incluida en un certificado

Según el profesor, un certificado contiene:

1. Número de **versión** y **serie**.
2. Identificación del **emisor** o autoridad de certificación.
3. Identificación del **sujeto** o titular.
4. **Periodo de validez**.
5. **Clave pública** del titular.
6. Información del método o algoritmo de **firma**.
7. **Firma digital de la autoridad de certificación**.

**Clave esencial:** el certificado distribuye y acredita la **clave pública**; la **clave privada** debe mantenerse protegida.

### Estados de un certificado

| Estado | Significado |
|---|---|
| **Emitido / vigente** | Se encuentra dentro de su periodo de validez y no consta anulado |
| **Expirado** | Ha superado su fecha de validez |
| **Revocado** | Ha sido invalidado antes de su caducidad |
| **Suspendido** | Estado temporal de suspensión previsto en la clasificación del PDF |

**Revocar** no significa lo mismo que **expirar**: la revocación es una invalidación anticipada; la expiración llega al terminar el periodo de validez.

## 20. Solicitud, emisión y validación de certificados (Parte 2, páginas 21 y 22)

### 20.1. CSR: solicitud de certificado

**CSR** significa *Certificate Signing Request*. Es la petición que presenta un solicitante para obtener un certificado firmado por una autoridad de certificación.

La diapositiva describe el proceso general:

1. El solicitante prepara una **CSR**.
2. La autoridad comprueba la identidad según el procedimiento establecido.
3. La CA **firma el certificado con su clave privada**.
4. Se entrega el certificado al solicitante.

### ¿Quién genera el par de claves?

El PDF muestra dos posibilidades y recomienda aquella en la que **el solicitante genera su propio par de claves** y envía **solo la solicitud**, manteniendo la clave privada en su equipo.

**Regla para estudiar:** la **clave privada del titular no debe distribuirse** junto con la CSR o el certificado público.

### 20.2. Comprobaciones de un navegador

Al recibir un certificado de un sitio web, el navegador necesita comprobar, según el proceso explicado en el temario:

- Su **periodo de validez**.
- Que el certificado se corresponde con el **servidor o identidad** esperada.
- Que la **firma** y la cadena de confianza conducen a una autoridad conocida.
- Que no figure como **revocado**, según los mecanismos disponibles.

El sistema de confianza se apoya en certificados de **autoridades de certificación** instaladas o reconocidas por el navegador.

### CRL y CSR: no son lo mismo

- **CSR:** solicitud de emisión de un certificado.
- **CRL:** *Certificate Revocation List*, lista de certificados revocados.

### Autoridades citadas en el PDF

En España: **FNMT, ACCV y Edicom**. También aparecen otros ejemplos internacionales en la diapositiva.

## 21. Firma digital (Parte 2, página 23)

Una **firma digital** permite comprobar la **autenticidad del firmante**, la **integridad del documento** y aportar evidencia de **no repudio**.

### Funcionamiento básico que representa el profesor

1. Se calcula un **hash** o resumen del documento.
2. El firmante utiliza su **clave privada** para generar la firma sobre ese resumen.
3. El receptor utiliza la **clave pública correspondiente** para verificarla.
4. Si el documento cambia, la verificación de integridad deja de coincidir.

### Qué garantiza y qué no

| Propiedad | ¿La aporta la firma digital? |
|---|---|
| **Autenticidad** | Sí, al vincular la firma con una clave verificable |
| **Integridad** | Sí, permite detectar cambios en lo firmado |
| **No repudio** | Sí, como evidencia de la participación del firmante según el modelo del temario |
| **Confidencialidad** | **No por sí sola**: el documento puede seguir siendo legible |

**Pregunta clásica:** una firma digital no sirve por sí misma para mantener secreto el contenido de un documento. Para eso es necesario **cifrarlo**.

### Aplicaciones citadas en la presentación

- Contratos electrónicos.
- Facturas electrónicas.
- Declaraciones tributarias.
- Notificaciones judiciales.
- Contratación pública.
- Sellado de tiempo y otros trámites digitales.

## 22. SSL, TLS y HTTPS (Parte 2, página 24)

### SSL y TLS

- **SSL** (*Secure Sockets Layer*) es una familia de protocolos antigua.
- **TLS** (*Transport Layer Security*) es su sucesor.
- Protegen las comunicaciones de aplicaciones que inicialmente no incorporaban cifrado.

### HTTPS

**HTTPS** es **HTTP protegido mediante TLS**. Se utiliza para proteger la comunicación entre el navegador y el servidor web.

El PDF también cita comunicaciones de correo y transferencia de archivos protegidas mediante protocolos de seguridad.

### ¿Por qué TLS utiliza técnicas híbridas?

Se combinan dos funciones:

- Mecanismos **asimétricos** para autenticar extremos y proteger o acordar los secretos de sesión.
- **Cifrado simétrico** para proteger los datos intercambiados, debido a su velocidad.

### Versiones

- **SSL 3.0**: versión antigua, desaconsejada por sus vulnerabilidades.
- **TLS 1.3**: versión recomendada explícitamente en la diapositiva.

**Idea de examen:** HTTPS implica que la comunicación emplea protección criptográfica, pero **no garantiza por sí solo** que el contenido de la página sea fiable.

## 23. Vulnerabilidades y ataques relacionados con SSL/TLS (Parte 2, páginas 25 y 26)

El profesor recoge los siguientes ataques históricos.

| Ataque | Año del PDF | Qué debes recordar |
|---|---|---|
| **BEAST** | 2011 | Ataque relacionado con SSL/TLS antiguos, especialmente TLS 1.0; puede comprometer información de una sesión |
| **Heartbleed** | 2014 | Fallo en **OpenSSL** que permite extraer fragmentos de memoria, potencialmente con información sensible |
| **POODLE** | 2014 | Explota una **degradación a SSL 3.0** y sus debilidades |
| **FREAK** | 2015 | Aprovecha el uso de suites de cifrado débiles **EXPORT** |
| **SSLStrip / MITM** | — | Manipulación de una comunicación o engaño para impedir o simular su protección |
| **Domain Fronting** | — | Técnica que aprovecha diferencias en la identificación del destino TLS/HTTP para ocultar el destino real |

### Explicación sencilla

**BEAST:** se relaciona con el cifrado de versiones antiguas de SSL/TLS. El temario cita ataques de intermediario y robo de información de sesión.

**Heartbleed:** se trata de una vulnerabilidad de una implementación, **OpenSSL**, y no de una técnica de adivinar contraseñas. Puede permitir leer datos de memoria.

**POODLE:** un atacante intenta **forzar una versión antigua del protocolo** (downgrade) para aprovechar sus fallos.

**FREAK:** se basa en la posibilidad de negociar o utilizar **cifrados débiles de exportación**.

**SSLStrip / MITM:** un intermediario puede manipular la conexión o engañar al usuario sobre la seguridad de la sesión.

**Domain Fronting:** técnica relacionada con la ocultación del destino de determinadas solicitudes, descrita en las diapositivas como una forma de eludir restricciones.

### Recomendaciones finales de las diapositivas

- Mantener actualizado el software criptográfico.
- Preferir versiones modernas de TLS, especialmente **TLS 1.3** según el temario.
- No continuar cuando un navegador muestra un **error de certificado** sin comprender y resolver la causa.
- Comprobar que la página utiliza la conexión segura esperada.

---

# REPASO FINAL DE TEORÍA — DIFERENCIAS QUE DEBES DOMINAR

| Conceptos | Diferencia fundamental |
|---|---|
| **Seguridad física / lógica** | Protege equipos / protege datos y accesos digitales |
| **Identificación / autenticación** | Declarar quién eres / demostrarlo |
| **Autenticación / autorización** | Verificar identidad / decidir permisos |
| **Fuerza bruta / diccionario** | Combinaciones posibles / palabras y claves habituales |
| **Permisos tradicionales / ACL** | Propietario, grupo y otros / permisos individualizados adicionales |
| **ACL de acceso / por defecto** | Permisos actuales / herencia de permisos futuros |
| **Criptografía / criptoanálisis** | Proteger mediante cifrado / intentar recuperar datos o claves |
| **Simétrica / asimétrica** | Una clave secreta / par pública y privada |
| **Cifrado en bloque / en flujo** | Grupos de bits / secuencia cifrante |
| **Hash / cifrado** | Resumen no reversible / transformación reversible con clave |
| **Sustitución / permutación** | Cambia símbolos / cambia el orden |
| **Cifrado / esteganografía** | Oculta significado / oculta presencia del mensaje |
| **AES / RSA** | Simétrico / asimétrico |
| **PGP / GPG** | Sistema de protección de mensajes / implementación libre OpenPGP |
| **Clave pública / privada** | Puede compartirse / debe protegerse |
| **CA / RA** | Emite y firma certificados / verifica identidades |
| **CSR / CRL** | Solicitud de certificado / lista de revocaciones |
| **Certificado / firma digital** | Vincula pública y titular / acredita autenticidad e integridad |
| **Firma / cifrado** | Verifica origen e integridad / protege confidencialidad |
| **DV / OV / EV** | Dominio / organización / validación ampliada |
| **Expiración / revocación** | Finaliza la validez temporal / se anula anticipadamente |
| **SSL / TLS** | Protocolo antiguo / sucesor moderno |
| **BEAST / Heartbleed / POODLE / FREAK** | TLS antiguo / memoria OpenSSL / downgrade SSL 3.0 / cifrado EXPORT |

## Nombres y cifras del profesor para memorizar

| Elemento | Dato |
|---|---|
| **DES** | 56 bits |
| **3DES** | Triple DES; el PDF cita 112 bits |
| **AES** | 128, 192 o 256 bits |
| **IDEA** | 128 bits |
| **RSA** | Rivest, Shamir y Adleman (1978) |
| **Criptografía de clave pública** | Diffie y Hellman (1976) |
| **PGP** | Phil Zimmermann (1991) |
| **GPG** | GNU Privacy Guard / OpenPGP |
| **TLS** | Versión recomendada en las diapositivas: 1.3 |
| **Hash citados** | MD4, MD5, RIPEMD-160, SHA-1 |
| **Tres factores** | Saber, tener, ser |
| **Permisos Linux** | r = 4, w = 2, x = 1 |
| **PKI** | Confianza y gestión de certificados |

**Resumen de la UD3 en una frase:** la seguridad lógica controla quién puede acceder a la información; la criptografía protege los datos; las firmas, certificados y las autoridades de certificación permiten verificar origen, integridad y confianza en las comunicaciones.

---

*Fin de los apuntes. Contenido exclusivamente teórico, basado en los dos PDF de la Unidad 3. No se incluyen entregables ni prácticas.*
