[[ABB - arboles (del aula virtual)]]


Nivel 2:           [ 50 ]
                  /      \
Nivel 1:      [ 30 ]    [ 70 ]
              /    \    /    \
Nivel 0:  [ 20 ]  null null  null
          /    \
       null   null

Fase de Ida (Llamadas descendentes)                Fase de Vuelta (Retornos y Cálculos)
-----------------------------------                ------------------------------------

alturaRec(50)                                      Retorna: 1 + max(1, 0) = 2
  │                                                  ▲
  ├──> alturaRec(30) [Izq de 50] ────────────────────┼──> Retorna: 1 + max(0, -1) = 1
  │      │                                           │      ▲
  │      ├──> alturaRec(20) [Izq de 30] ─────────────┼──────┼──> Retorna: 1 + max(-1, -1) = 0
  │      │      │                                    │      │      ▲
  │      │      ├──> alturaRec(nullptr) [Izq] ───────┼──────┼──────┼──> Retorna -1
  │      │      └──> alturaRec(nullptr) [Der] ───────┼──────┼──────┼──> Retorna -1
  │      │                                           │      │
  │      └──> alturaRec(nullptr) [Der de 30] ────────┼──────┴─────────> Retorna -1
  │                                                  │
  └──> alturaRec(70) [Der de 50] ────────────────────┴────────────────> Retorna: 1 + max(-1, -1) = 0
         │                                                  ▲
         ├──> alturaRec(nullptr) [Izq de 70] ───────────────┼─────────> Retorna -1
         └──> alturaRec(nullptr) [Der de 70] ───────────────┴─────────> Retorna -1
     

```
int contarEstaciones(Nodo* p) {
    // 1. EL FRENO DE MANO: Si no hay estación, hay 0 estaciones.
    if (p == nullptr) return 0;

    // 2. LA DELEGACIÓN Y EL CÁLCULO
    // Yo cuento como 1, más lo que haya a mi izquierda, más lo que haya a mi derecha.
    return 1 + contarEstaciones(p->izq) + contarEstaciones(p->der);
}
```

```
▶️ INICIA EL PROGRAMA: contarEstaciones(50)

// --- MUNDO DE LA ESTACIÓN 50 ---
LÍNEA 1: ¿50 es nullptr? -> Falso.
LÍNEA 2: return 1 + contarEstaciones(40) + contarEstaciones(60);
         // ⏸️ ¡PAUSA! El 50 se congela esperando al 40.

      // --- MUNDO DE LA ESTACIÓN 40 ---
      LÍNEA 1: ¿40 es nullptr? -> Falso.
      LÍNEA 2: return 1 + contarEstaciones(nullptr) + contarEstaciones(nullptr);
               // ⏸️ ¡PAUSA! El 40 se congela esperando a su izquierda.

            // --- MUNDO DEL NULLPTR (Izq del 40) ---
            LÍNEA 1: ¿nullptr es nulo? -> Verdadero.
            return 0; // 🔙 Devuelvo 0.

      // --- DE VUELTA AL MUNDO DEL 40 ---
      // Ya tiene la izquierda (0). Ahora va a la derecha.
      // ⏸️ ¡PAUSA! Llama al hijo derecho.

	            // --- MUNDO DEL NULLPTR (Der del 40) ---
            LÍNEA 1: ¿nullptr es nulo? -> Verdadero.
            return 0; // 🔙 Devuelvo 0.

      // --- DE VUELTA AL MUNDO DEL 40 ---
      LÍNEA 2: return 1 + 0 + 0; // Hace la cuenta = 1.
               // 🔙 El 40 se destruye y le devuelve un "1" a su jefe, el 50.

// --- DE VUELTA AL MUNDO DEL 50 ---
// El 50 tiene la primera parte: return 1 + 1 (del lado izq) + contarEstaciones(60);
// ⏸️ ¡PAUSA! Ahora le toca preguntar al 60.

      // --- MUNDO DE LA ESTACIÓN 60 ---
      LÍNEA 1: ¿60 es nullptr? -> Falso.
      LÍNEA 2: return 1 + contarEstaciones(nullptr) + contarEstaciones(nullptr);
      
      // (Hace exactamente lo mismo que el 40, recibe 0 de ambos lados)
      LÍNEA 2: return 1 + 0 + 0; // Hace la cuenta = 1.
               // 🔙 El 60 se destruye y le devuelve un "1" al 50.

// --- DE VUELTA AL MUNDO DEL 50 ---
// El 50 por fin tiene todos los números de sus empleados.
LÍNEA 2: return 1 (él mismo) + 1 (rama izq) + 1 (rama der);
LÍNEA 2: return 3;

⏹️ FIN DEL PROGRAMA. Total de estaciones = 3.
```



```
void imprimir(Nodo* p) {
    if (p == nullptr) return;       // LÍNEA 1: El freno de mano
    
	  imprimir(p->izq);               // LÍNEA 2: Pausa y viaje a IZQUIERDA
    
    std::cout << p->dato << ", ";   // LÍNEA 3: ¡Imprimo el valor! (El famoso Espacio B)
    
    imprimir(p->der);               // LÍNEA 4: Pausa y viaje a la DERECHA
}
```

```
▶️ INICIA: imprimir(50)

// --- MUNDO DEL 50 ---
LÍNEA 1: ¿50 es nullptr? -> Falso. Sigo.
LÍNEA 2: imprimir(40) 
         // ⏸️ ¡PAUSA! El 50 se congela en la Línea 2 y abre el mundo del 40.

      // --- MUNDO DEL 40 ---
      LÍNEA 1: ¿40 es nullptr? -> Falso. Sigo.
      LÍNEA 2: imprimir(nullptr) // El 40 va a su izquierda.
               // ⏸️ ¡PAUSA! El 40 se congela en la Línea 2.

            // --- MUNDO DEL NULLPTR (Izquierda del 40) ---
            LÍNEA 1: ¿nullptr es nulo? -> ¡Verdadero! 
            return; // 🔙 Me destruyo y vuelvo.

      // --- DE VUELTA AL MUNDO DEL 40 ---
      // El 40 se descongela de la Línea 2. Pasa a la siguiente.
      LÍNEA 3: cout << 40            👉 ¡PANTALLA: "40, "!
      LÍNEA 4: imprimir(nullptr) // El 40 va a su derecha.
               // ⏸️ ¡PAUSA! El 40 se congela en la Línea 4.

            // --- MUNDO DEL NULLPTR (Derecha del 40) ---
            LÍNEA 1: ¿nulo es nulo? -> ¡Verdadero! 
            return; // 🔙 Me destruyo y vuelvo.

      // --- DE VUELTA AL MUNDO DEL 40 ---
      // El 40 terminó todas sus líneas. 
      // 🔙 Se destruye y le avisa a su jefe (el 50) que ya terminó.

// --- DE VUELTA AL MUNDO DEL 50 ---
// El 50 por fin se descongela de la Línea 2. Ya procesó toda su izquierda.
LÍNEA 3: cout << 50                  👉 ¡PANTALLA: "40, 50, "!
LÍNEA 4: imprimir(60) // El 50 va a su derecha.
         // ⏸️ ¡PAUSA! El 50 se congela en la Línea 4.

      // --- MUNDO DEL 60 ---
      LÍNEA 1: ¿60 es nullptr? -> Falso. Sigo.
      LÍNEA 2: imprimir(nullptr) // El 60 va a su izquierda.
               // ⏸️ ¡PAUSA! El 60 se congela.

            // --- MUNDO DEL NULLPTR (Izquierda del 60) ---
            LÍNEA 1: return; // 🔙 Vuelvo.

      // --- DE VUELTA AL MUNDO DEL 60 ---
      LÍNEA 3: cout << 60            👉 ¡PANTALLA: "40, 50, 60, "!
      LÍNEA 4: imprimir(nullptr) // El 60 va a su derecha.
               // ⏸️ ¡PAUSA! El 60 se congela.

            // --- MUNDO DEL NULLPTR (Derecha del 60) ---
            LÍNEA 1: return; // 🔙 Vuelvo.

      // --- DE VUELTA AL MUNDO DEL 60 ---
      // El 60 terminó todas sus líneas. 
      // 🔙 Se destruye y vuelve al 50.

// --- DE VUELTA AL MUNDO DEL 50 ---
// El 50 terminó su Línea 4. Ya no le quedan líneas.

⏹️ FIN DEL PROGRAMA. 
Resultado en pantalla: 40, 50, 60,
```


```
template <class T>
void ABB<T>::clearRec(Nodo<T>* p) {
    if (p == nullptr) return;
    clearRec(p->izq);
    clearRec(p->der);
    delete p; // Liberación de memoria dinámica
}

template <class T>
void ABB<T>::clear() {
    clearRec(raiz); // Pasamos el nodo inicial
    raiz = nullptr; // Excelente práctica: evitar punteros colgantes
}
```


```
- Raíz: 50
    
- Hijo izquierdo: 40 (hoja)
    
- Hijo derecho: nullptr
  
INICIO: clearRec(Nodo 50)

-- CONTEXTO: Nodo 50 --
¿50 es nullptr? Falso.
Llama a: clearRec(40) -> El Nodo 50 se PAUSA en esta línea.

    -- CONTEXTO: Nodo 40 --
    ¿40 es nullptr? Falso.
    Llama a: clearRec(nullptr_izq) -> El Nodo 40 se PAUSA.

        -- CONTEXTO: nullptr_izq del 40 --
        ¿nullptr es nulo? Verdadero.
        Retorna (vuelve silenciosamente).

    -- CONTEXTO: Nodo 40 --
    Continúa. Llama a: clearRec(nullptr_der) -> El Nodo 40 se PAUSA de nuevo.

        -- CONTEXTO: nullptr_der del 40 --
        ¿nullptr es nulo? Verdadero.
        Retorna.

    -- CONTEXTO: Nodo 40 --
    Continúa. Ejecuta: delete 40; 
    El nodo 40 se elimina de la memoria RAM. La función termina y retorna al padre.

-- CONTEXTO: Nodo 50 --
El Nodo 50 se reanuda (terminó de limpiar su rama izquierda).
Llama a: clearRec(nullptr_der) -> El Nodo 50 se PAUSA.

    -- CONTEXTO: nullptr_der del 50 --
    ¿nullptr es nulo? Verdadero.
    Retorna.

-- CONTEXTO: Nodo 50 --
Continúa. Ejecuta: delete 50;
El nodo 50 se elimina de la memoria RAM.

FIN DEL PROGRAMA.
```




