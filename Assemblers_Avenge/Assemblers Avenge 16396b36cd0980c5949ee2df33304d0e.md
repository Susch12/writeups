# Assemblers Avenge

Class: pwn
Status: Done

Challenge

![image.png](image.png)

Revisamos las seguridades del binario.

![image.png](image%201.png)

Hacemos una prueba de ejecución para ver que hace el programa. 

![image.png](image%202.png)

Okay parece algo muy simple, probamos con el input /bin/sh y este es el output

![image.png](image%203.png)

Veamos un poco como se ve el código en ensamblador con el debugger gef y nos muestra la siguiente funciones

![image.png](image%204.png)

Vamos analizando las funciones en su código en ensamblador, tenemos la función _start que podría decirse que se trata de como si fuese la función main() ya que se llaman a todas las demás funciones como se puede ver en la imagen.

![image.png](image%205.png)

La función que podemos usar para obtener una /bin/sh es la función _read que contiene las siguientes instrucciones

![image.png](image%206.png)

Lo que hace la función es que almacena los datos leídos en el espacio reservado en la pila, específicamente en la dirección [rbp-0x8] y debido a que podemos ingresar valores mayor a 0x18 podemos realizar un buffer con tan solo 0x10 bytes. Ahora debemos encontrar como explotar este bug. 

Revisando las funciones podemos usar como gadget la instrucción ‘jmp rsi’ que encontramos en la función _exit

![image.png](image%207.png)

Al usar un input de 24 bytes los registros se denotan de la siguiente manera

![image.png](image%208.png)

Podemos notar que en el registro $rsi se almacena toda nuestra cadena, cabe recordar que necesitamos tan solo 8 bytes para realizar nuestro desbordamiento y que nuestro payload iniciaria en la instrucción rbp-0x8, esto hará que los primeros 8 bytes serán desperdiciados entonces nos dejarían con 0x10 bytes para usar nuestro /bin/sh. Para nuestra suerte /bin/sh ya existe en la memoria así que no es necesario escribirlo en nuestro payload, sin embargo necesitamos tanto del gadget jmp_rsi y configurar registros para ejecutar execve("/bin/sh", 0, 0)

De acuerdo aquí esta el exploit y comprobaremos su funcionamiento

![image.png](image%209.png)

![image.png](image%2010.png)

![image.png](image%2011.png)

Listo!