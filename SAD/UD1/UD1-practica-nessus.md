Como conectamos Ubuntu y Metasploitable

Para que las dos máquinas virtuales pudieran hablar entre sí de forma privada, hicimos lo siguiente:

1. VirtualBox
   - Le pusimos un segundo cable de red  a cada máquina .
   - Les pusimos el mismo nombre a las dos para que supieran que están conectadas a la misma red privada.

2. Configurar Ubuntu:
   - Le dimos una dirección fija a su nuevo cable, que fue la 192.168.56.10.

3. Configurar Metasploitable:
   - Encendimos su segundo cable de red eth1 y le asignamos otra dirección fija vecina, que fue la 192.168.56.20.

4. Comprobación:
   - Probamos ping desde Ubuntu hacia Metasploitable y vimos que se respondían perfectamente, lo que significa que ya están conectadas.

# Informe de auditoría de vulnerabilidades

## 1. Contexto y alcance
En este trabajo hemos analizado la seguridad de una máquina virtual llamada Metasploitable, la cual está fabricada a propósito con fallos para poder practicar. El objetivo principal es descubrir qué puntos débiles tiene para saber cómo encontrarlos y solucionarlos.

## 2. Metodología
Para realizar el análisis, hemos montado una red privada usando VirtualBox. Desde un ordenador con Ubuntu, hemos usado una herramienta de escaneo llamada Nessus. Este programa actúa como un inspector que revisa a fondo la máquina objetivo para buscar todos sus fallos de seguridad.

## 3. Resumen de resultados

| Severidad | Cantidad |
| :--- | :--- |
| Critical | 9 |
| High | 3 |
| Medium | 30 |
| Low | 8 |
| Info | 140 |

Cuáles tienen una puntuación CVSS más alta:
Las vulnerabilidades con la puntuación CVSS más alta son las de nivel crítico, alcanzando una puntuación de 10

## 4. Vulnerabilidades clasificadas

| Vulnerabilidad | Severidad / CVSS | Origen (diseño / implementacion / uso) | Breve descripcion |
| :--- | :--- | :--- | :--- |
| SSL Version 2 and 3 Protocol Detection | Critica 9.8 | Diseno | El fallo viene de como se invento y se penso el sistema de seguridad al principio, haciendo que tenga fallos grandes que permiten que un ladron se meta en medio de la conversacion para cotillear. |
| SSL DROWN Attack Vulnerability | Media 5.9 | Implementacion | El fallo es un error especifico al escribir el codigo del programa, lo que hace que use herramientas viejas y debiles con las que se puede descifrar la informacion facilmente. |
| SSL Anonymous Cipher Suites Supported | Baja 5.9 | Uso | Ocurre por dejar mal configurado el programa o dejar activadas cosas inseguras de fabrica, permitiendo que cualquiera entre sin demostrar quien es. |

## 5. Análisis en profundidad

Que es: Son las cerraduras de seguridad antiguas que usan los programas para proteger la información. Como son de hace muchos años tienen fallos de diseño muy grandes y ya no sirven para proteger nada
Como se explota: Un pirata informático que este en la misma red se coloca en medio de la comunicación, obliga al ordenador a usar esas cerraduras viejas y débiles, y así consigue leer y robar todos los datos privados.
Como se mitiga: Entrar en la configuración y apagar por completo esas cerraduras antiguas (SSL 2 y SSL 3) para usar otras modernas y seguras que no se puedan romper tan fácil.
Referencia: Plugin de Nessus ID 20007 (SSL Version 2 and 3 Protocol Detection).

## 6. Recomendaciones
Para solucionar los problemas encontrados y dejar el sistema seguro, proponemos las siguientes acciones ordenadas por prioridad:

1. Apagar protocolos antiguos: Desactivar por completo el uso de sistemas de seguridad viejos y actualizar todo a tecnologías modernas.
2. Actualizar el software: Instalar las versiones más recientes de los programas para corregir los fallos de código que traían de fábrica.
3. Corregir configuraciones por defecto:** Cambiar las opciones que vienen activadas de serie de forma insegura, eliminando accesos anónimos o contraseñas débiles.

## 7. Conclusión
Sabiendo lo que sabemos ahora, no firmaríamos un contrato de mantenimiento para dejar este sistema funcionando tal como está, ya que tiene demasiadas puertas abiertas y es muy peligroso. Solo aceptaríamos hacernos cargo bajo la condición estricta de arreglar todos los fallos críticos, apagar los servicios viejos y aplicar una limpieza de seguridad completa antes de conectarlo a cualquier red real.

