# Great Old Talisman

Class: pwn
Status: Done

Challenge: 

![image.png](image.png)

El zip contenia los siguientes archivos: 

![image.png](image%201.png)

Analizamos la seguridad del binario y tiene las siguientes protecciones

![image.png](image%202.png)

Ahora pasamos el binario a ghidra para ver cómo funciona. 

Empezando con main tenemos lo siguiente: 

![image.png](image%203.png)

A simple vista solo destacariamos la instrucción read(0,talis + (long)local_14 * 8,2), sin embargo, no existe ninguna instrucción ret y solo existe el exit después de la función read. Por lo que no existen algún lugar donde suceda un buffer overflow. 

En ghidra podemos encontrar la función read_flag que es la siguiente

![image.png](image%204.png)

Ahora el objetivo es desde el binario llamar a read_flag

Ejecución del binario: 

![image.png](image%205.png)

Probamos con un payload más grande para si podemos obtener un SIGSEV

![image.png](image%206.png)

Al parecer solo acepta escribir 2 bytes. Cabe recordar que la función en la que escribimos es read(0,talis + (long)local_14 * 8,2). Entonces solo tenemos la capacidad de escribir 2 bytes a partir de un offset de talis. Esto nos da una primitiva de escritura out-of-bounds (OOB). La idea es apuntar a la Tabla de Offsets Globales (GOT), donde las direcciones de las funciones externas se almacenan cuando se llaman dentro del programa.

Para esto necesitamos la dirección de talis 

![image.png](image%207.png)

Y la tabla got de exit@GLIBC 

![image.png](image%208.png)

Hay que darse cuenta de que talis y las entradas de la GOT están muy cerca entre sí. De hecho, podemos calcular el offset relativo entre talis y la entrada de exit en la GOT:

![image.png](image%209.png)

El desplazamiento está escalado por el tamaño de long de 8 bytes en la instrucción de read. Entonces necesitamos ingresar -4 para escribir en la entrada de exit. 
Ahora vamos a desarrollar el exploit 

![image.png](image%2010.png)

En este caso se usa la instrucción p16(context.binary.sym.read_flag & 0xffff), desarrollando un poco esta hace lo siguiente: 
- context.binary.sym.read_flag: Obtiene la dirección de la función read_flag del binario cargado.

- & 0xffff: Toma solo los últimos 2 bytes (16 bits) de esa dirección. Esto es necesario porque el programa solo lee 2 bytes en la dirección calculada (read(0, talis + offset, 2)).

- p16(...): Convierte esos 2 bytes en formato little-endian, el estándar en la mayoría de los sistemas Linux.

Usamos el exploit

![image.png](image%2011.png)

Efectivamente funcionó. 

![image.png](image%2012.png)