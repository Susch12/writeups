# You know 0xDiablos

Class: pwn
Status: Done

![Untitled](Untitled.png)

Al descomprimir nos damos cuenta que solo viene un ejecutable

Lo pasamos por ghidra y viene la función principal, la función vuln y la función flag 

![Untitled](Untitled%201.png)

![Untitled](Untitled%202.png)

![Untitled](Untitled%203.png)

La ejecución del programa usual es el siguiente: 

![Untitled](Untitled%204.png)

![Untitled](Untitled%205.png)

Verificamos la seguridad que tiene el archivo vuln con la herramienta checksec

![Untitled](Untitled%206.png)

Investigando un poco más me di cuenta que podría explotarse el no canary 

El ejecutar el file para obtener más información nos damos cuenta de que es un archivo de 32 bits 

![Untitled](Untitled%207.png)

Probamos con un patrón 1024 de gef 

![Untitled](Untitled%208.png)

![Untitled](Untitled%209.png)

Vemos que la ejecución se detuvo en el eip 0x62616177 ahora debemos buscar cuanto es necesario para llegar al eip 

![Untitled](Untitled%2010.png)

Ahora sabemos que necesitamos agregar 188 bytes para el payload

Aquí podemos retomar la función flag 

![Untitled](Untitled%2011.png)

Investigando podemos decir que podríamos usar el ret2win
Para un ataque ret2win se usa la siguiente formula
padding + flagAddr + returnAddress + param1 + param2

param1: 0xdeadbeef

param2: 0xc0ded00d

Creamos un script: 
para explotarlo 

![Untitled](Untitled%2012.png)

EL output: 

![Untitled](Untitled%2013.png)