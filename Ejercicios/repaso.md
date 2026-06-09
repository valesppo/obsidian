[[GRAFOS]]


```
Una brigada debe llegar a una estación dañada atravesando únicamente estaciones habilitadas. El sistema no necesita saber la cantidad de transbordos, solo si existe al menos una ruta segura.

La red se modela como un grafo no ponderado. Una ruta es segura si todas las estaciones del camino están habilitadas.

Implemente exactamente este método:

bool RedSegura::existeRutaSegura(int origenId, int destinoId)

Debe devolver:

- `false` si algún id es inválido.
- `false` si origen o destino no están habilitados.
- `false` si no existe ruta segura.
- `true` si origen y destino son la misma estación habilitada.
- `true` si existe al menos una ruta segura.

**Contrato completo de la cola provista:**

template <typename TData>
class Queue {
public:
    Queue();

    // Agrega value al final de la cola.
    void enqueue(const TData& value);

    // Elimina el elemento del frente. Si la cola está vacía, no hace nada.
    void dequeue();

    // Devuelve una COPIA del elemento del frente.
    // Solo debe llamarse si isEmpty() == false.
    TData front() const;

    // Devuelve true si la cola no tiene elementos.
    bool isEmpty() const;

    // Devuelve la cantidad de elementos actuales.
    int size() const;
};

**Código base visible para el estudiante:**

struct EstacionSegura {
    std::string nombre;
    bool habilitada;

    EstacionSegura(std::string nombreEstacion = "",
                   bool estaHabilitada = true);
};

class RedSegura {
private:
    static const int MAX_ESTACIONES = 20;

    EstacionSegura estaciones[MAX_ESTACIONES];
    bool hayTunel[MAX_ESTACIONES][MAX_ESTACIONES];
    int cantidadEstaciones;

public:
    RedSegura();

    int agregarEstacion(std::string nombre,
                        bool habilitada = true);

    void conectarBidireccional(int origenId,
                               int destinoId);

    // Método a implementar.
    bool existeRutaSegura(int origenId,
                          int destinoId);
};

**Restricciones:**

- Use BFS con `Queue<int>`, donde cada `int` representa el id de una estación pendiente de explorar.
- No use `std::queue`.
- No use `vector`.
- No use recursión.
```

bool RedSegura::existeRutaSegura(int origenId, int destinoId){

		// hacemos las validaciones de los iDs
	if(origenId<0 || origenId>=cantidadEstaciones || destinoId<0 || destinoId>=cantidadEstaciones) return false;
	if(!estaciones[origenId].habilitada || !estaciones[destinoId].habilitada) return false;
	if(origenId == destinoId) return true;
		
		// inicializamos la lista que vamos a usasr y la cola uqe vamos a usar
	bool visitado[MAX_ESTACIONES] = {false};
	Queue<int> cola;

	visitado[origenId] = true;
	cola.enqueue(origenId);

		// ahora hacemos el motor del bfs para decidir si existe al menos un camino que cumpla las condiciones

	while(!cola.isEmpty()){
		int act = cola.front();
		cola.dequeue();
		for(int v=0; v<cantidadEstaciones ; v++){
			if(estaciones[v].habilitada && !visitado[v] && hayTunel[actual][v]){
				visitado[v] = true;
				cola.enqueue(v);
			}
		}
	}
return false;
}



---



```
Después de una caída parcial de infraestructura, el centro de control debe estimar cuántas estaciones siguen comunicadas dentro del mismo bloque operativo que una estación origen.

Una estación pertenece a la misma componente operativa si puede alcanzarse desde el origen usando túneles y pasando solo por estaciones operativas.

Implemente exactamente estos métodos:

int RedComponentes::dfsComponente(int estacionId, bool visitada[]);
int RedComponentes::tamanoComponenteOperativa(int origenId);

`tamanoComponenteOperativa` debe devolver `0` si el id es inválido o si la estación origen no está operativa.

**Código base visible para el estudiante:**

struct NodoOperativo {
    std::string nombre;
    bool operativo;

    NodoOperativo(std::string nombreEstacion = "",
                  bool estaOperativo = true);
};

class RedComponentes {
private:
    static const int MAX_ESTACIONES = 20;

    NodoOperativo estaciones[MAX_ESTACIONES];
    bool hayTunel[MAX_ESTACIONES][MAX_ESTACIONES];
    int cantidadEstaciones;

    // Helper recursivo a implementar.
    int dfsComponente(int estacionId, bool visitada[]);

public:
    RedComponentes();

    int agregarEstacion(std::string nombre,
                        bool operativo = true);

    void conectarBidireccional(int origenId,
                               int destinoId);

    // Método público a implementar.
    int tamanoComponenteOperativa(int origenId);
};

**Restricciones:**

- Use DFS recursivo.
- No use queue, stack, vector ni arreglos dinámicos.
- Use el arreglo `visitada` para evitar ciclos.
```

int RedComponentes::dfsComponente(int estacionId, bool visitada[]){

	visitada[estacionId] = true;
	int cont = 1;

	for(int v=0; v<cantidadEstaciones; v++){
		if(estaciones[v].operativo && !visitada[v] && hayTunel[estacionId][v]){
			cont = cont + dfsComponente(v, visitada);
		}
	}
return cont;
}


int RedComponentes::tamanoComponenteOperativa(int origenId){

	if(origenId<0 || origenId>=cantidadEstaciones || !estaciones[origenId].operativo) return 0;
	bool vis[MAX_ESTACIONES] = {false};
	return dfsComponente(origenId, vis);
}



```
Los túneles del sistema nocturno tienen un nivel de riesgo operativo según interferencia, seguridad y estado estructural. El centro de control necesita seleccionar la ruta de menor riesgo acumulado para mover una brigada.

La red se modela como un grafo ponderado:

- Cada estación es un nodo.
- Cada tramo bidireccional tiene un riesgo positivo.
- El riesgo de una ruta es la suma de los riesgos de sus tramos.

Como todos los riesgos son positivos, se debe usar **[Dijkstra](https://fcefyn.aulavirtual.unc.edu.ar/mod/resource/view.php?id=79284 "Dijkstra")**.

Implemente exactamente este método:

int RedRiesgo::riesgoMinimo(int origenId, int destinoId)

Debe devolver:

- `-1` si algún id es inválido.
- `-1` si no hay ruta.
- `0` si origen y destino coinciden.
- El menor riesgo acumulado en cualquier otro caso.

**Código base visible para el estudiante:**

struct TramoRiesgo {
    bool existe;
    int riesgo;

    TramoRiesgo(bool existeTramo = false,
                int riesgoTramo = 0);
};

class RedRiesgo {
private:
    static const int MAX_ESTACIONES = 20; 
    static const int RIESGO_INFINITO = 999999;

    std::string nombres[MAX_ESTACIONES];
    TramoRiesgo tramos[MAX_ESTACIONES][MAX_ESTACIONES];
    int cantidadEstaciones;

public:
    RedRiesgo();

    int agregarEstacion(std::string nombre);

    void conectarBidireccional(int origenId,
                               int destinoId,
                               int riesgo);

    // Método a implementar.
    int riesgoMinimo(int origenId,
                     int destinoId);
};

**Restricciones:**

- No use `priority_queue`.
- No use `vector`.
- Implemente [Dijkstra](https://fcefyn.aulavirtual.unc.edu.ar/mod/resource/view.php?id=79284 "Dijkstra") con arreglos locales.
- No use `INT_MAX`; use `RIESGO_INFINITO`, que está definido en la clase.
```


int RedRiesgo::riesgoMinimo(int origenId, int destinoId){

	if(origenId<0 || origenId >= cantidadEstaciones || destinoId<0 || destinoId >= cantidadEstaciones) return -1;
	if(origenId == destinoId) return 0;

	int dist[MAX_ESTACIONES];
	bool visitado[MAX_ESTACIONES] = {false};

	for(int i=0; i<cantidadEstaciones; i++){
		dist[i] = RIESGO_INFINITO;
	}
	dist[origenId] = 0;

	for(int i=0; i<cantidadEstaciones; i++){
		int actual = -1;
		int minDist = RIESGO_INFINITO;
		for(int v=0;v<cantidadEstaciones; v++){
			if(!visitado[v] && dist[v] < minDist){
				minDist = dist[v];
				actual = v;
			}
		} 
		if(actual == -1 || actual == destinoId) break;
		visitado[actual] = true;

		for(int vecino=0; vecino<cantidadEstaciones; vecino++){
			if(tramos[actual][vecino].existe && !visitado[vecino]){
				int costo = dist[actual] + tramos[actual][vecino].riesgo;
				if(costo<dist[vecino]){
						dist[vecino] = costo;
				}
			}
		}
	}
if(dist[destinoId] == RIESGO_INFINITO) return -1;
return dist[destinoId];
}