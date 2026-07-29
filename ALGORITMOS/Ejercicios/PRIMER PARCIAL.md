
# Ejercicio 1 QUEUE PALINDROMO

```
El sistema de monitoreo de señales neuronales de cybercordoba registra secuencias de eventos en una cola. el equipo de exodus systems necesita detectar si una secuencia de eventos es un palindromo, propiedad que indica una anomalia simetrica en la señal.

Tarea: implementar el metodo bool esPalindromo() dentro de la clase Queue<TData>. el metodo debe retornar true si los elementos de la cola forman un palindromo, false en caso contrario. 

 
RESTRICCIONES:

No usar loops sobre punteros internos. solo usar las operaciones publicas del ADT: enqueue, dequeue, front, isEmpty, size.
se permite usar un Stack<TData> auxiliar.
la cola debe quedar con el mismo contenido y orden que al inicio.
no usar arrays, vectores ni estructuras STL

EJEMPLOS:

Queue: [1,2,3,2,1] -> true
Queue: [A,B,C] -> false
Queue: [x] -> true
Queue: [] -> true



template<typename TData>
class Stack {
private: 
	struct Node {
		TData data;
		Node* next;
		explicit Node(const TData& d) : data(d), next(nullptr) {}
	};
	Node*       m_top;
	std::size_t m_size;


public:
	Stack() : m_top(nullptr), m_size(0) {}
	~Stack() { while( !isEmpty()) pop();}
	
	void push(const TData& value){
	
	
	}
}
```

	bool esPalindromo(){

		auto nSizeQueue = size();
		Stack<TData> pilaAux;

		for(std::size_t element = 0; element < nSizeQueue; element++){
			auto value = front();
			dequeue();
			pilaAux.push(value);
			enqueue(value);
		}
		
		bool esPalindromo = true;
		for(std::size_t element = 0; element < nSizeQueue; element++){
			auto value = front();
			dequeue();
			if(value != pilaAux.top()){
				esPalindromo = false;
			}
			
			pilaAux.pop();
			enqueue(value);
			
		}
		return esPalindromo;
	}



# EJERCICIO 2 STACK INVERSION DE PILA

```
El pipeline de procesamieno de comandos de los drones de cybercordoba apila instrucciones en orden de llegada. ante una señal de revision, el sistema debe invertir el orden del stack de comandos pendientes antes de ejecutarlos.

implementar el metodo stack<tdata> invertir() const dentro de la clase stack<tdata>. el metodo debe retornar un nuevostack con los elementos en orden ivnerso al original. el stack original no debe modificarse.

solo usar operaciones publicas del ADT: push pop top isempty size
se permite usar un segundo stack<tdata> auxiliar
no usar arrays, vectores ni estructuras de la stl

original: top -> [5,4,3,2,1] (5 es el tope)
original: top -> [1,2,3,4,5] (1 es el nuevo tope)

```

	private:
	void invertirAux(Node* current, Stack<TData>& result) const{
		if(current == nullptr){
			return;
		}
		result.push(current->data);
		invertirAux(current->next,result);
	}
	
	public:
	Stack<TData> invertir() const {
		Stack<TData> result;
		invertirAux(m_top,result);
	}
	// iterativo
		Stack<TData> result;
		auto current = m_top;
		while(current != nullptr){
			result.push(current->data);
			current = current->next;
		}
		return = result;


# EJERCICIO 3 Lista doblemente enlazada: eliminacion de duplicados consecutivos

```
Los logs de actividad neuronal de cybercordoba se almacenan en una lista doblemente enlazada. los registros duplicados consecutivos representan redundacia en la señal y deben ser eliminados para reducir el costo de transmision entre nodos de la red.

Implementar el metodo void eliminarDuplicados() dentro de la clase DLL<Tdata>. el metodo debe eliminar todos los nodos cuyo valor sea igual al del nodo inmediatamente anterior. la lista debe modificarse in-place y la memoria de los nodos eliminados debe liberarse correctamente.

no usar estructuras auxiliares(arrays, vectoresmstacks,queues)
mantener correctos los punteros prev y next de todos los nodos sobrevivientes, inclutendo m_head y m_tail
actualizar m_size correctamente

```

void eliminarDuplicados() {
        
        // 1. Caso base: Si la lista está vacía o tiene un solo elemento, no hay duplicados.
        if (m_head == nullptr || m_head == m_tail) {
            return;
        }

        Node* curr = m_head;

        // 2. Recorremos mientras exista un nodo siguiente para comparar
        while (curr->next != nullptr) {
            
            if (curr->data == curr->next->data) {
                // 3. Identificamos el nodo duplicado que vamos a borrar
                Node* aEliminar = curr->next;

                // 4. Pointer Splicing (Re-enlace de punteros)
                // El 'next' del actual salta al nodo que le sigue al que vamos a borrar
                curr->next = aEliminar->next;

                if (aEliminar->next != nullptr) {
                    // Si el nodo a borrar NO era el último, el 'prev' de su sucesor ahora apunta a 'curr'
                    aEliminar->next->prev = curr;
                } else {
                    // Si el nodo a borrar SÍ era el último, actualizamos el puntero de la clase
                    m_tail = curr;
                }

                // 5. Liberamos memoria y actualizamos tamaño
                delete aEliminar;
                --m_size;

                // IMPORTANTE: No avanzamos 'curr' acá, porque el nuevo curr->next 
                // también podría ser un duplicado del mismo valor.
            } else {
                // 6. Si no son iguales, es seguro avanzar al siguiente nodo
                curr = curr->next;
            }
        }
    }



# OTROS EJERCICIOS

![[Pasted image 20260608181723.png|318]]

```
// P3 - IMPLEMENTAR AQUÍ
    void moverBloqueInicialAlFinal() {
        // 1. Casos base: lista vacía o con un solo elemento.
        if (m_head == nullptr || m_head == m_tail) {
            return; 
        }

        // 2. Identificar el final del bloque maximal inicial.
        Node* blockEnd = m_head;
        while (blockEnd->next != nullptr && blockEnd->next->data == m_head->data) {
            blockEnd = blockEnd->next;
        }

        // 3. Si el bloque abarca toda la lista, no hay nada que mover.
        if (blockEnd == m_tail) {
            return;
        }

        // 4. Guardar referencias a los nodos clave para el empalme (splicing).
        Node* oldHead = m_head;
        Node* newHead = blockEnd->next;
        Node* oldTail = m_tail;

        // 5. Re-enlazar (Pointer Splicing)
        // a. Configurar el nuevo inicio de la lista
        newHead->prev = nullptr;
        m_head = newHead;

        // b. Conectar el bloque extraído al final de la lista original
        oldTail->next = oldHead;
        oldHead->prev = oldTail;

        // c. Cerrar el nuevo final de la lista
        blockEnd->next = nullptr;
        m_tail = blockEnd;

        // Nota: m_size no se modifica porque no se crean ni destruyen nodos.
    }
```

![[Pasted image 20260608182406.png|385]]

```
// IMPLEMENTAR AQUÍ
    void intercambiarMitades() {
        // 1. Si la pila está vacía (o tiene 0 elementos, aunque el enunciado 
        // garantiza cantidad par), no hay nada que hacer.
        if (isEmpty()) return;

        std::size_t n = size();
        std::size_t mitad = n / 2;

        // Se permiten stacks auxiliares para retener los datos temporalmente
        Stack<TData> mitadSuperior;
        Stack<TData> mitadInferior;

        // 2. Extraer la primera mitad (la que está en el tope original)
        for (std::size_t i = 0; i < mitad; ++i) {
            mitadSuperior.push(top());
            pop();
        }

        // 3. Extraer la segunda mitad (el fondo original)
        for (std::size_t i = 0; i < mitad; ++i) {
            mitadInferior.push(top());
            pop();
        }

        // En este punto, la pila original quedó vacía.
        
        // 4. Insertar primero la mitad que antes estaba arriba. 
        // Como las pilas invierten el orden natural, al pasarlo de la 
        // pila auxiliar a la original, el orden interno se restaura mágicamente.
        while (!mitadSuperior.isEmpty()) {
            push(mitadSuperior.top());
            mitadSuperior.pop();
        }

        // 5. Insertar la mitad que antes estaba en el fondo.
        // Quedará posicionada en el nuevo tope, conservando su orden.
        while (!mitadInferior.isEmpty()) {
            push(mitadInferior.top());
            mitadInferior.pop();
        }
    }
```

![[Pasted image 20260608183659.png|399]]

```
// IMPLEMENTAR AQUÍ
    void fusionarCanales() {
        // 1. Si la cola está vacía, no hay nada que fusionar.
        // El enunciado garantiza cantidad par, así que si no es 0, es al menos 2.
        if (isEmpty()) return;

        std::size_t n = size();
        std::size_t mitad = n / 2;

        // Se permite usar una Queue auxiliar para retener la primera mitad
        Queue<TData> primeraMitad;

        // 2. Extraer la primera mitad de la cola original
        for (std::size_t i = 0; i < mitad; ++i) {
            primeraMitad.enqueue(front());
            dequeue();
        }

        // 3. Intercalar ambas mitades
        // En este punto, la cola original contiene solo la "segunda mitad" original.
        for (std::size_t i = 0; i < mitad; ++i) {
            // a. Encolar el elemento de la primera mitad (wearable) al fondo
            enqueue(primeraMitad.front());
            primeraMitad.dequeue();

            // b. Tomar el elemento de la segunda mitad (laboratorio) que quedó
            // en el frente de la cola original, y mandarlo al fondo.
            enqueue(front());
            dequeue();
        }
    }
```


---

```
 4. DLL: Mover Cabeza al Final (Modo Arquitecto)

Acá tenemos que agarrar el nodo `m_head`, desconectarlo, y mandarlo después de `m_tail`. Es una operación pura de reconexión O(1). Cero ciclos, pura cirugía.

void moverCabezaAlFinal() {
        // Si hay 0 o 1 elemento, mover la cabeza al final deja la lista igual.
        if (m_head == nullptr || m_head == m_tail) return;

        // 1. Guardamos una referencia a la vieja cabeza para no perderla.
        Node* oldHead = m_head;

        // 2. Desconectamos la cabeza actual y avanzamos m_head al segundo nodo.
        m_head = oldHead->next;
        m_head->prev = nullptr;

        // 3. Conectamos la vieja cabeza al final de la lista.
        m_tail->next = oldHead;
        oldHead->prev = m_tail;
        
        // 4. Cortamos el avance de la vieja cabeza (porque ahora es la última).
        oldHead->next = nullptr;

        // 5. Actualizamos el puntero oficial de la cola.
        m_tail = oldHead;
        
        // No tocamos m_size porque la cantidad de elementos es la misma.
    }

```

---

```
### SLL: Eliminar el segundo estado (Modo Arquitecto)

En una lista simplemente enlazada, solo podés ir hacia adelante. Para borrar el segundo nodo, te parás en el primero (`m_head`), puenteás al tercero, y borrás el segundo.

void eliminarSegundo() {
        // Si la lista está vacía o tiene un solo nodo, abortamos.
        if (m_head == nullptr || m_head->next == nullptr) return;

        // 1. Identificamos el nodo que sobra.
        Node* aEliminar = m_head->next;

        // 2. Puenteamos: la cabeza ahora apunta al tercer nodo (o a nullptr si no hay tercero).
        m_head->next = aEliminar->next;

        // 3. Caso borde crítico: si borramos el último nodo, hay que actualizar m_tail.
        if (aEliminar == m_tail) {
            m_tail = m_head;
        }

        // 4. Liberamos la memoria del fantasma.
        delete aEliminar;
        --m_size;
    }
```

---


```
### Stack: Intercambio Cuántico (Modo Usuario)

Acá tenemos que desarmar la pila, pero guardando en variables separadas los dos elementos que queremos intercambiar (el primero y el último). Usamos la pila auxiliar para retener "el sándwich" del medio.
void intercambiarTopYFondo() {
        // Si hay 0 o 1 elemento, no hay nada que intercambiar.
        if (size() < 2) return;

        // 1. Guardamos el tope y lo sacamos.
        TData viejoTop = top();
        pop();

        // 2. Pasamos el "relleno" a la auxiliar.
        // Frenamos cuando quede exactamente 1 elemento en la original (el fondo).
        Stack<TData> aux;
        while (size() > 1) {
            aux.push(top());
            pop();
        }

        // 3. Guardamos el fondo y vaciamos la pila.
        TData viejoFondo = top();
        pop();

        // 4. Reconstruimos en el nuevo orden.
        // El que era el tope, ahora entra primero (se va al fondo).
        push(viejoTop);

        // Devolvemos el "relleno". Al pasar de aux a la original, 
        // el orden se restaura por la doble inversión de LIFO.
        while (!aux.isEmpty()) {
            push(aux.top());
            aux.pop();
        }

        // El que era el fondo, entra último (queda en el nuevo tope).
        push(viejoFondo);
    }
```

---


```
### Queue: Rotar Qubits (Modo Usuario)

En este ejercicio, la clave es aprovechar la política FIFO. Sacamos un elemento por el frente y lo volvemos a meter por el fondo. Repitiendo esto `k` veces, la cola "rota" sobre sí misma de forma circular.

void rotar(int k) {
        if (isEmpty() || k <= 0) return;
        
        // Optimización: si rotamos 5 veces una cola de 5 elementos, 
        // queda igual. Usamos módulo para ahorrar trabajo innecesario.
        k = k % size(); 
        
        for (int i = 0; i < k; ++i) {
            TData val = front();
            dequeue();
            enqueue(val);
        }
    }
```

---

```
### P1 — Queue: Desentrelazado de frecuencias (Modo Usuario)

**Contexto:** El receptor de un dron capta señales de dos satélites diferentes en una misma cola. Las señales llegan siempre perfectamente intercaladas (una del Satélite A, una del Satélite B, etc.). Exodus Systems requiere que separes y agrupes las señales: primero todas las del Satélite A, seguidas por todas las del Satélite B, conservando su orden cronológico relativo.

**Tarea:** Implementar el método `void desentrelazar()` dentro de la clase `Queue<TData>`. Si la cola contiene: `[A1, B1, A2, B2, A3, B3]` Debe transformarse en: `[A1, A2, A3, B1, B2, B3]`

**Restricciones:**

- Se garantiza que la cantidad de elementos es par.
    
- No usar loops sobre punteros internos. Solo usar operaciones públicas del ADT: `enqueue`, `dequeue`, `front`, `isEmpty`, `size`.
    
- Se permite usar **una (1)** `Queue<TData>` auxiliar.
    
- No usar arrays, vectores ni estructuras de la STL.
  
```

// P1 - IMPLEMENTAR AQUÍ
    
    void desentrelazar() {
        // 1. Casos base: si está vacía o tiene solo 2 elementos (ya están ordenados)
        if (size() <= 2) return;

        std::size_t n = size();
        std::size_t pares = n / 2;
        
        // El enunciado permite exactamente una cola auxiliar
        Queue<TData> sateliteB;

        // 2. Fase de Separación
        // Iteramos por la cantidad de pares (A, B) que hay en la cola.
        for (std::size_t i = 0; i < pares; ++i) {
            
            // a. Procesamos la señal del Satélite A
            TData senalA = front();
            dequeue();
            enqueue(senalA); // La volvemos a meter al fondo de la original
            
            // b. Procesamos la señal del Satélite B
            TData senalB = front();
            dequeue();
            sateliteB.enqueue(senalB); // La aislamos en la cola auxiliar
        }

        // En este punto, la cola original dio una vuelta completa reteniendo 
        // solo las señales A, y la auxiliar retuvo todas las señales B.

        // 3. Fase de Reensamble
        // Volcamos todas las señales del Satélite B al final de la cola original.
        while (!sateliteB.isEmpty()) {
            enqueue(sateliteB.front());
            sateliteB.dequeue();
        }
    }

---

```
### P2 — Singly Linked List (SLL): Promoción del último comando (Modo Arquitecto)

**Contexto:** En el bus de comandos unidireccional (SLL) del sistema de evasión, el comando que quedó rezagado al final de la lista de espera acaba de alcanzar una prioridad crítica. Debe ser movido inmediatamente al frente de la lista para ser el próximo en ejecutarse.

**Tarea:** Implementar el método `void promoverUltimo()` dentro de la clase `SLL<TData>`. El método debe tomar el último nodo de la lista y colocarlo como el primer nodo (`m_head`).

**Ejemplos:** `[10, 20, 30, 40]` -> `[40, 10, 20, 30]` `[5, 9]` -> `[9, 5]` `[7]` -> `[7]`

**Restricciones:**

- Operar in-place. **No** intercambiar los valores de la propiedad `data`.
    
- Modificar exclusivamente los punteros `next`, `m_head` y `m_tail`.
    
- No crear ni destruir nodos (prohibido `new` y `delete`).
    
- Cuidado con los casos base (lista vacía o de un solo elemento).
```


	void promoverUltimo() {
        // 1. Casos base: si está vacía o tiene 1 solo elemento, no hay nada que mover.
        if (m_head == nullptr || m_head == m_tail) return;

        // 2. Necesitamos encontrar el nodo ANTEÚLTIMO.
        Node* prev = m_head;
        while (prev->next != m_tail) {
            prev = prev->next;
        }

        // 3. Pointer Splicing (El recableado)
        // a. El que era el último nodo ahora apunta a la cabeza vieja.
        m_tail->next = m_head;

        // b. La clase actualiza su cabeza para que sea el último nodo.
        m_head = m_tail;

        // c. El anteúltimo nodo se convierte en la nueva cola.
        m_tail = prev;

        // d. Cortamos el enlace del nuevo último nodo para que no haya ciclos.
        m_tail->next = nullptr;
    }
---

```
### P3 — Doubly Linked List (DLL): Swap de extremos (Modo Arquitecto)

**Contexto:**

El archivo de auditoría del sistema operativo mantiene los logs en una lista doblemente enlazada. A causa de una corrupción de los índices de borde, se requiere intercambiar físicamente el primer nodo con el último nodo de la lista.

**Tarea:**

Implementar el método `void intercambiarExtremos()` dentro de la clase `DLL<TData>`. El nodo que era `m_head` pasa a ser `m_tail`, y el nodo que era `m_tail` pasa a ser `m_head`.

**Ejemplo:**

`[100, 200, 300, 400]` -> `[400, 200, 300, 100]`

**Restricciones:**

- Operar in-place. **No** intercambiar los valores de la propiedad `data`.
    
- Modificar exclusivamente los punteros `next`, `prev`, `m_head` y `m_tail`.
    
- Considerar la complejidad estructural si la lista tiene menos de 3 elementos.
    
- No usar estructuras auxiliares ni loops (esta operación debe ser estrictamente $\mathcal{O}(1)$).
```

	void intercambiarExtremos() {
        // 1. Casos base: 0 o 1 elemento, no hay nada que intercambiar.
        if (m_head == nullptr || m_head == m_tail) return;

        Node* oldHead = m_head;
        Node* oldTail = m_tail;

        // 2. Caso Especial: Exactamente 2 elementos (A <-> B)
        if (oldHead->next == oldTail) {
            oldHead->next = nullptr;
            oldHead->prev = oldTail;
            
            oldTail->next = oldHead;
            oldTail->prev = nullptr;
        } 
        // 3. Caso General: 3 o más elementos (A <-> ... <-> Z)
        else {
            Node* segundo = oldHead->next;
            Node* anteultimo = oldTail->prev;

            // Mover la vieja cola al frente
            oldTail->next = segundo;
            segundo->prev = oldTail;
            oldTail->prev = nullptr;

            // Mover la vieja cabeza al fondo
            oldHead->prev = anteultimo;
            anteultimo->next = oldHead;
            oldHead->next = nullptr;
        }

        // 4. Actualizar los punteros oficiales de la clase
        m_head = oldTail;
        m_tail = oldHead;
    }




