
✦ El Trabajo Práctico consiste en diseñar e implementar un sistema concurrente en Java 21 que simula una granja industrial de impresión 3D.

  A continuación te resumo los puntos clave de forma clara y estructurada:

  ---

  1. El Objetivo Principal
  Procesar un conjunto de órdenes de trabajo (N órdenes, numeradas del 1 a totalOrders) a través de un pipeline de 4 etapas concurrentes, gestionando una matriz de impresoras compartidas de forma segura (sin condiciones de carrera,
  bloqueos mutuos ni espera activa).

  ---

  2. Entidades Principales

   3. Órdenes de impresión:
      - Empiezan en CREATED.
      - Deben terminar exactamente en uno de 4 estados finales: APPROVED, REJECTED, PRINT_FAILED o DEFECTIVE.
      - No pueden quedar órdenes en estados intermedios al terminar la simulación.

   4. Impresoras:
      - Organizadas en una matriz (filas × columnas).
      - Estados: AVAILABLE (libre), RESERVED (en uso) y OUT_OF_SERVICE (rota/descartada).
      - Llevan un identificador único (ej: P-0-0), un contador de usos y la orden asociada.
      - Si una impresora pasa a OUT_OF_SERVICE, no puede reutilizarse jamás.

  ---

  5. Las 4 Etapas Concurrentes (Pipeline)

  Todas las etapas arrancan al mismo tiempo al inicio del programa (no se ejecutan una después de la otra de forma secuencial):

    1 [CREATED]
    2     │
    3     ▼
    4 Etapa 1: Asignación (3 hilos)
    5     │  • Toma orden CREATED
    6     │  • Busca impresora AVAILABLE y la pasa a RESERVED
    7     │  • Incrementa contador de uso de la impresora
    8     ▼
    9 [WAITING_VALIDATION]
   10     │
   11     ▼
   12 Etapa 2: Validación del modelo 3D (2 hilos)
   13     │  • Consulta OutcomeDecider.isModelValid(...)
   14     ├── Si INVÁLIDO ──► [REJECTED] (Terminal) + Impresora vuelve a AVAILABLE
   15     └── Si VÁLIDO   ──► [READY_TO_PRINT] (Impresora sigue RESERVED)
   16                              │
   17                              ▼
   18                         Etapa 3: Impresión (3 hilos)
   19                              │  • Consulta OutcomeDecider.isPrintSuccessful(...)
   20                              ├── Si FALLA  ──► [PRINT_FAILED] (Terminal) + Impresora pasa a OUT_OF_SERVICE
   21                              └── Si ÉXITO  ──► [PRINTED] + Impresora vuelve a AVAILABLE
   22                                                     │
   23                                                     ▼
   24                                                Etapa 4: Control de Calidad (2 hilos)
   25                                                     │  • (Ya no usa impresoras)
   26                                                     │  • Consulta OutcomeDecider.isQualityApproved(...)
   27                                                     ├── Si RECHAZADO ──► [DEFECTIVE] (Terminal)
   28                                                     └── Si APROBADO  ──► [APPROVED] (Terminal)

  ---

  6. Reglas Críticas y Restricciones Técnicas

   7. Restricción estricta de concurrencia (evaluada por tests automáticos):
      - PERMITIDO: Thread, Runnable, bloques/métodos synchronized, wait(), notify() / notifyAll(), ReentrantLock, Semaphore, ejecutores básicos (ExecutorService, ThreadPoolExecutor) y colecciones estándar de Java.
      - PROHIBIDO: BlockingQueue, clases atómicas (AtomicInteger, etc.), colecciones concurrentes (ConcurrentHashMap, etc.), Condition, CountDownLatch, CyclicBarrier, streams paralelos, Vector, Hashtable, wildcards de concurrencia (import
        java.util.concurrent.*) y espera activa (while(...) consumiendo CPU).
   8. Determinismo:
      - OutcomeDecider recibe la semilla de la simulación y el ID de la orden. Para los mismos datos de entrada, las órdenes deben terminar siempre en el mismo estado final, sin importar qué hilo ejecutó qué cosa.
   9. Manejo de tiempos:
      - Cada hilo debe dormir (Thread.sleep) el tiempo indicado en la configuración (delay.*.ms) una sola vez por cada orden procesada.
   10. Terminación limpia:
      - La simulación finaliza cuando todas las órdenes alcanzan su estado terminal.
      - No deben quedar hilos vivos, ni órdenes en tránsito, ni impresoras en RESERVED.
      - Prohibido usar Thread.stop() o System.exit().

  ---

  11. Archivos de Salida (en resultados/)

  Al terminar la ejecución, se deben generar 3 archivos:
   12. eventos.csv: Registro cronológico estricto de cada cambio de estado de cada orden (con secuencia monotónica incremental, marca de tiempo, hilo, estado origen y destino).
   13. elementos.csv: Tabla final con una fila por cada orden ordenada por ID (estado final, impresora utilizada y cantidad de veces que pasó por cada etapa).
   14. resumen.properties: Resumen estadístico (total procesadas, aprobadas, rechazadas, tiempo total, etc.).

  ---

  15. Entregables Adicionales Requeridos

   - Implementación en ConcurrentSimulation.java (sin tocar las clases de ar.edu.unc.fcefyn.pcp.tp1.api).
   - Tests unitarios y de concurrencia propios en src/test/java.
   - Diagrama de clases (docs/diagrama-clases.png) y de secuencia (docs/diagrama-secuencia.png).
   - Informe en docs/informe.pdf (análisis de condiciones de carrera, recursos compartidos, tiempos teóricos vs reales, justificación de diseño).
   - integrantes.json completo.
   - Tag de git y archivo .zip para entregar en el aula virtual.


✦ Para este trabajo práctico, el código se divide en tres categorías: lo que está prohibido tocar, lo que debes modificar, y lo que debes crear desde cero.

  ---

  1. Lo que NO debes tocar (Prohibido por la cátedra)
   * Todo el paquete ar.edu.unc.fcefyn.pcp.tp1.api:
     - ConfigurationLoader.java
     - OrderSnapshot.java
     - OrderState.java
     - OutcomeDecider.java
     - PrinterSnapshot.java
     - PrinterState.java
     - Simulation.java
     - SimulationConfig.java
     - SimulationResult.java
    (El enunciado prohíbe explícitamente modificar las firmas o el comportamiento de estas clases, ya que son la interfaz con la que los tests automáticos evaluarán tu solución).

   * Main.java (en ar.edu.unc.fcefyn.pcp.tp1.app): Ya está listo; simplemente carga el archivo de configuración y llama a tu simulación.

   * Los tests públicos existentes en src/test/.../publictests: No debes modificarlos, deben pasar tal cual están.

  ---

  2. El archivo existente que SÍ debes modificar
   * ConcurrentSimulation.java (ubicado en src/main/java/ar/edu/unc/fcefyn/pcp/tp1/solution/):
     - Actualmente solo tiene un método execute(SimulationConfig config) que lanza una excepción (UnsupportedOperationException).
     - Aquí es donde se orquesta el inicio de la simulación, la creación y arranque de los hilos, la espera de su finalización (join), la escritura de los archivos de salida y el retorno del SimulationResult.

  ---

  3. Los archivos y clases que DEBES CREAR tú
  Para mantener el código limpio y modular, dentro de ar.edu.unc.fcefyn.pcp.tp1.solution (o en subpaquetes que crees allí) vas a crear:

   4. Estructuras de Sincronización:
      - Una cola o buffer bloqueante propio (ej: WorkQueue<T> o OrderBuffer), implementado con synchronized y wait()/notifyAll() para comunicar las etapas.
   5. Entidades Internas del Modelo:
      - Order: representa la orden en ejecución (guarda su estado, historial, impresora asignada, y contadores de cuántas veces pasó por cada etapa).
      - Printer: representa una impresora individual (id, fila, columna, estado, contador de usos, orden asignada).
      - PrinterPool o PrinterMatrix: monitor que administra el acceso concurrente a las impresoras (buscar libres, reservar, liberar, marcar fuera de servicio).
   6. Workers (Hilos de cada etapa):
      - Clases que implementen Runnable o extiendan Thread para cada una de las 4 etapas:
        - Asignación
        - Validación
        - Impresión
        - Control de Calidad
   7. Registro y Persistencia de Resultados:
      - EventLogger: para registrar de forma sincronizada y secuencial cada evento en eventos.csv.
      - ReportWriter o generador de reportes: para escribir elementos.csv y resumen.properties al finalizar.
   8. Tests propios (en src/test/java/...):
      - Nuevos tests que prueben casos límite, concurrencia y ausencia de condiciones de carrera.

  ---

  9. Archivos de entrega adicionales a completar
   - integrantes.json: Completar con los datos de tu grupo y repositorio.
   - docs/: Agregar los diagramas (diagrama-clases.png, diagrama-secuencia.png) y el informe final en PDF (informe.pdf).
   - resultados/: Donde se generarán eventos.csv, elementos.csv y resumen.properties tras la ejecución.
