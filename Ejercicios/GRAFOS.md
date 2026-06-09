[[ejercicios]]

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

```
bool RedSegura::existeRutaSegura(int origenId, int destinoId) {
    if(origenId < 0 || origenId >= cantidadEstaciones || destinoId < 0 || destinoId >= cantidadEstaciones) return false;
    
    if(!estaciones[origenId].habilitada || !estaciones[destinoId].habilitada) return false;
    
    if (origenId == destinoId) return true;
    
    bool visitado[MAX_ESTACIONES] = {false};
    Queue<int> cola;
    
    visitado[origenId] = true;//
    cola.enqueue(origenId);
    
    while(!cola.isEmpty()){
        int actual = cola.front();
        cola.dequeue();
        
        if(actual == destinoId) return true;
        
        for(int vecino = 0; vecino<cantidadEstaciones; vecino++){
            if(hayTunel[actual][vecino] && !visitado[vecino] && estaciones[vecino].habilitada){
                visitado[vecino] = true;
                cola.enqueue(vecino);
            }
        }
        
    }
    
    return false;
}
```


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

```
int RedComponentes::dfsComponente(int estacionId, bool visitada[]) {
    visitada[estacionId] = true;
    int cont = 1 ;
    
    for(int vecino=0; vecino<cantidadEstaciones; vecino++){
        if(hayTunel[estacionId][vecino] && !visitada[vecino] && estaciones[vecino].operativo){
            cont = cont+ dfsComponente(vecino, visitada);
            
        }
    }
    
    return cont;
}

int RedComponentes::tamanoComponenteOperativa(int origenId) {
    if(origenId<0 || origenId>=cantidadEstaciones) return 0;
    if(!estaciones[origenId].operativo) return 0;
    
    bool visitada[MAX_ESTACIONES] = {false};
    
    return dfsComponente(origenId, visitada); 

}
```

---


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

```
int RedRiesgo::riesgoMinimo(int origenId, int destinoId) {
    // 1. Validaciones Iniciales (Early Exits)
    if (origenId < 0 || origenId >= cantidadEstaciones || 
        destinoId < 0 || destinoId >= cantidadEstaciones) {
        return -1;
    }

    if (origenId == destinoId) {
        return 0;
    }

    // 2. Estructuras de Memoria (Restricción: Arreglos locales, no std::vector)
    int dist[MAX_ESTACIONES];
    bool visitado[MAX_ESTACIONES];

    // Inicialización al estilo C clásico
    for (int i = 0; i < MAX_ESTACIONES; ++i) {
        dist[i] = RIESGO_INFINITO; // Restricción: Usar constante interna
        visitado[i] = false;
    }

    // Caso base: El riesgo de estar donde ya estoy es 0
    dist[origenId] = 0;

    // 3. Motor Dijkstra Original O(V^2)
    // Damos tantas vueltas como vértices haya
    for (int i = 0; i < cantidadEstaciones; ++i) {
        
        // --- PAYLOAD A: EL REEMPLAZO DE LA PRIORITY QUEUE ---
        // Buscamos a mano el nodo no visitado que tenga el menor riesgo acumulado
        int minDist = RIESGO_INFINITO;
        int nodoActual = -1;

        for (int v = 0; v < cantidadEstaciones; ++v) {
            if (!visitado[v] && dist[v] < minDist) {
                minDist = dist[v];
                nodoActual = v;
            }
        }

        // Si nodoActual sigue siendo -1, significa que todos los nodos restantes 
        // son inalcanzables (su distancia es RIESGO_INFINITO). Cortamos para ahorrar CPU.
        if (nodoActual == -1) {
            break; 
        }

        // Si ya llegamos al destino, no tiene sentido seguir calculando rutas a otros lados.
        if (nodoActual == destinoId) {
            break;
        }

        // Consolidamos el nodo (ya encontramos el riesgo mínimo inamovible hacia él)
        visitado[nodoActual] = true;

        // --- PAYLOAD B: RELAJACIÓN DE LAS ARISTAS ---
        // Revisamos a sus vecinos directos por la matriz de adyacencia
        for (int vecino = 0; vecino < cantidadEstaciones; ++vecino) {
            
            // Si hay vía de conexión y todavía no cerramos a ese vecino
            if (tramos[nodoActual][vecino].existe && !visitado[vecino]) {
                
                int costoRuta = dist[nodoActual] + tramos[nodoActual][vecino].riesgo;
                
                // ¿Descubrimos un camino de menor riesgo? ¡Actualizamos!
                if (costoRuta < dist[vecino]) {
                    dist[vecino] = costoRuta;
                }
            }
        }
    }

    // 4. Retorno de Resultados
    if (dist[destinoId] == RIESGO_INFINITO) {
        return -1; // No hay ruta
    }

    return dist[destinoId];
}

```


---


```
Durante la madrugada, algunos túneles consumen distinta cantidad de energía por interferencias electromagnéticas. Antes de enviar un tren de mantenimiento, el centro de control debe calcular el costo energético mínimo entre dos estaciones.

La red se modela como un grafo ponderado:

- Cada estación es un nodo.
- Cada túnel bidireccional tiene un costo energético positivo.
- El costo de una ruta es la suma de los costos de sus túneles.

Como todos los costos son positivos, la estrategia correcta es **[Dijkstra](https://fcefyn.aulavirtual.unc.edu.ar/mod/resource/view.php?id=79284 "Dijkstra")**.

Implemente exactamente este método:

int MetroNocturno::costoMinimoEnergia(int origenId, int destinoId)

Debe devolver:

- `-1` si algún id es inválido.
- `-1` si no existe ruta.
- `0` si origen y destino coinciden.
- El costo energético mínimo en cualquier otro caso.

**Código base visible para el estudiante:**

struct Tunel {
    bool existe;
    int costoEnergia;

    Tunel(bool existeTunel = false,
          int costo = 0);
};

class MetroNocturno {
private:
    static const int MAX_ESTACIONES = 20;
    static const int COSTO_INFINITO = 999999;

    std::string nombres[MAX_ESTACIONES];
    Tunel conexiones[MAX_ESTACIONES][MAX_ESTACIONES];
    int cantidadEstaciones;

public:
    MetroNocturno();

    int agregarEstacion(std::string nombre);

    void conectarBidireccional(int origenId,
                               int destinoId,
                               int costoEnergia);

    // Método a implementar.
    int costoMinimoEnergia(int origenId,
                           int destinoId);
};

**Restricciones:**

- No use `priority_queue`.
- No use `vector`.
- Implemente [Dijkstra](https://fcefyn.aulavirtual.unc.edu.ar/mod/resource/view.php?id=79284 "Dijkstra") con arreglos locales.
- No use `INT_MAX`; use `COSTO_INFINITO`, que está definido en la clase.
```

```
int MetroNocturno::costoMinimoEnergia(int origenId, int destinoId) {
    
    if(origenId<0 || origenId>=cantidadEstaciones || destinoId<0 || destinoId>=cantidadEstaciones) return -1;
    if(origenId == destinoId) return 0;
    
    int distancia[MAX_ESTACIONES];
    bool visitado[MAX_ESTACIONES] = {false};
    
    for(int i=0; i<cantidadEstaciones; i++){
        distancia[i] = COSTO_INFINITO;
    }
    
    distancia[origenId] = 0;
    
    for(int v=0; v<cantidadEstaciones; v++){
        
        int minDist = COSTO_INFINITO;
        int actual = -1;
        
        for(int vecino1=0;vecino1<cantidadEstaciones; vecino1++){
            if(!visitado[vecino1] && distancia[vecino1]<minDist){
                minDist = distancia[vecino1] ;
                actual = vecino1;
            }
        }
        if(actual ==-1) break;
        if(actual == destinoId) break;
    
    
        visitado[actual] = true;
    
        for(int vecino = 0; vecino<cantidadEstaciones; vecino++){
            if(conexiones[actual][vecino].existe && !visitado[vecino] ){
                int costo = distancia[actual] + conexiones[actual][vecino].costoEnergia;
                if(costo < distancia[vecino]){
                    distancia[vecino] = costo;
                    }
                }
            }
    }    
    if(distancia[destinoId] == COSTO_INFINITO){
        return -1;
    }
    return distancia[destinoId];
}
```


---

```
El sistema de observabilidad registra estaciones críticas en un árbol AVL ordenado por `EstacionCritica.id`. Para auditar el estado del balanceo, se necesita calcular el factor de balance de una estación específica.

El factor de balance se define como:

altura(subárbol izquierdo) - altura(subárbol derecho)

En este ejercicio, la altura de un árbol vacío es `0` y la altura de una hoja es `1`.

Implemente exactamente estos métodos:

int MonitorAVL::alturaDe(NodoAVL* nodo);
int MonitorAVL::factorBalanceDe(int estacionId);

`factorBalanceDe` debe devolver `999` si la estación no existe.

**Código base visible para el estudiante:**

struct EstacionCritica {
    int id;
    std::string nombre;

    EstacionCritica(int idEstacion = -1,
                    std::string nombreEstacion = "");
};

class NodoAVL {
public:
    EstacionCritica dato;
    NodoAVL* izquierdo;
    NodoAVL* derecho;

    explicit NodoAVL(EstacionCritica estacion);
};

class MonitorAVL {
private:
    NodoAVL* raiz;

public:
    MonitorAVL();
    void insertarParaTest(EstacionCritica estacion);

    // Métodos a implementar.
    int alturaDe(NodoAVL* nodo);
    int factorBalanceDe(int estacionId);
};

**Restricciones:**

- Use recursión para calcular altura.
- Use la propiedad de búsqueda por id para encontrar la estación.
- No use estructuras auxiliares.
```

```
int MonitorAVL::alturaDe(NodoAVL* nodo) {
    if(nodo == nullptr) return 0;
    int h_izq = alturaDe(nodo->izquierdo);
    int h_der = alturaDe(nodo->derecho);

    return 1+std::max(h_izq,h_der);
}

int MonitorAVL::factorBalanceDe(int estacionId) {
    NodoAVL* nd = raiz;
    while(nd !=nullptr && nd->dato.id != estacionId){
        if(estacionId < nd->dato.id){
            nd = nd->izquierdo;       qqq
        }
        else{
            nd = nd->derecho;
        }
    }
    if(nd == nullptr) return 999;
    return alturaDe(nd->izquierdo) - alturaDe(nd->derecho);
}
```


---


```
El despacho nocturno guarda servicios programados en un BST ordenado por `ServicioTren.minutoSalida`. Para estimar carga operativa, se necesita contar cuántos servicios salen dentro de una ventana horaria.

Implemente exactamente estos métodos:

int AgendaServicios::contarEnVentanaRec(NodoServicio* actual,
                                        int minutoDesde,
                                        int minutoHasta);

int AgendaServicios::cantidadServiciosEnVentana(int minutoDesde,
                                                int minutoHasta);

La ventana es inclusiva: un servicio cuenta si:

minutoDesde <= minutoSalida <= minutoHasta

Si la ventana es inválida (`minutoDesde > minutoHasta`), debe devolver `0`.

**Código base visible para el estudiante:**

struct ServicioTren {
    int codigoTren;
    int minutoSalida;
    std::string anden;

    ServicioTren(int codigo = -1,
                 int minuto = 0,
                 std::string nombreAnden = "");
};

class NodoServicio {
public:
    ServicioTren dato;
    NodoServicio* izquierdo;
    NodoServicio* derecho;

    explicit NodoServicio(ServicioTren servicio);
};

class AgendaServicios {
private:
    NodoServicio* raiz;

    // Helper recursivo a implementar.
    int contarEnVentanaRec(NodoServicio* actual,
                           int minutoDesde,
                           int minutoHasta);

public:
    AgendaServicios();
    void insertar(ServicioTren servicio);

    // Método público a implementar.
    int cantidadServiciosEnVentana(int minutoDesde,
                                   int minutoHasta);
};

**Restricciones:**

- Use recursividad.
- Use la propiedad del BST para podar ramas que no pueden aportar resultados.
- No use vector, arreglos auxiliares ni estructuras STL.
```

```
int AgendaServicios::contarEnVentanaRec(NodoServicio* actual,
                                           int minutoDesde,
                                           int minutoHasta) {
    if(actual == nullptr) return 0;
    
    if(actual->dato.minutoSalida < minutoDesde){
      return  contarEnVentanaRec(actual->derecho, minutoDesde, minutoHasta);
    }
    if(actual->dato.minutoSalida > minutoHasta){
        return  contarEnVentanaRec(actual->izquierdo, minutoDesde, minutoHasta);
    }
    
    return 1 + contarEnVentanaRec(actual->derecho, minutoDesde, minutoHasta) + 
        contarEnVentanaRec(actual->izquierdo, minutoDesde, minutoHasta);
}

int AgendaServicios::cantidadServiciosEnVentana(int minutoDesde,
                                                int minutoHasta) {
    if(minutoDesde>minutoHasta){
        return 0;
    }
    
    NodoServicio* nodo = raiz;
    
    return contarEnVentanaRec(nodo,minutoDesde,minutoHasta);
}
```


