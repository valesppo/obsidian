
# Capitulo 1

● Parametros = lo que el metodo espera.
● Argumentos = lo que vos pasás
● Primitivos → no se modifican afuera
● Objetos → se puede modificar su estado
● this se usa para distinguir atributos

==**Constructores**:==

* Son el bloque de codigo que inicializa un objeto cuando se crea con new.
* No tienen ningun tipo de retorno (ni siquiera void) y su nombre debe coincidir con el de la clase
* pueden estar sobrecargados (varios constructores con diferentes parametros)
* se puede llamar a otro constructor de la misma clase con this(...) (constructor chaining)
* si no definis ninguno, java crea un constructor por defecto sin parametros.
* si se usa un throw se termina le constructor tambien.

```
public class Car {
    String model;
    int year;

    // Parameterized Constructor
    public Car(String model, int year) {
        this.model = model;
        this.year = year;
    }

    public static void main(String[] args) {
        Car car = new Car("Toyota", 2021);
        System.out.println("Model: " + car.model + ", Year: " + car.year);
    }
}

```

==**Campos (fields):**==

* Son las variables dentro de una clase. Pueden ser
	* Instancia: cada objeto tiene su copia (private int vida)
```
	public class Coche {
    // Campos de instancia
    String modelo;
    int año;
    boolean dobleTraccion;
    
    public Coche(String modelo, int año) {
        this.modelo = modelo;
        this.año = año;
    }
}   
```

	* Estaticos (static): compartidos por la clase (static int contador)

```
	public class Coche {
    static int totalCoches = 0; // Campo de clase
    String modelo;
    
    public Coche(String modelo) {
        this.modelo = modelo;
        totalCoches++; // Incrementa el contador global
    }
}   
```
* Modificadores comunes: private, protected, public, final, static.
* Buenas practicas: mantener campos private y exponerlos mediante getters/setters (encapsulacion)


==**Metodos** ==:

 * Bloques que realizan acciones. Tienen: modificador, ripo de retorno, nombre, parametros.
 * Pueden ser estaticos o de instancia.
 * Sobrecarga: mismo nombre, distinto listado de parametros.
 * Override: redefinir metodo de la superclase (ej. toString()).
 * Si un metodo es de retorno primitivo puede retornar su wrapper ripo objeto y viceversa
 * this referencia la objeto actual


==**Herencia:**==

* Herencia es un mecanismo de la programacion orientada a objetos que permite que una clase (subclase) herede atributos y comportamientos de otra clase (super clase)
* Permite reusar codigo, modelar relaciones is-a (Un Perro es un Animal) y soportar polimorfismo (mismo mensaje, distinta respuesta segun la instancia).
  
	* **extends** --- una clase hereda de otra clase: class Hijo extends Padre {...}  
	* **implements** --- una clase implementa una o mas interfaces: class C implements I1, I2 {...}
	* Java permite herencia simple de clases (una sola superclase), pero multiple de interfaces.
	* **abstract** --- clase o metodo abstracto (la clase no se puede instanciar; metodos sin implementacion obligan a la subclase a implementarlos)
	* **final** --- si una clase es final no se puede extender; si un metodo es final no se puede sobreescribir; si un atributo es final es inmutable despues de asignacion.
	* **super** --- referencia a la superclase (para acceder a campos y metodos de la superclase o invocar su constructor).
	* **@Override** --- anotacion opcional pero recomendada para indicar que un metodo sobreescribe otro de la superclase. 

```
// 1. Interfaz que define comportamiento (implements)
interface Volador {
    void volar();
}

// 2. Clase abstracta base (abstract, extends implícito de Object, super en constructor)
abstract class Animal {
    protected String nombre;

    // Constructor que usa super (implícito o explícito)
    public Animal(String nombre) {
        // super() se llama implícitamente aquí a Object
        this.nombre = nombre;
    }

    // Método abstracto que debe ser implementado
    public abstract void hacerSonido();
}

// 3. Subclase concreta que extiende la abstracta e implementa la interfaz
class Pajaro extends Animal implements Volador {

    // Constructor: usa 'super' para inicializar la parte de Animal
    public Pajaro(String nombre) {
        super(nombre); // Llama al constructor de la clase abstracta Animal
    }

    // Implementación del método abstracto de Animal
    @Override
    public void hacerSonido() {
        System.out.println(nombre + " hace un sonido.");
    }

    // Implementación del método de la interfaz Volador
    @Override
    public void volar() {
        System.out.println(nombre + " está volando.");
    }
}

// Clase de prueba
public class Main {
    public static void main(String[] args) {
        Pajaro pajaro = new Pajaro("Tweety");
        pajaro.hacerSonido(); // Hereda/Implementa de Animal
        pajaro.volar();       // Implementa de Volador
    }
}    
```

clave del BNA 1598