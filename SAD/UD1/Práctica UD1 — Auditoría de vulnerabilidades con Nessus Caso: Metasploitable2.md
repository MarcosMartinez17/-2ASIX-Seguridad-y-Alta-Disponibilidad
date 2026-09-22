Resumen: Como conectamos Ubuntu y Metasploitable

Para que las dos máquinas virtuales pudieran hablar entre sí de forma privada, hicimos lo siguiente:

1. VirtualBox
   - Le pusimos un segundo "cable de red" virtual a cada máquina (Ubuntu y Metasploitable).
   - Les pusimos el mismo nombre a las dos para que supieran que están conectadas a la misma red privada.

2. Configurar Ubuntu:
   - Le dimos una dirección fija a su nuevo cable, que fue la 192.168.56.10.

3. Configurar Metasploitable:
   - Encendimos su segundo cable de red eth1 y le asignamos otra dirección fija vecina, que fue la 192.168.56.20.

4. Comprobación:
   - Probamos ping desde Ubuntu hacia Metasploitable y vimos que se respondían perfectamente, lo que significa que ya están conectadas.


| Vulnerabilidad | Severidad / CVSS | Origen (diseño / implementación / uso) | Breve descripción |
| :--- | :--- | :--- | :--- |
| SSL Version 2 and 3 Protocol Detection | Crítica / 9.8 | Diseño | El fallo viene de cómo se ideó y diseñó el protocolo en su origen con problemas criptográficos estructurales, permitiendo ataques de intermediario. |
| SSL DROWN Attack Vulnerability | Media / 5.9 | Implementación | El fallo es un error concreto en la forma en la que el código del software implementó el soporte para SSLv2, permitiendo descifrar el tráfico con claves débiles. |
| SSL Anonymous Cipher Suites Supported | Baja / 5.9 | Uso | Se debe a una mala configuración o a dejar activadas por defecto opciones inseguras, permitiendo cifrados anónimos que no comprueban la identidad. |
