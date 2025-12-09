# Restaurant - HTB - pwn

Class: pwn
Status: Done

![Untitled](Untitled.png)

Usamos el comando pwn con el parámetro checksec y obtenemos lo siguiente:
 

![Untitled](Untitled%201.png)

Lo pasamos por ghidra y buscamos la función main para ver que hace el programa

![Untitled](Untitled%202.png)

Probamos con la opción 1 que nos lleva a la función fill(), que contiene lo siguiente:
(Notice we can leak the `libc runtime` address via the **puts** call. Also we can overflow the buffer of **local_28** variable to control the **RIP**. Let’s check the `drink()` function now.) 

![Untitled](Untitled%203.png)

Ahora anlizamos la función drink() 

![Untitled](Untitled%204.png)

Después la pasamos a gef para intentar hacer un desbordamiento en drink

Al parecer con un patron 100 bytes podría llegar al RIP

![Untitled](Untitled%205.png)

Al pasarle el patrón e inyectarlo en la función fill() 

![Untitled](Untitled%206.png)

Detuvo la operación en el ret, así que apuntaremos cerca de dicho

![Untitled](Untitled%207.png)

Indagando en los comandos de gef di con la forma para encontrar una variable capaz  con el siguiente valor, después di con la herramienta Ropper que sustituye a ROPgadget, ya que está sería capaz de encontrar más información. 

![Untitled](Untitled%208.png)

![Untitled](Untitled%209.png)

![Untitled](Untitled%2010.png)

creamos el exploit y con el template de la herramienta de pwninit 

Ejecución del exploit 

![Untitled](Untitled%2011.png)

Ahora podemos decir que cada instrucción es de 8 bytes, probaremos llenarlo mayor a 40

Dato: 
Al ejecutar el programa, nos damos cuenta que al hacer un salto, este ejecuta el brinco y finaliza

![Untitled](Untitled%2012.png)

Ahora debemos modificar agregando la instrucción .recvuntil(b'\n') script para poder obtener la versión filtrada, la siguiente declaración nos ayudará. 

`leakedputsLibc = u64(sh.recvuntil(b'\n').strip().ljust(8, b'\x00'))`

Por lo siguiente se agrego las siguiente declaraciones: 

![Untitled](Untitled%2013.png)

El output: 

![Untitled](Untitled%2014.png)

Ahora apuntamos hacía la librería, aquí no hay tanto problema debido a que está se encontraba en el zip. 
Para encontrar la forma de atacar la librería, encontré la siguiente declaraciones 

```
libcBase = leakedputsLibc - libc.sym['puts']
info('libcBase: %#x', libcBase)

libc.address = libcBase
```

Nos servirá para apuntar correctamente nuestro payload 

![Untitled](Untitled%2015.png)

Ahora que tenemos todos nuestro valores correctos agregamos los valores concretos para que se nos devuelva una shell

![Untitled](Untitled%2016.png)

Obtenemos un shell 

![Untitled](Untitled%2017.png)

![Untitled](Untitled%2018.png)