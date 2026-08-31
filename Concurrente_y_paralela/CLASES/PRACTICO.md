PRIMER CLASE PRACTICA

No es necesario usar un java avanzado, podemos usar el java8 que seria lo mejor.

Usar excalidraw para los diagramas
Usar visual paradigm para diagramas de clases
Se pregunta en parciales para que sirve el bloque parallel
Para los ejercicios se usa el libro de cupbook9 algo asi

**Diagramas UML:** Lenguaje unificado de modelado
es un lenguaje grafico para modelar, visualizar, especificar, construir y documentar un sistema de software

**Diagrama de clases:** 
![](../imagenes/Pasted%20image%2020260806154212.png)

Se deben mostrar la visibilidad de los miembros de la clase (atributo o metodo)
( - ) para privado
( + ) para publico
( # ) para protejido 

- Flecha con triangulo vacio  =  significa herencia

- Flecha con un rombo vacio y una flecha normal = significa agregacion
![](../imagenes/Pasted%20image%2020260806154638.png)

- Flecha normal o sin flecha = asociacion, peromte asociar objetos

- Flecha punteada normal = instanciacion o uso, se usa para denotar la instanciacion y dependencia de una clase a otra 


**Diagrama de secuencia:** Se representa por una flecha entre un objeto y el invocado, representa la llamada de un metodo (operacion) de un objeto en particular 
El metodo invocado retorna un valor,
![](../imagenes/Pasted%20image%2020260806155642.png)

-  Fragmento combinado: 
	-  Permite representar la interaccion entre fragmentos 
	- Un fragmento combinado se define por un operador interaccion y los   operandos de interaccion correspondientes



**Clase del 13 practica**

start lo usamos para comenzar el metodo run 
pregunta de cualquier etapa,¿Como se le da vida a un hilo?, ¿diferencia entre waiting, block y sleeping? (el sleeping se despierta solo, no espera a sera activado como blocking o waiting)

- **NEW**: el objeto `Thread` fue creado pero no se llamó `start()` todavía.
- **RUNNABLE**: está ejecutando o listo para ejecutar (esperando que el planificador del SO le dé CPU).
- **BLOCKED**: está esperando entrar a una sección `synchronized` que otro hilo tiene ocupada.
- **WAITING / TIMED_WAITING**: está pausado esperando una señal explícita (`wait()`, `join()`, `sleep()`).
- **TERMINATED**: terminó de ejecutar `run()`.

```java
Thread t = new Thread(() -> System.out.println("hola"));
System.out.println(t.getState()); // NEW
t.start();
System.out.println(t.getState()); // RUNNABLE (probablemente)

```
# El problema central: condiciones de carrera (race conditions)
Como los hilos comparten memoria, si dos hilos leen y modifican la misma variable **al mismo tiempo**, el resultado depende de un orden de ejecución impredecible. Esto es una **condición de carrera**.

```java
class Contador {
    private int valor = 0;

    public void incrementar() {
        valor++; // esto NO es una operación atómica
    }

    public int getValor() {
        return valor;
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Contador contador = new Contador();

        Runnable tarea = () -> {
            for (int i = 0; i < 100000; i++) {
                contador.incrementar();
            }
        };

        Thread t1 = new Thread(tarea);
        Thread t2 = new Thread(tarea);
        t1.start();
        t2.start();
        t1.join(); // esperar a que t1 termine
        t2.join(); // esperar a que t2 termine

        System.out.println("Valor final: " + contador.getValor());
        // Esperarías 200000, pero casi seguro da menos
    }
}
```
**¿Por qué falla?** `valor++` en realidad son tres pasos: leer `valor`, sumarle 1, escribir el resultado. Si dos hilos hacen esto "al mismo tiempo", pueden ambos leer el mismo valor antes de que ninguno escriba, y uno de los incrementos se pierde.

`join()` en el ejemplo de arriba merece explicación: hace que el hilo que lo llama (acá, `main`) **espere** hasta que el hilo `t1`/`t2` termine, antes de seguir. Sin eso, `main` podría imprimir el resultado antes de que los hilos terminen de contar.


# ==clase del 20 practica==

**Seccion Critica:** Bloque de codigo o una parte del programa donde se accede a un recurso y no puede ser ejecutada por mas de un hilo al mismo tiempo

# Synchronized
Cada objeto en java tiene un candado invisible asociado (se llama monitor). Synchronized es la palabra que le dice a un hilo: "antes de ejecutar este codigo, toma el candado de este objeto. Si otro hilo ya lo tiene, espera tu turno".

Cuando ponés `synchronized` en la firma de un método de instancia, todo el cuerpo del método pasa a ser la sección crítica, y el candado que se usa es el del objeto sobre el que se llama (`this`).
```java
class Contador {
    private int valor = 0;

    public synchronized void incrementar() {
        valor++;
    }
}

ES EQUIVALENTE A ESTO: 

public void incrementar() {
    synchronized (this) {
        valor++;
    }
}
```
El metodo static synchronized es un caso especial porque no usa el candado de un objeto si no el de la clase.

```java
class Contador {
    private static int total = 0;

    public static synchronized void incrementarTotal() {
        total++;
    }
}
```
aca el candado no es una instancia (this), sino de Contador. Esto importa porque si tenes synchronized de instancia y static synchronized en la misma clase, son candados distintos. Es un error comun pensar que protegen lo mismo.

En vez de sincronizar el metodo entero, delimitas manualmente donde empieza y termina la seccion critica:
```java
public void procesar() {
    calculoLibre(); // no crítico, corre sin bloquear a nadie

    synchronized (this) {
        valor++; // sección crítica explícita y mínima
    }

    logear("listo"); // no crítico
}
```
Esto es mejor que sincronizar todo el metodo porque reduce el tiempo que un hilo mantiene el candado tomado

En java, cualquier objeto puede usarse como candado, no hay una llave especial "Lock" obligatoria para sychronized
```java
Object llave = new Object(); // un objeto cualquiera, dedicado solo a ser candado

synchronized (llave) {
    // sección crítica
}
¿Por qué new Object() y no algo más elaborado? Porque no necesitás ningún comportamiento especial del objeto
```

Dos bloques sychronized solo se bloquean entre si si usan la misma llave (mismo objeto)

```java
class Cuenta {
    private double saldo;
    private final Object llaveSaldo = new Object();

    public void depositar(double monto) {
        synchronized (llaveSaldo) {
            saldo += monto;
        }
    }

    public void retirar(double monto) {
        synchronized (llaveSaldo) {
            saldo -= monto;
        }
    }
}

Acá, depositar y retirar, aunque son métodos distintos, se bloquean mutuamente porque comparten la misma llave (`llaveSaldo`). Si un hilo está depositando, otro hilo que quiere retirar tiene que esperar — exactamente lo que necesitás, porque ambos tocan `saldo`.

```

Un error clasico seria usar llaves distintas para proteger el mismo dato:
```java
class Cuenta {
    private double saldo;

    public void depositar(double monto) {
        synchronized (new Object()) { // ¡llave nueva cada vez!
            saldo += monto;
        }
    }
}
```
Esto no protege nada. Cada llamada crea un objeto Object nuevo como candado, asi que dos hilos nunca compiten por la misma llave, es como si sychronized no estuviera.

**¿Porque elegir un objeto dedicado en vez de this?**
Con sychronized (this), el candado es el objeto entero y cualquier otro codigo que tambien sincronice sobre el mismo objeto (incluso codigo externo a tu clase que tenga una referencia a tu objeto) compite por el mismo candado, aunque proteja algo totalmente distinto.
```java
class Cuenta {
    private double saldo;
    private String historialLog = "";

    public synchronized void depositar(double monto) { // usa el candado de 'this'
        saldo += monto;
    }

    public synchronized void agregarLog(String linea) { // también usa el candado de 'this'
        historialLog += linea;
    }
}
aca, depositar y agregarLog protegen datos completamente distintos, pero como ambos usan (this) como candado, se bloquean entre si sin necesidad real

```


# Ejemplo de synchronized 

```java
public class Main {

    public static void main(String[] args) {
        Cinema cinema = new Cinema();

        TicketOffice1 ticketOffice1 = new TicketOffice1(cinema);
        Thread thread1 = new Thread(ticketOffice1, "TicketOffice1");
// Cuando haces new Thread(ticketOffice1,"TicketOffice1"), lo que estas haciendo es crear un objeto Thread (que sabe manejar un hilo del SO) y decirle "Cuando arranques, ejecuta el run() de este otro objeto (ticketOffice1) no el tuyo propio"
        TicketOffice2 ticketOffice2 = new TicketOffice2(cinema);
        Thread thread2 = new Thread(ticketOffice2, "TicketOffice2");

        thread1.start();
        thread2.start();

        try {
            thread1.join();
            thread2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.printf("Room 1 Vacancies: %d\n", cinema.getVacanciesCinema1());
        System.out.printf("Room 2 Vacancies: %d\n", cinema.getVacanciesCinema2());

    }

    /**
     Room 1 Vacancies: 5
     Room 2 Vacancies: 6
     */

}

class TicketOffice1 implements Runnable {
    private Cinema cinema;

    public TicketOffice1(Cinema cinema) {
        this.cinema = cinema;
    }

    @Override
    public void run() {
        cinema.sellTickets1(3);
        cinema.sellTickets1(2);
        cinema.sellTickets2(2);
        cinema.returnTickets1(3);
        cinema.sellTickets1(5);
        cinema.sellTickets2(2);
        cinema.sellTickets2(2);
        cinema.sellTickets2(2);
        // Sala 1: 3+2-3+5=7
        // Sala 2: 2+2+2+2=8
    }
}

class TicketOffice2 implements Runnable {
    private Cinema cinema;

    public TicketOffice2(Cinema cinema) {
        this.cinema = cinema;
    }

    @Override
    public void run() {
        cinema.sellTickets2(2);
        cinema.sellTickets2(4);
        cinema.sellTickets1(2);
        cinema.sellTickets1(1);
        cinema.returnTickets2(2);
        cinema.sellTickets1(3);
        cinema.sellTickets2(2);
        cinema.sellTickets1(2);
        // Sala 1: 2+1+3+2=8
        // Sala 2: 2+4-2+2=6
    }
}

class Cinema {

    private long vacanciesCinema1;
    private long vacanciesCinema2;

    private final Object controlCinema1, controlCinema2;

    public Cinema() {
        controlCinema1 = new Object();
        controlCinema2 = new Object();
        vacanciesCinema1 = 20;
        vacanciesCinema2 = 20;
    }

    /**
     * Cinema1
     */
    public boolean sellTickets1(Integer number) {
        synchronized (controlCinema1) {
            if (number < vacanciesCinema1) {
                vacanciesCinema1 -= number;
                return true;
            }
            return false;
        }
    }

    /**
     * Cinema1
     */
    public boolean returnTickets1(int number) {
        synchronized (controlCinema1) {
            vacanciesCinema1 += number;
            return true;
        }
    }

    /**
     * Cinema2
     */
    public boolean sellTickets2(int number) {
        synchronized (controlCinema2) {
            if (number < vacanciesCinema2) {
                vacanciesCinema2 -= number;
                return true;
            }

            return false;
        }
    }

    /**
     * Cinema2
     */
    public boolean returnTickets2(int number) {
        synchronized (controlCinema2) {
            vacanciesCinema2 += number;
            return true;
        }
    }

    public long getVacanciesCinema1() {
        return vacanciesCinema1;
    }

    public long getVacanciesCinema2() {
        return vacanciesCinema2;
    }

}
```

### 1. `Main`: quién arranca qué

```java
Cinema cinema = new Cinema();               // se crea UNA sola instancia compartida
TicketOffice1 ticketOffice1 = new TicketOffice1(cinema);
Thread thread1 = new Thread(ticketOffice1, "TicketOffice1");
```

`ticketOffice1` es un `Runnable` que **recibe la misma instancia de `cinema`** en su constructor. Esto es la clave de todo: ambos hilos no tienen cada uno su propia `Cinema`, comparten literalmente el mismo objeto en memoria (mismo heap). Por eso hace falta sincronización — si cada hilo tuviera su propio `Cinema`, no habría ningún dato compartido y `synchronized` sobraría (como vimos en la respuesta anterior sobre cuándo hace falta).

`thread1.join()` y `thread2.join()` en el `main` hacen que el hilo principal espere a que **ambos** terminen antes de imprimir los resultados finales — si no estuviera el `join()`, podría imprimirse el resultado antes de que los hilos terminaran de vender entradas.

### 2. `TicketOffice1` y `TicketOffice2`: las tareas concurrentes

Cada uno implementa `Runnable` y en su `run()` hace una secuencia de llamadas a métodos de `cinema`. Importante: **dentro de cada `run()`, las llamadas son secuenciales** (una detrás de la otra, porque están en el mismo hilo). Lo que es concurrente es que **`run()` de `TicketOffice1` y `run()` de `TicketOffice2` corren al mismo tiempo, en hilos distintos**.

Entonces sí puede pasar, por ejemplo, que en el instante en que `thread1` está ejecutando `cinema.sellTickets1(3)`, `thread2` esté ejecutando `cinema.sellTickets2(2)` — **al mismo tiempo**, sobre el mismo objeto `cinema`.

### 3. `Cinema`: por qué hay dos candados y no uno

java

```java
private final Object controlCinema1, controlCinema2;
```

Esto es exactamente el patrón que vimos en la respuesta anterior sobre "llaves separadas por recurso" (`llaveSaldo` vs `llaveLog`). `vacanciesCinema1` y `vacanciesCinema2` son **dos datos independientes** — no hay ninguna razón para que proteger uno bloquee al otro.

java

```java
public boolean sellTickets1(Integer number) {
    synchronized (controlCinema1) {   // candado de la Sala 1
        if (number < vacanciesCinema1) {
            vacanciesCinema1 -= number;
            return true;
        }
        return false;
    }
}
```

`sellTickets1` y `returnTickets1` comparten `controlCinema1` como llave → se bloquean mutuamente entre sí. `sellTickets2` y `returnTickets2` comparten `controlCinema2` → se bloquean entre sí. Pero **`sellTickets1` nunca bloquea a `sellTickets2`**, porque usan llaves distintas — pueden ejecutarse en paralelo real, en núcleos distintos de CPU, sin esperarse.

Si en cambio hubieran usado `synchronized (this)` en los cuatro métodos, la Sala 2 quedaría bloqueada esperando cada vez que alguien opera sobre la Sala 1, aunque no tengan nada que ver — exactamente el problema que identificamos con `depositar`/`agregarLog` en la respuesta anterior.

### 4. Trazando la ejecución con un caso concreto

Tomemos solo las dos primeras líneas de cada hilo, que es donde **sí podría haber una intersección real** (ambos tocan la Sala 1 con `sellTickets1`/`sellTickets1`):

```
Hilo 1: cinema.sellTickets1(3)   ─┐
Hilo 2: cinema.sellTickets1(2)   ─┘  compiten por controlCinema1 — uno espera al otro
```

Si el Hilo 1 entra primero: lee `vacanciesCinema1 = 20`, hace `20 < 3`? no, entonces `vacanciesCinema1 -= 3` → 17, y libera el candado. Recién ahí el Hilo 2 puede entrar: lee 17, `17 < 2`? no, `17 -= 2` → 15. Sin `synchronized`, ambos podrían leer 20 al mismo tiempo y terminar con 17 o 18 en vez de 15 — el mismo problema del contador que vimos al principio, pero acá aplicado a un caso real.

Mientras tanto, si en simultáneo el Hilo 1 estuviera ejecutando `cinema.sellTickets2(2)`, **eso no espera nada del punto anterior** — usa `controlCinema2`, un candado totalmente distinto.

### 5. El resultado final (por qué da 5 y 6, no lo que dicen los comentarios)

Los comentarios en el código (`// Sala 1: 3+2-3+5=7`) calculan cada hilo _por separado_, como si el otro no existiera. Pero **las salas son compartidas**, así que hay que sumar el efecto de ambos hilos sobre la misma sala:

**Sala 1** (empieza en 20): ventas de Hilo 1 → `-3, -2, +3, -5`; ventas de Hilo 2 → `-2, -1, -3, -2`. Total: `20 -3-2+3-5 -2-1-3-2 = 5`.

**Sala 2** (empieza en 20): ventas de Hilo 1 → `-2,-2,-2,-2`; ventas de Hilo 2 → `-2,-4,+2,-2`. Total: `20 -2-2-2-2 -2-4+2-2 = 5`...

Déjame verificar Sala 2 con más cuidado: Hilo1 hace `sellTickets2(2)` tres veces (-2,-2,-2) más una al final (-2) = 4 veces de -2 = -8. Hilo2 hace `sellTickets2(2)` (-2), `sellTickets2(4)` (-4), `returnTickets2(2)` (+2), `sellTickets2(2)` (-2) = -6. Total: `20 - 8 - 6 = 6`. Coincide con el comentario `Room 2 Vacancies: 6`.

Lo esencial acá no es el número en sí, sino entender que **el resultado depende de la suma de las operaciones de ambos hilos sobre el mismo dato compartido**, y que `synchronized (controlCinemaX)` es lo único que garantiza que esa suma dé un resultado consistente sin operaciones perdidas — sin importar en qué orden intercalado el sistema operativo decida ejecutar las instrucciones de cada hilo.

# Comunicacion entre hilos 

- **wait()**: el hilo suelta el candado que tiene y se queda esperando, hasta que alguien lo despierte. Solo se puede llamar dentro de un bloque `synchronized` sobre la misma llave.
- **notify()**: despierta a **uno** de los hilos que está esperando en esa llave (no se sabe cuál, es al azar entre los que esperan).
- **notifyAll()**: despierta a **todos** los hilos que esperan esa llave; cada uno vuelve a chequear su condición y solo sigue el que la cumple.

```java
class Buffer {
    private boolean disponible = false;
    private final Object candado = new Object();

    public void esperarDato() throws InterruptedException {
        synchronized (candado) {
            while (!disponible) {
                System.out.println("Voy a esperar...");
                candado.wait(); // acá el hilo pasa a WAITING
            }
            System.out.println("¡Dato listo, sigo!");
        }
    }

    public void avisarListo() {
        synchronized (candado) {
            disponible = true;
            candado.notify(); // despierta al que está en wait()
        }
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Buffer buffer = new Buffer();

        Thread consumidor = new Thread(() -> {
            try { buffer.esperarDato(); } catch (InterruptedException e) {}
        });
        consumidor.start();

        Thread.sleep(100); // le damos tiempo a que el consumidor llegue a wait()
        System.out.println("Estado del consumidor: " + consumidor.getState()); // WAITING

        buffer.avisarListo(); // el productor avisa
        consumidor.join();
    }
}
```

**WAIT()**
Cuando un hilo llama a `candado.wait()`, pasan **tres cosas**, en este orden:

1. **Suelta el candado** que tenía tomado (el de `synchronized (candado)`). Esto es clave: si no lo soltara, nadie más podría entrar a cambiar la condición que está esperando, y quedaría esperando para siempre.
2. **El hilo pasa al estado `WAITING`**  En este estado, el hilo no consume CPU — el sistema operativo simplemente no le da tiempo de ejecución hasta que algo lo despierte. Esto es justo lo que evita el busy-waiting del punto 1.
3. Se queda ahí, dormido, hasta que otro hilo llame a `notify()` o `notifyAll()` sobre ese mismo candado.
 **Detalle importante:** cuando el hilo se despierta, **no entra directamente a ejecutar** — primero tiene que volver a conseguir el candado (porque lo había soltado en el paso 1). Si otro hilo lo tiene tomado en ese momento, el hilo que se despertó pasa brevemente por `BLOCKED` esperando el candado, antes de volver a `RUNNABLE`.

**NOTIFY() / NOTIFYALL()**
- **`notify()`** despierta a **uno** de los hilos que están en `WAITING` sobre ese candado — cuál, no lo decidís vos, es una elección interna de la JVM.
- **`notifyAll()`** despierta a **todos** los hilos que están esperando ese candado. Cada uno, al despertarse, vuelve a competir por el candado, y cuando lo consigue, **revisa de nuevo su condición** (por eso el `while` en vez de `if`, como vimos antes).

**LOCKS**
Es la alternativa moderna a synchronized, pensada para los casso donde synchronized se queda corto.

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Contador {
    private int valor = 0;
    private final Lock lock = new ReentrantLock();

    public void incrementar() {
        lock.lock(); // toma el candado (equivalente a entrar a un bloque synchronized)
        try {
            valor++;
        } finally {
            lock.unlock(); // SIEMPRE en un finally, para liberar el candado pase lo que pase
        }
    }
}
```

**¿Que gana lock sobre synchronized?**

- **tryLock():** intentar sin bloquear indefinidamente
```java
if (lock.tryLock()) {
    try {
        valor++;
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("No pude entrar, sigo haciendo otra cosa");
}
```
Con synchronized, sin el candado esta ocupado, tu hilo se queda esperando sin alternativa. con tryLock podes decidir "si esta ocupado hago otra cosa"

- **lockInterruptibly():** un hilo esperando puede ser interrumpido. Un hilo bloqueado en synchronized esperando un candado no puede ser cancelado desde afuera.
- Condiciones multiples por candado (Condition), que es la version mejorada de wait()/notify()
- 





# Clase del 27 practica

para los ejercicios ver el libro java que pasaron

deamons -> son hilos de baja prioridad

el hilo puede ignorar la peticion de ser interrupido por (isInterrupted())
cuando hacemos el interrupt no entra en ningun estado ni cambia el estado el hilo, solo levanta una flag
no afecta a la prioridad del hilo

la interrupcion mientras esta durmiendo, seria cuando se despierta, o sea su interrupcion es despertarse y vuelve a ready to run, o sea aca si cambia de estado


el JOIN() espera a que otros hilos terminen basicamente 
parcial es la clase 8 o sea la tercer clase de septiembre, quedan 4 clases a partir de hoy 27


## Overview

Repaso de conceptos de concurrencia en Java: interrupción de hilos (interrupt), sleep y join, con ejemplos prácticos y resolución de dudas sobre comportamiento y manejo de excepciones. Se conversó además sobre formato del parcial (teórico-práctico, 10 preguntas), fechas aproximadas y organización de grupos y entregas de trabajos prácticos.

## Puntos clave técnicos tratados

- Interrupción de hilos (Thread.interrupt / isInterrupted)
    
    - Se explicó que interrupt no “mata” al hilo: levanta una bandera (flag) que el hilo debe chequear para terminar voluntariamente.
    - Ejemplo: PrimeGenerator extiende Thread, dentro del run se verifica isInterrupted() en cada iteración; el main duerme 2 segundos y luego hace task.interrupt(). Tras el interrupt, el hilo puede completar una iteración más si no comprueba la bandera inmediatamente.
    - Si el hilo ignora la bandera (no la chequea), puede nunca detenerse.
    - Cuando un hilo está durmiendo (Thread.sleep) o en join y se le aplica interrupt, Java despierta al hilo inmediatamente y se lanza InterruptedException — en ese caso no hace falta chequear la bandera explícitamente (la interrupción “despierta” al hilo).
    - isInterrupted() y Thread.interrupted(): aclaraciones implícitas sobre lectura de la bandera y comportamiento (se discutió que interrupt simplemente “marca” la bandera; leerla obtiene true/false).
    - Interrupción sobre hilos en estados Waiting o Blocked también los despierta / hace lanzar la excepción correspondiente.
- Uso de InterruptedException y manejo de excepciones
    
    - Ejemplo FileSearch (búsqueda recursiva en directorios): al detectar isInterrupted() se lanza InterruptedException hacia arriba.
    - Distinción entre excepciones chequeadas y no chequeadas: las excepciones chequeadas deben declararse en la firma o capturarse; en run() (al implementar Runnable o sobrescribir run en Thread) no se puede cambiar la firma, por lo que run debe manejar la excepción internamente (try/catch).
    - Importancia de manejar excepciones para no exponer errores en producción (ej.: stack traces visibles en páginas web). Se comentó que se verá manejo con excepciones y handlers en próximas clases.
- Thread.sleep y TimeUnit
    
    - sleep suspende el hilo por la cantidad de milisegundos indicada; debe manejar InterruptedException.
    - TimeUnit ofrece métodos alternativos (ej. TimeUnit.SECONDS.sleep) con misma semántica.
    - Si un hilo dormido es interrumpido, sale inmediatamente de sleep lanzando InterruptedException.
- join y variantes
    
    - join se usa para esperar a que otro hilo termine su ejecución: el hilo que invoca join queda bloqueado hasta la finalización del hilo objetivo.
    - Existen sobrecargas: join() (espera indefinidamente), join(long millis) (espera como máximo X ms), y join(long millis, int nanos) (mayor precisión). Si finaliza el tiempo de espera, el join retorna y la ejecución continúa aunque el hilo objetivo no haya terminado.
    - join también lanza InterruptedException si el hilo que espera es interrumpido mientras está en join; por eso debe manejarse/capturarse.
    - Ejemplo: grupos de hilos (Group) que imprimen mensajes; main hace join sobre cada hilo para asegurar que los recursos/preparaciones terminen antes de continuar.

## Ejemplos prácticos revisados en clase

- PrimeGenerator: hilo que calcula e imprime primos; main lo inicia, duerme 2 segundos y lo interrumpe; se mostró cómo el hilo detecta la bandera y finaliza correctamente si chequea isInterrupted().
- FileSearch (FallSearch / FindSearch): búsqueda recursiva en directorios; ejemplo de tirar InterruptedException cuando se detecta interrupción y necesidad de propagar o capturar excepciones.
- Clock: hilo que imprime la fecha 10 veces con sleeps de 1s; main lo interrumpe tras 5s para mostrar que sleep+interrupt despiertan y lanzan excepción.
- DataLoader y NetworkConnectionLoader: dos loaders (uno duerme 4s, otro 9s); main inicia ambos y usa join sobre los dos para asegurar que la configuración esté cargada antes de continuar; se mostró comportamiento si se invierten órdenes o si alguno ya terminó antes del join.

## Preguntas y clarificaciones destacadas (con respuestas)

- ¿Qué hace interrupt()? — Levanta la bandera de interrupción del hilo objetivo; no fuerza la terminación inmediata salvo si el hilo está en sleep/join/wait (en cuyo caso se despierta y se lanza InterruptedException).
- ¿El hilo puede ignorar la interrupción? — Sí: si no chequea la bandera ni captura la excepción, puede seguir ejecutando indefinidamente.
- ¿Cómo “reactivar” un hilo interrumpido? — No se reactiva; interrupt es una notificación. Para reanudar trabajo habría que implementar lógica para volver a lanzar/crear el hilo o invocar nuevos métodos desde el programador.
- ¿Qué pasa con el estado del hilo tras interrupt? — No existe un estado “interrumpido” persistente; interrupt marca la bandera, y los estados de hilo siguen siendo los definidos por JVM (RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED). Si el hilo termina, su estado pasa a TERMINATED.
- ¿join bloquea al main? — Sí, el hilo que llama a join queda bloqueado hasta que el hilo objetivo finalice o hasta que expire el tiempo si se usa la variante con timeout.
- ¿Qué sucede si se lanza una excepción desde un hilo y no se maneja? — En aplicaciones serias (p. ej. web) es mala práctica dejar excepciones sin capturar; puede exponer detalles internos y generar errores visibles (stack traces) para usuarios.

## Decisiones y acción a seguir

- Material de práctica y ejercicios:
    - Se reconoció la necesidad de ejercicios prácticos para programar (corregir código roto, implementar soluciones) y se valoró la idea de publicar problemas y soluciones, posiblemente basados en el “Java Goodbook” citado por el docente.
    - El profesor considerará generar guías/ejercicios adicionales para practicar inclusión de interrupciones, sleeps y joins.
- Parcial y logística:
    - Parcial: será teórico-práctico (10 preguntas de respuesta corta — 1 línea a 1-2 párrafos). La parte práctica no exige escribir código, sino responder cuestiones relacionadas a lo visto en clase.
    - Fecha aproximada: entre la semana 8 y 9 del curso; se mencionaron fechas tentativas hacia finales de septiembre (posibles días 22, 24, 25 o 29), y que probablemente las mesas sean en martes (a confirmar). Profesor publicará la fecha final en el sistema (LEV / LED).
    - Se indicó que las consultas sobre modelo del parcial (corregir código vs escribir desde 0) y trozos prácticos se tomarán en cuenta: se anticipó que habrá ejercicios prácticos tipo “corregir código o preguntas cortas”.
- Organización de grupos y entregas:
    - Hay 154 inscriptos, ~131 con grupo; 23 alumnos sin grupo según conteo provisiorio (lista parcial de nombres fue leída por el profesor durante la clase).
    - Se pidió que quien no tenga grupo envíe correo al profesor (copiando a los dos docentes/ingeniero) y también usar el grupo de WhatsApp para coordinar.
    - Se anticipó que la modalidad de entrega del Trabajo Práctico 1 será modificada (por la cantidad de grupos) y se comunicará cómo será la entrega.

## Tareas y responsables

- Profesor (MAURICIO LUDEMANN CATALAN)
    - Publicar o coordinar ejercicios/guías prácticas adicionales para que los alumnos practiquen (posible extracción de ejemplos del libro Java Goodbook).
    - Confirmar y publicar fecha y modalidad definitiva del parcial en la plataforma (LEV/LED).
    - Comunicar la nueva modalidad de entrega del Trabajo Práctico 1.
- Alumnos sin grupo
    - Enviar correo al profesor (en copia a los demás docentes) para ser asignados a grupo; usar grupo de WhatsApp si corresponde.
- Grupo/Comunidad de alumnos
    - Compartir pregunteros o compilaciones de preguntas para estudio (se comentó que existe un preguntero extenso de más de 100 preguntas, aunque no oficial).

## Observaciones finales

- Se enfatizó la importancia de manejar correctamente interrupciones y excepciones en programas reales para evitar errores visibles y vulnerabilidades (ej.: stack traces en páginas web).
- Se anunció que en próximas clases se verán handlers de excepciones y mejores prácticas para el manejo global de errores.