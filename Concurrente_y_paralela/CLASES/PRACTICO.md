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