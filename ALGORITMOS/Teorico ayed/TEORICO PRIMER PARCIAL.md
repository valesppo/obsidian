[[TEORICO segundo parcial ayed]]

### Fase 1: Lectura y Clasificación del Problema (El Contrato)

Antes de escribir la primera línea de código, tenés que definir en qué "modo" vas a operar según las restricciones del enunciado.

- **Modo Usuario (ADT Estricto):** Si el enunciado dice _"Solo usar métodos públicos"_ o prohíbe iterar sobre punteros internos. Acá **no existen** los nodos para vos. Vas a jugar con la matemática de las políticas LIFO/FIFO.
    
- **Modo Arquitecto (Bajo Nivel):** Si el enunciado dice _"In-place"_, _"Re-enlazar punteros"_ o _"No usar estructuras auxiliares"_. Acá sos el dueño de la memoria y vas a operar directamente sobre `m_head`, `m_tail`, `next` y `prev`.
    

### Fase 2: Metodología para Pilas (Stack) y Colas (Queue) - Modo Usuario

Acá el objetivo es mover datos de un contenedor a otro sin romper el orden cronológico ni alterar la complejidad $\mathcal{O}(n)$.

1. **Validar Casos Base (Early Returns):**
    
    - Comprobá siempre `if (isEmpty()) return;` para evitar excepciones de _underflow_ al intentar hacer `pop()` o `dequeue()`.
        
2. **Congelar el Estado Inicial:**
    
    - Guardá el tamaño original: `std::size_t n = size();`.
        
    - **Regla de oro:** Nunca uses `while (!isEmpty())` si vas a volver a insertar elementos en la misma estructura (por ejemplo, encolando al fondo de la misma cola), porque vas a crear un ciclo infinito. Usá siempre un `for` iterando exactamente $n$ veces.
        
3. **Elegir el Buffer Estratégico:**
    
    - Si usás una **Pila auxiliar**, el orden original se **invierte** al volcar los datos. (Dos volcados en pila restauran el orden).
        
    - Si usás una **Cola auxiliar**, el orden original se **mantiene**.
        
4. **Desensamble y Reensamble:**
    
    - Extraé el frente/tope, procesá la lógica (comparar, filtrar, contar) y guardá el elemento en el buffer.
        
    - Al terminar, volcá el buffer de vuelta a la estructura original para restaurar su estado.
        

### Fase 3: Metodología para SLL y DLL - Modo Arquitecto

Acá estás haciendo cirugía a corazón abierto. El orden en el que asignás las cosas es la diferencia entre un $\mathcal{O}(1)$ perfecto y un _segmentation fault_.

1. **Definir los Punteros Temporales (Scouts):**
    
    - Nunca asumas que podés avanzar y modificar al mismo tiempo sin perder el rastro.
        
    - Definí iteradores claros: `Node* curr = m_head;` y, si estás borrando, `Node* aEliminar = nullptr;`.
        
2. **Dibujar el "Pointer Splicing" Mentalmente:**
    
    - Identificá los nodos A, B y C. Si querés aislar B, tenés que conectar A con C _antes_ de borrar B.
        
3. **Establecer los Nuevos Enlaces (Orden Estricto):**
    
    - Primero conectá los punteros del nodo _nuevo_ o del nodo _destino_ hacia el resto de la lista.
        
    - Recién después, rompé los enlaces del nodo _viejo_ que apuntan a la estructura.
        
    - _Ejemplo DLL:_ `curr->prev->next = curr->next;`
        
4. **Gestionar los Extremos (Casos Borde):**
    
    - ¿Qué pasa si el nodo a borrar/mover es el `m_head`? Tenés que actualizar el puntero de la clase (`m_head = curr->next`).
        
    - ¿Qué pasa si es el `m_tail`? Lo mismo (`m_tail = curr->prev`).
        
    - ¿Y si era el único nodo de la lista? (`m_head = m_tail = nullptr`).
        
5. **Limpiar la Memoria y el Estado:**
    
    - Si eliminaste un nodo, ejecutá `delete nodo;` inmediatamente para evitar fugas de memoria.
        
    - Actualizá `m_size` si agregaste o quitaste elementos.
        

### Checklist de Supervivencia en Exámenes

- **Punteros a la nada:** ¿Revisaste qué pasa si haces `curr->next->prev` y `curr->next` resulta ser `nullptr`? Siempre validá con un `if` antes de encadenar flechas.
    
- **Complejidad cuadrática oculta:** Cuidado con usar funciones como `size()` o métodos de búsqueda _dentro_ de la condición de un ciclo `while` si estas funciones operan en $\mathcal{O}(n)$. Vas a degradar tu algoritmo a $\mathcal{O}(n^2)$ sin darte cuenta.
    
- **Variables huérfanas:** Si moviste la cabeza o la cola, ¿te aseguraste de anular sus punteros salientes (`m_head->prev = nullptr`)?

---

### 1. Pilas y Colas (ADTs - Modo Usuario)

Acá el juego es el **movimiento de datos**. No existen los nodos, solo las políticas LIFO (Pila) y FIFO (Cola).

**Reglas de Oro:**

- **La trampa del tamaño variable:** NUNCA uses `while(!isEmpty())` o `for(int i=0; i<size(); i++)` si vas a volver a inyectar datos en la misma estructura (ej. desencolar y volver a encolar). El tamaño va a cambiar y vas a crear un bucle infinito.
    
    - _Solución:_ Siempre congelá el tamaño inicial: `int n = size();` y usá un `for` que vaya hasta `n`.
        
- **Efectos secundarios de las auxiliares:**
    
    - Pasar de Pila a Pila Auxiliar $\rightarrow$ **INVIERTE** el orden. (Para restaurarlo, tenés que volver a pasarlo a la original).
        
    - Pasar de Cola a Cola Auxiliar $\rightarrow$ **MANTIENE** el orden.
        
- **Early Returns (Casos Base):** Tu primera línea siempre tiene que ser `if (isEmpty()) return;`. Si te piden intercambiar o rotar, a veces necesitás `if (size() < 2) return;`.
    
- **Prohibido el `const_cast` (salvo emergencia):** Si un método es `const`, teóricamente no podés usar `pop()` o `push()`. Si el enunciado no te deja usar `const_cast`, entonces la función debe devolver una copia o no podés resolverlo in-place.
    

### 2. Listas Simplemente Enlazadas (SLL - Modo Arquitecto)

Acá sos el dueño de la memoria, pero estás en una **calle de mano única**. No podés mirar hacia atrás.

**Reglas de Oro:**

- **El "Prev" es tu mejor amigo:** Para insertar o eliminar un nodo en la posición $X$, tu puntero iterador tiene que frenar sí o sí en la posición $X-1$. Si llegaste a $X$, ya te pasaste y no podés retroceder.
    
- **La regla del trapecista (Orden de Splicing):** NUNCA sueltes tu cuerda actual antes de agarrar la nueva.
    
    - _Correcto:_ Primero el nuevo nodo apunta a la lista (`nuevo->next = curr->next`), y _después_ la lista apunta al nuevo nodo (`curr->next = nuevo`).
        
- **Cuidado de los Extremos:**
    
    - Si modificás o borrás el _primer_ nodo, acordate de actualizar `m_head`.
        
    - Si modificás o borrás el _último_ nodo (su `next` era `nullptr`), acordate de actualizar `m_tail`.
        
- **Variables, no Nodos:** Recordá lo que vimos: hacer `Node* tmp = m_head;` NO es crear un nodo nuevo. Es solo crear una etiqueta (un puntero en el stack) para no perder de vista la casa. El profe solo te penaliza si usás `new`.
    

### 3. Listas Doblemente Enlazadas (DLL - Modo Arquitecto)

Acá tenés una **avenida doble mano**. Tenés más poder (podés borrar un nodo sin buscar al anterior), pero tenés el doble de cables para conectar.

**Reglas de Oro:**

- **La regla de las 4 flechas:** Cada vez que insertás un nodo en el medio de una DLL, tenés que actualizar 4 punteros en este orden estricto:
    
    1. `nuevo->next = curr->next;`
        
    2. `nuevo->prev = curr;`
        
    3. `curr->next->prev = nuevo;` (¡Ojo! Solo si `curr->next` no es `nullptr`).
        
    4. `curr->next = nuevo;`
        
- **La plaga del Segmentation Fault:** Antes de escribir `algo->next->prev`, **siempre** tenés que estar $100\%$ seguro de que `algo->next` no es `nullptr`. Si el nodo es el último de la lista y le pedís a la "nada" que te dé su puntero `prev`, el programa crashea instantáneamente.
    
    - _Solución:_ Usar un `if (algo->next != nullptr) { ... }`.
        
- **Borrar in-place:** Si te piden borrar duplicados o filtrar, guardá el nodo a borrar en un puntero temporal `Node* aEliminar = curr;`, mové tu `curr` al siguiente (`curr = curr->next`), arreglá los punteros alrededor de `aEliminar`, y recién al final hacé `delete aEliminar;`.
    

### 4. Análisis de Complejidad (Teoría Big-O)

El profe quiere ver si sabés identificar cuellos de botella. Buscá siempre el **peor caso**.

**Reglas de Oro:**

- **La trampa de las Constantes:** Leé la letra chica del enunciado. Si te dice que la ventana "K" o el intervalo "M" son valores constantes predefinidos, entonces para el análisis asintótico valen $\mathcal{O}(1)$. Si no lo aclara, son variables y deben aparecer en tu resultado final (ej. $\mathcal{O}(N \cdot K)$).
    
- **Llamadas Ocultas (El Asesino Silencioso):** Un `for` que da $N$ vueltas es $\mathcal{O}(N)$. Pero si adentro tiene un `v.insert(v.begin(), x)` (que cuesta $\mathcal{O}(N)$), todo el bloque se vuelve **$\mathcal{O}(N^2)$**. No mires solo los bucles, mirá qué métodos se llaman adentro.
    
- **La sumatoria de Gauss:** Un bucle `for(j = 0; j < i; j++)` anidado dentro de un `for(i = 0; i < N; i++)` siempre, siempre da como resultado la progresión $\frac{N(N-1)}{2}$, que se traduce en un **peor caso de $\mathcal{O}(N^2)$**.
    
- **El "While" mentiroso (Análisis Amortizado):** Si tenés un `while` adentro de un `for` (como el ejercicio de los picos de tensión), preguntate: _"¿Cuántas veces puede ejecutarse la operación interna del while EN TOTAL durante toda la vida del programa?"_. Si un elemento entra a la pila una vez, solo puede salir una vez. Por más anidado que esté, si hace a lo sumo $N$ operaciones totales, la complejidad global es **$\mathcal{O}(N)$**.
    

### Checklist de Pánico durante el Examen

Si el código en papel no te cierra o te mariaste:

1. **Dibuja 3 cajitas:** Hacé una lista de 3 elementos (`A -> B -> C`).
    
2. **Seguí tus propias líneas de código con el dedo:** "Si hago esto, B deja de apuntar a C... ¿Cómo llego a C ahora?".
    
3. **Verificá los extremos:** "¿Qué pasa si mi código recibe una lista de 1 solo elemento? ¿Qué pasa si recibe una lista vacía?". Si tu código sobrevive a la lista vacía y a la lista de 1 elemento sin tirar error, tenés el $80\%$ del ejercicio asegurado.