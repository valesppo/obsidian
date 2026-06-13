 [[GRAFOS]]
# DIJKSTRA

**Variante B: Devolver EL CAMINO (Ruta de Nodos)** Si no te piden el costo, sino el recorrido físico (Ej: `0 -> 3 -> 5`). Acá no devolvés un `int`, devolvés modificando un arreglo `int previo[]` pasado por parámetro.

- **En el Payload (Relajación):** ```cpp if (costoViaje < dist[v]) { dist[v] = costoViaje; previo[v] = actual; // "Para llegar a 'v', el paso anterior óptimo es 'actual'" }
    
- **El `return`:** Es `void`. El Coordinador luego arma el camino leyendo el arreglo `previo` de atrás para adelante.


```
El algoritmo de cálculo de costos del `MetroNocturno` ha sido aprobado. Sin embargo, en caso de un alto consumo de energía anómalo, la auditoría exige conocer **la ruta exacta** que tomó el tren de mantenimiento, y no solamente el costo final.

La red se modela como un grafo ponderado donde todos los costos de energía son positivos.

Implemente exactamente este método: `int MetroNocturno::rutaCostoMinimo(int origenId, int destinoId, int rutaEstaciones[]);`

**Debe devolver:**

- El costo energético mínimo total si existe un camino.
    
- `-1` si algún ID es inválido o si no existe ninguna ruta posible.
    
- En el caso de éxito, el arreglo `rutaEstaciones` debe ser llenado secuencialmente con los IDs de las estaciones visitadas desde el origen hasta el destino. El final de la ruta válida en el arreglo debe marcarse con un `-1`.
  
```

```
struct Tunel {
    bool existe;
    int costoEnergia;
    Tunel(bool existeTunel = false, int costo = 0);
};

class MetroNocturno {
private:
    static const int MAX_ESTACIONES = 20;
    static const int COSTO_INFINITO = 999999;
    Tunel conexiones[MAX_ESTACIONES][MAX_ESTACIONES];
    int cantidadEstaciones;

public:
    MetroNocturno();
    // Método a implementar:
    int rutaCostoMinimo(int origenId, int destinoId, int rutaEstaciones[]);
};



int MetroNocturno::rutaCostoMinimo(int origenId, int destinoId, int rutaEstaciones[]) {
    
    // 1. EARLY EXITS
    if (origenId < 0 || origenId >= cantidadEstaciones || destinoId < 0 || destinoId >= cantidadEstaciones) return -1;
    if (origenId == destinoId) {
        rutaEstaciones[0] = origenId;
        rutaEstaciones[1] = -1;
        return 0;
    }

    // 2. MEMORIA ESTÁTICA
    int dist[MAX_ESTACIONES];
    bool vis[MAX_ESTACIONES] = {false};
    int previo[MAX_ESTACIONES]; // NUEVO: Para guardar de dónde venimos

    for (int i = 0; i < cantidadEstaciones; i++) {
        dist[i] = COSTO_INFINITO;
        previo[i] = -1;
    }
    dist[origenId] = 0;

    // 3. MOTOR DIJKSTRA
    for (int i = 0; i < cantidadEstaciones; i++) {
        int minDist = COSTO_INFINITO;
        int actual = -1;

        // Buscar mínimo
        for (int v = 0; v < cantidadEstaciones; v++) {
            if (!vis[v] && dist[v] < minDist) {
                minDist = dist[v];
                actual = v;
            }
        }

        if (actual == -1 || actual == destinoId) break;
        vis[actual] = true;

        // Relajación
        for (int v = 0; v < cantidadEstaciones; v++) {
            if (conexiones[actual][v].existe && !vis[v]) {
                int costoViaje = dist[actual] + conexiones[actual][v].costoEnergia;
                if (costoViaje < dist[v]) {
                    dist[v] = costoViaje;
                    previo[v] = actual; // DEJAMOS LA MIGA DE PAN
                }
            }
        }
    }

    if (dist[destinoId] == COSTO_INFINITO) return -1; // No se encontró camino

    // 4. RECONSTRUCCIÓN DE LA RUTA (El GPS)
    int rutaInvertida[MAX_ESTACIONES];
    int cantidadPasos = 0;
    int pasoActual = destinoId;

    // Caminamos hacia atrás desde el destino
    while (pasoActual != -1) {
        rutaInvertida[cantidadPasos] = pasoActual;
        cantidadPasos++;
        pasoActual = previo[pasoActual];
    }

    // El arreglo quedó al revés (ej: 5 -> 2 -> 0). Lo volcamos al derecho en el arreglo del usuario.
    int indiceVolcado = 0;
    for (int i = cantidadPasos - 1; i >= 0; i--) {
        rutaEstaciones[indiceVolcado] = rutaInvertida[i];
        indiceVolcado++;
    }
    
    rutaEstaciones[indiceVolcado] = -1; // Marcador final obligatorio según enunciado

    return dist[destinoId];
}
```

![[Pasted image 20260603131903.png]]

# DFS

**Variante A: Booleano (Detectar si hay salida en un laberinto)** Si el DFS busca algo específico, el `return` tiene que "burbujear" hacia arriba.
```
bool dfsBuscar(int actual, int destino, bool vis[]) {
    if (actual == destino) return true; // ¡Lo encontré!
    vis[actual] = true;
    
    for (int v = 0; v < cantidad; v++) {
        if (matriz[actual][v] && !vis[v]) {
            // ERROR COMÚN: Poner solo "dfsBuscar(v, destino, vis);"
            // Si el hijo lo encuentra, el padre ignora el resultado.
            // CORRECTO: Capturar y propagar el éxito.
            if (dfsBuscar(v, destino, vis) == true) {
                return true; 
            }
        }
    }
    return false; // Revisé todos mis vecinos y ninguno encontró nada.
}
```

**Variante B: Acumular / Contar nodos** El código viaja, llega al fondo, y a medida que vuelve, va sumando.
```
int dfsContar(int actual, bool vis[]) {
    vis[actual] = true;
    int sumaLocal = 1; // Me cuento a mí mismo

    for (int v = 0; v < cantidad; v++) {
        if (matriz[actual][v] && !vis[v]) {
            // El resultado del hijo se acumula en mi variable
            sumaLocal = sumaLocal + dfsContar(v, vis);
        }
    }
    return sumaLocal; // Le devuelvo la caja con la suma total a mi padre
}
```


---

# GRAFOS BIPARTITOS

```

El centro de datos principal ha sufrido una brecha de seguridad. Para contener el daño, el equipo de infraestructura necesita dividir todos los servidores operativos en exactamente dos subredes virtuales (VLAN 0 y VLAN 1). Por protocolo estricto, si existe un enlace físico directo entre dos servidores, **obligatoriamente** deben ser asignados a VLANs distintas para evitar saltos de red maliciosos.

La red se modela como un grafo no ponderado:

- Cada servidor es un nodo.
    
- Cada enlace físico bidireccional es una arista.
    

Implemente exactamente este método: `bool Datacenter::esPosibleSegmentar(int servidorOrigenId);`

**Debe devolver:**

- `true` si es posible asignar todos los servidores alcanzables desde el origen a las dos VLANs respetando el protocolo.
    
- `false` si en algún momento se detecta un conflicto insalvable (dos servidores conectados físicamente en la misma VLAN).
    
- `false` si el `servidorOrigenId` es inválido o no está operativo.
  
struct Servidor {
    bool operativo;
    std::string ip;
    Servidor(bool op = false, std::string ipAddr = "");
};

class Datacenter {
private:
    static const int MAX_SERVIDORES = 50;
    Servidor servidores[MAX_SERVIDORES];
    bool enlaceFisico[MAX_SERVIDORES][MAX_SERVIDORES];
    int cantidadServidores;

public:
    Datacenter();
    // Método a implementar:
    bool esPosibleSegmentar(int servidorOrigenId);
};
```

```
	bool Datacenter::esPosibleSegmentar(int servidorOrigenId){                                                      
	// empezamos poniendo las validaciones para los iDs
	if(servidorOrigenId <0 || servidorOrigenId >= cantidadServidores) return false;
	
	// ahora inicializamos las cosas que vamos a usar (una cola y una lista con los visitados)
	int vlan[MAX_SERVIDORES];
	Queue<int> cola;
	
	for(int i=0; i<cantidadServidores; i++){
		vlan[i] = -1;
	}
	
	vlan[servidorOrigenId] = 0;
	cola.enqueue(servidorOrigenId);
	
	while(!cola.isEmpty()){
		int actual = cola.front();
		cola.dequeue();
		for(int v=0; v<cantidadServidores; v++){
			if(enlaceFisico[actual][v] && servidores[v].operativo){
				if(vlan[v] == -1){
					vlan[v] = 1 - vlan[actual];
					cola.enqueue(v);
				}
				else if(vlan[v] == vlan[actual]){
					return false;
				}
			}
		}
	}
	return true;
}
```


![[Pasted image 20260603115959.png|638]]



---

# DFS 

```
El motor narrativo de **CyberpunkCba** requiere que las misiones de la historia principal se jueguen en un orden lógico estricto. Muchas misiones son pre-requisitos absolutos de otras misiones posteriores. Para precargar los recursos del juego de manera eficiente, el motor necesita calcular un orden de ejecución válido para todas las misiones.

La historia se modela como un Grafo Dirigido Acíclico (DAG):

- Cada misión es un nodo.
    
- Una arista dirigida de A hacia B indica que A es un pre-requisito de B.
    

Implemente exactamente estos métodos:

C++

void MotorNarrativo::dfsSecuenciar(int actual, bool visitado[], int orden[], int &posicion);
void MotorNarrativo::generarOrdenMisiones(int ordenResultado[]);

**Comportamiento esperado:** El método `generarOrdenMisiones` debe inicializar las estructuras necesarias y procesar todo el grafo, dejando en el arreglo `ordenResultado` la secuencia válida de los IDs de las misiones, de tal forma que ninguna misión aparezca antes que sus pre-requisitos.

struct Mision {
    int id;
    bool activa;
    std::string nombre;
};

class MotorNarrativo {
private:
    static const int MAX_MISIONES = 30;
    Mision misiones[MAX_MISIONES];
    bool esPrerequisitoDe[MAX_MISIONES][MAX_MISIONES]; // Matriz dirigida
    int totalMisionesActivas;

    // Helper a implementar:
    void dfsSecuenciar(int actual, bool visitado[], int orden[], int &posicion);

public:
    MotorNarrativo();
    // Método a implementar:
    void generarOrdenMisiones(int ordenResultado[]);
};

```

```
struct Mision {
    int id;
    bool activa;
    std::string nombre;
};

class MotorNarrativo {
private:
    static const int MAX_MISIONES = 30;
    Mision misiones[MAX_MISIONES];
    bool esPrerequisitoDe[MAX_MISIONES][MAX_MISIONES]; // Matriz dirigida
    int totalMisionesActivas;

    // Helper a implementar:
    void dfsSecuenciar(int actual, bool visitado[], int orden[], int &posicion);

public:
    MotorNarrativo();
    // Método a implementar:
    void generarOrdenMisiones(int ordenResultado[]);
};


// --- EL ESCUADRÓN (Helper Recursivo) ---
void MotorNarrativo::dfsSecuenciar(int actual, bool visitado[], int orden[], int &posicion) {
    visitado[actual] = true;

    for (int v = 0; v < totalMisionesActivas; v++) {
        // Si 'actual' es pre-requisito de 'v', primero debemos secuenciar 'v'
        if (esPrerequisitoDe[actual][v] && misiones[v].activa && !visitado[v]) {
            dfsSecuenciar(v, visitado, orden, posicion);
        }
    }
    
    // POST-ORDEN: Ya no hay más dependencias. Anoto la misión y retrocedo el índice.
    orden[posicion] = actual;
    posicion--; 
}

// --- EL COORDINADOR (Wrapper Público) ---
void MotorNarrativo::generarOrdenMisiones(int ordenResultado[]) {
    bool visitado[MAX_MISIONES] = {false};
    
    // Arrancamos desde la última posición del arreglo
    int posicionIndice = totalMisionesActivas - 1; 

    // Revisamos todo el grafo por si hay componentes desconectadas
    for (int i = 0; i < totalMisionesActivas; i++) {
        if (misiones[i].activa && !visitado[i]) {
            dfsSecuenciar(i, visitado, ordenResultado, posicionIndice);
        }
    }
}


```