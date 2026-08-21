

que es el interleaving (pregunta de coloquio)

**CONCURRENCIA:**
(Organiza y coordina tareas que pueden solaparse en el tiempo)
Es la capacidad de un sistema para gestionar varias tareas que avanzan de manera entretejida. No implica que se ejecuten al mismo tiempo; significa que el diseño permite alternar entre ellas de forma segura. Sistemas operativos, bucles de eventos y servidores de peticiones son ejemplos tipicos: coordinan recursos, deciden que tarea sigue y mantienen la coherencia del estado compartido.

**PARALELISMO:**
El paralelismo se cenra en ejecutar tareas simultaneas usando multiples unidades de ejecucion. Puede ocurrir en diferentes niveles: varios nucleos de CPU, instrucciones vectoriales (SIMD) o calculo masivo en GPU. El objetivo es acelerar un trabajo dividiendolo en partes que puedan resolverse al mismo tiempo sin bloquearse entre si.

**¿Porque no son lo mismo?**
La concurrencia es un **modelo de organización**; el paralelismo es un **modelo de ejecución**. Un programa puede estar diseñado para manejar muchas solicitudes sin que ninguna se bloquee (concurrencia), aun cuando un solo núcleo las ejecute en turnos. A la inversa, una tarea de ciencia de datos puede dividirse en partes independientes y ejecutarse en distintos núcleos (paralelismo) sin necesidad de coordinar interacciones complejas entre ellas.

En resumen: la concurrencia resuelve _qué_ tareas existen y cómo se relacionan; el paralelismo resuelve _cuántas_ de ellas se ejecutan exactamente al mismo tiempo.


- **Vida diaria, cocina familiar**: un chef prepara salsa mientras el horno cocina el pan. Alterna atención entre tareas (concurrencia) y, si hay dos personas cocinando, algunas acciones ocurren en paralelo.
- **Vida diaria, atención al cliente**: un agente atiende varias llamadas pasando de una a otra (concurrencia). Si hay varios agentes trabajando a la vez, las llamadas se atienden en paralelo.
- **Informática, servidor web**: acepta miles de solicitudes. Concurrencia permite que ninguna espere bloqueada por otra; el paralelismo aparece cuando varias solicitudes se procesan simultáneamente en distintos hilos o procesos.
- **Informática, renderizado 3D**: un motor divide una escena en fragmentos que la GPU procesa al mismo tiempo (paralelismo de datos). La coordinación de tareas de IO, física y audio se maneja de forma concurrente.

###  ¿Por qué existen los hilos?

Un programa normal se ejecuta como un único **proceso**, y dentro de ese proceso, por defecto, todo el código corre en un único **hilo** (thread) de ejecución secuencial: una instrucción después de la otra.

El problema: si tu programa tiene que hacer varias cosas que no dependen unas de otras (por ejemplo, descargar un archivo _y_ dibujar una interfaz gráfica _y_ escuchar el teclado), hacerlo todo secuencialmente es un desperdicio. Mientras esperás la descarga, la CPU está ociosa en vez de atender la interfaz.

Los hilos permiten que **un mismo proceso tenga múltiples líneas de ejecución concurrentes**, compartiendo memoria (variables, objetos) pero avanzando de forma independiente.

**Proceso:** Un proceso es un programa en ejecucion, un proceso simple tiene un hilo de ejecucion, un programa, entradas/salidas y estados.
* **Procesos cooperantes:** Se entiende que los procesos interactuan entre si.
* **Procesos independientes:** No requieren informacion de otros.

**Proceso vs. Hilo — la distinción clave:**

- Un **proceso** tiene su propio espacio de memoria, aislado de otros procesos (ej: abrir Chrome y abrir Word son dos procesos distintos, no comparten memoria).
- Un **hilo** vive _dentro_ de un proceso y comparte memoria (heap) con los demás hilos de ese proceso. Cada hilo tiene su propio _stack_ (para variables locales y llamadas a métodos), pero el heap es compartido.


Los programas concurrentes estan compuestos por procesos (threads o componentes) que necesitan interactuar. Existen varios mecanismos de interaccion entre procesos, entre estos se encuentran la memoria compartida y el pasaje de mensajes.

Ademas los programas concurrentes deben, en general, colaborar para llegar a un objetivo comun, para lo cual la sincronizacion entre procesos es crucial.

**Problemas comunes de los programas concurrentes:**
- Violacion de propiedades universales (invariantes)
- **Starvation** (Inanicion): Uno o mas procesos quedan esperando indefinidamente un mensaje o la liberacion de un recurso 
	- Hay 1 CPU y varios procesos en cola.
	- Proceso C tiene prioridad baja.
	- Siempre llegan procesos nuevos con prioridad alta (A y B), que se ejecutan antes.
	- C espera, pero nunca llega el momento en que le toque ejecutar porque el planificador favorece siempre a los de prioridad alta.
	- Resultado: C “se queda con hambre” ⇒ starvation.
	-  Estrategias: backoff exponencial, prioridades balanceadas y uso de `SwitchToThread`/`Sleep` con pausas prudentes.
	
- **Deadlock**: Dos o mas procesos esperan mutuamente el avance del otro. 
	- Hilo A: necesita Recurso 1 y luego Recurso 2.
	- Hilo B: necesita Recurso 2 y luego Recurso 1.
	- Secuencia:
	    1. A bloquea Recurso 1.
	    2. B bloquea Recurso 2.
	    3. A intenta bloquear Recurso 2, pero está ocupado por B.
	    4. B intenta bloquear Recurso 1, pero está ocupado por A.
	- Resultado: A y B esperan para siempre ⇒ deadlock.
	- Prevención: definir un orden global de adquisición, evitar locks innecesarios y fijar timeouts en operaciones de bloqueo para detectar el problema en pruebas (`WaitForSingleObject` con tiempo finito + log).
	
- **Livelock:** Dos o mas procesos no pueden avanzar en su ejecucion porque continuamente responden a los cambios en el estado de otros procesos
	- Dos hilos intentan adquirir dos candados (o un recurso doble).
	- Si uno detecta conflicto, suelta el candado y “vuelve a empezar”.
	- Pero ambos hacen lo mismo exactamente al mismo tiempo:
	    1. Hilo A agarra Candado 1, Hilo B agarra Candado 2.
	    2. A intenta Candado 2 y falla; lo suelta.
	    3. B intenta Candado 1 y falla; lo suelta.
	    4. Vuelven a intentar y repiten el ciclo.
	- No hay deadlock (no están “esperando” bloqueados), pero tampoco avanzan ⇒ livelock.

**Operacion atomica:** En programación concurrente, una operación es **atómica** cuando se ejecuta de manera **indivisible e ininterrumpida**, lo que significa que **no puede ser interrumpida** por otros hilos o procesos y **no se queda a medias**.

**Interleaving:** Son los procesos concurrentes que se ejecutan intercalando las acciones atomicas que las componen.
- **Mecanismo de ejecución:** En lugar de que cada proceso se ejecute de principio a fin sin interrupciones, el planificador del sistema operativo asigna pequeños turnos de CPU a cada tarea. Esto crea la ilusión de simultaneidad, especialmente en sistemas de un solo núcleo.  
- **No determinismo:** Dado que el orden exacto en que ocurren estos entrelazamientos puede variar entre ejecuciones, los programas concurrentes son inherentemente no determinísticos. Esto significa que dos ejecuciones del mismo código pueden producir resultados diferentes o diferentes órdenes de operaciones. 
- **Implicaciones en la sincronización:** El **interleaving** es la raíz de problemas como las **condiciones de carrera** y las **violaciones de atomicidad**.  La sincronización (usando mutex, semáforos, etc.) tiene como objetivo principal restringir estos entrelazamientos para garantizar que ciertas secuencias de instrucciones se ejecuten como una unidad atómica, excluyendo la intercalación de otros hilos durante su ejecución.


**Sistema de transicion de estados:**  Es un grafo dirigido en el cual los nodos son los estados del sistema (posiblemente infinitos estados), las aristas son las transisicones atomicas de estados en estados, dadas por las sentencias del sistema.

# Estados de los procesos:
![420](../imagenes/Pasted%20image%2020260818191827.png)

**Modelo de 3 estados (el nucleo de la ejecucion):** 
* Ready: El proceso tiene todo lo que necesita para trabajar, excepto el procesador. Esta en fila esperando su turno.
* Running: El proceso tiene el procesador y esta ejecutando sus instrucciones en este preciso momento.
* Blocked: El proceso no puede avanzar, incluso si le dieran el procesador, porque esta esperando que ocurra un evento externo (como leer un archivo del disco duro, esperar que el usuario presione una tecla, o esperar la respuesta de otro proceso).
**Modelo 5 estados (El ciclo completo de vida):**
+ Activo: esta ejecutandose
+ Preparado: Todas las tareas estan listas para ejecutarse pero se espera a que un/el procesador quede libre (hay otros procesos mas prioritarios en ejecucion)
+ Bloqueado o suspendido: Que se termine una operacion de E/S o que se reciba una señal de sincronizacion
+ Nonato: Indica que el programa realmente existe pero todavia no es conocido por el OS
+ Muerto: Cuando ha terminado su ejecucion o el sistema operativo a detectado un error fatal.

# Estados de un hilo:

![443](../imagenes/Pasted%20image%2020260818194918.png)

+ **New:** El hilo acaba de ser creado en la memoria (se construyó el objeto, por eso la flecha dice `construct`), pero el sistema operativo aún no lo ha puesto en la fila para ejecutarse. Solo cuando en el código se llama al método `Thread.start()`, el hilo cobra vida y pasa a estar listo.

+ **Ready to run:** El hilo tiene el control de la CPU en este milisegundo y está ejecutando su código. **El rol del Scheduler:** Notarás que hay un óvalo naranja llamado **Scheduler** que envuelve a `Ready-to-Run` y `Running`. El planificador de la CPU está constantemente moviendo los hilos entre "Listo" y "Ejecutando" (dándoles turnos de fracciones de segundo) para que parezca que todos avanzan a la vez.

+ **Dead:** El hilo terminó de hacer su trabajo (su método `run()` completó su tarea o hubo un error que lo forzó a salir). Un hilo muerto no puede volver a iniciar.

+ **Sleeping - pausa por tiempo:** El hilo decide pausarse a sí mismo voluntariamente por un tiempo específico usando `Thread.sleep(milisegundos)`. Es como poner una alarma. No necesita que nadie lo despierte. Simplemente espera a que el tiempo transcurra (`Elapsed Time ends`) y automáticamente vuelve a la fila de `Ready-to-Run`.

+ **Waiting - pausa por señales:** El hilo se pausa de forma indefinida porque necesita que **otro hilo** le avise que ya puede continuar. Entra en este estado usando `lock.wait()`. Un hilo en _Waiting_ jamás se despertará solo por más que pase el tiempo. Depende exclusivamente de que otro hilo ejecute un comando de notificación (`lock.notify()` o `lock.notifyAll()`) para decirle: "Oye, ya hice mi parte, te toca". Al recibir la señal, vuelve a `Ready-to-Run`.

+ **Blocking - pausa por recurso o sincronizacion:** El hilo intenta acceder a un recurso que está ocupado o es lento. Entra aquí por dos razones principales:
	+ **Bloqueo por I/O:** Está esperando leer un archivo grande del disco duro o esperando datos de internet.
	+ **Bloqueo por Monitor (Candado):** Está intentando entrar a una sección de código protegido (`Synchronize block`), pero otro hilo ya tiene la llave ("lock") y está adentro.
	El hilo no está esperando un tiempo ni un aviso explícito, está esperando que **se libere un recurso**. Tan pronto como el disco duro termine de traer el dato (`I/O completed`) o el otro hilo suelte la llave de la sección protegida (`lock acquired`), este hilo se desbloquea y vuelve a `Ready-to-Run`







**ver indeterminismo**
**entender redes de petri**

que es el universo del discurso (pregunta de coloquio) : es la combinacion de todos los simbolos en el orden que quieras


