<<<<<<< HEAD
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



# OTROS EJERCICIOS

![[Pasted image 20260608181723.png|325]]

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

```