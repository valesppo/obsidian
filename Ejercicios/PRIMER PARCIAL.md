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





# EJERCICIO 3 Lista doblemente enlazada: eliminacion de duplicados consecutivos

```
Los logs de actividad neuronal de cybercordoba se almacenan en una lista doblemente enlazada. los registros duplicados consecutivos representan redundacia en la señal y deben ser eliminados para reducir el costo de transmision entre nodos de la red.

Implementar el metodo void eliminarDuplicados() dentro de la clase DLL<Tdata>. el metodo debe eliminar todos los nodos cuyo valor sea igual al del nodo inmediatamente anterior. la lista debe modificarse in-place y la memoria de los nodos eliminados debe liberarse correctamente.

no usar estructuras auxiliares(arrays, vectoresmstacks,queues)
mantener correctos los punteros prev y next de todos los nodos sobrevivientes, inclutendo m_head y m_tail
actualizar m_size correctamente

```

