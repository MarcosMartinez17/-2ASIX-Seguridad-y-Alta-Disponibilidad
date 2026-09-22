Resumen: Como conectamos Ubuntu y Metasploitable

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

Punto 6:

Resumen de vulnerabilidades detectadas

| Severidad | Cantidad |
| :--- | :--- |
| Critical | 9 |
| High | 3 |
| Medium | 30 |
| Low | 8 |
| Info | 140 |

Cuáles tienen una puntuación CVSS más alta:
Las vulnerabilidades con la puntuación CVSS más alta son las de nivel crítico, alcanzando una puntuación de 10 como el servicio VNC 


Punto 7:

| Vulnerabilidad | Severidad / CVSS | Origen (diseño / implementación / uso) | Breve descripción |
| :--- | :--- | :--- | :--- |
| SSL Version 2 and 3 Protocol Detection | Crítica / 9.8 | Diseño | El fallo viene de cómo se ideó y diseñó el protocolo en su origen con problemas criptográficos estructurales, permitiendo ataques de intermediario. |
| SSL DROWN Attack Vulnerability | Media / 5.9 | Implementación | El fallo es un error concreto en la forma en la que el código del software implementó el soporte para SSLv2, permitiendo descifrar el tráfico con claves débiles. |
| SSL Anonymous Cipher Suites Supported | Baja / 5.9 | Uso | Se debe a una mala configuración o a dejar activadas por defecto opciones inseguras, permitiendo cifrados anónimos que no comprueban la identidad. |

Punto 8:

Que es: Son las cerraduras de seguridad antiguas que usan los programas para proteger la información. Como son de hace muchos años tienen fallos de diseño muy grandes y ya no sirven para proteger nada
Como se explota: Un pirata informático que este en la misma red se coloca en medio de la comunicación, obliga al ordenador a usar esas cerraduras viejas y débiles, y así consigue leer y robar todos los datos privados.
Como se mitiga: Entrar en la configuración y apagar por completo esas cerraduras antiguas (SSL 2 y SSL 3) para usar otras modernas y seguras que no se puedan romper tan fácil.
Referencia: Plugin de Nessus ID 20007 (SSL Version 2 and 3 Protocol Detection).
