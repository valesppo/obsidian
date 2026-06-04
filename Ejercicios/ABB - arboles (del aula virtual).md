[[ejercicios]]


```
template <class T>
class Nodo {
public:
    T dato;
    Nodo* izq;
    Nodo* der;
    explicit Nodo(const T& x) : dato(x), izq(nullptr), der(nullptr) {}
};

template <class T>
class ABB {
private:
    Nodo<T>* raiz = nullptr;

    // ---- Helpers ----
    int alturaRec(const Nodo<T>* p) const;
    int tamRec(const Nodo<T>* p) const;
    bool containsRec(const Nodo<T>* p, const T& x) const;
    const Nodo<T>* minNode(const Nodo<T>* p) const;
    const Nodo<T>* maxNode(const Nodo<T>* p) const;
    Nodo<T>* eraseRec(Nodo<T>* p, const T& x);
    void inordenRec(const Nodo<T>* p, std::vector<T>& out) const;
    void clearRec(Nodo<T>* p);
    Nodo<T>* insertarRec(Nodo<T>* p, const T& x);

public:
    ABB() = default;
    ~ABB(){ clear(); }

    bool isEmpty() const ;
    void clear();
    bool contains(const T& x);
    void erase(const T& x);
    const T& min() const;
    const T& max() const;
    int height() const;
    int size()   const;
    std::vector<T> inorden() const;
    void insert(const T& x);
};
`````


==Implementá la inserción en un Árbol Binario de Búsqueda (ABB) usando punteros crudos (`Nodo*`). Completá el helper recursivo `insertarRec(Nodo* p, const T& x)` y el wrapper público `insert(const T& x)`. Debés mantener el invariante del ABB: los menores a la izquierda, los mayores a la derecha. **No** insertes duplicados (si `x == p->dato`, no hagas nada). Al finalizar, el árbol debe contener todos los elementos insertados y su recorrido inorden debe quedar ordenado.==

**HELPER**: es una función, clase o componente diseñado para facilitar tareas comunes, tediosas o repetitivas sin formar parte de la lógica de negocio principal de la aplicación

**WRAPPER**: estructura de código, clase o función que **envuelve otro componente** para simplificar su uso, adaptar su interfaz o extender su funcionalidad sin modificar el código original

```
// --- HELPER PRIVADO RECURSIVO ---
template <class T>
Nodo<T>* ABB<T>::insertarRec(Nodo<T>* p, const T& x) {
    // 1. Caso base: encontramos un hueco vacío.
    // Creamos el nodo y devolvemos su dirección de memoria.
    if (p == nullptr) {
        return new Nodo<T>(x);
    }

    // 2. Si el valor es menor, delegamos la inserción hacia la izquierda.
    // CLAVE: Enganchamos el resultado al puntero izq del nodo actual.
    if (x < p->dato) {
        p->izq = insertarRec(p->izq, x);
    } 
    // 3. Si el valor es mayor, delegamos hacia la derecha.
    else if (p->dato < x) { 
        p->der = insertarRec(p->der, x);
    }
    
    // 4. Si x == p->dato, no entramos a ningún 'if' (no hacemos nada, evitamos duplicados).

    // 5. Devolvemos el nodo actual para reconstruir los enlaces al volver de la recursión.
    return p;
}

// --- WRAPPER PÚBLICO ---
template <class T>
void ABB<T>::insert(const T& x) {
    // Iniciamos la recursión pasándole la raíz actual y el valor.
    // El resultado pisa la raíz (vital por si el árbol estaba vacío).
    raiz = insertarRec(raiz, x);
}
```


==Implementá el cálculo del **tamaño** del árbol. Completá el helper `tamRec(const Nodo* p)` que devuelva la cantidad de nodos del subárbol con raíz en `p`, y el método público `size()` que retorne el tamaño total del ABB. La solución debe ser recursiva: caso base `p == nullptr` devuelve `0`; caso recursivo devuelve `1 + tamaño(izq) + tamaño(der)`.==


```
template <class T>
int ABB<T>::tamRec(const Nodo<T>* p) const {
    if(p == nullptr) return 0;
    return 1 + tamRec(p->izq) + tamRec(p->der);
}

template <class T>
int ABB<T>::size() const {
    return tamRec(raiz);
}
```


==Implementá la obtención de **mínimo** y **máximo** del ABB. Completá los helpers `minNode(const Nodo* p)` (avanza siempre por `izq`) y `maxNode(const Nodo* p)` (avanza por `der`), y los wrappers públicos `min()` y `max()` que devuelven referencias a los datos de esos nodos. Si el árbol está vacío, ambos métodos deben lanzar `std::runtime_error("ABB vacio")`.==

```// Helpers (nodo extremo por la izquierda/derecha)
template <class T>
const Nodo<T>* ABB<T>::minNode(const Nodo<T>* p) const{
    if(p==nullptr) return nullptr;
    
    while(p->izq != nullptr){
        p = p->izq;
    }
    return p;
    
}

template <class T>
const Nodo<T>* ABB<T>::maxNode(const Nodo<T>* p) const{
    if(p==nullptr) return nullptr;
    
    while(p->der != nullptr) {
        p = p->der;
    }
    return p;
}

// Wrappers públicos que devuelven referencias a T
template <class T>
const T& ABB<T>::min() const{
    if(raiz == nullptr){
        throw std::runtime_error("ABB vacio");
    }
    return minNode(raiz)->dato;
}

template <class T>
const T& ABB<T>::max() const{
    if(raiz == nullptr){
        throw std::runtime_error("ABB vacio");
    }
    return maxNode(raiz)->dato;
}
```


==Implementá la **altura** del ABB. Completá el helper `alturaRec(const Nodo* p)` que calcule la cantidad de niveles del subárbol (árbol vacío: `0`), y el método público `height()` que retorne la altura del árbol completo. Usá la definición recursiva: `altura(p) = 1 + max(altura(p->izq), altura(p->der))`, con caso base `0` para puntero nulo==

```
template <class T>
int ABB<T>::alturaRec(const Nodo<T>* p) const{
    if(p == nullptr) return -1;
    
    int alturaIzq = alturaRec(p->izq);
    int alturaDer = alturaRec(p->der);
    
    return 1 + std::max(alturaIzq, alturaDer);
}

template <class T>
int ABB<T>::height() const{
    return alturaRec(raiz);
}
```


==Implementá el **recorrido inorden** que devuelva un `std::vector<T>` con los datos del árbol en orden ascendente. Completá el helper `inordenRec(const Nodo* p, vector<T>& out)` (recorré izquierda → nodo → derecha) y el método público `inorden()` que cree el vector, reserve espacio opcionalmente con `reserve(size())`, invoque el helper desde la raíz y lo retorne. En un ABB correcto, la salida debe estar ordenada.==

```
template <class T>
void ABB<T>::inordenRec(const Nodo<T>* p, std::vector<T>& out) const{
    if(p==nullptr) return;
    
    inordenRec(p->izq, out);
    out.push_back(p->dato);
    inordenRec(p->der,out);
}

template <class T>
std::vector<T> ABB<T>::inorden() const{
    std::vector<T> res;
    inordenRec(raiz, res);
    return res;
}

```


==Implementá la búsqueda en el ABB. Completá el helper `containsRec(const Nodo* p, const T& x)` y el método público `contains(const T& x)`. La función debe devolver `true` si el valor existe en el árbol y `false` en caso contrario. Usá la propiedad de orden del ABB para decidir recursivamente si buscar por izquierda o por derecha. Considerá correctamente el caso de árbol vacío.==

```
template <class T>
bool ABB<T>::containsRec(const Nodo<T>* p, const T& x) const{
    if(p==nullptr) return false;
    if(x==p->dato) return true;
  
    
    if(x>p->dato){
        return containsRec(p->der, x);
    }
    else{
        return containsRec(p->izq, x);
    }
}

template <class T>
bool ABB<T>::contains(const T& x) const{
    return containsRec(raiz,x); 
}

```