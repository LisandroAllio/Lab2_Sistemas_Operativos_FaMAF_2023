# Laboratorio 2: Semáforos en XV6 #

# Introducción #
En este laboratorio se implementará un sistema de Semáforos para espacio de usuario,
que sirven como mecanismo de sincronización entre procesos. Se implementarán en la
versión RISC-V de XV6 (sistema operativo con fines académicos) en espacio de kernel y
deberá proveer syscalls accesibles desde espacio de usuario.

Como los semáforos vienen en varios estilos, en este laboratorio vamos a implementar
sólo un estilo de semáforos llamado semáforos nombrados inspirándose en los
semáforos nombrados que define POSIX.

Características de los semáforos nombrados:

- Son administrados por el kernel.

- Están disponibles globalmente para todos los procesos del sistema operativo
(i.e. no hay semáforos "privados").

- Su estado se preserva mientras el SO esté activo (i.e., se pierden entre reinicios).

- Cada semáforo tiene un "nombre" que lo identifica con el kernel, en nuestro caso
los nombres son números enteros entre 0 y un límite máximo (idea similar a los
file descriptors).

# Su trabajo #
1) Implementar 4 syscalls: sem_open(), sem_up(), sem_down(),sem_close().
2) Implementar un programa de espacio de usuario “pingpong” que funcione de la
“manera natural”.

# Las Syscalls #
int sem_open(int sem, int value) → Abre y/o inicializa el semáforo “sem” con un
valor arbitrario “value”.

int sem_close(int sem) →Libera el semáforo “sem”.

int sem_up(int sem) →Incrementa el semáforo ”sem” desbloqueando los procesos
cuando su valor es 0.

int sem_down(int sem) →Decrementa el semáforo ”sem” bloqueando los procesos
cuando su valor es 0. El valor del semaforo nunca puede ser menor a 0

Para todas las syscalls el valor “sem” es un entero entre 0 y un número máximo a definir
por ustedes. Cada una de las funciones anteriores devuelven 0 en caso de error.

Analizar y definir cuales son los posibles errores para cada una de ellas. Posiblemente
surjan preguntas como “que sucede si ejecutamos sem_close() de un semáforo que aún
está siendo utilizado” o “que sucede si ejecutamos un sem_up() de un semáforo cuando
su valor es igual al valor de inicialización”. Ustedes deben decidir qué se hace en estos
casos, definiendo así las políticas de su sistema de semáforos.

Para implementar las syscalls deberán usar acquire() release(), wakeup(),
sleep() y argint(). Es parte del laboratorio que investiguen y aprendan sobre estas
funciones del kernel (Ver las ayudas al final).

También es parte del laboratorio que:

- Lo que implementen esté libre de problemas de concurrencia y condiciones de
carrera.

- Hagan validación de los argumentos de sus syscalls.

# El programa “pingpong” #
Deberán escribir un programa de usuario que sincroniza la escritura por pantalla de la
cadena "ping" y "pong" usando nuestro sistema de semáforos.

El comando “pingpong” deberá tomar como argumento un entero N (rally) que será la
cantidad de veces que aparecerá la palabra "ping" y la palabra "pong" por pantalla.

El programa pingpong deberá hacer un solo fork y con los dos procesos resultantes:

- Uno deberá imprimir "ping" N veces, pero nunca imprimir dos "ping" seguidos sin
que haya un "pong" al medio.

- El otro deberá imprimir "pong" N veces, pero nunca imprimir dos "pong" seguidos
sin que haya un "ping" al medio.

- La secuencia resultante siempre debe empezar con “ping”. Por ejemplo

  $ pingpong 1
  ping
  pong

- En caso de que haya un error, el comando el mensaje de error debe empezar con
ERROR en mayúsculas. por ejemplo Todo error tiene que arrancar con ERROR

  $ pingpong -1

  ERROR: El numero de rounds tiene que ser mayor a 1

- Se debe retornar el prompt de consola solo cuando el juego haya terminado por
completo (obviamente siempre que el comando pingpong se haya ejecutado en
modo foreground).
Es decir, deberá haber 2N líneas con "ping" y "pong" intercalados. Esta sincronización
entre procesos es posible de realizar usando semáforos, y así deberán hacerlo. Al
terminar la ejecución del programa, los semáforos deberán estar liberados para nuevos
usos.
