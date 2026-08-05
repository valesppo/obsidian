 	[[ejercicios]]

# ==GRAFOS==

**Definicion:** un grafo G=(v,e) es un par donde V es un conjundo de vertices (Nodos) y E es un conjunto de aristas.

**Grafo completo:** Son los grafos con todas las aristas posibles.
**Grafo disperso:** Son los grafos que tienen relativamente pocas aristas.
**Grafo denso:** Le falta muy pocas aristas de todas las posibles.

![[Pasted image 20260525201700.png|462]]

**Grafo NO dirigido**: Las aristas no tienen direccion (la conexion entre A y B es la misma que entre B y A). Son un par NO ordenado
**Grado de un vertice (no dirigidos)**: el numero de aristas que sale de un vertice. G(0) = 4 y G(3) = 2
![[Pasted image 20260524193128.png]]

**Grafo dirigido**: Las aristas tienen direccion (una arista de A a B no es lo mismo que de B a A)

**Arista con lazo (bucle)**: Una arista que conecta un vertice consigo misma. {C}

**Grafos simples**: No tienen lazos ni multiples aristas entre el mismo par de vertices.

**Grado de Entrada/Salida (en grafos dirigidos)**: El número de aristas que entran/salen de un vértice. inG(A)= 3 , outG(A)=1, G(A) = inG + outG = 4

**Walk:** Una secuencia de vértices donde cada vértice (excepto el último) es adyacente al siguiente en la secuencia. {D, A, B, E}

**Path (camino simple):** Walk que NO repite vertices.

**Ciclo:** Un camino que comienza y termina en el mismo vértice {A, B, E, A}

![[Pasted image 20260524193955.png]]


---

**--REPRESENTACION DE GRAFOS--**

# **LISTA DE ADYACENCIA**:

Para cada vertice, se mantiene una lista de sus vertices adyacentes. O sea que en lugar de crear una grilla gigante como lo es con la matriz de adyacencia, aca creamos solo un arreglo en donde cada posicion de esa lista es un vector o una linkedlist dinamico con los vecinos reales que tiene esa estacion.
Ademas si queres saber cuales son los vecinos de un vertice, simplemente lees la posicion de ese vertice y te devuelve los vecinos.

**Complejidad espacial:** $O(V + E)$

 * - addEdge(u, v): O(1) amortizado
 * - getNeighbors(u): O(deg(u))
 * - hasEdge(u, v): O(deg(u))
 * - removeEdge(u, v): O(deg(u))
 * Donde deg(u) es la cantidad de vecinos reales del vértice u.
**Eficiente para agrafos dispersos**

![[Pasted image 20260524205109.png|466]]

# `std::vector<std::vector<int>> lista; // Un vector que adentro tiene otros vectores`


--La gran debilidad que tiene la lista de adyacencia es que si te pregunto si **vertice1** esta conectado con **vertice2**, la computadora tiene que ir a la lista del **vertice1** y empezar a leer elemento por elemento a ver si esta el **vertice2**, lo cual es muy lento-- .

**Usá Lista de Adyacencia** para casi todo lo demás (Grafos **Dispersos**). Facebook, Google Maps, redes de trenes y los ejercicios de tu facultad usan listas de adyacencia en el 95% de los casos, porque el ahorro de memoria es brutal y los algoritmos que vamos a usar (como BFS y DFS) necesitan pedir constantemente "dame todos los vecinos de X", tarea en la que la Lista es la campeona absoluta.

[https://drive.google.com/file/d/1w_ipsD32CBZgP-zNBduLgoQ-48uBMzIY/view?usp=sharing](https://drive.google.com/file/d/135AlfTg_9Y5GDccL-qhXFWxpvublf4cC/view?usp=sharing)


---

# **MATRIZ DE ADYACENCIA:** 
Se utiliza una matriz booleana VxV donde las filas son el origen y las columnas son el destino "(vertice i,vertice j)". Si hay una via entre dos vertices, ponemos un **1** y si no existe ponemos un **0**.
Para grafos ponderados, se puede almacenar el peso en lugar de un booleano (con infinito para indicar la ausencia de arista).

**-Facil verificar la existencia de una arista entre dos vertices O(1).**
**-Requiere espacio O(V²) lo cual puede ser ineficiente para grafos dispersos**


![[Pasted image 20260525170000.png|323]]
Aca en el ejemplo de la imagen seria algo asi:
					destinos
               1 2 3 4 5
posiciones 	  1  0 1 0 0 0
			2  1 0 1 1 0
            3  0 1 0 1 0
            4  0 1 1 0 1
            5  0 0 0 1 0 
            
entonces nos fijamos en el vertice de posicion 1 y vemos si tiene una arista con el mismo vertice, como no hay arista ponemos un 0, despues en el vertice de posicion 1, nos fijamos is tiene arista con el destino del vertice 2, y como si la tiene ponemos un 1. Y asi con todo los demas.

**Usá Matriz de Adyacencia** SOLO cuando el grafo es **Denso** (casi todos los nodos están conectados con casi todos los demás). Un ejemplo técnico: las conexiones entre microchips en una placa base, o un mapa de un videojuego donde cada baldosa se conecta con sus 8 vecinas.


| **Operación / Recurso**        | **Matriz de Adyacencia**                   | **Lista de Adyacencia**                    |
| ------------------------------ | ------------------------------------------ | ------------------------------------------ |
| **Memoria Total**              | $O(V^2)$                                   | $O(V + E)$                                 |
| **Agregar Vértice nuevo**      | $O(V^2)$ (Hay que redimensionar la matriz) | $O(1)$ (Se agrega una fila vacía al final) |
| **Agregar Arista nueva**       | $O(1)$                                     | $O(1)$                                     |
| **Verificar si existe Arista** | $O(1)$                                     | $O(V)$ (En el peor caso)                   |
| **Obtener vecinos**            | Lento (Revisa todos los vértices)          | Rápido (Solo lee los reales)               |
| **Caso de uso ideal**          | Grafos Densos                              | Grafos Dispersos                           |
https://drive.google.com/file/d/1w_ipsD32CBZgP-zNBduLgoQ-48uBMzIY/view?usp=sharing

---

# Grafo BIPARTITO:
Un grafo es bipartito si V puede particionarse en dos conjuntos disjuntos X e Y tal que toda arista conecta un vertice de X con uno de Y. Nunca hay aristas X-X ni Y-Y.

![[Pasted image 20260525205953.png|418]]

**Deteccion coloring via BFS:**
1. Colorear nodo fuente: color 0.
2. Colorear todos sus vecinos: color 1.
3. Sus vecinos: color 0. Y así.
4. Si en algún paso un vecino ya tiene el MISMO color que el nodo actual → ciclo impar → NO es bipartito.

**Complejidad: O(V+E)**. Si es desconectado, repetir desde cada componente

Un grafo es bipartito SI Y SOLO SI no contiene ningún ciclo de longitud impar (ej. triángulos, pentágonos).


---

# BFS (busqueda en anchura):

El BFS se expande como cuando tirás una piedra en un charco de agua: por **ondas o anillos concéntricos**. Primero visita a sus vecinos directos (a 1 estación de distancia). Cuando termina con todos ellos, recién ahí pasa a los vecinos de sus vecinos (a 2 estaciones), y así sucesivamente.

Invariante: cuando se procesa un nodo u a distancia d, todos sus vecinos quedan encolados a distancia d+1. Nunca se procesa un nodo a distancia k sin haber procesado TODOS los de distancia k-1.

BFS encuentra el camino minimo en aristas en grafos NO PONDERADOS. para ponderados usamos dijkstra.

-- Usa **queue (FIFO)** ya que se procesan primero los nodos descubiertos antes, o sea que BFS garantiza que cuando se procesa un nodo, todos los de distancia menor ya fueron procesados.
En cambio si usaramos una PILA (LIFO) se procesarian el ultimo descubierto,y eso seria DFS.

**Tiempo O(V+E)**: Cada vertice entra y sale de la queue exactamente una vez -> O(V) y cada arista es examinada exactamente dos veces (una por cada endpoint) -> O(E).

```
#include <queue>

void bfs(int inicio) {
    std::vector<bool> visitado(numVertices, false);
    std::queue<int> cola;

    cola.push(inicio);
    visitado[inicio] = true;

    while (!cola.empty()) {
        int actual = cola.front();
        cola.pop();
        
        // Procesar nodo actual (ej. imprimirlo)
        
        for (int vecino : listaAdyacencia[actual]) {
            if (!visitado[vecino]) {
                visitado[vecino] = true;
                cola.push(vecino); // Al final de la fila
            }
        }
    }
}
```

**ESTADOS WHITE / BLACK / GRAY**

* WHITE (no descubierto): nodo que BFS aún no ha alcanzado.
* GRAY (descubierto): nodo en la cola, sus vecinos aún no han sido procesados.
* BLACK (finalizado): nodo procesado, todos sus vecinos ya fueron encolados.

Esta tripartición evita ciclos: solo encolar nodos WHITE. Un vecino GRAY o BLACK ya fue descubierto — ignorar.

BFS tree: las aristas que descubren nodos WHITE forman un árbol con raíz en source. El path en este árbol es el camino mínimo en aristas

```
Para obtener el path de src a target:
vector<int> getPath(int target, vector<int>& parent) {
  vector<int> path;
  for (int v=target; v!=-1; v=parent[v])
    path.push_back(v);
  reverse(path.begin(), path.end());
  return path;  // vacío si no alcanzable
}
```

https://drive.google.com/file/d/1mMYUAM9qcwyVQTpQPL38EdWnsH6hcbzQ/view?usp=sharing



---

# DFS (Busqueda en profundidad):

El explorador avanza por un camino mientras pueda, cuando ya no puede avanzar, retrocede exactamente por donde vino.
Nunca pierde el hilo.
 1 - Se visita un nodo
 2 - Se intenta visitar un vecino no visitado
 3 - Desde ese vecino, se intenta ir todavia mas profundo
 4 - Cuando no quedan vecinos nuevos, se finaliza el nodo y se vuelve al padre

DFS permite registrar **timestamps**:

	d[v] = tiempo de descubrimiento -> se asigna cuando v se visita por primera vez 
	f[v] = tiempo de finalizacion -> se asigna cuando ya se exploraron todos los descendientes alcanzables desde v dentro del recorrido DFS

**Estados y transiciones:** WHITE → GRAY: primer descubrimiento de v (d[v] asignado).
							 GRAY → BLACK: v y todos sus descendientes fueron explorados (f[v] asignado)

En cualquier momento durante DFS, la secuencia de nodos GRAY en la call stack forma un PATH desde la raíz actual hasta el nodo que se está procesando.

Esta propiedad es la clave de la detección de ciclos: si al explorar desde u encontramos un vecino v que ya es GRAY, hay un back edge → ciclo.

**Clasificacion de aristas:**

* Tree edge: arista (u,v) donde v es WHITE al ser descubierto desde u. Forma el DFS tree.

* Back edge: arista (u,v) donde v es GRAY (ancestro de u en la call stack). SOLO BACK EDGES indican ciclos en directed graphs.

* Forward edge: arista (u,v) donde v es BLACK y d[u] < d[v] (u fue descubierto antes que v → v es descendiente ya finalizado). Solo en directed.

* Cross edge: arista (u,v) donde v es BLACK y d[u] > d[v]. Conecta nodos en árboles distintos o ramas sin relación. Solo en directed.

* Key: en UNDIRECTED graphs, solo existen tree edges y back edges.




https://drive.google.com/file/d/1cZiSLxjrX9--IKIcmWlaNR4TYG2dGgbx/view?usp=sharing

