Resumen: Como conectamos Ubuntu y Metasploitable

Para que las dos máquinas virtuales pudieran hablar entre sí de forma privada, hicimos lo siguiente:

1. VirtualBox
   - Le pusimos un segundo "cable de red" virtual a cada máquina (Ubuntu y Metasploitable).
   - Les pusimos el mismo nombre a las dos para que supieran que están conectadas a la misma red privada.

2. Configurar Ubuntu:
   - Le dimos una dirección fija a su nuevo cable, que fue la 192.168.56.10.

3. Configurar Metasploitable:
   - Encendimos su segundo cable de red eth1 y le asignamos otra dirección fija vecina, que fue la 192.168.56.20.

4. **Comprobación:**
   - Probamos a mandar un mensaje de prueba ping desde Ubuntu hacia Metasploitable y vimos que se respondían perfectamente, lo que significa que ya están conectadas.
