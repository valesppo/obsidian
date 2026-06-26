
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