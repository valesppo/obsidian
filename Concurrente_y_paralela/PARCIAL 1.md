interleaving, estados de un hilo (de donde vienen hacia donde van), porque una maquina(turing,moore,mealy,de pila, lineal acotado y finito) es mejor que otra, monitores,synchronized,lock,con que automata se derivan formulas,modelo reactivo,como se protegen los recursos compartidos,automatas,gramatica,operaciones atomicas,tipos de lenguajes relacionados con su gramatica




# Resumen de Concurrente y Paralela: Parcial 1

**Cómo está armado:** primero el **resumen por temas** (cada tema con su definición, el concepto y ejemplos). Después, en un bloque aparte, las **47 preguntas con respuestas cortas**. Al final hay un **anexo** con material extra que no está en las preguntas.

## Índice

**Parte I: Concurrencia**
1. Concurrencia y paralelismo
2. Interleaving y no determinismo
3. Condición de carrera y operaciones atómicas
4. Testing y verificación de software
5. Sección crítica y protección de recursos compartidos
6. `synchronized`
7. `wait()`, `notify()` y `notifyAll()`
8. Monitores
9. Semáforos
10. Locks y ownership (dueño)
11. Variables locales de hilo (`ThreadLocal`)
12. Estados de un hilo o proceso
13. Cantidad de estados de un proceso con varios hilos
14. Hilos daemon y finalización de un programa

**Parte II: Teoría de la computación**
15. Lenguajes y gramáticas
16. Autómatas
17. Jerarquía de Chomsky
18. Potencia de las máquinas y tesis de Church-Turing
19. Análisis de lenguajes: `aⁿbⁿ` y variantes
20. Máquinas de Moore y de Mealy

**Parte III: Sistemas reactivos y modelado**
21. Programas reactivos y tiempo real
22. Modelado y verificación

**Preguntas y respuestas cortas (P1 a P47)** · **Anexo (A1 a A8)**

---

# PARTE I: CONCURRENCIA

## 1. Concurrencia y paralelismo

**Definiciones**
- **Concurrencia:** varias tareas **progresan en períodos superpuestos**. Puede ocurrir en un solo núcleo alternando entre tareas. Es una propiedad de la **estructura** del programa.
- **Paralelismo:** varias tareas se ejecutan **literalmente al mismo tiempo**, en varios núcleos o procesadores. Es una propiedad de la **ejecución**.

| | Concurrente | Paralelo |
|---|---|---|
| Idea | Tareas que se superponen en el tiempo | Tareas que corren a la vez |
| Requiere | Un núcleo alcanza (alternando) | Varios núcleos |
| Cómo se ve | Intercalado (*interleaving*) | Simultaneidad real |
| Objetivo típico | Atender varias cosas (E/S, eventos) | Acelerar cálculo |

Todo paralelo es concurrente, pero no todo concurrente es paralelo.

**Proceso vs hilo.** Un proceso tiene su propio espacio de memoria. Los **hilos** de un mismo proceso **comparten memoria** (heap), y eso es la fuente de todos los problemas de concurrencia.

**Ejemplo.** Un servidor que atiende a 100 clientes con un solo núcleo es concurrente (alterna entre clientes). Si además tiene 8 núcleos y atiende a 8 a la vez, hay paralelismo.

---

## 2. Interleaving y no determinismo

**Definición.** El **interleaving** (intercalado) es el modelo con el que se razona la ejecución concurrente: equivale a **una única secuencia** que mezcla las **acciones atómicas** de todos los procesos, en cualquier orden posible, **respetando el orden interno de cada proceso**.

**Concepto**
- Un proceso `A` es la secuencia `a1, a2, a3…` y otro `B` es `b1, b2…`. El interleaving `a1 b1 b2 a2 a3` es válido. `a2 a1 …` **no** lo es (rompe el orden de `A`).
- Quien elige el intercalado es el **planificador (scheduler)**, y el programador no lo controla. Por eso el comportamiento es **no determinista**: dos corridas pueden dar trazas distintas.
- **Cantidad:** con procesos de `n` y `m` acciones hay `(n+m)! / (n!·m!)` interleavings. Con 2 y 2 son 6, con 3 y 2 son 10, con 3 y 3 son 20. Crece explosivamente.
- **Granularidad:** depende de qué se considere "acción atómica". Cuanto más grande es lo atómico, menos interleavings hay.
- Sirve igual para un procesador (alternancia) que para varios (simultaneidad).
- Un programa concurrente es correcto solo si **todos** los interleavings posibles dan resultados aceptables.

**Determinismo.** Si el sistema es determinístico (mismas entradas ⇒ mismo resultado) y las acciones son **atómicas**, todos los interleavings terminan en el **mismo resultado**: las trazas difieren, pero lo observable es igual. Esto vale cuando las acciones atómicas son independientes o **conmutativas**. Si no conmutan (`x = x + 1` y `x = x * 2`, ambas atómicas), el orden cambia el resultado y hace falta sincronizar a nivel lógico.

**Ejemplo.** Dos hilos de 2 acciones (`a1 a2` y `b1 b2`) tienen 6 interleavings: `a1a2b1b2`, `a1b1a2b2`, `a1b1b2a2`, `b1a1a2b2`, `b1a1b2a2`, `b1b2a1a2`.

---

## 3. Condición de carrera y operaciones atómicas

### Condición de carrera (race condition)
**Definición.** Situación en la que el resultado depende del orden de intercalado de accesos de varios hilos a un recurso compartido, y al menos uno escribe.

**Por qué ocurre con acciones no atómicas.** Una sentencia que parece una sola (`valor++`) son varias instrucciones (`LOAD`, `ADD`, `STORE`). Entre ellas otro hilo puede intercalarse, y como el planificador elige un orden distinto en cada corrida, el resultado cambia (o es erróneo).

**Ejemplo (pérdida de actualización, *lost update*).** `valor = 0` y dos hilos hacen `valor++`:

| Paso | Hilo 1 | Hilo 2 | `valor` en memoria |
|---|---|---|---|
| 1 | LOAD → r1 = 0 | | 0 |
| 2 | | LOAD → r2 = 0 | 0 |
| 3 | ADD → r1 = 1 | | 0 |
| 4 | STORE valor = 1 | | 1 |
| 5 | | ADD → r2 = 1 | 1 |
| 6 | | STORE valor = 1 | **1** |

Debería ser 2. Otra corrida puede dar 2, y por eso el error es intermitente.

```java
class Contador { int valor = 0; void incrementar() { valor++; } }   // NO es seguro

Contador c = new Contador();
Runnable tarea = () -> { for (int i = 0; i < 100_000; i++) c.incrementar(); };
Thread t1 = new Thread(tarea), t2 = new Thread(tarea);
t1.start(); t2.start(); t1.join(); t2.join();
System.out.println(c.valor);   // casi nunca 200000
```

### Operaciones atómicas
**Definición.** Una operación es **atómica** si es **indivisible**: se ejecuta completa o no se ejecuta ("todo o nada"), y ningún otro hilo puede observar un estado intermedio ni intercalarse a mitad de camino.

**Para qué sirven**
- Son las "instrucciones" que el interleaving mezcla: cuanto más grande es lo atómico, menos interleavings y más fácil razonar.
- Permiten construir la sincronización: las primitivas de exclusión mutua se apoyan en instrucciones atómicas de hardware (**test-and-set**, **compare-and-swap (CAS)**).
- Evitan condiciones de carrera en operaciones simples sin locks.

**En Java, qué es atómico y qué no**
- Lectura/escritura de primitivos de hasta 32 bits y de referencias: **atómica**.
- `long` y `double` sin `volatile`: **no garantizado**.
- `volatile`: hace atómica la lectura o escritura simple y da **visibilidad**, pero **no** las operaciones compuestas. `volatile int x; x++` sigue siendo una carrera.
- `i++`, *check-then-act* ("si es null, asigno") y *read-modify-write*: **no atómicas**.
- `java.util.concurrent.atomic` usa **CAS** por hardware, sin locks. Protege **una sola variable**.

```java
AtomicInteger valor = new AtomicInteger();
valor.incrementAndGet();   // atómico, sin synchronized
```

**Memoria real.** El compilador y la CPU pueden reordenar y cachear, así que un hilo puede no ver lo que escribió otro. Java lo regula con el *Java Memory Model* (*happens-before*): `synchronized`, `volatile`, `Lock` y los atómicos dan esas garantías.

---

## 4. Testing y verificación de software

### Testing de programas concurrentes
**Concepto.** El testing ejecuta el programa con algunas entradas y mira el resultado. En concurrencia eso no alcanza:
- Los interleavings son una cantidad enorme y un test recorre una fracción mínima.
- El planificador es no determinista: un test que pasó mil veces puede fallar en la siguiente corrida.
- Los errores (carreras, deadlock, inanición) dependen de tiempos y pueden desaparecer al agregar un `println` (*heisenbugs*).
- El testing puede mostrar que **hay** un error, pero no que **no hay** ninguno. **No garantiza corrección.**

Sirve para aumentar la confianza (pruebas de estrés, inyectar demoras), y se complementa con **razonamiento** (invariantes, monitores) y **modelado formal**.

### Verificación automática y sus límites
**Definición.** Verificar automáticamente es comprobar con una herramienta (por ejemplo *model checking*) que un modelo del sistema cumple una propiedad.

**Limitaciones**
1. **Indecidibilidad:** por el problema de la parada y el teorema de Rice, no existe un algoritmo general que decida propiedades no triviales de cualquier programa. Solo funciona en clases restringidas (por ejemplo, estados finitos).
2. **Explosión de estados:** con `n` hilos de `k` estados hay hasta `kⁿ` estados globales.
3. **Se verifica un modelo, no el programa real:** el modelo debe ser finito y abstracto. Una mala abstracción puede omitir errores o inventar otros.
4. **Depende de la especificación:** solo se verifica lo que se pidió.
5. **Costo computacional** alto y necesidad de conocimiento experto.
6. **Detalles reales difíciles de modelar:** memoria débil del hardware, reordenamientos, tiempo real.

**Idea que conecta con la jerarquía (tema 18):** más poder expresivo del modelo ⇒ menos cosas decidibles. Por eso se modelan los sistemas con autómatas **finitos** o redes de Petri **acotadas**.

---

## 5. Sección crítica y protección de recursos compartidos

### Sección crítica
**Definición.** Fragmento de código que accede a un **recurso compartido** (variable, objeto, archivo) y que debe ejecutarse **de a un hilo a la vez**. A eso se le llama **exclusión mutua**.

**Propiedades de una buena solución**
1. **Exclusión mutua:** a lo sumo un hilo adentro.
2. **Sin deadlock:** si varios quieren entrar, alguno entra.
3. **Sin inanición:** todo hilo que quiere entrar eventualmente entra.
4. **Progreso:** un hilo fuera de la sección no bloquea a los demás.

**Tiempo de una sección crítica**
- Adentro entra un hilo por vez: los demás esperan y esa parte se **serializa** (pierde paralelismo).
- Ejecutar código protegido cuesta bastante más que sin protección (adquirir y liberar el cerrojo y, con contención, cambios de contexto). El orden de magnitud que figuraba en la corrección de la cátedra era **"unas 5 veces más lento"** (no es una constante; conviene verificarlo).
- **Conclusión:** la sección crítica debe ser **lo más corta posible**. Cálculos largos y E/S van **fuera**.

### Cómo se protegen los recursos compartidos

| Estrategia | Idea | Ejemplo en Java |
|---|---|---|
| **No compartir** (confinamiento) | Cada dato lo usa un solo hilo | `ThreadLocal`, un hilo dueño del dato que recibe eventos por una cola |
| **Inmutabilidad** | Si nadie escribe, no hay carrera | `String`, `record`, campos `final` |
| **Atómicos** | Una variable, sin locks | `AtomicInteger`, `AtomicReference` |
| **Colecciones concurrentes** | Estructuras ya protegidas | `ConcurrentHashMap`, `BlockingQueue` |
| **Exclusión mutua explícita** | Cerrojo alrededor de la sección crítica | `synchronized`, `ReentrantLock` |
| **Sincronización de alto nivel** | Coordinar hilos | Semáforos, monitores, `Condition` |

```java
class Cuenta {
    private int saldo;
    synchronized void depositar(int m) { saldo += m; }
    synchronized int saldo() { return saldo; }        // las lecturas también
}
```

**Reglas:** todos los accesos (lecturas también) deben usar el **mismo** cerrojo. Proteger mal trae **deadlock** (espera circular), **livelock** (cambian de estado pero no avanzan) o **starvation**. Regla práctica: usar lo más simple y de más alto nivel que alcance.

---

## 6. `synchronized`

**Definición.** Modificador de Java que hace que un hilo deba **adquirir el cerrojo intrínseco (*monitor lock*) de un objeto** para ejecutar un bloque o método, y lo **libera al salir**. En Java **cada objeto tiene un cerrojo intrínseco**. Si otro hilo lo tiene, el hilo pasa a **BLOCKED** hasta conseguirlo.

**El argumento de `synchronized(a)`.** `a` es la **"llave"**: define **qué cerrojo se usa**.
- Dos hilos **no** pueden estar a la vez en bloques `synchronized` sobre **el mismo objeto**.
- Sobre **objetos distintos** sí (no se excluyen).
- No puede ser `null` (`NullPointerException`).
- Conviene que sea `private final`. Si cambia entre accesos, o es un `String` literal o un `Integer` (pueden compartirse), se rompe la protección.

**Formas**
```java
class Ejemplo {
    private int valor = 0;
    private final Object cerrojo = new Object();

    synchronized void a() { valor++; }                 // método: cerrojo = this
    static synchronized void b() { }                   // método estático: cerrojo = Ejemplo.class
    void c() { synchronized (cerrojo) { valor++; } }   // bloque: cerrojo = el objeto que elijo
}
```

**Propiedades**
- **Reentrante:** un hilo que ya tiene el cerrojo puede volver a entrar (un método synchronized que llama a otro del mismo objeto).
- **Liberación automática:** se libera al salir, incluso por excepción.
- **Visibilidad:** al salir se publican las escrituras y al entrar se ven las de quien salió antes (*happens-before*).
- Preferir **bloques** a métodos para mantener corta la sección crítica.

**Limitaciones (por qué existe `Lock`):** no se puede intentar tomar el cerrojo sin bloquearse, ni ponerle timeout, ni interrumpir la espera, ni pedir equidad, y hay una sola cola de espera por objeto.

---

## 7. `wait()`, `notify()` y `notifyAll()`

**Definición.** Métodos de `Object` que permiten a un hilo **esperar una condición** dentro de un monitor y a otro hilo **avisarle** cuando se cumple.

### Precauciones para usar `wait()`
1. Debe estar **dentro de `synchronized` sobre el mismo objeto** (`a.wait()` dentro de `synchronized(a)`). Si no, lanza `IllegalMonitorStateException`.
2. Hay que **manejar `InterruptedException`** (`try/catch` o `throws`).
3. Va **dentro de un `while`** que reevalúa la condición, nunca un `if`. Java usa semántica *Mesa*: al despertar, la condición pudo cambiar, y además hay despertares espurios.
4. Tiene que haber alguien que haga `notify/notifyAll` sobre el mismo objeto. Si no, el hilo duerme para siempre. Una notificación sin nadie esperando **se pierde**.

```java
synchronized (obj) {
    while (!condicion) {                        // while, no if
        try { obj.wait(); }
        catch (InterruptedException e) { Thread.currentThread().interrupt(); return; }
    }
    // acá la condición es cierta y tengo el monitor
}
```

### Qué hace `wait()`
1. Se llama con el monitor tomado.
2. **Libera el monitor**, para que otro hilo pueda entrar y hacer cierta la condición.
3. El hilo pasa a **WAITING** y queda en el ***wait set*** del objeto (duerme, sin consumir CPU).
4. Espera un `notify/notifyAll` de otro hilo del mismo monitor (o una interrupción, o el vencimiento en `wait(t)`).
5. Al despertar no ejecuta enseguida: pasa a **BLOCKED** y **readquiere el monitor** antes de retornar.
6. Retorna y hay que **reevaluar la condición** (por eso el `while`).

### Qué hace `notifyAll()`
1. Se llama con el monitor tomado.
2. **Despierta a TODOS** los hilos del *wait set* del objeto. (`notify()` despierta a **uno**, arbitrario.)
3. Los despertados pasan de **WAITING** a **BLOCKED**: compiten por readquirir el monitor.
4. **No libera el monitor**: el que notifica lo suelta recién al **salir del bloque `synchronized`**. Después los despertados entran **de a uno**, en orden no garantizado.
5. Cada uno, al entrar, **reevalúa su condición**. Si no se cumple, vuelve a `wait()`.
6. Si no hay nadie esperando, no tiene efecto.

**`notifyAll` vs `notify`.** Con hilos que esperan **condiciones distintas** (productores y consumidores mezclados), `notify` podría despertar a uno que no puede avanzar y la señal se pierde: el sistema se traba. `notifyAll` es la opción segura.

**Ejemplo:** el buffer del tema 8.

---

## 8. Monitores

**Definición.** Un **monitor** (Hoare / Brinch Hansen) es una construcción de sincronización de **alto nivel** que **encapsula los datos compartidos junto con las únicas operaciones que pueden acceder a ellos**.

**Qué ofrece**
1. **Exclusión mutua automática:** un solo hilo a la vez ejecuta código del monitor.
2. **Sincronización por condición:** un hilo puede **esperar** adentro a que se cumpla una condición (`wait`), soltando temporalmente la exclusión mutua, y otro lo despierta (`signal/notify`).

```
Entry set (esperan entrar) → [ MONITOR: datos privados + métodos ] ← un solo hilo adentro
                                    │ wait(): suelta el monitor y pasa al wait set
                                    ▼
                             Wait set (esperan una condición) ── notify/notifyAll ──► vuelven al entry set
```

**Ventajas**
- **Encapsulamiento:** todo acceso a los datos pasa por el monitor (con locks o semáforos sueltos es fácil olvidarse de proteger algo).
- La exclusión mutua es **automática**.
- La **espera por condiciones** viene integrada.
- Menos errores y más fácil de razonar que primitivas dispersas.

**En Java todo objeto es un monitor:** `synchronized` = entrada; `wait/notify/notifyAll` = variable de condición (una sola por objeto).

**Semántica de señalización**
- **Hoare (*signal & wait*):** el que señala cede el monitor al despertado, así que la condición sigue siendo cierta.
- **Mesa (*signal & continue*), la de Java:** el que señala sigue, y el despertado solo pasa a "listo". Por eso siempre `while`.

**Ejemplo: buffer acotado (productor/consumidor)**
```java
class Buffer {
    private final int[] datos;
    private int n = 0, in = 0, out = 0;
    Buffer(int capacidad) { datos = new int[capacidad]; }

    synchronized void poner(int x) throws InterruptedException {
        while (n == datos.length) wait();          // lleno → espero
        datos[in] = x; in = (in + 1) % datos.length; n++;
        notifyAll();                               // aviso que hay dato
    }
    synchronized int sacar() throws InterruptedException {
        while (n == 0) wait();                     // vacío → espero
        int x = datos[out]; out = (out + 1) % datos.length; n--;
        notifyAll();                               // aviso que hay lugar
        return x;
    }
}
```
Los datos son `private` y solo se tocan por métodos `synchronized`: eso es el encapsulamiento del monitor. Con `Lock` + `Condition` se pueden tener **varias colas de espera** (`noLleno`, `noVacio`) y despertar solo a quien corresponde.

**Monitor vs semáforo.** El semáforo es un contador de bajo nivel que **no está atado a los datos** que protege y es fácil usarlo mal. El monitor los une en una unidad.

---

## 9. Semáforos

**Definición.** Un **semáforo** (Dijkstra) es una variable **entera** más una **cola de hilos bloqueados**, que solo se manipula con dos operaciones **atómicas**:
```
wait / P / acquire :   s--;  si s < 0 → el hilo se bloquea en la cola
signal / V / release:  s++;  si s <= 0 → se despierta a un hilo de la cola
```
(En `java.util.concurrent.Semaphore` el contador nunca es negativo: `acquire` espera mientras no haya permisos.) Al bloquearse, el hilo **no gira**: queda dormido y no gasta CPU.

**Tipos**
- **Binario:** vale 0 o 1. Se usa como **mutex**.
- **General (contador):** cualquier entero ≥ 0. Cuenta unidades de un recurso (un pool de N conexiones).
- **Fuerte:** cola FIFO, sin inanición. **Débil:** orden de despertar no especificado, puede haber inanición. En Java `new Semaphore(n, true)` es el "fair".

**Ejemplos**
```java
Semaphore mutex = new Semaphore(1);              // binario: exclusión mutua
mutex.acquire();
try { /* sección crítica */ } finally { mutex.release(); }

Semaphore pool  = new Semaphore(3);              // contador: hasta 3 hilos a la vez
Semaphore listo = new Semaphore(0);              // señalización: A avisa a B
// Hilo A: ...trabajo...; listo.release();
// Hilo B: listo.acquire();   // arranca recién cuando A terminó
```

**Trampa clásica (productor/consumidor con semáforos):** hay que hacer `acquire` de `huecos`/`items` **antes** que del `mutex`. Si se invierte, un productor con el buffer lleno se duerme con el mutex tomado y se produce deadlock.

---

## 10. Locks y ownership (dueño)

### `Lock` (`java.util.concurrent.locks`)
**Definición.** Cerrojo **explícito** como objeto (`lock()` / `unlock()`). Ofrece lo mismo que `synchronized` y más control.

```java
class CuentaLock {
    private int saldo;
    private final ReentrantLock lock = new ReentrantLock();
    void depositar(int m) {
        lock.lock();
        try { saldo += m; }
        finally { lock.unlock(); }      // SIEMPRE en finally
    }
}
```
**Regla de oro:** `unlock()` en `finally`, porque el Lock no se libera solo. Si hay excepción y no se libera, queda tomado para siempre.

**Qué agrega:** `tryLock(tiempo)` (no espera para siempre), `lockInterruptibly()` (espera interrumpible), modo *fair* (`new ReentrantLock(true)`), varias `Condition` por lock y `ReadWriteLock` (muchos lectores o un escritor).

| | `synchronized` | `Lock` |
|---|---|---|
| Liberación | Automática | Manual (`finally`) |
| Intento sin bloquear / timeout | No | `tryLock` |
| Espera interrumpible | No | `lockInterruptibly` |
| Equidad | No | Opcional |
| Condiciones de espera | 1 por objeto | Varias (`Condition`) |
| Estado del hilo al esperar | BLOCKED | WAITING |

### Ownership (dueño)
**Definición.** Quién "posee" el recurso mientras lo tiene tomado y quién está autorizado a liberarlo.

| | `synchronized` | `Lock` | `Semaphore` |
|---|---|---|---|
| ¿Tiene dueño? | **Sí**: el hilo que tiene el monitor | **Sí**: el hilo que hizo `lock()` | **No** |
| ¿Quién libera? | Solo el dueño (automático al salir) | **Solo el dueño**. Otro hilo que haga `unlock()` recibe `IllegalMonitorStateException` | **Cualquier hilo** puede hacer `release()`, incluso sin `acquire()` |
| Reentrancia | Sí | Sí (contador de retenciones; un `unlock()` por cada `lock()`) | No |
| Uso natural | Exclusión mutua | Exclusión mutua | **Señalización** y limitar accesos |

**Consecuencias**
- Como el `Lock` tiene dueño, sirve para **exclusión mutua**: quien entra es quien sale.
- Como el semáforo no tiene dueño, un hilo puede "avisar" a otro (A hace `release`, B hace `acquire`). Pero un `release()` de más **aumenta los permisos** y rompe la exclusión mutua.

```java
ReentrantLock l = new ReentrantLock();
l.unlock();            // ✗ desde un hilo que no lo tomó: IllegalMonitorStateException
Semaphore s = new Semaphore(0);
s.release();           // ✓ válido desde cualquier hilo: ahora hay 1 permiso
```

---

## 11. Variables locales de hilo (`ThreadLocal`)

**Definición.** `ThreadLocal<T>` da a **cada hilo su propia copia independiente** de una variable. Lo que un hilo escribe no lo ve otro. Es la técnica de **confinamiento**: si nadie comparte el dato, no hay carrera y **no hace falta sincronizar**.

**Cuándo es necesario**
- **Objeto que NO es thread-safe usado desde muchos hilos.** `SimpleDateFormat` guarda estado interno mutable: si varios hilos comparten una instancia, los resultados se corrompen. Sincronizar serializa el trabajo, y crear uno nuevo en cada llamada es costoso. Solución: **una instancia por hilo**.
- **Contexto por hilo o pedido:** usuario autenticado, ID de transacción o de request que viaja por toda la cadena de llamadas sin pasarlo por parámetro.
- Un generador aleatorio, un buffer o una conexión por hilo (`ThreadLocalRandom`).

```java
class Fechas {
    private static final ThreadLocal<SimpleDateFormat> FMT =
        ThreadLocal.withInitial(() -> new SimpleDateFormat("dd/MM/yyyy"));

    static String formatear(Date d) { return FMT.get().format(d); }   // cada hilo usa SU copia
}
```

**Cuidado:** con *pools* de hilos, los hilos se reutilizan y el valor sobrevive entre tareas (puede filtrar datos o perder memoria). Hay que limpiar con `remove()` al terminar.

---

## 12. Estados de un hilo o proceso

### Modelo genérico de 5 estados
```
          admitido                despacho (dispatch)
 NUEVO ─────────────► LISTO ────────────────────────► EJECUCIÓN ── fin / error ──► TERMINADO
                        ▲   ▲                            │   │
                        │   └── fin de quantum, ─────────┘   │ espera un evento o recurso
                        │       apropiación, yield           ▼ (E/S, wait, lock ocupado, semáforo en 0)
                        └────────── ocurre el evento ─── BLOQUEADO
```

| Transición | Cuándo ocurre |
|---|---|
| Nuevo → Listo | El hilo se crea y es admitido en la cola de listos |
| Listo → Ejecución | El planificador le asigna la CPU (*dispatch*) |
| Ejecución → Listo | Se acaba el quantum, lo desplaza uno de mayor prioridad (apropiación) o hace `yield` |
| Ejecución → Bloqueado | Necesita algo no disponible: E/S, `wait()`, `sleep()`, lock ocupado, semáforo en 0 |
| Bloqueado → Listo | Llega la señal de desbloqueo: termina la E/S, `notify`, se libera el lock |
| Ejecución → Terminado | Termina su código o sale por error |

### Creación
- **De dónde viene:** de "la nada". Un proceso o hilo existente pide crear otro (Java: `new Thread(...)` y `start()`; SO: llamada al sistema de creación).
- **Qué hace:** el SO **construye la estructura de control** del hilo (identificador, contador de programa, registros, **pila propia**, prioridad, estado) y le asigna recursos. Todavía **no ejecuta**: queda en **Nuevo**.
- **A dónde va:** a **Listo** (Java: `NEW → RUNNABLE` con `start()`), y espera a que el planificador le dé la CPU.
- **Java:** `new Thread(...)` solo crea el objeto; `start()` crea el hilo de ejecución y llama a `run()`. Llamar a `run()` directo **no** crea un hilo, y un segundo `start()` lanza `IllegalThreadStateException`.

### Bloqueo
- **De dónde viene:** de **Ejecución**, cuando el hilo debe esperar un suceso (E/S, `wait()`, `sleep()`, lock tomado por otro, semáforo en 0).
- **Qué hace:** **no usa CPU**. Queda en una **cola de espera** asociada al evento o recurso, mientras otro hilo ocupa el procesador.
- **A dónde va:** a **Listo**, cuando llega la señal. **Nunca pasa directo a Ejecución.**

### Desbloqueo
- **De dónde viene:** de **Bloqueado**.
- **Qué lo provoca:** el evento esperado (otro hilo hace `notify`, `release` o libera el lock; termina la E/S; vence el `sleep`).
- **Qué hace:** el sistema saca al hilo de la cola de espera y lo pone en la **cola de listos**. No le da la CPU en ese instante.
- **A dónde va:** a **Listo**. Recién después el planificador lo elige (si tiene mayor prioridad que el que corre, puede desplazarlo).
- **Caso Java:** un hilo que hizo `wait()` y recibe `notify()` pasa **WAITING → BLOCKED** (debe readquirir el monitor) **→ RUNNABLE**.

### Estados en Java (`Thread.State`)

| Estado | Significado | Viene de | Va a |
|---|---|---|---|
| **NEW** | Creado, sin `start()` | (nacimiento) | RUNNABLE |
| **RUNNABLE** | Ejecutándose **o listo** esperando CPU (Java no los distingue) | NEW o al volver de una espera | BLOCKED, WAITING, TIMED_WAITING, TERMINATED |
| **BLOCKED** | Quiere entrar a un `synchronized` ocupado | RUNNABLE | RUNNABLE |
| **WAITING** | Espera indefinida (`wait()`, `join()`, `park`) | RUNNABLE | RUNNABLE (por `notify`, fin del `join`, `unpark`) |
| **TIMED_WAITING** | Igual, con tiempo máximo (`sleep(t)`, `wait(t)`, `join(t)`) | RUNNABLE | RUNNABLE (por tiempo o notificación) |
| **TERMINATED** | Terminó `run()` o salió por excepción. **No vuelve a arrancar** | RUNNABLE | (final) |

Detalles: `ReentrantLock.lock()` deja al hilo en **WAITING** (BLOCKED es específico de `synchronized`). Un hilo bloqueado en E/S de sockets o archivos figura como RUNNABLE para la JVM.

---

## 13. Cantidad de estados de un proceso con varios hilos

**Concepto.** Cada hilo está en uno de sus `k` estados **de forma independiente**. El **estado global** del proceso es la combinación (tupla) de los estados de todos los hilos. Con `n` hilos de `k` estados hay **kⁿ** estados globales.

**Ejemplos**
- **2 hilos × 2 estados = 2² = 4:** (A,A), (A,B), (B,A), (B,B).
- **4 hilos × 3 estados = 3⁴ = 81** estados globales.
- **3 hilos × 4 estados = 4³ = 64.**

> **Ojo con el 12.** Si se cuentan los estados **sumando** por hilo, 4 × 3 = 12 (y 2 hilos × 2 estados da 4 de las dos formas). El temario habla de **estados globales combinatorios**, lo que apunta a **kⁿ**. En el examen conviene **escribir el razonamiento** (3⁴ = 81 globales; 12 si se cuentan por hilo). La versión anterior anotaba 12 como clave: confirmarlo con la cátedra.

**Matiz.** Los estados **alcanzables** suelen ser menos que kⁿ por las restricciones (con exclusión mutua, dos hilos nunca están a la vez en la sección crítica). Esto es la **explosión de estados**: kⁿ crece exponencialmente y complica la verificación.

---

## 14. Hilos daemon y finalización de un programa

**Definición.** Un hilo **daemon** es un hilo de **servicio** (recolector de basura, monitoreo, limpieza) que **no mantiene viva a la JVM**. Los hilos comunes (de usuario, **no-daemon**) sí.

**Regla.** El programa termina cuando terminan **todos los hilos no-daemon**. Cuando termina el último, la JVM finaliza y **mata abruptamente a los daemon**, estén donde estén (sin garantizar `finally` ni liberar recursos).

**Caso típico: Main + varios productores + 2 daemons.**
- El programa termina cuando terminan **Main y todos los productores**.
- Si `main` termina antes, el programa **sigue** hasta que termine el último productor.
- Los daemon no cuentan: cuando el último no-daemon termina, se cortan.
- **Por qué:** los daemon son de servicio y no tiene sentido que sigan cuando ya no queda trabajo "real".

```java
Thread daemon = new Thread(() -> {
    while (true) {
        try { Thread.sleep(100); System.out.println("daemon vivo"); }
        catch (InterruptedException e) { }
    }
});
daemon.setDaemon(true);          // ANTES de start(), si no lanza IllegalThreadStateException
daemon.start();

new Thread(() -> {
    try { Thread.sleep(350); } catch (InterruptedException e) { }
    System.out.println("productor termina");
}).start();

System.out.println("main termina");   // main termina primero pero el programa sigue
// cuando termina el productor, la JVM cierra y mata al daemon
```

**Precauciones:** un hilo hereda la condición de daemon del que lo crea, y los daemon no deben tener trabajo crítico (escribir archivos, transacciones), porque pueden cortarse a la mitad.

---

# PARTE II: TEORÍA DE LA COMPUTACIÓN

## 15. Lenguajes y gramáticas

### Lenguaje
**Vocabulario**
- **Alfabeto (Σ):** conjunto finito de símbolos (`{0,1}`, `{a,b}`).
- **Cadena (palabra):** secuencia finita de símbolos del alfabeto. `ε` es la cadena vacía.
- **Lenguaje:** conjunto (posiblemente infinito) de cadenas sobre un alfabeto. Tiene **sintaxis** (qué cadenas son válidas, la gramática) y **semántica** (qué significan).

**Ejemplo.** Sobre `{a,b}`, el lenguaje "`a`s seguidas de igual cantidad de `b`s" es `{ab, aabb, aaabbb, …}`.

### Gramática
**Definición.** Una **gramática formal** es una cuádrupla **`G = (N, T, P, S)`**:
- **N:** no terminales (variables, se pueden reemplazar; mayúsculas).
- **T:** terminales (símbolos del lenguaje; minúsculas).
- **P:** producciones (reglas `α → β`).
- **S ∈ N:** símbolo inicial.

**Derivación.** Aplicar producciones desde `S` hasta quedar solo con terminales. El **lenguaje de `G`** es el conjunto de todas las cadenas de terminales derivables desde `S`.

**Ejemplo.** `S → aSb | ab` genera `aⁿbⁿ`. Derivación de `aaabbb`: `S ⇒ aSb ⇒ aaSbb ⇒ aaabbb`.

**Ambigüedad.** Una gramática es ambigua si una cadena tiene dos árboles de derivación distintos (por ejemplo `E → E+E | E*E | n` con `n+n*n`). Se resuelve con niveles de precedencia.

### Relación lenguaje ↔ gramática
Como un lenguaje puede ser infinito, no se lo puede listar. La **gramática** es una forma **finita de generarlo**. La otra forma de describirlo es **reconocerlo** con un **autómata** (tema 16).

---

## 16. Autómatas

**Definición.** Un **autómata** es una **máquina abstracta** con:
- un conjunto de **estados**,
- una **entrada** que consume símbolo a símbolo,
- reglas de **transición** (según estado actual y símbolo, pasa a otro estado),
- según el tipo, una **memoria adicional** (pila o cinta).

**Dos familias**
- **Reconocedores (aceptadores):** responden sí/no a "¿esta cadena pertenece al lenguaje?". Tienen estados finales. (Finito, de pila, LBA, Turing.)
- **Transductores:** producen una **salida**. (Moore y Mealy, tema 20.)

**Definiciones formales**
- **Autómata finito:** `M = (Q, Σ, δ, q₀, F)`: `Q` estados, `Σ` alfabeto, `δ` función de transición (en el AFD, `δ: Q × Σ → Q`), `q₀` estado inicial, `F ⊆ Q` estados finales.
- **Autómata de pila:** agrega el alfabeto de pila `Γ` y el símbolo inicial de pila `Z₀`.
- **Máquina de Turing:** agrega el alfabeto de cinta `Γ` y el símbolo blanco.

### Relación con las gramáticas
La gramática **genera** el lenguaje y el autómata lo **reconoce**: son dos caras de lo mismo. Para cada gramática hay un autómata que reconoce exactamente las mismas palabras, y viceversa.

**Regla de producción ↔ autómata**

| Regla | Qué representa en el autómata |
|---|---|
| **Regular** `A → aB` | Transición `δ(A, a) = B` de un autómata **finito** (los no terminales son los **estados**) |
| **Regular** `A → a` | Transición `δ(A, a)` a un **estado final** |
| **Regular** `S → ε` | El estado inicial es final |
| **Libre de contexto** `A → α` | En un autómata **de pila**: se **reemplaza el tope** de la pila (`A`) por `α`; los terminales del tope se consumen con la entrada |
| **Sensible al contexto** `αAβ → αγβ` | En un **LBA**: se reescribe la cinta sin salirse de la entrada |
| **Sin restricciones** `α → β` | En una **máquina de Turing**: reescritura libre |

**Ejemplo.** La regla `S → aSb` de `aⁿbⁿ` se ve en el autómata de pila como "apilo por cada `a` y desapilo por cada `b`".

### Los cuatro autómatas
- **Finito (AFD / AFN).** Sin memoria: solo el **estado actual**. Reconoce lenguajes **regulares**. AFD: exactamente una transición por estado y símbolo. AFN: puede haber varias o ninguna; tienen el mismo poder (todo AFN se convierte a AFD). **No puede contar sin límite.**
  ```java
  static boolean cantidadParDeA(String s) {       // estado 0 = par (final), 1 = impar
      int estado = 0;
      for (char c : s.toCharArray()) if (c == 'a') estado = 1 - estado;
      return estado == 0;
  }
  ```
- **De pila (AP).** Autómata finito **+ una pila** (LIFO ilimitada). La transición depende del estado, el símbolo de entrada y el **tope de la pila**. Reconoce lenguajes **libres de contexto**. Compara **una** cantidad contra otra (`aⁿbⁿ`: apila por cada `a`, desapila por cada `b`). No puede comparar tres (`aⁿbⁿcⁿ`). Acá el no determinismo sí importa (palíndromos `wwᴿ` necesitan "adivinar" el centro).
  ```java
  static boolean balanceado(String s) {            // paréntesis bien anidados
      Deque<Character> pila = new ArrayDeque<>();
      for (char c : s.toCharArray()) {
          if (c == '(') pila.push(c);
          else if (c == ')') { if (pila.isEmpty()) return false; pila.pop(); }
      }
      return pila.isEmpty();
  }
  ```
- **Linealmente acotado (LBA).** **Máquina de Turing cuya cinta está limitada al largo de la entrada.** Lee y escribe en cualquier posición pero no se pasa de los bordes. Reconoce lenguajes **sensibles al contexto**. Puede comparar **tres** cantidades (`aⁿbⁿcⁿ`).
- **Máquina de Turing (MT).** Autómata finito + **cinta infinita** de lectura/escritura con cabezal que se mueve en ambos sentidos. Reconoce lenguajes **recursivamente enumerables** (tipo 0). Es el modelo de **todo lo computable**. Limitación: hay problemas que ninguna máquina resuelve (**problema de la parada**).

---

## 17. Jerarquía de Chomsky

| Tipo | Lenguaje | Forma de las reglas | Autómata | Memoria | Ejemplo |
|---|---|---|---|---|---|
| **3** | Regular | `A → aB` o `A → a` | **Finito** | Solo el estado | `a⁺b*`, "cantidad par de `a`" |
| **2** | Libre de contexto | `A → α` (**un solo** no terminal a la izquierda) | **De pila** | Pila (LIFO, solo el tope) | `aⁿbⁿ`, paréntesis |
| **1** | Sensible al contexto | `α₁Aα₂ → α₁βα₂`, con `β ≠ ε` (no acorta) | **LBA** | Cinta limitada a la entrada | `aⁿbⁿcⁿ` |
| **0** | Recursivamente enumerable | `α → β` cualquiera | **Máquina de Turing** | Cinta infinita | Todo lo computable |

**Inclusión:** `Regular ⊂ Libre de contexto ⊂ Sensible al contexto ⊂ Recursivo ⊂ Rec. enumerable`. Cada tipo agrega libertad a las reglas, y eso corresponde a más memoria en la máquina.

### Lenguaje y gramática de tipo 1 (sensible al contexto)
**Definición.** Lenguaje generado por gramáticas con producciones **`α₁ A α₂ → α₁ β α₂`**, donde `A` es un no terminal, `α₁, α₂` son cadenas de terminales y no terminales (el **contexto**) y `β` es una cadena **no vacía**. `A` solo se reemplaza por `β` **cuando aparece entre `α₁` y `α₂`**. Como `β ≠ ε`, las producciones **no acortan**. Lo reconoce un **LBA**.

**Ejemplo: `aⁿbⁿcⁿ (n ≥ 1)`**
```
S → aSBC | aBC
CB → BC
aB → ab      bB → bb      bC → bc      cC → cc
```
Derivación de `aabbcc`: `S ⇒ aSBC ⇒ aaBCBC ⇒ aaBBCC ⇒ aabBCC ⇒ aabbCC ⇒ aabbcC ⇒ aabbcc`.

### Por qué el tipo 2 expresa menos que el tipo 1
1. **Reglas más restringidas:** en el tipo 2 el lado izquierdo es un solo no terminal, así que el reemplazo se hace **sin mirar el contexto**. Toda regla libre de contexto es un caso particular de regla sensible al contexto (con contexto vacío): `Tipo 2 ⊆ Tipo 1`.
2. **La inclusión es estricta:** `aⁿbⁿcⁿ` es de tipo 1 y **no** de tipo 2 (se prueba con el lema de pumping para libres de contexto).
3. **En términos de máquina:** la **pila** solo accede al tope y compara **una** cantidad; el **LBA** accede a toda la cinta y compara **tres**.

*(Detalle técnico: una regla `A → ε` no cumple "no acortar", pero todo lenguaje libre de contexto se puede generar sin ellas, salvo quizás la cadena vacía.)*

### Uso en un compilador
Regulares → **análisis léxico** (tokens). Libres de contexto → **análisis sintáctico**. Sensibles al contexto → cosas como "declarar antes de usar" (en la práctica se chequea con tabla de símbolos).

---

## 18. Potencia de las máquinas y tesis de Church-Turing

**Concepto.** "Más potente" significa **reconocer o calcular una clase de lenguajes más grande**, y depende de **un solo factor: el tipo de memoria** que tiene además de los estados.

`Finito ⊂ Pila ⊂ LBA ⊂ Turing`

| Máquina | Sí puede | NO puede |
|---|---|---|
| Finito | Cantidad par de `a`, `a⁺b*` | `aⁿbⁿ` (no cuenta sin límite) |
| Pila | `aⁿbⁿ`, paréntesis | `aⁿbⁿcⁿ` |
| LBA | `aⁿbⁿcⁿ` | Lo que necesita memoria no acotada |
| Turing | Todo lo computable | Problema de la parada (nadie puede) |

### Turing vs autómata a pila
- La **pila** es memoria **restringida**: LIFO, solo se ve el **tope**. Lo que se desapiló se perdió.
- La **MT** tiene **cinta infinita** de lectura/escritura con cabezal en ambos sentidos: **acceso libre** a toda la memoria.
- La MT reconoce lo que la pila no puede (`aⁿbⁿcⁿ`) y puede **simular** un autómata a pila, y no al revés.

### Turing vs autómata linealmente acotado
- El **LBA** solo usa la porción de cinta de la entrada (memoria proporcional a `n`).
- La **MT** tiene cinta **infinita**: usa la memoria que necesite.
- El LBA tiene un número **finito** de configuraciones, así que **siempre se puede decidir** si acepta. En la MT no (**problema de la parada**).

### Tesis de Church-Turing: ¿qué programas expresa cada máquina?
- **Máquina de Turing: sí.** Todo lo computable (todo algoritmo, cualquier programa en Java, C, etc.) tiene una MT asociada. Es una **tesis** (no un teorema), universalmente aceptada.
- **Autómata a pila: no.** Solo cubre lenguajes libres de contexto; un programa cualquiera puede necesitar acceder a memoria arbitrariamente.
- **LBA: no.** Solo cubre programas cuya memoria está acotada linealmente por la entrada.

**Contrapartida: más poder = menos análisis posible.** Regulares: casi todo es decidible. Libres de contexto: no se decide si dos gramáticas son equivalentes. Sensibles: no se decide si el lenguaje es vacío. Turing: casi nada (Rice). Por eso se usa la máquina **más simple que alcance**, y los sistemas concurrentes se modelan con autómatas **finitos**.

---

## 19. Análisis de lenguajes: `aⁿbⁿ` y variantes

**Método.** Preguntarse qué hay que "recordar": nada (regular), **una** cantidad (pila) o **varias** (LBA).

### `aⁿbⁿ : n ≥ 1`
**Tipo 2 (libre de contexto)** y **autómata de pila.** Gramática `S → aSb | ab`. El autómata apila por cada `a`, desapila por cada `b` y acepta si la pila queda vacía al terminar la entrada (alcanza uno determinista).

### ¿Puede un autómata finito con `aⁿbⁿ`?
**No.** Tendría que **recordar cuántas `a` leyó** y `n` no tiene cota, pero solo tiene finitos estados.

**Demostración corta (palomar / pumping).** Con `k` estados, al leer `a¹…aᵏ⁺¹` dos prefijos distintos `aⁱ` y `aʲ` (`i ≠ j`) terminan en el **mismo estado**. Como se acepta `aⁱbⁱ`, también se aceptaría `aʲbⁱ`, que **no** está en el lenguaje. Contradicción.

### `aⁿbᵐ : n ≥ 1, m ≥ 0`
**Autómata finito** (**regular**). `n` y `m` son **independientes**: solo hay que verificar el orden (primero `a`s, al menos una, y después `b`s). Es la expresión regular **`a⁺b*`**.

| Estado | lee `a` | lee `b` | ¿Final? |
|---|---|---|---|
| `q0` (inicio) | `q1` | error | No |
| `q1` (ya vi `a`) | `q1` | `q2` | **Sí** |
| `q2` (ya vi `b`) | error | `q2` | **Sí** |

Gramática regular equivalente: `S → aS | aB | a`, `B → bB | b`.

### `aⁿbᵐ : n ≥ 1, n ≥ m ≥ n`
**Truco:** `n ≥ m ≥ n` obliga a **`m = n`**. Es `aⁿbⁿ` con `n ≥ 1`: gramática de **tipo 2**, `S → aSb | ab`, autómata de **pila**. No se puede con tipo 3.

### Tabla de variantes

| Lenguaje | Tipo | Autómata | Por qué |
|---|---|---|---|
| `aⁿbᵐ` (n y m independientes) | 3, regular | Finito | No hay que comparar |
| `aⁿbⁿ` | 2 | Pila | Se compara **una** cantidad |
| `aⁿbᵐ` con `n ≥ m` | 2 | Pila | Apila las `a`, desapila por cada `b`, sobran |
| `aⁿbᵐcⁿ` | 2 | Pila | Compara `a` con `c` (las `b` no importan) |
| `aⁿbⁿcⁿ` | 1 | LBA | Se comparan **tres** cantidades |
| `wwᴿ` (palíndromos) | 2 | Pila **no determinista** | Hay que "adivinar" el centro |
| `ww` (copia) | 1 | LBA | Hay que comparar la primera mitad con la segunda |

### Extra: expresiones matemáticas con paréntesis
Paréntesis anidados a profundidad arbitraria requieren contar niveles sin límite: **autómata de pila / gramática libre de contexto**.
```
E → E + T | T        (suma: menor precedencia)
T → T * F | F        (producto: mayor precedencia)
F → ( E ) | n
```
En verificación de sistemas concurrentes, las fórmulas de **lógica temporal (LTL)** se traducen a **autómatas de Büchi**.

---

## 20. Máquinas de Moore y de Mealy

**Definición.** Ambas son **transductores**: autómatas **finitos con salida**. En memoria son autómatas finitos, pero calculan **funciones** en vez de decidir pertenencia. Son **equivalentes** entre sí (toda Moore se convierte en Mealy y viceversa).

| | Moore | Mealy |
|---|---|---|
| Salida depende de | Solo el **estado** (`λ: Q → Δ`) | **Estado + entrada** (`λ: Q × Σ → Δ`) |
| Salida asociada a | **Estados** | **Transiciones** |
| Reacción | Un ciclo después (hay que llegar al estado) | **Inmediata** |
| Cantidad de estados | Suele necesitar **más** | Suele necesitar **menos** |
| Seguridad / estabilidad | **Más segura** (salida estable) | Menos (glitches, depende de la entrada) |

**Por qué Mealy tiene menos estados.** La salida va en la transición, así que un mismo estado se puede alcanzar con salidas distintas. En Moore, si hay que emitir salidas distintas hay que **duplicar estados**. Convertir Mealy a Moore puede multiplicar los estados (hasta `|Q|·|Δ|`); Moore a Mealy mantiene la cantidad.

**Por qué Moore es más segura.** La salida depende **solo del estado**: es **estable durante todo el estado** y cambia solo al cambiar de estado (sincronizada con el reloj). No reacciona a ruido en la entrada (sin *glitches*) y no hay caminos combinacionales de la entrada a la salida, así que es más predecible y fácil de verificar. La Mealy reacciona en el mismo instante (más rápida y compacta), pero su salida puede cambiar con la entrada a mitad de un ciclo.

**Ejemplo Moore: semáforo** (la salida sale del estado)
```java
enum Semaforo {
    ROJO("STOP"), VERDE("GO"), AMARILLO("CUIDADO");
    final String salida;
    Semaforo(String s) { salida = s; }
    Semaforo siguiente() { return switch (this) { case ROJO -> VERDE; case VERDE -> AMARILLO; case AMARILLO -> ROJO; }; }
}
```

**Ejemplo Mealy: detector de dos `1` consecutivos** (la salida depende de estado y entrada)
```java
class Detector11 {
    private int estado = 0;                       // último bit visto
    int paso(int bit) { int salida = (estado == 1 && bit == 1) ? 1 : 0; estado = bit; return salida; }
}
```
- **Mealy:** 2 estados (`S0`: último bit 0, `S1`: último bit 1). La transición `S1 –1/1→ S1` emite 1.
- **Moore:** 3 estados (`A`: sin `1`, `B`: un `1`, `C`: dos o más `1`, con salida 1 en `C`).

---

# PARTE III: SISTEMAS REACTIVOS Y MODELADO

## 21. Programas reactivos y tiempo real

**Tres tipos de sistemas**
- **Transformacionales:** reciben entrada, calculan, **terminan** y dan una salida (un compilador).
- **Interactivos:** interactúan con el entorno pero **a su ritmo** (un servidor que atiende pedidos).
- **Reactivos:** mantienen **interacción continua con el entorno** y reaccionan a sus eventos **al ritmo que el entorno dicte** (molinete, marcapasos, controlador de ascensor).

**Características de un programa reactivo** (típico de sistemas **embebidos**)
1. **Interacción continua** con su entorno.
2. **Dirigido por eventos:** reacciona a estímulos externos.
3. **No termina** (ejecución potencialmente infinita); no se define por un resultado final.
4. **Concurrente y no determinista:** los eventos llegan en órdenes y momentos que no controla.
5. **Restricciones temporales** (tiempo real): importa **cuándo** responde, no solo qué responde.
6. Se modela con **estados y transiciones** (Mealy/Moore, Petri) y se especifica con propiedades de **seguridad** y **vivacidad**.

**Seguridad y vivacidad**
- **Seguridad (*safety*):** "nunca pasa algo malo" (nunca dos hilos en la sección crítica).
- **Vivacidad / progreso (*liveness*):** "eventualmente pasa algo bueno" (todo pedido de acceso termina siendo atendido).

**Ejemplo: molinete (máquina de Mealy)**
```java
class Molinete {                                   // (estado, evento) → (nuevo estado, salida)
    enum Estado { BLOQUEADO, LIBRE }
    private Estado estado = Estado.BLOQUEADO;
    String procesar(String evento) {
        if (estado == Estado.BLOQUEADO && evento.equals("MONEDA"))  { estado = Estado.LIBRE;     return "desbloquear"; }
        if (estado == Estado.LIBRE     && evento.equals("EMPUJAR")) { estado = Estado.BLOQUEADO; return "bloquear"; }
        return "nada";
    }
}
```
Un bucle de eventos con una cola de mensajes le entrega los eventos y **un solo hilo** toca el estado, así que no hace falta ningún lock (confinamiento).

### Starvation (inanición)
**Definición general.** Un hilo está listo pero **nunca obtiene** el recurso o la CPU porque otros pasan siempre antes.

**En un sistema de tiempo real.** Ocurre cuando **no se atienden las solicitudes de un proceso (hilo) en el tiempo en que se requiere la respuesta**: se pierde el **plazo (*deadline*)**. Acá no alcanza con "eventualmente": hay una cota de tiempo, así que es más grave.

- **Causas típicas:** prioridades donde los de alta acaparan la CPU, e **inversión de prioridad** (uno de baja prioridad tiene un lock que necesita uno de alta, y uno de prioridad media desplaza al de baja). Se soluciona con **herencia de prioridad**.
- **Deadlock vs livelock vs inanición:** en deadlock nadie avanza y todos están bloqueados; en livelock cambian de estado pero no progresan; en inanición *alguien* avanza y otro nunca.

**Tiempo real duro vs blando:** en el **duro** perder un plazo es una falla grave (frenos, marcapasos); en el **blando** solo degrada la calidad (video).

---

## 22. Modelado y verificación

### Por qué se modelan Datos, Recursos, Interacción y Concurrencia
Los modelos abstractos se centran en las características que **determinan si el sistema funciona bien** y donde se originan los errores. Los detalles irrelevantes (lenguaje, hardware, nombres) se **abstraen** para que el modelo sea analizable.

| Característica | Por qué importa |
|---|---|
| **Datos** | Son el **estado** del sistema. La corrección se expresa con **invariantes** sobre ellos, y los datos compartidos son la fuente de las carreras |
| **Recursos** | **Finitos** y de uso exclusivo (CPU, memoria, buffers, locks). Generan **contención, deadlock y starvation** |
| **Interacción** | Comunicación y sincronización entre componentes y con el entorno (memoria compartida, mensajes, eventos). **Define el comportamiento reactivo** |
| **Concurrencia** | Varias actividades progresan a la vez: aparecen los **interleavings** y el **no determinismo**, causa de la mayoría de los errores |

### Sistemas de transición de estados (grafos dirigidos)
**Concepto.** La semántica de un programa concurrente se basa en sistemas de transición de estados representados con **grafos dirigidos**.

- **Definición estándar (LTS):** los **nodos** son los **estados** y las **aristas** son las **transiciones**, etiquetadas con la **acción o evento** que provoca el cambio.
- **Formulación de la guía del temario:** "nodos como eventos, aristas como acciones". Es la misma estructura con otro vocabulario: la **arista** es la *acción* (el paso que lleva de un nodo a otro) y el **nodo** es el *evento* (el punto del grafo que resulta).
- **Para el examen:** contestá con la formulación de la guía y aclará en una línea la definición estándar. Si la cátedra tiene una diapositiva, ajustate a ella.

**Concurrencia.** Un programa concurrente es un conjunto de autómatas (uno por hilo) cuyo comportamiento conjunto es el **interleaving** de sus transiciones: el grafo global tiene todos los estados combinados (de ahí la explosión `kⁿ`).

### Redes de Petri (lo mínimo; detalle en el anexo A6)
- **Grafo bipartito dirigido:** **lugares** (círculos: condiciones, estados, recursos), **transiciones** (barras: eventos o acciones), **arcos** y **fichas** (el marcado es el estado).
- **Disparo:** una transición está **habilitada** si cada lugar de entrada tiene fichas suficientes. Al dispararse consume fichas de las entradas y produce en las salidas: `M' = M − Pre + Post`. Si hay varias habilitadas se elige una de forma **no determinista** (el interleaving otra vez).
- **Patrones:** secuencia, paralelismo (*fork*), sincronización (*join*), **conflicto** (un lugar con dos salidas) y recurso compartido.
- **Ejemplo, exclusión mutua:** un lugar `M` (mutex) con una ficha, compartido como entrada de las transiciones "entrar" de cada hilo. Si se dispara una, la otra queda deshabilitada. Invariante: `M + C1 + C2 = 1`.
- **Propiedades:** acotada, segura (1-acotada), **viva**, **sin deadlock**.

### Cómo se verifica seguridad y progreso
**Mecanismo general.** Se **modela** el sistema con un formalismo de estados (**autómatas finitos / sistemas de transición**, **redes de Petri**) y se lo **analiza de forma exhaustiva** (grafo de estados alcanzables e invariantes), sin ejecutar el programa.

| Tipo | Idea | Cómo se verifica |
|---|---|---|
| **Seguridad** | "Nunca pasa algo malo" (exclusión mutua, no desbordar un buffer) | **Ningún estado alcanzable es "malo"**. En Petri, con **invariantes** (`M + C1 + C2 = 1` prueba la exclusión mutua) y **acotación**. En autómatas, que no se alcance un estado de error |
| **Progreso / vivacidad** | "Eventualmente pasa algo bueno" (todo pedido se atiende) | **Ausencia de deadlock** (no existe estado alcanzable sin transiciones habilitadas), **vivacidad** (desde todo estado alcanzable se puede volver a disparar cada acción) y ausencia de inanición (bajo supuestos de equidad) |

**Herramientas en Petri:** grafo de marcas (finito si y solo si la red es acotada), árbol de cobertura (si no es acotada), **P-invariantes** y **T-invariantes**, **sifones y trampas** (teorema de Commoner en redes de libre elección).

**Límite:** se verifica el **modelo**, y solo si es finito (tema 4).

---

# PREGUNTAS Y RESPUESTAS CORTAS (P1 a P47)

Entre paréntesis está el tema del resumen donde se desarrolla.

**Concurrencia y testing**

1. **¿Cómo se ejecutan los procesos concurrentes? (interleaving)** Intercalando las acciones atómicas de los procesos, respetando el orden interno de cada uno; el planificador decide el orden. *(tema 2)*
2. **Diferencia entre ejecución paralela y concurrente.** Concurrente: varias tareas progresan en períodos superpuestos (puede ser un solo núcleo; es la estructura del programa). Paralelo: se ejecutan literalmente a la vez (varios núcleos; es la ejecución). *(1)*
3. **¿Es factible diseñar un testing que verifique un programa concurrente, con tantos interleavings?** No alcanza: cubre una fracción mínima, el planificador es no determinista y hay errores intermitentes. *(4)*
4. **¿Se puede garantizar que es correcto con testing?** No: el testing muestra la presencia de errores, no su ausencia. Se razona o se verifica formalmente. *(4)*
5. **Si el sistema es determinístico, ¿los interleavings de acciones atómicas llevan a resultados distintos?** No: dan el mismo resultado (con acciones independientes o conmutativas). Los resultados distintos vienen de la no atomicidad. *(2)*
6. **¿Por qué distintas corridas con acciones no atómicas dan resultados distintos o erróneos?** Una sentencia son varias instrucciones y otro hilo puede intercalarse en el medio, así que el resultado depende del orden (condición de carrera). *(3)*
7. **Limitaciones de la verificación automática.** Indecidibilidad (parada, Rice), explosión de estados, se verifica un modelo abstracto y no el programa, depende de la especificación y es costosa. *(4)*

**Sincronización**

8. **¿Cómo se protegen los recursos compartidos? Ejemplos.** No compartir (`ThreadLocal`), inmutabilidad, atómicos, colecciones concurrentes, `synchronized`/`Lock`, semáforos y monitores. *(5)*
9. **¿Qué es una sección crítica?** Código que accede a un recurso compartido y debe ejecutarse de a un hilo por vez (exclusión mutua). *(5)*
10. **Idea comparativa del tiempo de una sección crítica.** Debe ser corta porque serializa la ejecución. Orden de magnitud de la cátedra: unas 5 veces más lento que sin protección (verificar). *(5)*
11. **¿Cómo son las operaciones atómicas y para qué sirven?** Indivisibles, todo o nada, sin estados intermedios visibles. Son las instrucciones que se intercalan y la base de la sincronización (test-and-set, CAS). *(3)*
12. **`synchronized(a)` y el rol de su argumento.** Adquiere el cerrojo intrínseco del objeto `a`; el argumento es la "llave". Bloques sobre el mismo objeto se excluyen entre sí; sobre objetos distintos no. *(6)*
13. **Precauciones para incluir `wait()`.** Dentro de `synchronized` sobre el mismo objeto (si no, `IllegalMonitorStateException`), manejar `InterruptedException`, usarlo en un `while` y asegurar que alguien notifique. *(7)*
14. **Acciones de `wait()`.** Libera el monitor, pasa a WAITING en el *wait set*, espera un `notify`; al despertar pasa a BLOCKED, readquiere el monitor y retorna. *(7)*
15. **Acciones de `notifyAll()`.** Dentro de `synchronized`, despierta a todos los hilos del *wait set* (pasan a BLOCKED), no suelta el monitor hasta salir del bloque, y cada uno reevalúa su condición al entrar. *(7)*
16. **¿Qué es un monitor y qué ventajas tiene?** Encapsula datos compartidos y sus operaciones con exclusión mutua automática y variables de condición. Ventajas: encapsulamiento, menos errores, más fácil de razonar. *(8)*
17. **¿Qué es un semáforo y qué tipos conoce?** Entero + cola con operaciones atómicas `wait/signal` (P/V). Binario (mutex) y general (contador); fuerte (FIFO) y débil. *(9)*
18. **Ownership en Lock y Semaphore.** El `Lock` (y `synchronized`) tiene dueño: solo el hilo que lo tomó puede liberarlo (si no, `IllegalMonitorStateException`). El `Semaphore` no tiene dueño: cualquier hilo puede hacer `release`. *(10)*
19. **Caso en que es necesaria una variable local de hilo.** `SimpleDateFormat` (no thread-safe) o el contexto de usuario/transacción por hilo. Cada hilo tiene su copia, así que no se comparte y no hay que sincronizar. *(11)*

**Estados de hilos**

20. **Creación (de dónde viene, a dónde va, qué hace).** Viene de la nada (`new Thread` + `start()`); el SO arma la estructura de control y la pila; estado Nuevo; pasa a Listo y espera a que el planificador le dé la CPU. *(12)*
21. **Bloqueo.** Viene de Ejecución cuando espera un evento o recurso; no usa CPU y queda en una cola de espera; va a Listo cuando llega la señal, nunca directo a Ejecución. *(12)*
22. **Desbloqueo.** De Bloqueado a Listo cuando ocurre el evento esperado (notify, release, fin de E/S, timeout); el sistema lo pasa a la cola de listos y espera al planificador. *(12)*
23. **4 hilos con 3 estados cada uno.** 3⁴ = **81** estados globales (12 si se cuentan estados por hilo; aclarar el razonamiento). *(13)*
24. **2 hilos con 2 estados cada uno.** 2² = **4** estados globales. *(13)*
25. **Main + productores + 2 daemons: ¿cuándo termina y por qué?** Cuando terminan todos los hilos **no-daemon** (Main y productores). Los daemon no cuentan y se eliminan al terminar el último no-daemon. *(14)*

**Lenguajes y autómatas**

26. **Lenguaje y su relación con las gramáticas.** Lenguaje = conjunto de cadenas sobre un alfabeto. La gramática lo **genera** de forma finita mediante derivaciones. *(15)*
27. **Gramática, y regla de producción vs autómata.** `G = (N, T, P, S)`. Una regla regular `A → aB` equivale a la transición `δ(A,a) = B` de un autómata finito; una regla libre de contexto `A → α` reemplaza el tope de la pila en un autómata de pila. *(15, 16)*
28. **Autómata y su relación con las gramáticas.** Máquina abstracta (estados, alfabeto, transición, inicial, finales, más memoria) que **reconoce** un lenguaje. Para cada gramática hay un autómata equivalente: regular-finito, libre-pila, sensible-LBA, tipo 0-Turing. *(16)*
29. **Lenguaje de tipo 1 y sus gramáticas.** Sensible al contexto; producciones `α₁Aα₂ → α₁βα₂` con `β ≠ ε` (no acortan); lo reconoce un LBA. Ej.: `aⁿbⁿcⁿ`. *(17)*
30. **Por qué el tipo 2 tiene menos expresión que el tipo 1.** Sus reglas no miran contexto (caso particular del tipo 1) y hay lenguajes de tipo 1 que no son de tipo 2 (`aⁿbⁿcⁿ`). La pila compara una cantidad y el LBA, tres. *(17)*
31. **¿Por qué la MT es más potente que un autómata a pila?** La pila es LIFO y solo accede al tope; la MT tiene cinta infinita de lectura/escritura con acceso libre. *(18)*
32. **¿Por qué la MT es más potente que un LBA?** El LBA tiene la cinta acotada a la entrada; la MT, cinta infinita. Además, en el LBA siempre se decide si acepta y en la MT no (parada). *(18)*
33. **¿Un programa cualquiera puede ser expresado por una MT?** Sí (tesis de Church-Turing). *(18)*
34. **¿Por un autómata a pila?** No: solo cubre lenguajes libres de contexto. *(18)*
35. **¿Por un LBA?** No: solo si la memoria necesaria es lineal en la entrada. *(18)*
36. **`aⁿbⁿ : n ≥ 1`, ¿con qué autómata o gramática?** Gramática tipo 2, `S → aSb | ab`; autómata de pila. *(19)*
37. **¿Con un autómata finito? ¿Por qué?** No: habría que recordar cuántas `a` se leyeron y tiene finitos estados (palomar / pumping). *(19)*
38. **`aⁿbᵐ : n ≥ 1, m ≥ 0`, ¿con qué autómata?** Autómata finito (lenguaje regular, `a⁺b*`). *(19)*
39. **`aⁿbᵐ : n ≥ 1, n ≥ m ≥ n`, ¿con qué gramática?** Eso fuerza `m = n`, o sea `aⁿbⁿ`: gramática tipo 2 (libre de contexto); autómata de pila. *(19)*
40. **En una máquina de Moore, ¿a qué está relacionado el símbolo de salida?** Al **estado**. *(20)*
41. **¿Cuál tiende a tener menos estados, Mealy o Moore?** **Mealy**, porque la salida va en la transición; Moore duplica estados por cada salida distinta. *(20)*
42. **¿Cuál es más segura, Mealy o Moore?** **Moore**, porque la salida depende solo del estado, es estable y no reacciona a ruido en la entrada; Mealy puede tener glitches. *(20)*

**Reactivos y modelado**

43. **¿Qué caracteriza a un programa reactivo?** Interacción continua con el entorno, dirigido por eventos, no termina, concurrente y no determinista, con restricciones temporales. *(21)*
44. **Starvation en un sistema de tiempo real.** No se atienden las solicitudes de un hilo en el tiempo en que se requiere la respuesta (se pierde el plazo). *(21)*
45. **¿Por qué son importantes Datos, Recursos, Interacción y Concurrencia?** Determinan la corrección y ahí nacen los errores (invariantes, contención/deadlock, sincronización, interleavings); modelarlos permite razonar y verificar. *(22)*
46. **¿Cómo se verifica seguridad y progreso en el modelo?** Modelando con autómatas finitos o redes de Petri y analizando el grafo de alcanzabilidad e invariantes. Seguridad: ningún estado malo alcanzable. Progreso: sin deadlock y con vivacidad. *(22)*
47. **En el grafo de transición de estados, ¿quién representa las acciones y quién los eventos?** Aristas = acciones y nodos = eventos (según la guía); en la definición estándar, nodos = estados y aristas = acciones/eventos etiquetados. *(22)*

---

# ANEXO: Material complementario (fuera de las 47 preguntas)

> Contenido de la versión anterior que no está en las preguntas pero sirve para profundizar: hilos y SMP (A1), exclusión mutua por software/hardware (A2), semáforos en detalle (A3), paso de mensajes (A4), deadlock e inanición (A5), redes de Petri en detalle (A6), API de concurrencia de Java (A7) y rendimiento (A8).

## A1. Hilos, SMP y micronúcleos (Módulo 1)

### A1.1 Proceso vs hilo a nivel sistema operativo
- **Proceso:** unidad de **propiedad de recursos** (espacio de direcciones, archivos abiertos, etc.). Contiene al menos un hilo.
- **Hilo:** unidad de **ejecución y planificación**. Tiene su propio contador de programa, registros, pila y estado. **Comparte** con los demás hilos del proceso el espacio de direcciones (heap, código, datos globales) y los archivos abiertos.
- **Ventajas de los hilos:** crearlos y destruirlos es más barato; el cambio de contexto es más barato (no se cambia de espacio de direcciones); se comunican directo por memoria compartida sin pasar por el kernel; aprovechan multiprocesadores.
- **Costo:** la memoria compartida obliga a sincronizar (todo el Bloque B).

**Hilos de usuario (ULT) vs de kernel (KLT):**

| | Nivel usuario (ULT) | Nivel kernel (KLT) |
|---|---|---|
| Quién los gestiona | Una biblioteca en espacio de usuario | El sistema operativo |
| Cambio de hilo | Muy rápido (sin llamada al sistema) | Requiere pasar por el kernel |
| Si uno se bloquea (E/S) | Se bloquea **todo el proceso** | Solo ese hilo |
| Multiprocesador | No aprovecha varios núcleos | Sí |

**Modelos de mapeo:** N:1 (muchos ULT sobre un KLT), 1:1 (cada hilo de usuario es un hilo del kernel) y M:N (mezcla). Los `Thread` clásicos de Java son 1:1 con hilos del SO. Desde Java 21 existen los **hilos virtuales**, planificados por la JVM (M:N), muy baratos para tareas con mucha espera de E/S.

### A1.2 Multithreading por hardware y SMT
- **SMT (Simultaneous Multithreading):** un mismo núcleo mantiene el contexto de **varios hilos** y en un mismo ciclo emite instrucciones de hilos distintos, aprovechando unidades funcionales que de otro modo quedarían ociosas. Ejemplo: Hyper-Threading (2 hilos lógicos por núcleo físico).
- El SO ve cada hilo lógico como un procesador más, pero **no equivale a duplicar el núcleo**: comparten caché y unidades de ejecución. Mejora el *throughput* total, no la velocidad de un hilo individual.

### A1.3 Multiprocesamiento simétrico (SMP)
- Varios procesadores **idénticos**, con **memoria principal compartida** y conectados por bus o interconexión, controlados por **un único SO**.
- **Simétrico:** cualquier procesador puede ejecutar cualquier tarea, incluido el código del kernel. (En el **asimétrico** hay un maestro que corre el kernel y esclavos que ejecutan usuario.)
- Acceso a memoria con tiempo uniforme (**UMA**). Con muchos procesadores aparece **NUMA** (acceso más rápido a la memoria "cercana").
- **Ventajas:** rendimiento, disponibilidad (si falla uno el sistema sigue), escalado incremental.
- **Problemas de diseño:** varios procesos concurrentes simultáneos en el kernel, planificación, sincronización, coherencia de cachés (que todos vean el valor actualizado), tolerancia a fallos.

### A1.4 Micronúcleos
- **Idea:** el kernel deja solo lo **esencial** (planificación básica, comunicación entre procesos por paso de mensajes, gestión de memoria básica, interrupciones). El resto (sistema de archivos, drivers, red) corre como **servidores en espacio de usuario**.
- **Ventajas:** modularidad, extensibilidad, portabilidad, fiabilidad (un driver que falla no tumba el kernel), buen soporte para sistemas distribuidos.
- **Desventaja:** más **paso de mensajes** entre componentes, lo que cuesta rendimiento. Ejemplos: Mach, QNX, MINIX. Contrastan con los kernels monolíticos (Linux).

### A1.5 Clasificación de Flynn (SIMD y MIMD)

| Sigla | Significado | Ejemplo |
|---|---|---|
| SISD | Una instrucción, un dato | Procesador secuencial clásico |
| **SIMD** | **Una** instrucción sobre **muchos datos** a la vez | Instrucciones vectoriales, GPU |
| MISD | Varias instrucciones sobre un dato | Raro en la práctica |
| **MIMD** | **Varias** instrucciones sobre **varios datos** | Multiprocesadores, clusters |

MIMD se divide según la memoria: **compartida** (SMP, NUMA) o **distribuida** (clusters, multicomputadoras).

### A1.6 Memoria compartida vs distribuida

| | Memoria compartida | Memoria distribuida |
|---|---|---|
| Comunicación | Leer/escribir variables comunes | Paso de mensajes por red |
| Sincronización | Locks, monitores, semáforos | Implícita en send/receive |
| Programación típica | Hilos Java, OpenMP | MPI, sockets |
| Problemas típicos | Condiciones de carrera, coherencia de caché | Latencia, serialización, fallos de red |
| Escalabilidad | Limitada por el bus/memoria | Muy alta |

**Caso de estudio del programa (qué hace falta para soportar múltiples hilos):** una estructura por hilo (bloque de control con registros, pila, estado), un planificador que los reparta entre procesadores, **primitivas atómicas de hardware** (test-and-set, CAS) para construir la sincronización, memoria compartida **coherente** y mecanismos de exclusión mutua y comunicación.

---

## A2. Exclusión mutua: soluciones por software y por hardware (Módulo 2)

### A2.1 Contexto y términos
- **Multiprogramación** (varios procesos en un procesador), **multiprocesamiento** (varios procesadores) y **procesamiento distribuido** (varias máquinas): los tres generan concurrencia.
- Conceptos base: sección crítica, exclusión mutua, condición de carrera, deadlock, livelock, inanición, operación atómica (temas 3 y 5).
- **Historia (a grandes rasgos):** Dijkstra formaliza la exclusión mutua y los semáforos (años 60), Hoare y Brinch Hansen los monitores (años 70), Hoare el paso de mensajes CSP (fines de los 70).

**Requisitos de una solución de exclusión mutua:** solo un proceso a la vez en la sección crítica; un proceso fuera de ella no bloquea a otros; no hay deadlock ni inanición; sin suposiciones sobre velocidades ni cantidad de procesadores; un proceso permanece en la sección crítica un tiempo finito.

### A2.2 Soluciones por software (espera activa)
Dos procesos, sin ayuda del hardware, usando solo lecturas y escrituras de variables compartidas.
- **Intento 1, turno alterno:** una variable `turno` dice quién entra. Garantiza exclusión mutua, pero **obliga a alternarse** (un proceso lento frena al otro) y si uno muere el otro queda esperando.
- **Intento 2, banderas ("quiero entrar"):** cada proceso levanta su bandera y espera que la del otro esté baja. Si ambos levantan a la vez, **deadlock**.
- **Algoritmo de Dekker:** combina banderas y turno, y resuelve el problema para dos procesos.
- **Algoritmo de Peterson:** versión más simple con la misma idea.

```java
class Peterson {                       // solo para 2 hilos (id 0 y 1)
    private volatile boolean quiere0, quiere1;
    private volatile int turno;

    void lock(int i) {
        if (i == 0) { quiere0 = true; turno = 1;          // "quiero, pero cedo el turno"
                      while (quiere1 && turno == 1) Thread.onSpinWait(); }
        else        { quiere1 = true; turno = 0;
                      while (quiere0 && turno == 0) Thread.onSpinWait(); }
    }
    void unlock(int i) { if (i == 0) quiere0 = false; else quiere1 = false; }
}
```
**Idea clave:** si los dos quieren entrar, gana el que **no** fue el último en escribir `turno` (cada uno cede el turno al otro). Cumple exclusión mutua, sin deadlock y sin inanición. **Desventajas:** **espera activa** (*busy waiting*, gasta CPU girando), es difícil de generalizar a N procesos y necesita `volatile` para la visibilidad.

### A2.3 Soluciones por hardware
- **Deshabilitar interrupciones:** en un monoprocesador, si nada interrumpe, nadie se intercala. **No sirve en multiprocesador** y es peligroso dejarlo al usuario.
- **Instrucciones atómicas de máquina:** **test-and-set** (lee el valor y escribe `true` en una sola operación indivisible), **exchange/swap** y **compare-and-swap (CAS)**.

```java
class SpinLock {                                   // test-and-set con AtomicBoolean
    private final AtomicBoolean ocupado = new AtomicBoolean(false);
    void lock()   { while (ocupado.getAndSet(true)) Thread.onSpinWait(); }  // getAndSet = test-and-set
    void unlock() { ocupado.set(false); }
}
```
**Ventajas:** valen para N procesos y N procesadores, son simples y sirven para varias secciones críticas. **Desventajas:** espera activa, **puede haber inanición** (el que entra se elige arbitrariamente) y **puede haber deadlock** (por ejemplo con prioridades: un proceso de alta prioridad gira esperando a uno de baja que nunca corre).

---

## A3. Semáforos (Módulo 2)

**Concepto (Dijkstra):** un semáforo es una variable **entera** más una **cola de procesos bloqueados**, que solo se manipula con dos operaciones **atómicas**:
```
semWait(s)   (P / acquire):  s--;  si s < 0 → el hilo se bloquea en la cola de s
semSignal(s) (V / release):  s++;  si s <= 0 → se despierta a un hilo de la cola de s
```
- **Binario:** vale 0 o 1 (sirve como mutex).
- **Contador (general):** vale cualquier entero ≥ 0 (cuenta unidades de un recurso).
- **Fuerte:** la cola es FIFO (sin inanición). **Débil:** el orden de despertar no está especificado (puede haber inanición).
- Cuando se bloquea, el hilo **no gira**: pasa a estado bloqueado (no gasta CPU), a diferencia de los spinlocks.

```java
Semaphore mutex = new Semaphore(1);            // binario: exclusión mutua
mutex.acquire();
try { /* sección crítica */ } finally { mutex.release(); }

Semaphore pool = new Semaphore(3);             // contador: hasta 3 hilos a la vez
Semaphore listo = new Semaphore(0);            // señalización: A avisa a B
// Hilo A: ...trabajo...; listo.release();
// Hilo B: listo.acquire();  // arranca recién cuando A terminó
new Semaphore(3, true);                        // "fair" = semáforo fuerte (FIFO)
```

**Productor/consumidor con semáforos:**
```java
class BufferSem {
    private final int[] datos = new int[10];
    private int in = 0, out = 0;
    private final Semaphore huecos = new Semaphore(10);   // lugares libres
    private final Semaphore items  = new Semaphore(0);    // datos disponibles
    private final Semaphore mutex  = new Semaphore(1);    // protege los índices

    void poner(int x) throws InterruptedException {
        huecos.acquire(); mutex.acquire();                // ¡primero huecos, después mutex!
        datos[in] = x; in = (in + 1) % datos.length;
        mutex.release(); items.release();
    }
    int sacar() throws InterruptedException {
        items.acquire(); mutex.acquire();
        int x = datos[out]; out = (out + 1) % datos.length;
        mutex.release(); huecos.release();
        return x;
    }
}
```
**Trampa clásica:** si se invierte el orden (`mutex.acquire()` antes que `huecos.acquire()`), un productor con el buffer lleno se queda dormido **con el mutex tomado** y ningún consumidor puede entrar: deadlock.

**Semáforo vs lock vs monitor:** el semáforo **no tiene dueño** (cualquier hilo puede hacer `release`), no está atado a los datos que protege, y un error en el orden de las operaciones es fácil de cometer. El lock sí tiene dueño. El monitor encapsula datos y sincronización, y es la opción de más alto nivel.

---

## A4. Paso de mensajes (Módulo 2)

**Concepto:** los procesos **no comparten memoria**; se comunican intercambiando mensajes. Sirve al mismo tiempo para **sincronizar** (el receptor espera hasta que llega algo) y para **comunicar datos**. Es el modelo natural de sistemas distribuidos y micronúcleos.

**Primitivas:** `send(destino, mensaje)` y `receive(origen, mensaje)`.

**Sincronización:**
- `send` **bloqueante** (espera hasta que el mensaje se recibe) o **no bloqueante** (sigue enseguida).
- `receive` **bloqueante** (espera si no hay mensaje; es lo habitual) o **no bloqueante**.
- **Rendezvous:** ambos bloqueantes. Emisor y receptor se "encuentran" en el mismo instante.

**Direccionamiento:**
- **Directo:** se nombra al proceso (`send(P2, m)`). Puede ser explícito (el receptor indica de quién) o implícito (recibe de cualquiera).
- **Indirecto:** se envía a una estructura intermedia (**buzón, cola o puerto**). Permite relaciones uno a uno, uno a muchos, muchos a uno y muchos a muchos.

**Exclusión mutua con mensajes:** un mensaje "testigo" (*token*) circula en un buzón. Quien lo recibe entra a la sección crítica y al salir lo reenvía.

**Comparación con memoria compartida:** con mensajes no hay condiciones de carrera sobre datos compartidos, y es más fácil razonar y distribuir, pero copiar mensajes cuesta más. En Java, `BlockingQueue` funciona como **buzón** (el bucle de eventos del molinete del tema 21 ya era paso de mensajes):
```java
BlockingQueue<String> canal = new SynchronousQueue<>();     // rendezvous
new Thread(() -> { try { canal.put("hola"); }               // send bloqueante
                   catch (InterruptedException e) {} }).start();
String m = canal.take();                                    // receive bloqueante
```
`LinkedBlockingQueue` o `ArrayBlockingQueue` dan un buzón con buffer (send no bloqueante mientras haya lugar).

---

## A5. Interbloqueo e inanición (Módulo 5)

### A5.1 Principios del interbloqueo
**Definición:** un conjunto de procesos está en **interbloqueo (deadlock)** cuando cada uno **espera un recurso que tiene otro del conjunto**, así que ninguno puede avanzar.

**Tipos de recursos:**
- **Reutilizables:** se usan y se devuelven (CPU, memoria, archivos, locks). El deadlock aparece por peticiones cruzadas.
- **Consumibles:** se crean y se consumen (mensajes, señales, interrupciones). El deadlock aparece, por ejemplo, si dos procesos hacen `receive` esperando un mensaje del otro.

**Condiciones de Coffman** (las cuatro **juntas** son necesarias):
1. **Exclusión mutua:** el recurso lo usa uno solo a la vez.
2. **Retención y espera (hold and wait):** un proceso retiene recursos mientras pide otros.
3. **Sin expropiación (no preemption):** no se le puede quitar un recurso a la fuerza.
4. **Espera circular:** existe un ciclo de procesos donde cada uno espera al siguiente.

**Grafo de asignación de recursos:** nodos proceso y recurso, arcos de asignación (recurso → proceso) y de petición (proceso → recurso). Un **ciclo** es condición necesaria de deadlock. Con **un solo ejemplar** por recurso, además es **suficiente**.

### A5.2 Prevención (negar una condición, antes de que ocurra)

| Condición a negar | Cómo | Costo |
|---|---|---|
| Exclusión mutua | Hacer recursos compartibles | No siempre se puede (ej: impresora) |
| Retención y espera | Pedir **todos** los recursos de una vez | Baja utilización, posible inanición |
| Sin expropiación | Si no consigue uno, **libera** lo que tiene, o se le expropia | Solo sirve si el estado se puede guardar y restaurar |
| Espera circular | **Ordenar** los recursos y pedirlos siempre en orden creciente | Obliga a saber el orden con anticipación |

### A5.3 Predicción o evitación (*avoidance*)
No se niega ninguna condición, pero **cada petición se decide dinámicamente** para no entrar en un estado inseguro.
- **Estado seguro:** existe al menos una secuencia en la que todos los procesos pueden terminar. **Inseguro:** no hay garantía (no implica deadlock, pero lo permite).
- **Algoritmo del banquero (Dijkstra):** se concede un recurso solo si el estado resultante sigue siendo seguro.
- **Requisitos:** cada proceso declara de antemano su **máximo** de recursos, los procesos son independientes y el número de recursos es fijo.
- **Costo:** ejecutar el algoritmo en cada petición.

### A5.4 Detección y recuperación
Se **permite** el deadlock y se lo detecta periódicamente (buscando ciclos o reduciendo el grafo de asignación).
- **Recuperación:** abortar todos los procesos involucrados, abortarlos de a uno hasta romper el ciclo, retroceder a puntos de control (*rollback*) o expropiar recursos.

### A5.5 Estrategia integrada
Se **agrupan los recursos en clases**, se **ordenan las clases** (prevención de espera circular) y **dentro de cada clase** se usa la técnica más adecuada (por ejemplo: espacio de intercambio con "pedir todo de una vez", recursos de proceso con evitación, memoria principal con expropiación, recursos internos con ordenamiento).

### A5.6 Cena de los filósofos
**Problema:** 5 filósofos en una mesa redonda, 5 tenedores (uno entre cada par). Para comer cada uno necesita **los dos** tenedores contiguos. Piensan y comen alternadamente.
- **Solución ingenua:** cada uno toma primero el izquierdo y después el derecho. Si todos toman el izquierdo a la vez, **deadlock** (retención y espera + espera circular).
- **Soluciones:**
  1. **Limitar a 4** filósofos sentados a la vez (un semáforo con 4): siempre alguien puede tomar los dos.
  2. **Ordenar los recursos:** numerar los tenedores y tomar siempre primero el de número menor (rompe la espera circular).
  3. **Tomar ambos o ninguno** de forma atómica (con un monitor, o con `tryLock`).
- La inanición puede persistir si dos filósofos se turnan y dejan sin comer a un vecino.

```java
class Filosofo implements Runnable {
    private final ReentrantLock menor, mayor;             // tenedores, ordenados por número
    Filosofo(int i, ReentrantLock[] tenedores) {
        int a = i, b = (i + 1) % tenedores.length;
        menor = tenedores[Math.min(a, b)];
        mayor = tenedores[Math.max(a, b)];
    }
    public void run() {
        while (true) {
            menor.lock();                                  // siempre en el mismo orden global
            try {
                mayor.lock();
                try { /* comer */ } finally { mayor.unlock(); }
            } finally { menor.unlock(); }
            /* pensar */
        }
    }
}
```

### A5.7 Inanición (*starvation*)
- **Definición general:** un proceso está listo pero **nunca obtiene** el recurso o la CPU porque otros pasan siempre antes.
- **En un sistema de tiempo real:** la inanición se da cuando **no se atienden las solicitudes de un proceso (hilo) en el tiempo en que se requiere la respuesta**. Ahí no alcanza con "eventualmente": hay **plazos (deadlines)**.
- **Inversión de prioridad:** un hilo de baja prioridad tiene un lock que necesita uno de alta, y uno de prioridad media desplaza al de baja, así que el de alta queda esperando indefinidamente. Se soluciona con **herencia de prioridad**.
- **Deadlock vs livelock vs inanición:** en deadlock nadie avanza y todos están bloqueados; en livelock cambian de estado pero no progresan; en inanición **alguien** avanza y otro nunca.

**Primitivas de sincronización de hilos** (para el "caso de estudio" del módulo): `join`, `wait/notify`, `synchronized`, `Lock/Condition`, `Semaphore`, `CountDownLatch`, `CyclicBarrier`, `Phaser`, `Exchanger` (ver A7).

## A6. Redes de Petri (Módulos 3, 4 y 6)

Es el modelo formal que la materia usa para **especificar, analizar y verificar** sistemas concurrentes. Se relaciona directo con las temas 2 y 22: es otra forma de modelar el "estado + eventos + interleaving", pero con **concurrencia explícita**.

### A6.1 Bases: lugares, transiciones, arcos, marcado, disparo

Una red de Petri es un **grafo bipartito dirigido** con dos tipos de nodos:
- **Lugares (círculos):** representan **condiciones, estados o recursos** ("hilo listo", "lock libre", "buffer con dato").
- **Transiciones (barras):** representan **eventos o acciones** ("tomar el lock", "producir").
- **Arcos:** unen lugar → transición (entrada) o transición → lugar (salida), siempre entre nodos de distinto tipo. Pueden llevar un **peso**.
- **Marcado:** cuántas **fichas (tokens)** hay en cada lugar. El marcado **es el estado** del sistema. `M₀` es el marcado inicial.

**Definición:** `R = (P, T, Pre, Post, M₀)`. `Pre(p,t)` es el peso del arco `p → t`; `Post(p,t)` es el del arco `t → p`.

**Regla de disparo:**
1. Una transición `t` está **habilitada (sensibilizada)** en `M` si `M(p) ≥ Pre(p,t)` para todos sus lugares de entrada.
2. Al **dispararse** consume `Pre(p,t)` fichas de cada entrada y produce `Post(p,t)` fichas en cada salida: `M' = M − Pre(·,t) + Post(·,t)`.
3. El disparo es **atómico** e instantáneo. Si hay varias transiciones habilitadas, se dispara una **elegida de forma no determinista** (es el interleaving otra vez).

**Matriz de incidencia y ecuación fundamental:** `C = Post − Pre`. Si `σ` es el vector que cuenta cuántas veces se disparó cada transición:
```
M' = M₀ + C · σ          (ecuación fundamental / de estado)
```
Es una condición **necesaria** para que `M'` sea alcanzable, pero **no suficiente** (puede existir `σ` que la cumpla sin que exista una secuencia de disparo válida).

**Patrones básicos de concurrencia:**

| Patrón | Estructura | Modela |
|---|---|---|
| Secuencia | `t1 → p → t2` | Orden de acciones |
| Paralelismo (fork) | Una transición con **dos** lugares de salida | Se lanzan dos actividades en paralelo |
| Sincronización (join) | Una transición con **dos** lugares de entrada | Espera a que ambas terminen |
| Conflicto | Un lugar con **dos** transiciones de salida | Elección o competencia por un recurso |
| Recurso compartido | Lugar con fichas que se consumen y devuelven | Locks, buffers, semáforos |

**Ejemplo: exclusión mutua entre dos procesos**

| Transición | Consume (Pre) | Produce (Post) |
|---|---|---|
| `e1` (entrar 1) | `R1`, `M` | `C1` |
| `s1` (salir 1) | `C1` | `R1`, `M` |
| `e2` (entrar 2) | `R2`, `M` | `C2` |
| `s2` (salir 2) | `C2` | `R2`, `M` |

`R1, R2` = procesos en reposo, `C1, C2` = en sección crítica, `M` = mutex libre. **Marcado inicial:** `R1 = R2 = M = 1`, el resto 0.
- En `M₀`, `e1` y `e2` están habilitadas, pero **comparten `M`**: si se dispara una, la otra se deshabilita. Ese conflicto **es** la exclusión mutua.
- **Invariante:** `M + C1 + C2 = 1` siempre, así que `C1` y `C2` nunca están marcados a la vez.

**Simulador mínimo en Java** (mismo `M' = M − Pre + Post`):
```java
class RedPetri {
    int[] marcado;            // marcado[p] = fichas en el lugar p
    int[][] pre, post;        // pre[t][p], post[t][p]

    boolean habilitada(int t) {
        for (int p = 0; p < marcado.length; p++)
            if (marcado[p] < pre[t][p]) return false;
        return true;
    }
    void disparar(int t) {
        if (!habilitada(t)) throw new IllegalStateException("t" + t + " no habilitada");
        for (int p = 0; p < marcado.length; p++)
            marcado[p] += post[t][p] - pre[t][p];
    }
}
```

### A6.2 Redes autónomas y no autónomas
- **Autónoma:** su evolución depende **solo de su estructura y del marcado**. No hay influencia del exterior ni del tiempo.
- **No autónoma:** el disparo depende además de algo **externo**: eventos (sincronizadas), condiciones y acciones (interpretadas) o tiempo (temporales, estocásticas). Son las del Módulo 6.

### A6.3 Redes de Petri especiales

| Tipo | Idea |
|---|---|
| **Simple / ordinaria** | Todos los arcos tienen peso 1 |
| **Generalizada** | Arcos con peso > 1 |
| **Pura** | Sin bucles propios (ningún lugar es entrada y salida de la misma transición) |
| **Finita** | Cada lugar tiene una **capacidad máxima** de fichas |
| **Coloreada** | Las fichas tienen **valor o tipo (color)**: condensa estructuras repetidas (por ejemplo, N hilos idénticos en una sola subred) |
| **Extendida** | Agrega **arcos inhibidores** (la transición se habilita si el lugar está **vacío**). Gana el poder de una máquina de Turing |
| **Prioritaria** | Asigna **prioridades** para resolver conflictos |
| **No autónoma** | Sincronizada, interpretada, temporizada, estocástica (A6.6) |

*(La terminología de "simple" varía según el autor; confirmá con el material de la cátedra.)*

### A6.4 Estructuras particulares
- **Grafo de estado (máquina de estados):** cada **transición** tiene exactamente **una entrada y una salida**. Modela **conflicto y decisión** (como un autómata finito) pero **no** paralelismo ni sincronización.
- **Grafo de eventos (grafo marcado):** cada **lugar** tiene exactamente **una transición de entrada y una de salida**. Modela **paralelismo y sincronización** pero **no** conflicto.
- **Libre de conflicto:** ningún lugar tiene más de una transición de salida (no hay elección).
- **Libre elección (free choice):** si un lugar tiene varias salidas, es la **única entrada** de cada una de ellas. El conflicto se resuelve sin depender de otros lugares, y eso permite un análisis muy potente (teorema de Commoner, más abajo).

### A6.5 Propiedades (Módulo 4)

| Propiedad | Definición | Qué garantiza en un sistema |
|---|---|---|
| **Delimitada (k-acotada)** | Ningún lugar supera `k` fichas en ningún marcado alcanzable | Recursos y buffers acotados, espacio de estados finito |
| **Segura** | Delimitada con `k = 1` | Cada condición vale 0 o 1 (sin desbordes) |
| **Viva** | Desde **todo** marcado alcanzable se puede llegar a uno que habilite cada transición | Ninguna acción "muere" nunca |
| **Punto muerto (deadlock)** | Marcado alcanzable en el que **ninguna** transición está habilitada | Es la situación a evitar |
| **Conflicto estructural** | Dos transiciones comparten un lugar de entrada | Hay competencia potencial |
| **Conflicto efectivo** | En un marcado, ambas están habilitadas y **no alcanzan las fichas** para las dos | Disparar una deshabilita la otra |
| **Concurrencia** | Dos transiciones habilitadas que **no interfieren**; pueden dispararse en cualquier orden o a la vez | Paralelismo real |
| **Disparos múltiples** | Una misma transición puede dispararse **varias veces** seguidas por tener fichas de sobra | Mayor grado de paralelismo |
| **Persistencia** | Una transición habilitada **sigue habilitada** hasta dispararse, salvo por su propio disparo | Ausencia de conflicto efectivo |

Relaciones útiles: **viva ⇒ sin punto muerto** (la vuelta no vale); una red viva y acotada tiene un comportamiento cíclico sano. La vivacidad tiene niveles (L0 muerta hasta L4 viva).

**Invariantes (análisis estructural, sin simular):**
- **Invariante de marcado / componente conservador (P-invariante):** un vector `y ≥ 0` con `yᵀ · C = 0`. Entonces `yᵀ · M = yᵀ · M₀` **en todo marcado alcanzable**: cierta suma ponderada de fichas es **constante**. Ejemplo: `M + C1 + C2 = 1` (exclusión mutua). Sirve para demostrar **seguridad**, como exclusión mutua y acotación.
- **Invariante de disparo / componente repetitivo (T-invariante):** un vector `x ≥ 0` con `C · x = 0`. Es un conjunto de disparos que **devuelve la red al mismo marcado**: un ciclo de comportamiento (por ejemplo, un hilo completa "entrar y salir" y todo vuelve al inicio).

**Análisis por enumeración:**
- **Grafo de marcas (alcanzabilidad):** nodos = marcados alcanzables, arcos = disparos. Es **finito si y solo si** la red es acotada. Actúa como un autómata finito: se verifican propiedades recorriéndolo.
- **Árbol de cobertura (*root tree*, Karp-Miller):** cuando la red **no es acotada** el grafo sería infinito. Se lo reemplaza por un árbol con raíz `M₀` en el que, si un marcado nuevo **cubre** a uno anterior de su rama (mayor o igual y distinto), los lugares que crecieron se marcan con **ω (infinito)**.

**Sifones y trampas:**
- **Sifón:** conjunto de lugares `S` tal que toda transición que **produce** en `S` también **consume** de `S` (`•S ⊆ S•`). Si un sifón se **vacía**, queda vacío **para siempre**: es causa típica de **deadlock**.
- **Trampa:** conjunto `S` tal que toda transición que **consume** de `S` también **produce** en `S` (`S• ⊆ •S`). Si una trampa tiene una ficha, **siempre** tendrá alguna.
- **Teorema de Commoner (redes de libre elección):** la red es **viva** si y solo si **todo sifón contiene una trampa marcada**.

**Grafos de eventos fuertemente conectados:** son **vivos** si y solo si **todo circuito tiene al menos una ficha**; y **seguros** si además todo lugar pertenece a un circuito con **exactamente una** ficha.

**Software de análisis:** herramientas que calculan invariantes, grafo de alcanzabilidad, vivacidad y acotación, y permiten simular (por ejemplo PIPE, TINA, CPN Tools; confirmá cuál usa la cátedra).

**Caso de estudio del programa:** las propiedades de **seguridad** (exclusión mutua, acotación, invariantes) y de **progreso** (vivacidad, ausencia de deadlock) se verifican **modelando** el sistema con redes de Petri o autómatas finitos y analizando su grafo de alcanzabilidad e invariantes. Una red acotada es equivalente a un autómata finito; una no acotada es más expresiva; con arcos inhibidores llega al poder de Turing.

### A6.6 Redes de Petri no autónomas (Módulo 6)

**Redes sincronizadas.**
- A cada transición se le asocia un **evento externo**. Una transición **habilitada solo se dispara cuando ocurre su evento**.
- **Eventos simultáneos:** varios eventos pueden ocurrir a la vez y disparan varias transiciones.
- **Disparo iterado:** al ocurrir un evento externo puede dispararse una **secuencia** de transiciones (la secuencia de disparo elemental) hasta llegar a un marcado **estable**.
- **Marcado estable:** aquel donde no hay transiciones "inmediatas" habilitadas. El **árbol de cobertura** se construye sobre los marcados **estables** alcanzables.
- Propiedades: **prontitud/estabilidad**, acotación, seguridad, vivacidad y ausencia de deadlock, ahora evaluadas considerando los eventos.

**Redes interpretadas (por control).**
- Se agregan **condiciones (variables booleanas) y eventos** a las transiciones, y **acciones** (salidas) a los lugares. La red es la **parte de control** de un sistema y comanda a la parte operativa (por ejemplo, un autómata programable).
- **Algoritmo de interpretación (idea general):** en cada ciclo se leen las entradas, se determinan las transiciones habilitadas cuya condición es verdadera, se disparan, se actualiza el marcado y se ejecutan las acciones de los lugares marcados.

**Redes temporales.**
- **P-temporizadas:** una ficha, al llegar a un lugar, queda **no disponible durante un tiempo `d`**. **T-temporizadas:** una transición se dispara **`d` unidades después** de quedar habilitada. **Tiempo constante:** los retardos son valores fijos.
- **Comportamiento estacionario:** pasado un transitorio, el sistema entra en un régimen periódico.
- **Rendimiento:** en un grafo de eventos, el **tiempo de ciclo** de un circuito es `(suma de retardos) / (número de fichas)`. El circuito con **mayor** tiempo de ciclo es el **crítico** (cuello de botella), y el rendimiento (*throughput*) es su inversa. Ejemplo: un circuito con retardos 2 + 3 + 1 = 6 y 2 fichas tiene tiempo de ciclo 3.

**Redes estocásticas.** Los retardos son **variables aleatorias**, normalmente exponenciales. El modelo equivale a una **cadena de Markov de tiempo continuo**, de la que se calculan probabilidades estacionarias, utilización y *throughput*.

**Caso de estudio:** simular una red temporal para medir rendimiento y escalabilidad del algoritmo implementado.

---

## A7. La API de concurrencia de Java (prácticas)

### A7.1 Gestión básica de hilos
```java
Thread t = new Thread(() -> System.out.println("hola"), "mi-hilo");
t.setDaemon(false);      // un hilo daemon no impide que la JVM termine
t.start();               // crea el hilo; run() directo NO crea nada
t.join();                // espera a que termine (WAITING)
```
- **Interrupción cooperativa:** `t.interrupt()` **no mata** al hilo, solo activa una bandera o lanza `InterruptedException` si está en `sleep/wait/join`. El hilo decide cómo terminar:
```java
Thread t = new Thread(() -> {
    while (!Thread.currentThread().isInterrupted()) { /* trabajo */ }
});
```
- `ThreadLocal<T>`: una copia del dato **por hilo** (confinamiento). `setUncaughtExceptionHandler`: qué hacer si el hilo muere por una excepción.
- **Runnable vs Callable:** `Runnable.run()` no devuelve nada ni lanza excepciones chequeadas; `Callable<V>.call()` **devuelve un valor** y puede lanzar excepciones.

### A7.2 Mecanismos de sincronización de alto nivel

| Clase | Para qué | Se reutiliza |
|---|---|---|
| `Semaphore` | Limitar accesos a N (A3) | Sí |
| `CountDownLatch(n)` | Un hilo espera a que **n eventos** ocurran (`countDown()` / `await()`) | **No** (un solo uso) |
| `CyclicBarrier(n)` | **n hilos se esperan mutuamente** en un punto y siguen juntos | Sí |
| `Phaser` | Barrera con **fases** y participantes dinámicos | Sí |
| `Exchanger<V>` | **Dos** hilos intercambian datos en un punto | Sí |
| `Condition` | Colas de espera asociadas a un `Lock` (tema 10) | Sí |

```java
CountDownLatch listos = new CountDownLatch(3);
for (int i = 0; i < 3; i++)
    new Thread(() -> { /* inicializar */ listos.countDown(); }).start();
listos.await();                 // main sigue recién cuando los 3 avisaron

CyclicBarrier barrera = new CyclicBarrier(3, () -> System.out.println("fase completa"));
// cada hilo, al terminar su fase: barrera.await();  → todos siguen juntos
```

### A7.3 Ejecutores (*Executor framework*)
**Idea:** separar **qué** hay que hacer (la tarea) de **cómo y en qué hilo** se ejecuta. Se reutilizan hilos de un **pool** en lugar de crear uno por tarea (que es caro).
```java
ExecutorService pool = Executors.newFixedThreadPool(4);
Future<Integer> f = pool.submit(() -> 6 * 7);       // Callable → Future
System.out.println(f.get());                         // bloquea hasta tener el resultado (42)
pool.shutdown();                                     // no acepta tareas nuevas; termina las pendientes
pool.awaitTermination(1, TimeUnit.MINUTES);
```

| Factoría | Comportamiento |
|---|---|
| `newFixedThreadPool(n)` | n hilos fijos, cola ilimitada |
| `newCachedThreadPool()` | crea hilos según demanda y reutiliza los ociosos |
| `newSingleThreadExecutor()` | un solo hilo, tareas en orden |
| `newScheduledThreadPool(n)` | tareas con retardo o periódicas |
| `newVirtualThreadPerTaskExecutor()` | un hilo virtual por tarea (Java 21) |

- **`Future`:** `get()` (espera), `isDone()`, `cancel()`. `invokeAll` lanza varias tareas y espera a todas; `invokeAny` devuelve la primera que termine.
- **`CompletableFuture`:** encadenar tareas asíncronas sin bloquear (`supplyAsync`, `thenApply`, `thenCombine`), muy en línea con el modelo reactivo.

### A7.4 Fork/Join
- **Idea:** **divide y vencerás** en paralelo. Una tarea se parte en subtareas hasta un **umbral**; se resuelven las chicas directamente y se **combinan** los resultados.
- **Work-stealing:** cada hilo del `ForkJoinPool` tiene su cola de tareas, y el que se queda sin trabajo **le roba** tareas a otro. Balancea la carga automáticamente.
- `RecursiveTask<V>` (devuelve valor) o `RecursiveAction` (no devuelve). Los *parallel streams* usan este pool por debajo.
```java
class SumaTask extends RecursiveTask<Long> {
    private static final int UMBRAL = 10_000;
    private final int[] a; private final int ini, fin;
    SumaTask(int[] a, int ini, int fin) { this.a = a; this.ini = ini; this.fin = fin; }

    @Override protected Long compute() {
        if (fin - ini <= UMBRAL) {                          // caso base: secuencial
            long s = 0; for (int i = ini; i < fin; i++) s += a[i]; return s;
        }
        int mid = (ini + fin) >>> 1;
        SumaTask izq = new SumaTask(a, ini, mid), der = new SumaTask(a, mid, fin);
        izq.fork();                                          // izq corre en otro hilo
        return der.compute() + izq.join();                   // der en este hilo, luego combina
    }
}
// long total = ForkJoinPool.commonPool().invoke(new SumaTask(datos, 0, datos.length));
```

### A7.5 Estructuras de datos concurrentes
- `ConcurrentHashMap` (bloquea por partes, no todo el mapa), `CopyOnWriteArrayList` (copia al escribir: ideal con muchas lecturas y pocas escrituras), `ConcurrentLinkedQueue`, `ConcurrentSkipListMap`.
- **`BlockingQueue`** (productor/consumidor sin escribir wait/notify): `ArrayBlockingQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue`, `SynchronousQueue`, `DelayQueue`. `put` bloquea si está llena, `take` si está vacía.
- **Atómicos:** `AtomicInteger`, `AtomicReference`, `LongAdder` (mejor con mucha contención).
- **`Collections.synchronizedList(...)` vs colecciones concurrentes:** las primeras envuelven **todo** con un solo lock (y iterar exige sincronizar a mano); las concurrentes escalan mejor y sus iteradores son **débilmente consistentes** (no lanzan `ConcurrentModificationException`).

### A7.6 Adaptar el comportamiento por defecto
```java
AtomicInteger n = new AtomicInteger();
ThreadFactory fabrica = r -> {                               // nombre y tipo de hilo propios
    Thread t = new Thread(r, "worker-" + n.incrementAndGet());
    t.setDaemon(true);
    return t;
};
ExecutorService pool = new ThreadPoolExecutor(
        2, 4,                                                // hilos base y máximo
        30, TimeUnit.SECONDS,                                // vida de los hilos extra ociosos
        new ArrayBlockingQueue<>(10),                        // cola acotada
        fabrica,
        new ThreadPoolExecutor.CallerRunsPolicy());          // qué hacer si se llena (rechazo)
```
Se puede además **extender** `ThreadPoolExecutor` (métodos `beforeExecute` / `afterExecute`), usar una `ForkJoinWorkerThreadFactory` propia o construir sincronizadores nuevos sobre `AbstractQueuedSynchronizer`.

### A7.7 Testing y depuración de aplicaciones concurrentes
- **Por qué es difícil:** el resultado depende del interleaving, no es reproducible y los errores aparecen de forma intermitente (*heisenbugs*).
- **Estrategias:** pruebas de **estrés** (muchos hilos y muchas iteraciones), **inyectar demoras** (`sleep`, `yield`) para forzar interleavings raros, arrancar los hilos a la vez con un `CountDownLatch`, y verificar **invariantes** al final.
- **Herramientas:** análisis estático (SpotBugs), *thread dumps* (`jstack`, VisualVM), detección de deadlocks con `ThreadMXBean.findDeadlockedThreads()`, `jcstress` (pruebas de concurrencia de OpenJDK) y **model checking** (por ejemplo Java PathFinder).
- **Idea clave:** ningún testing garantiza correctitud. Se complementa con **razonamiento** (invariantes, monitores) y **modelado formal** (Petri, autómatas).

---

## A8. Rendimiento, complejidad de los modelos y sistemas reactivos/tiempo real

### A8.1 Rendimiento y escalabilidad
- **Speedup:** `S = T₁ / Tₚ` (tiempo con 1 procesador sobre tiempo con `p`). **Eficiencia:** `E = S / p`.
- **Ley de Amdahl:** si una fracción `f` del programa es **secuencial**, `S ≤ 1 / (f + (1 − f)/p)`. Nunca superás `1/f`. Ejemplo: `f = 0,1` y `p = 8` da `S ≈ 4,7`; con infinitos procesadores, el máximo es 10.
- **Ley de Gustafson:** si el problema crece con los procesadores, el speedup escalado es `S = p − f·(p − 1)`.
- **Costos que reducen el rendimiento:** sincronización y **contención** de locks, creación de hilos, comunicación, desbalance de carga, secciones críticas largas. Por eso conviene **cerrojos cortos**, pools de hilos y estructuras sin locks.
- **Escalabilidad:** cómo se comporta el rendimiento al aumentar hilos o procesadores. Se **mide** (o se modela con Petri temporales o estocásticas).

### A8.2 Gestión del tamaño y complejidad de los modelos
- **Explosión de estados:** el estado global de un sistema de `n` hilos con `k` estados cada uno tiene hasta **kⁿ** combinaciones (por ejemplo, 4 hilos de 3 estados: 3⁴ = 81 estados globales). Enumerar todo se vuelve inviable.
- **Técnicas para controlarla:** **abstracción** (modelar solo lo relevante), **modularidad y jerarquía** (refinar subredes), **redes coloreadas** (una subred para N hilos idénticos), **reducciones** (fusionar lugares o transiciones en serie que no cambian las propiedades) y **análisis estructural** (invariantes, sifones/trampas) en lugar de enumerar todos los estados.

### A8.3 Sistemas reactivos y de tiempo real (qué caracteriza a un programa reactivo)
Un programa **reactivo** (típico de sistemas **embebidos**):
1. Mantiene una **interacción continua con su entorno**.
2. Es **dirigido por eventos**: reacciona a estímulos externos.
3. **No termina** (ejecución potencialmente infinita); no se define por un resultado final.
4. Es **concurrente y no determinista**: los eventos llegan en órdenes y momentos que no controla.
5. Tiene **restricciones temporales** (tiempo real): importa **cuándo** responde, no solo qué responde.
6. Se modela con **estados y transiciones** (Mealy/Moore, Petri) y se especifica con propiedades de **seguridad** y **vivacidad**.

**Tiempo real duro vs blando:** en el **duro** perder un plazo (deadline) es una falla grave (frenos, marcapasos); en el **blando** solo degrada la calidad (video). Java estándar **no garantiza** tiempo real (recolector de basura, planificación del SO); existe la especificación RTSJ para eso.

---
