
```
public class Tablero {
    // Acá hay de todo: Sensores, Actuadores, Pantallas...
    private ArrayList<Dispositivo> componentesConectados;

    public Tablero() {
        this.componentesConectados = new ArrayList<>();
    }
    
    // ... método para agregar componentes ...
    
    
    **Tu Misión de Examen (Ejercicio 4):** Escribí un método público llamado `obtenerSensores()` adentro de la clase `Tablero`. Este método tiene que:

1. Crear un buffer (un nuevo `ArrayList`) preparado para guardar **solo** objetos de tipo `Sensor`.
    
2. Recorrer la lista principal `componentesConectados`.
    
3. Filtrar para detectar cuáles de esos componentes son realmente Sensores. _(Pista: usá la compuerta lógica `if (componente instanceof Sensor)`)_.
    
4. Hacer el **Downcasting** (el cast forzado) para transformar temporalmente ese `Dispositivo` en un `Sensor` y poder guardarlo en tu nueva lista.
    
5. Retornar la lista de sensores.
   
   
   
   
   public void getDispositivo(Dispositivo m){
	 componentesConectados.add(m);
}

    public ArrayList<Sensor> obtenerSensores() {
        
        ArrayList<Sensor> nuevo = new ArrayList<>();
        
        for(Dispositivo x : componentesConectados) {
            
            // No comparamos con ==, le preguntamos directo a Java qué es el objeto
            if(x instanceof Sensor) {
                
                // 2. EL DOWNCASTING (Forzamos la traducción)
                // Le decimos a Java: "Tratá a la variable x como un Sensor y guardalo"
                nuevo.add((Sensor) x);
            }
        }
        
        return nuevo;
    }
    
    
**Mirá la clase Padre en el UML:** Si ves que tiene un atributo tipo `String tipo` o un `enum Categoría`, usás eso con un `if` normal (como en el Acuático).
    
**Si el Padre es genérico puro:** Si no tiene ningún atributo que lo identifique, usás `instanceof` para saber qué clase hija está escondida ahí adentro.
   
   
```


```
public class Laboratorio {

	private ArrayList<Dispositivo> equipos;
	
	public Laboratorio(){
	
		this.equipos = new ArrayList<>();
	}
	
	public void agregarEquipo(Dispositivo d){
		if(d.isEmpty() || d == null){ return; }
		equipos.add(d);
	}
	
	public ArrayList<Refrigerador> getRefrigeradoresCriticos(int umbral){
	
		ArrayList<Refrigerador> buffer = new ArrayList<>();
		
		for(Refrigerador d : equipos){
			if(d instanceof Refrigerador)
				Refrigerador ref = (Refrigerador) d;
				if(ref.getTemperatura() > umbral){
					buffer.add(ref);
				}
		}
		return buffer;
	}
}
```

==**EJERCICIO 1**==
![[ejercicio2 1.png|458]]
```
public abstract class Dispositivo {

    private String id;
    private String nombre;
    private boolean activo;

    /** Constructor Dispositivo.
     * por defecto los dispositivos se crean activos
     */
    public Dispositivo(String id, String nombre) {
        // TODO: completar
        this.id = id;
        this.nombre = nombre;
        this.activo = true;
    }

    public String getId() {
        // TODO: completar
        return id;
    }

    public String getNombre() {
        // TODO: completar
        return nombre;
    }

    public void setNombre(String nombre) {
        // TODO: completar
        this.nombre = nombre;
    }

    public boolean isActivo() {
        // TODO: completar
        return this.activo;
    }

    public void setActivo(boolean activo) {
        this.activo = activo;
    }

    public abstract String obtenerDescripcionEstado();

    @Override
    public String toString() {
        // TODO: completar
        return " <"+getId()+"> " + " - " + "<"+getNombre()+">" + " - " + "["+isActivo()+"]";
    }

    /** 
     * Un dispositivo se considera igual a otro si su "id" es el mismo
     */
    @Override
    public boolean equals(Object obj) {
        // TODO: completar
        if(this == obj){
	        return true;
        }
        if(obj == null || !(obj instanceof Dispositivo)){
	        return false;
        }
        
        Dispositivo otro = (Dispositivo) obj;
        
        return this.id.equals(otro.getId());
        
    }
```

---

==**EJERCICIO 2**==
![[ejercicio3.png|614]]

```
// Declare la clase "ValvulaRiego" para que cumpla la relación de herencia con 
// la clase "Dispositivo"

/* Complete aquí la declaracion de la clase*/  
public class ValvulaRiego extends Dispositivo {
    private boolean abierta;

    public ValvulaRiego(String id, String nombre) {
        // TODO: completar llamada al constructor padre
        // TODO: inicializar la válvula cerrada
        super(id,nombre);
        this.abierta = false;
    }

    public void abrir() throws RiegoException {
        // TODO: si la válvula está inactiva, lanzar RiegoException
        // TODO: abrir la válvula
        if(!this.isActivo()){
	        throw new RiegoException(" ");
        }
        this.abierta = true;
    }

    public void cerrar() throws RiegoException {
        // TODO: cerrar la válvula
        this.abierta = false;
    }
        
    

    public boolean isAbierta() {
	    return this.abierta;    
    }

    @Override
    public String obtenerDescripcionEstado() {
        // TODO: completar
        // Retorna el texto "Valvula abierta" o "Valvula cerrada" segun corresponda
        if(this.abierta == true){
            return "Valvula abierta";
        } 
        else {
            return "Valvula cerrada";
        }
    }
}
```


---

==**EJERCICIO 3**==
![[ejercicio4.png|614]]

```
Se desea que cada ZonaRiego tenga un estado actual que determine qué acción debe realizar el sistema.

Para ello se utiliza la interfaz EstadoRiego y las clases EstadoNormal, EstadoSeco y EstadoRegando.

Complete los métdos indicados de clase ZonaRiego:
   - inicializar el estado en EstadoNormal
   - implementar getValvula()
   - implementar getEstado()
   - implementar setEstado(...)
   - completar actualizarEstado()
   - completar regar()

import java.util.ArrayList;
import java.util.HashSet;

public class ZonaRiego  {

    private String nombre;
    private ArrayList<SensorHumedad> sensores;
    private HashSet<Visualizador> observadores;
    private ValvulaRiego valvula;
    private EstadoRiego estado;

    public ZonaRiego(String nombre, ValvulaRiego valvula) {
        // TODO: inicializar el estado en EstadoNormal
        // TODO: Inicializar el resto de las variables de instancia 
        // nombre, valvula, sensores, etc.
        this.nombre = nombre;
        this.valvula = valvula;
        estado = getNombre().NORMAL;
        observadores = new HashSet<>();
        sensores = new ArrayList<>();
     }

//NO TOCAR ESTOS METODOS, estos serán reemplazados para el testing
//ASUMA QUE ESTAN CORRECTAMENTE IMPLEMENTADOS
    public String getNombre() {return nombre;}
    public HashSet<Visualizador> getObservadores(){ return observadores;}
    public ArrayList<SensorHumedad> getSensores(){ return sensores;}
    public double calcularHumedadPromedio() throws RiegoException{return 0.0;}
    public void agregarObservador(Visualizador observador) {}
    public void quitarObservador(Visualizador observador) {}
    public void notificarObservadores(String mensaje) {}

//Implementar a partir de aquí
    public ValvulaRiego getValvula() {
        // TODO: devolver la válvula
        
        return this.valvula;
    }

    public EstadoRiego getEstado() {
        // TODO: devolver el estado actual
        return this.estado;
    }

    public void setEstado(EstadoRiego estado) {
        // TODO: modificar el estado actual
        this.estado = estado;
    }

    public void actualizarEstado() throws RiegoException {
        // TODO: calcular la humedad promedio (utilice el método calcularHumedad)
        // TODO: si el promedio es mayor a 50, asignar EstadoNormal
        // TODO: si el promedio está entre 20 y 50 inclusive, asignar EstadoSeco
        // TODO: si el promedio es menor a 20, asignar EstadoRegando
        // TODO: ejecutar el estado actual
        double a = calcularHumedadPromedio();
        
        if(a > 50){
	        this.estado = new EstadoNormal();
        }
        else if(a > 20 && a <= 50){
	        this.estado = new EstadoSeco();
        }
        else if(a < 20){
	        this.estado = new EstadoRegando();
        }
        this.estado.ejecutar(this); 
    }

    public void regar() throws RiegoException {
        // TODO: asignar EstadoRegando
        // TODO: ejecutar el estado actual
        this.estado = new EstadoRegando();
        this.estado.ejecutar(this);
    }
}

```

---

![[ejercicio5.png]]

```
Se desea completar la clase ControladorRiego, encargada de administrar todas las zonas del sistema

public class ControladorRiego{

    //La clase posee un HashMap<String, ZonaRiego>, donde la clave es el nombre de la zona.
    private HashMap<String, ZonaRiego> zonas;

    public ControladorRiego() {
        // TODO: inicializar el mapa de zonas
        zonas = new HashMap<>();
    }

    public void agregarZona(ZonaRiego zona) {
        // TODO: agregar la zona al mapa usando su nombre como clave
        zonas.put(zona.getNombre(), zona);
    }

    /** 
     * Busca una ZonaReigo por nombre
     * lanzar una RiegoException si no existe una zona con el nombre recibido.
     */
    public ZonaRiego buscarZona(String nombre) throws RiegoException {
        // TODO: si no existe la zona, lanzar RiegoException
        // TODO: devolver la zona correspondiente
        if(!zonas.containsKey(nombre)){
	        throw new RiegoException();
        }
        return this.zonas.get(nombre);
    }

    public void regarZona(String nombre) throws RiegoException {
        // TODO: buscar la zona por nombre
        // TODO: ejecutar el método regar() de la zona
         ZonaRiego n = this.buscarZona(nombre);
         n.regar();
    }

    /**
     * El metodo ejecutarCicloControl representa un “tick” del reloj del
     * sistema: debe recorrer todas las zonas y actualizar 
     * el estado de cada una.
     */
    public void ejecutarCicloControl() throws RiegoException {
        // TODO: recorrer todas las zonas del mapa
        // TODO: ejecutar actualizarEstado() sobre cada zona
        for(ZonaRiego i : this.zonas.values()){
	        i.actualizarEstado();
        }
    }
}
```


---


![[ejercicio6.png|727]]
```
Se desea que cada ZonaRiego tenga un estado actual que determine qué acción debe realizar el sistema.

Para ello se utiliza la interfaz EstadoRiego y las clases EstadoNormal, EstadoSeco y EstadoRegando.

Implemente al clase EstadoRegando

// Codifique la clase EstadoRegando tal que implemente la interfaz EstadoRiego

// Cuando se ejecuta este estado, debe abrir la valvula ede la zona de riego
// y notificar a los observadores de la zona un mensaje:
// "La zona está REGANDO. La válvula está abierta."
// El nombre a retonrar es el valor correspondiente del enum EstadoZona.
```





```
public abstract class Componente {

    private String id;
    private String nombre;
    private boolean encendido;

    /**
     * Constructor Componente.
     * Por defecto los componentes se crean encendidos (true).
     */
    public Componente(String id, String nombre) {
        // TODO: completar inicialización de variables
        this.id = id;
        this.nombre = nombre;
        this.encendido = true;
        
    }

    public String getId() {
        // TODO: completar
        return this.id;
        
    }

    public String getNombre() {
        // TODO: completar
        return this.nombre;
        
    }

    public void setNombre(String nombre) {
        // TODO: completar
        this.nombre = nombre;
    }

    public boolean isEncendido() {
        // TODO: completar devolviendo el estado de forma limpia (sin if redundante)
        return this.encendido;
    }

    public void setEncendido(boolean encendido) {
        this.encendido = encendido;
    }

    public abstract String obtenerResumen();

    /** * Un componente se considera igual a otro si su "id" es el mismo.
     */
    @Override
    public boolean equals(Object obj) {
        // TODO: completar los 3 pasos del equals profesional (identidad, tipo y casteo/comparación)
        if(this == obj){
	        return true;
        }
        else if(obj == null || !(obj instanceof Componente)){
	        return false;
        }
	    Componente nuevo = (Componente) obj;
	    return this.id.equals(nuevo.getId());
        
    }
}
```

---


```
// TODO: Declarar la clase Ventilador heredando de Componente
public class Ventilador extends Componente {
    
    private boolean girando;

    public Ventilador(String id, String nombre) {
        // TODO: llamada al constructor padre
        // TODO: inicializar el ventilador detenido (false)
        super(id,nombre);
        this.girando = false;
        
    }

    public void arrancar() throws ClimaException {
        // TODO: si el componente está apagado (inactivo), lanzar ClimaException
        // TODO: hacer que el ventilador empiece a girar
	    if(!isEncendido){
	       throw new ClimaException(); 
        }
        this.girando = true;
    }

    public void detener() throws ClimaException {
        // TODO: hacer que el ventilador deje de girar
        this.girando = false;
    }

    public boolean isGirando() {
        // TODO: devolver si está girando de forma directa
        return this.girando;
    }

    @Override
    public String obtenerResumen() {
        // TODO: Retornar "Ventilador girando" o "Ventilador detenido" según corresponda
        if(isGirando){
	        return "Ventilador girando";
        }
        else{
	        return "Ventilador detenido";
        }
    }
}
```

---

```
import java.util.ArrayList;

public class SectorInvernadero {

    private String nombre;
    private ArrayList<SensorTemperatura> sensores;
    private Ventilador ventilador;
    private EstadoSector estado;

    public SectorInvernadero(String nombre, Ventilador ventilador) {
        // TODO: Inicializar las variables de instancia (guardar el ventilador recibido, crear lista vacía, etc.)
        // TODO: inicializar el estado instanciando la clase EstadoOptimo
        this.nombre = nombre;
        this.ventilador = ventilador;
        sensores = new ArrayList<>();
        this.estado = new EstadoOptimo();
        
    }

    // MÉTODOS DE TEST (NO TOCAR, ASUMA QUE ESTÁN IMPLEMENTADOS)
    public String getNombre() { return nombre; }
    public ArrayList<SensorTemperatura> getSensores() { return sensores; }
    public double calcularTemperaturaPromedio() throws ClimaException { return 0.0; }
    public void notificarAlerta(String mensaje) {}

    // IMPLEMENTAR A PARTIR DE AQUÍ
    public Ventilador getVentilador() {
        // TODO: devolver el ventilador
        return this.ventilador;
    }

    public EstadoSector getEstado() {
        // TODO: devolver el estado actual
        return this.estado;
        
    }

    public void setEstado(EstadoSector estado) {
        // TODO: modificar el estado actual
        this.estado = estado;
    }

    public void actualizarEstado() throws ClimaException {
        // TODO: calcular la temperatura promedio
        // TODO: evaluar rangos (¡Cuidado con la sintaxis matemática en Java!) e instanciar el chip de Estado que corresponda
        // TODO: ejecutar el estado actual pasándole este sector como parámetro
        
    }

    public void forzarVentilacion() throws ClimaException {
        // TODO: asignar EstadoVentilando y ejecutarlo
        
    }
}
```