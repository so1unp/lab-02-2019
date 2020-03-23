
# Laboratorio 2 - Llamadas al Sistema

_Nota_: las respuestas a las preguntas en los ejercicios pueden incluirlas en un archivo de texto con el nombre `respuestas.txt`.

## Ejercicio 1

El programa `hola.c` imprime el mensaje `¡Hola Mundo!` en la _salida estándar_, utilizando la función de biblioteca [`printf()`](http://man7.org/linux/man-pages/man3/printf.3.html). 

Compilarlo con el comando `make hola` y ejecutarlo utilizando el comando `strace`, como se indica a continuación, para ver cuales son llamadas al sistema que utiliza el programa:

```bash
$ make hola
$ strace bin/hola > /dev/null
```

**Nota**: mediante `> /dev/null` se redirije la _salida estándar_ de `bin/hola` al archivo especial del sistema `/dev/null`, que descarta cualquier dato que se escriba en el mismo. De esta manera, se evita que la salida del comando `hola` se mezcle con la de `strace`.

Responder:

1. Identificar las llamadas al sistema utilizadas por las funciones de biblioteca empleadas en el programa, `printf` y `exit`.
2. Describir los parámetros que se utilizan en la invocación de la llamada al sistema que se encarga de imprimir el mensaje en la _salida estándar_.

## Ejercicio 2 - Llamadas al sistema para archivos

Completar el programa `copy.c` para que permita realizar copias de un archivo:

```bash
$ copy archivo-original archivo-nuevo
```

Se deben tener en cuenta las siguientes consideraciones:

* Si `archivo-original` no existe, entonces el programa debe notificarlo al usuario y terminar la ejecución. Se debe indicar al entorno que el programa falló, retornando `EXIT_FAILURE`.
* Si `archivo-nuevo` ya existe, se sobreescribe.
* Los permisos de `archivo-nuevo` deben ser `0644`.

Además, deben utilizar **únicamente** las siguientes llamadas al sistema:

* [`open()`](http://man7.org/linux/man-pages/man2/open.2.html)
* [`read()`](http://man7.org/linux/man-pages/man2/read.2.html)
* [`write()`](http://man7.org/linux/man-pages/man2/write.2.html)
* [`close()`](http://man7.org/linux/man-pages/man2/close.2.html)

## Ejercicio 3 - Señales

Completar el programa `ouch.c` para que cada vez que reciba la señal `SIGINT` imprima por la _salida estándar_ `Ouch!`. El programa debe terminar luego recibir 5 señales.

Para atrapar la señal se debe emplear la llamada al sistema [`sigaction()`](http://man7.org/linux/man-pages/man2/sigaction.2.html). Utilizar la llamada al sistema [`pause()`](http://man7.org/linux/man-pages/man2/pause.2.html) para evitar realizar una espera activa.

La señal `SIGINT` puede ser disparada desde el teclado, mediante la combinación `Ctrl+C` (que en la jerga se indica como `^C`).

## Ejercicio 4 - Traza de llamadas al sistema

En este ejercicio, se modificara el _kernel_ de _xv6_ para que imprima un mensaje cada vez que se invoca una llamada al sistema. Este mensaje indicará el nombre de la llamada al sistema ejecutada y el valor que retorna.

Para esto, se debe modificar la función `syscall()` en el archivo `syscall.c`. Esta función es ejecutada por el sistema operativo cuando se detecta que una _interrupción por software_ ha sido generada desde un proceso de usuario.

Una vez hecha la modificación, al compilar y ejecutar _xv6_ en QEMU se tendría que ver algo similar a esto:

```bash
xv6...
cpu0: starting
sys_fstat -> 0
sys_write -> -1
...
sys_write -> 1
sys_fork -> 2
sys_exec -> 0
sys_open -> 3
sys_close -> 0
$sys_write -> 1
 sys_write -> 1
```

Las últimas lineas muestran como el proceso `init` crea y ejecuta el programa `sh` (el interprete de comandos) y luego `sh` escribiendo el símbolo `$` (el _prompt_ o símbolo de sistema).

### Entrega

Agregar en el directorio del Laboratorio:

* Una copia del archivo `syscall.c` modificado (ejecutar `git add syscall.c` para agregarlo al _commit_).

* Un archivo de texto donde este el resultado de ejecutar el comando `echo hola`, y se explique brevemente para que se invocan las llamadas al sistema que aparecen.

## Ejercicio 5 - Implementar una nueva llamada al sistema

Este ejercicio vamos a modificar nuevamente el _kernel_ de a _xv6_, para agregar una **nueva llamada al sistema** que retorne al usuario la fecha UTC actual del sistema.

Para facilitar la implementación, utilizar como base el código fuente de una llamada al sistema ya existente, como por ejemplo `uptime()`.

Para leer el reloj del sistema y obtener la fecha UTC, hay que usar la función auxiliar `cmostime()`, implementada en el archivo `lapic.c`. Esta función requiere como parámetro un puntero a una estructura `struct rtcdate`, definida en el archivo `date.h`.

Para probar la nueva llamada al sistema, usar el archivo `date.c` como base para un programa que invoca la llamada al sistema e imprime por la salida estándar la fecha UTC. Se debe agregar `date.c` a la lista  `UPROGS` del `Makefile` de _xv6_ para que sea compilado como un programa de usuario.

### Entrega

Agregar en el directorio del Laboratorio un archivo de texto donde se explique brevemente las modificaciones realizadas en cada uno de los archivos de _xv6_ que hayan modificado. Recordar utilizar `git add` para que Git agregue este archivo en el _commit_.

---

¡Fin del Laboratorio 2!
