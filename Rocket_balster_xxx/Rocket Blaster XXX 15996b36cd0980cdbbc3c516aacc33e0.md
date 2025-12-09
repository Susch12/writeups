# Rocket Blaster XXX

Class: pwn
Status: Done

Primero comenzamos con el analisis del binario con la herramienta checksec

![image.png](image.png)

Desde este punto nos da ciertas pistas de lo que podríamos hacer, pero continuaremos viendo su ejecución 

![image.png](image%201.png)

Es un bonito banner, pero necesitamos ver que ejecuta el programa como tal, así que pasaremos a usar ghidra 

![image.png](image%202.png)

Tenemos la función main en el decompiler y podemos ver la función banner, sin embargo no contiene algo que podría ser de interés debido a que solo imprime principalmente la imagen. 

![image.png](image%203.png)

Revisando las funciones en ghidra encontraremos una función bastante interesante llamada fill_ammo() que contiene lo siguiente:

![image.png](image%204.png)

Lo cual lo hace parecido al reto Buffer Overflow 2 de la plataforma PicoCTF, sin embargo en este nos piden tres variables. Ahora podemos empezar a construir un script.  

Primera versión: 

![image.png](image%205.png)

Nota: probando el script ajusto solo cuando llena el buffer a 0x28 y termina funcionando

Script final:

![image.png](image%206.png)

Prueba en ejecución local: 

![image.png](image%207.png)

Como puede verse en el entorno local, ahora probaré de forma remota

![image.png](image%208.png)