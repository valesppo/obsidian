Leer stallings william

**lunes que viene toma un preguntero sobre la lectura**

tenemos trabajos practicos en el practico tambien


==**CAPITULO 1: Introduccion a los sistemas de comunicaciones**==

STALLINGS William. Comunicaciones y Redes de Computadoras. 7ma. Edición
PARTE I - Descripción general
Capítulo 1. Introducción a las comunicaciones de datos y redes
1.1. Un modelo para las comunicaciones
1.2. Comunicaciones de datos
1.3. Redes de transmisión de datos
Redes de área amplia
Redes de área local
Redes inalámbricas
Redes de área metropolitana

PARTE II - Comunicaciones de datos
Capítulo 3. Transmisión de datos
3.1. Conceptos y terminología
Terminología utilizada en transmisión de datos
Frecuencia, espectro y ancho de banda
3.2. Transmisión de datos analógicos y digitales
Datos analógicos y digitales
Señales analógicas y digitales
Transmisión analógica y digital
3.3. Dificultades en la transmisión
Atenuación
Distorsión de retardo
Ruido





![638](../imagenes/Pasted%20image%2020260804142827.png)

Stallings nos explica que el objetivo de cualquier sistema de comunicacion es, sencillamente, intercambiar informacion entre dos entidades. Para que esto suceda, necesitamos 5 elementos claves.

* **La fuente**: Es el dispositivo que genera los datos que queres mandar (tu cerebro pensando un mensaje, tu telefono o pc).
* **El transmisor:** Agarra esos datos de la fuente y los transforma/codifica en señales electromagneticas que si pueden viajar por el canal fisico. Ejemplo, un modem, agarra bit de la compu y los traduce a ondas de sonido o luz.
* **El sistema de transmision:** El camino por donde viaja la señal. Puede ser algo tan simple como un cable de cobre tirado en tu habitacion, o un quilombo re complejo de redes interconectadas que cruzan el oceano.
* **El receptor:** Hace el trabajo inverso al transmisor. Agarra la señal del canal (que suele venir distorsionada) y la vuelve a convertir en datos que la maquina del otro lado entienda.
* **El destino:** El dispositivo final que recibe y procesa la informacion. Ejemplo: la pantalla de tu amigo que recibe tu mensaje.

Aunque todo esto parezca muy sencillo, en realidad no es tan asi, es mas complejo. Para hacerse una idea, la imagen siguiente lista algunas de las tareas claves que se deber realizar en un sistema de comunicaciones (es medio arbitrario ya que se pueden añadir elementos, mezclar items etc)


![704](../imagenes/Pasted%20image%2020260804145215.png)

-  **Utilizacion del sistema de transmision:** Se refiere al uso eficiente de los medios fisicos compartidos, lo cual suele requerir tecnicas de multiplexacion (compartir canal) y mecanismos de control de congestion para garantizar que la red no se sature por una demanda excesiva de datos.
- **Implementacion de la interfaz:** Es el puente o conexion fisica necesarioa para que un dispositivo de hardware pueda acoplarse e interactuar con el medio de transmision.
- **Generacion de la señal:** Una vez que la interfaz esta conectada, los datos deben convertirse en señales electromagneticas reales. Las caracteristicas de esta señal (forma, intensidad, voltaje) deben ser precisas para que logren propagarse por el medio fisico y el receptor pueda interpretarlas como datos validos.
- **Sincronizacion:** Es la coordinaccion de tiempos entre emisor y receptor. El receptor necesita saber exactamente cuando comienza y termina una señal, y cual es la duracion exacta de cada elemento de señal o bit para decodificar el mensaje sin errores.
- **Gestion del intercambio:** Establece las convenciones de cooperacion cuando dos entidades se comunican durante un periodo de tiempo. Esto incluye definir si transmiten al mismo tiempo (Full-Duplex) o por turnos (Half-Duplex), y acordar la cantidad de datos que se enviaran.
- **Detección y corrección de errores:** Son los mecanismos preparados para identificar alteraciones en la señal provocadas por el ruido del canal. Si ocurre una contingencia, el sistema debe saber detectarla y reaccionar (ya sea corrigiendo el error matemáticamente o pidiendo una retransmisión).
- **Control de flujo:** Permite al receptor regular la velocidad y la cantidad de datos que le envía el emisor. El objetivo es evitar que una transmisión demasiado rápida sature y desborde la memoria temporal (buffer) del dispositivo que la recibe.
- **Direccionamiento:** Consiste en asignar identificadores únicos a cada componente del sistema (como computadoras o procesos/aplicaciones) para que la red sepa a quién debe entregar exactamente los datos.
- **Encaminamiento (Routing):** Es el proceso mediante el cual se determina y selecciona la ruta más eficiente que deben seguir los datos a través de los nodos de conmutación intermedios para llegar desde la estación de origen hasta el destino final.
- **Recuperación:** Son los procedimientos orientados a retomar y estabilizar la comunicación ante fallos, como la caída de un enlace o un error en la conexión lógica, asegurando que la transmisión se complete.
- **Formato de mensajes:** Es el acuerdo mutuo sobre la estructura de los datos que se van a intercambiar, definiendo la sintaxis, el orden de los bytes y la organización de la información de control dentro de los paquetes.
- **Seguridad:** Incluye las políticas y herramientas (como el cifrado y la autenticación) diseñadas para proteger la confidencialidad de la información y asegurar la infraestructura de la red frente a monitoreos o accesos no autorizados.
- **Gestión de red:** Engloba las tareas de administración global necesarias para configurar el sistema, monitorizar su estado en tiempo real, reaccionar ante fallos y planificar el crecimiento futuro de la infraestructura de comunicaciones.

# Redes de transmision de datos

**Redes de area amplea (WAN) :** Generalmente, se considera como redes de area amplia a todas aquellas que cubren una extensa area geografica y utilizan circuitos proporcionados por una entidad proveedora de servicios de telecomunicacion. Generalmente, una WAN consistse en una serie de dispositivos de conmutacion interconectados. La transmision generada por cualquier dispositivo se encaminara a traves de estos nodos internos hasta alcanzar el destino.
Las WAN se han implementado usando una de las dos tecnologías siguientes: conmutación de circuitos y conmutación de paquetes. Últimamente, se está empleando como solución la técnica de retransmisión de tramas ( frame relay), así como las redes ATM.

**Conmutacion de circuitos :**   Es un mecanismo esencial en las redes de comunicaciones que permite la entrega de datos cuando el origen y el destino no están directamente conectados. En las redes de conmutación de circuitos, para interconectar dos estaciones se establece un camino dedicado a través de los nodos de la red. El camino es una secuencia conectada de enlaces físicos entre nodos. En cada enlace, se dedica un canal lógico a cada conexión. Los datos generados por la estación fuente se transmiten por el camino dedicado tan rápido como se pueda. En cada nodo, los datos de entrada se encaminan o conmutan por el canal apropiado de salida sin retardos. El ejemplo más ilustrativo de la conmutación de circuitos es la red de telefonía.

**Conmutacion de paquetes :**  La conmutación de paquetes es un método utilizado en las redes de datos, donde la información se divide en unidades más pequeñas llamadas paquetes. Cada paquete se pasa de nodo en nodo en la red siguiendo algún camino entre la estación origen y la destino. En cada nodo, el paquete se recibe completamente, se almace- na durante un breve intervalo y posteriormente se retransmite al siguiente nodo. En la conmutación de paquetes, los paquetes se transmiten de manera independiente a través de la red y pueden seguir diferentes rutas hacia su destino

- **Conmutacion de paquetes vs Conmutacion de circuitos :** 
	- En el método de conmutación de circuitos, un mensaje se recibe en el mismo orden que se envió desde el origen, mientras que en el método de conmutación de paquetes, los mensajes se reciben desordenados y se ensamblan en el destino.
	- La conmutación de circuitos necesita una ruta dedicada entre el origen y el destino antes de que comience la transferencia de datos, pero la conmutación de paquetes no necesita una ruta dedicada desde el origen al destino.
	- El método de conmutación de circuitos se implementa en la capa física, mientras que la conmutación de paquetes se implementa en la capa de red.

**Retransmision de tramas (frame relay) :** es un mecanismo de control de errores que opera principalmente en la capa de enlace de datos (Capa 2 del modelo OSI) para garantizar la entrega confiable de datos sobre un canal físico propenso a errores. Mientras que las redes originales de conmutación de paquetes se diseña- ron para ofrecer una velocidad de transmisión al usuario final de 64 kbps, las redes con retransmisión de tramas están diseñadas para operar eficazmente a velocidades de transmisión de usuario de hasta 2 Mbps. La clave para conseguir estas velocidades reside en eliminar la mayor parte de la información redundante usada para el control de errores y, en consecuencia, el procesamiento asociado.

**ATM :** El Modo de Transferencia Asíncrono (ATM, Asynchronous Transfer Mode), a veces denominado como modo de retransmisión de celdas (cell relay), es la culminación de todos los desarrollos en conmutación de circuitos y conmutación de paquetes. ATM se puede considerar como una evolu- ción de la retransmisión de tramas. La diferencia más obvia entre retransmisión de tramas y ATM es que la primera usa paquetes de longitud variable, llamados «tramas», y ATM usa paquetes de longitud fija denominados «celdas». Al igual que en retransmisión de tramas, ATM introduce poca información adicional para el control de errores, confiando en la inherente robustez del medio de transmisión así como en la lógica adicional localizada en el sistema destino para detectar y corregir errores. Al utilizar paquetes de longitud fija, el esfuerzo adicional de procesamiento se reduce incluso todavía más que en retransmisión de tramas. El resultado es que ATM se ha diseñado para trabajar a velocidades de transmisión del orden de 10 a 100 Mbps, e incluso del orden de Gbps. 
ATM se puede considerar, a su vez, como una evolución de la conmutación de circuitos. En la conmutación de circuitos se dispone solamente de circuitos a velocidad fija de transmisión entre los sistemas finales. ATM permite la definición de múltiples canales virtuales con velocidades de transmisión que se definen dinámicamente en el instante en el que se crea el canal virtual. Al utilizar celdas de tamaño fijo, ATM es tan eficaz que puede ofrecer un canal a velocidad de transmisión constante aunque esté usando una técnica de conmutación de paquetes. Por tanto, en este sentido, ATM es una generalización de la conmutación de circuitos en la que se ofrecen varios canales, en los que la velocidad de transmisión se fija dinámicamente para cada canal según las necesidades.

**Redes de area local (LAN)** : 