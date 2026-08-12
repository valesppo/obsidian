## Parte 1: Ejercicios de Codificación Práctica en C++

### Ejercicio 1: Árboles Binarios de Búsqueda (BST) — Suma de Rango

En aplicaciones de bases de datos avanzadas, es muy común realizar "búsquedas por rango", como obtener la suma de todas las ventas realizadas entre dos fechas o identificar saldos dentro de un margen específico.

**Consigna:**

Implementa la función miembro privada `int sumaRango(nodo<T>* aux, int L, int R)`.

Esta función debe recorrer el árbol y retornar **la suma de todos los valores de los nodos que se encuentren estrictamente dentro del rango $[L, R]$** (es decir, $L \le \text{info} \le R$).

**Restricciones de Optimización:**

No debes recorrer todo el árbol a ciegas. Debes **aprovechar la propiedad fundamental de los Árboles Binarios de Búsqueda (BST)** para podar ramas innecesarias: si el nodo actual es menor que $L$, es imposible que en su subárbol izquierdo existan valores válidos. De manera similar, pooda el subárbol derecho si el nodo supera a $R$.

```
#include <iostream>
using namespace std;

template <class T> class nodo {
public:
    T info;
    nodo* der, * izq;
};

template <class T> class arbol {
private:
    nodo<T>* raiz;
    void ArbolBusq(T x, nodo<T>*& nuevo);
    int sumaRango(nodo<T>* aux, int L, int R); // -> FUNCIÓN A IMPLEMENTAR

public:
    arbol() { raiz = NULL; };
    ~arbol() {};
    void CreaArbolBus(T x);
    int SumaRango(int L, int R) { return sumaRango(raiz, L, R); }
};

// IMPLEMENTA LA SOLUCIÓN AQUÍ -----------------------------------------
template <class T> 
int arbol<T>::sumaRango(nodo<T>* aux, int L, int R) {
    // Escribe tu código aquí
    if(aux == nullptr) return 0;
    int suma = 0;
    if(aux->info >= L && aux->info <= R){
		suma = aux->info;   
    }
    if(aux->info >= L){
	    suma = suma + sumaRango(aux->izq, L ,R);
    }
	 if(aux->info <= R){
	    suma = suma + sumaRango(aux->der, L ,R);
    }   
    return suma;
    
}
```


---

### Ejercicio 2: Grafos Dirigidos y Matriz de Adyacencia — El "Influencer Destacado"

En el análisis de redes sociales, un "Influencer Destacado" (teóricamente conocido como _Sumidero Universal_ o _Universal Sink_) es una persona a la que **todos los demás usuarios de la red siguen**, pero que **ella no sigue a nadie** (su lista de seguidos está vacía).

**Consigna:**

Implementa la función miembro `int encontrarInfluencer()`.

La función debe analizar la matriz de adyacencia del grafo dirigido y retornar el índice (ID) del usuario que cumple exactamente con la definición de "Influencer Destacado". Si no existe tal usuario en la red, debe retornar `-1`.

**Restricciones:**

- Recuerda que en este grafo, `matrizAdj[u][v] == 1` significa que el usuario $u$ sigue al usuario $v$.
    
- Un Influencer con ID $k$ debe tener `matrizAdj[k][j] == 0` para todo $j$, y `matrizAdj[i][k] == 1` para todo usuario $i \neq k$.
    
- **Reto de eficiencia:** ¿Puedes resolverlo en complejidad temporal **$O(V)$** sin verificar toda la matriz $O(V^2)$? (Pista: Un método elegante es descartar candidatos por pares o simular dos punteros). Si lo haces en $O(V^2)$ también será válido pero sumará menos puntaje en optimización.

```c++
#include <iostream>
#include <vector>

using namespace std;

class Grafo {
private:
    int V;                  // Número de vértices
    vector<vector<int>> matrizAdj; // Matriz de adyacencia

public:
    Grafo(int vertices) {
        V = vertices;
        matrizAdj.assign(vertices, vector<int>(vertices, 0));
    }

    void agregarArista(int u, int v) {
        matrizAdj[u][v] = 1; // Grafo DIRIGIDO (u sigue a v)
    }

    // Función miembro a probar
    int encontrarInfluencer();
};

// IMPLEMENTA LA SOLUCIÓN AQUÍ -----------------------------------------
int Grafo::encontrarInfluencer() {
    int candidato = 0;

    // Paso 1: Encontrar candidato en O(V)
    for (int i = 1; i < V; ++i) {
        if (matrizAdj[candidato][i] == 1) {
            // El candidato sigue a i, entonces el candidato ya no sirve
            candidato = i; 
        }
    }

    // Paso 2: Verificar que el candidato realmente sea influencer
    for (int i = 0; i < V; ++i) {
        if (i != candidato) {
            // Debe cumplirse: 
            // 1. El candidato no debe seguir a nadie (matrizAdj[candidato][i] == 0)
            // 2. Todos deben seguir al candidato (matrizAdj[i][candidato] == 1)
            if (matrizAdj[candidato][i] == 1 || matrizAdj[i][candidato] == 0) {
                return -1; // No es influencer
            }
        }
    }
    return candidato;
}
```


```
#include <iostream>
using namespace std;

template <class T> struct Node { T data; Node* next; };

template <class T> class PilaLimitada {
private:
    Node<T>* head;
    int size;
    int MAX;
public:
    PilaLimitada(int m) : head(nullptr), size(0), MAX(m) {}

    void push(T x) {
    if (size == MAX) {
        if (head == nullptr) return; // Por seguridad
        if (head->next == nullptr) { // Solo hay 1 elemento
            delete head; head = nullptr;
        } else {
            Node<T>* temp = head;
            while (temp->next->next != nullptr) temp = temp->next;
            delete temp->next;
            temp->next = nullptr;
        }
        size--; // Importante actualizar el contador
    }
    // Lógica de inserción al inicio (esto estaba bien)
    Node<T>* nuevo = new Node<T>{x, head}; 
    head = nuevo;
    size++;
}
    }
};
```

```
// Usa la clase nodo que ya conoces
template <class T> class arbol {
private:
    nodo<T>* raiz;
    int altura(nodo<T>* aux){
	   if(aux == nullptr) return 0;
		int hi = altura(aux->izq);
		int hd = altura(aux->der);
		return 1 + max(hi,hd);   
    } 

public:
    int diferenciaAltura() {
        // TU CÓDIGO AQUÍ:
        // 1. Calcular altura de la rama izquierda.
        // 2. Calcular altura de la rama derecha.
        // 3. Retornar la diferencia absoluta (abs).
	    int hi = altura(raiz->izquierda);
	    int hd = altura(raiz->derecha);
	    
	    return abs(hd - hi);
    }
};
```


```
bool Grafo::hayCaminoCorto(int u, int v) {
    // TU CÓDIGO AQUÍ:
    // Pista: Un camino de máximo 2 saltos existe si:
    // 1. u y v son adyacentes (u->v es 1).
    // 2. O existe un nodo 'w' tal que u->w y w->v.
    // ¡Recuerda que con la matriz es muy fácil verificarlo!
    
    if(matrizAdj[u][v] == 1){
	    return true;
    }
    for(int i=0; i<V; i++){
	    if(matrizAdj[u][i] == 1 && matrizAdj[i][v] == 1){
		    return true;
	    }
    }
    
    return false;
}
```



```
bool Grafo::esConexo(){
	int n = adj.size();
	if(n == 0) return true;
	
	vector<bool> visitados {n,false};
	queue<int> cola;
	
	cola.push(0);
	visitados[0] = true;
	int nodosVis = 1;
	
	while(!cola.empty()){
		int a = cola.front();
		cola.pop();
		for(int vecino : adj[a])
			if(!visitados[vecino]){
				visitados[vecino] = true;
				cola.push(vecino);
				nodosVis++:
			}
	}
	return nodosVis == n;

}
```


```
bool esEspejo(nodo<T>* nodo1, nodo<T>* nodo2){

	if(nodo1 == nullptr && nodo2 == nullptr){
		return true;
	}
	if(nodo1 == nullptr || nodo2 == nullptr){
		return falses;
	}
	
	return (nodo1->dato == nodo2->dato) && esEspejo(nodo1->izq, nodo2->der) && esEspejo(nodo1->der, nodo2->izq);


}
```


```
#include <iostream>
#include <vector>
using namespace std;

class GrafoDirigido {
private:
    int V;
    vector<vector<int>> adj; // Lista de adyacencia 

public:
    GrafoDirigido(int vertices) : V(vertices) {
        adj.resize(V);
    }

    void agregarAristaDirigida(int u, int v) {
        adj[u].push_back(v); // u apunta a v (u -> v) [cite: 8]
    }

    int calcularGradoEntrada(int v) {
        if (v < 0 || v >= V) return -1; // Validación de seguridad

        int gradoEntrada = 0;

        // Recorremos todos los vértices del grafo
        for (int i = 0; i < V; i++) {
            // Por cada vértice i, revisamos su lista de adyacentes
            for (int vecino : adj[i]) {
                if (vecino == v) {
                    gradoEntrada++; // Encontramos una arista que apunta hacia v
                }
            }
        }

        return gradoEntrada;
    }
};
```

```
struct Node{

int v;

Node* next;

Node(int x) : v(x) , next(nullptr){

}

};

  

class Robot{

public:

Robot(std::queue<int> entrada, int u) : colaEntrada(entrada) , umbral(u){}

int procesarFlujo();

private:

std::queue<int> colaEntrada;

std::stack<int> pilaUrgentes;

int umbral;

};

  

int Robot::procesarFlujo(){

std::queue<int> copia = colaEntrada;

while(!copia.empty()){

int x = copia.front();

if(x>=umbral){

pilaUrgentes.push(x);

}

copia.pop();

}

if(pilaUrgentes.empty()) return -1;

  

Node* head = nullptr;

Node* actual = nullptr;

int valorMax = pilaUrgentes.top();

  

while(!pilaUrgentes.empty()){

int valor = pilaUrgentes.top();

pilaUrgentes.pop();

if(valor > valorMax){

valorMax = valor;

}

Node* nuevo = new Node(valor);

  

if(head == nullptr){

head = nuevo;

actual = head;

}

else{

actual->next = nuevo;

actual = nuevo;

}

}

return valorMax;

}
```


```
struct NodeABB{

int v;

NodeABB* left;

NodeABB* right;

NodeABB(int x) : v(x) , left(nullptr), right(nullptr){}

};

  

class ABB{

public:

ABB() : root(nullptr){}

void insertar(int x);

bool esBalanceado();

private:

NodeABB* root;

int altura(NodeABB* t);

bool check(NodeABB* t);

};

  
  

int ABB::altura(NodeABB* t){

if(t == nullptr){

return 0;

}

  

int hi = altura(t->left);

int hd = altura(t->right);

  

return 1 + std::max(hi,hd);

}

  

bool ABB::check(NodeABB* t){

if(t==nullptr){

return true;

}

int altIzq = altura(t->left);

int altDer = altura(t->right);

  

if(std::abs(altIzq - altDer) > 2){

return false;

}

  

return check(t->left) && check(t->right);

  

}

bool ABB::esBalanceado(){

return check(root);

}
```


```
class Grafo{

public:

Grafo(int n) : N(n), adj(n){}

void agregarArista(int u, int v){

adj[u].push_bakc(v);

adj[v].push_bakc(u);

}

bool esConexo();

bool tieneCiclo();

private:

int N;

std::vector<int> adj;

bool dfsCiclo(int u, int padre, std::vector<int>& vis)

};

  
  

bool Grafo::esConexo(){

if(N == 0){

return true;

}

std::queue<int> cola;

std::vector<bool> visitados (N,false);

  

cola.push(0);

visitados[0] = true;

int nodosVis = 1;

  

while(!cola.empty()){

int actual = cola.front();

cola.pop();

for(int vecino : adj[actual]){

if(!visitados[vecino]){

visitados[vecino] = true;

cola.push(vecino);

nodosVis++;

}

}

}

  

return nodosVis == N;

}

  
  

bool Grafo::dfsCiclo(int u, int padre, std::vector<int>& vis){

vis[u] = 1;

  

for(int vecino : adj[u]){

if(!vis[vecino]){

if(dfsCiclo(vecino, u, vis)){

return true;

}

}

else if(vecino!=padre){

return true;

}

}

return false;

}

  

bool Grafo::tieneCiclo(){

if (N == 0) return false;

std::vector<int> vis(N,0);

for(int i =0; i<N;i++){

if(!vis[i]){

if(dfsCiclo(i,-1,vis)){

return true;

}

}

}

return false;

}
```



```
struct Node {
    int v;
    Node* next;
    Node(int x) : v(x), next(NULL) {}
};

class Procesador {
public:
    Procesador(std::queue<int> e) : entrada(e) {}
    int procesar(); // Implementar
private:
    std::queue<int> entrada;
};



int Procesador::procesar(){
        std::queue<int> copia = entrada;
        std::stack<int> pilaPar;
        while(!copia.empty()){
            int v = copia.front();
            if(v%2 == 0){
                pilaPar.push(v);
                }
            copia.pop();
            }
        if(pilaPar.empty()) return 0;
        
        Node* head = nullptr;
        Node* actual = nullptr;
        int cont = 0;
        
        while(!pilaPar.empty()){
            int valor = pilaPar.top();
            pilaPar.pop();
            cont++;
            Node* nuevo = new Node(valor);
            if(head == nullptr){
                head = nuevo;
                actual = head;
                }
            else{
                actual->next = nuevo;
                actual = nuevo;
                }    
            
            }
            
        Node* temp = head;
        while(temp != nullptr){
            Node* borrar = temp;
            temp = temp->next;
            delete borrar;
            }
    return cont;
 }
```



```
template <class T>
int arbol<T>::nivelDeNodo(nodo<T>* aux, T x, int nivel)
{
    // Escribir la solución aquí
    // Tip: aprovechá que es un ABB para ir solo hacia un lado.
    if(aux == nullptr) return -1;
    if(aux->info == x) return nivel;
    
    if(x > aux->info){
        nivel++;
        return nivelDeNodo(aux->der, x, nivel);
        }
    else{
        nivel++;
       return nivelDeNodo(aux->izq, x, nivel);
        } 
 
}
```


```
void ListaFotogramas::invertir()
{
    // Restricción 4: Si la lista tiene 0 o 1 nodos, no hace nada
    if (head == nullptr || head->next == nullptr) {
        return;
    }

    Nodo* prev = nullptr;
    Nodo* curr = head;
    Nodo* siguiente = nullptr;

    while (curr != nullptr) {
        // 1. Guardar temporalmente el siguiente nodo
        siguiente = curr->next;
        
        // 2. Invertir el puntero del nodo actual hacia atrás
        curr->next = prev;
        
        // 3. Avanzar los punteros 'prev' y 'curr' un paso hacia adelante
        prev = curr;
        curr = siguiente;
    }

    // 4. Restricción 3: Actualizar el head al nuevo primer elemento (que ahora es el último original)
    head = prev;
}
```

```
string SistemaGuardia::atenderProximo()
{
    // Escribir la solución aquí
    // Recordá: queue usa front() para ver el primero y pop() para sacarlo.

    
    if(!colaUrgentes.empty() ){
        std::string a = colaUrgentes.front();
        colaUrgentes.pop();
        return a;
        }
    if(!colaRegulares.empty()){
        std::string b = colaRegulares.front();
        colaRegulares.pop();
        return b;
        }
 
 return "Sala vacia";
 
}
```


```
// SOLUCIÓN AL PROBLEMA — dfsDesde ----------------------------------------
void Red::dfsDesde(int v)
{
    // 1. Marcar el nodo actual como visitado
    visitado[v] = true;
    
    // 2. Recorrer todos los vecinos adyacentes del nodo v
    for (int vecino : adj[v]) {
        // 3. Si el vecino no ha sido visitado, profundizar recursivamente
        if (!visitado[vecino]) {
            dfsDesde(vecino);
        }
    }
}

// SOLUCIÓN AL PROBLEMA — contarComponentes --------------------------------
int Red::contarComponentes()
{
    int componentes = 0;
    
    // Asegurarnos de limpiar el vector de visitados por seguridad
    fill(visitado.begin(), visitado.end(), false);
    
    // Recorrer todos los vértices del grafo
    for (int i = 0; i < V; i++) {
        // Si encontramos un nodo no visitado, descubrimos una nueva componente
        if (!visitado[i]) {
            dfsDesde(i); // El DFS marcará toda la isla conectada a este nodo
            componentes++; // Incrementamos el contador de componentes
        }
    }
    
    return componentes;
}
```