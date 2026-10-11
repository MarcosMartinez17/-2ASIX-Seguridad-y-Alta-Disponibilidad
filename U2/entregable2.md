# Entregable 2 — Atacando y defendiendo Estudio Técnico Torrent S.L.

## Paso 1. Datos que pueden aprovechar los atacantes

| Dato encontrado | Fuente (1-6) | ¿Para qué le sirve al atacante? | ¿Cómo se puede evitar? |
|---|---|---|---|
| Los nombres y trabajos de Marta, Andrés y Lucía. | 1 | Para hacerse pasar por uno de ellos o ganarse la confianza de alguien. | Publicar solo los datos necesarios y comprobar quién pide información. |
| Los correos electrónicos de los tres socios. | 1 | Para enviar mensajes falsos que parezcan importantes. | Enseñar a reconocer correos sospechosos y activar la verificación en dos pasos. |
| La dirección y el horario de la oficina. | 1 | Para saber dónde está la empresa y cuándo puede haber menos gente. | Tener buenas cerraduras, alarma y control de quién entra. |
| La empresa utiliza GestiCloud para clientes y facturas. | 1 | Para enviar correos falsos que aparenten venir de ese programa. | Entrar al programa desde su web habitual, no desde enlaces inesperados. |
| La web indica que usa GestWeb 2.4. | 1 | Para buscar fallos de seguridad de esa versión. | Mantener la web y sus programas actualizados. |
| El servidor usa Ubuntu 8.04 y otros programas antiguos. | 2 | Para intentar aprovechar fallos conocidos de programas sin actualizar. | Cambiar a versiones actuales que reciban actualizaciones de seguridad. |
| Hay tres ordenadores con Windows 7. | 2 | Para buscar puntos débiles en esos ordenadores. | Actualizar los equipos a un sistema con soporte. |
| Las copias de seguridad se guardan en un NAS. | 2 | Para intentar acceder a ellas, borrarlas o dejarlas inutilizables. | Proteger las copias y guardar otra copia separada. |
| En el registro del dominio aparece el móvil y correo personal de Marta. | 3 | Para contactar con ella usando engaños más creíbles. | Ocultar los datos personales del registro cuando sea posible. |
| Se conocen las direcciones de los servidores de la empresa. | 4 | Para saber qué servicios están conectados a internet. | Dejar accesibles solo los servicios necesarios y protegerlos. |
| La configuración del correo permite demasiada libertad (`SPF +all`). | 4 | Para intentar enviar correos que parezcan de la empresa. | Corregir la configuración para indicar qué servidores pueden enviar correos. |
| Faltan otras protecciones del correo (DKIM y DMARC). | 4 | Para facilitar que un correo falso parezca auténtico. | Activar esas protecciones para detectar mensajes suplantados. |
| Marta publicó una foto con una nota adhesiva legible en un monitor. | 5 | Para leer información que podría ser privada, como claves o apuntes internos. | Revisar las fotos antes de subirlas y no dejar datos sensibles a la vista. |
| Marta contó que trabaja desde una cafetería con su wifi. | 5 | Para intentar aprovechar una conexión poco segura. | Evitar redes públicas para tareas importantes o usar una conexión protegida. |
| La respuesta automática dice que Marta estará fuera del 1 al 15 de agosto. | 6 | Para elegir un momento en el que ella no pueda comprobar los mensajes. | No dar tantos detalles sobre las ausencias en respuestas automáticas. |
| Se indica que Lucía lleva los pagos y los cambios de datos bancarios. | 6 | Para dirigirle una petición falsa de cambio de cuenta. | Confirmar cualquier cambio de cuenta por teléfono, usando un número conocido. |
| La respuesta automática incluye el móvil de Andrés. | 6 | Para llamarlo y hacerse pasar por un cliente o un compañero. | Comprobar la identidad antes de dar información o hacer cambios. |

## Paso 2. Plan de ataque (máximo 5 líneas)

1. Primero reuniría los nombres, correos y datos de la empresa que están publicados.
2. Aprovecharía las vacaciones de Marta para enviar a Lucía un correo falso haciéndome pasar por ella.
3. En ese correo intentaría convencerla de cambiar la cuenta bancaria de una factura.
4. También buscaría fallos en los ordenadores y programas antiguos de la empresa.
5. Mi objetivo sería conseguir dinero o información privada de la empresa.

## Paso 3. Las tres medidas más importantes

### 1. Confirmar los cambios de cuenta bancaria por teléfono

Es la más importante para evitar una estafa directa. Si Lucía recibe un correo pidiendo cambiar una cuenta, debe llamar a un número que ya conozca para comprobar que la petición es real. Así no se fía solo de lo que pone en un mensaje.

### 2. Actualizar los ordenadores y los servidores

La empresa utiliza Windows 7 y Ubuntu 8.04, que son sistemas muy antiguos. Si se siguen usando, pueden tener fallos de seguridad fáciles de aprovechar. Actualizarlos ayuda a evitar que alguien entre en los equipos o robe archivos.

### 3. Mejorar la seguridad del correo de la empresa

Ahora mismo es más fácil que alguien mande un correo haciéndose pasar por la empresa. Configurar bien SPF y activar DKIM y DMARC ayuda a reconocer estos mensajes falsos. Esto es importante porque los empleados y clientes podrían confiar en ellos.

**¿Por qué he elegido estas tres?** Porque ayudan a evitar tres problemas graves: perder dinero por una estafa, que alguien entre en los ordenadores y que suplanten el correo de la empresa.
