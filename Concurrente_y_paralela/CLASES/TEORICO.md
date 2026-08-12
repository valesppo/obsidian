

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