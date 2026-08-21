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



==**Clase teorica del 10**==

# Espectro electromagnetico:
Es el conjunto de todas las frecuencias posibles a las que se produce radiacion electromagnetica
![488](../../Pasted%20image%2020260821113822.png)

- **Espectro de una señal**: rango de frecuencias que contiene.
- **Ancho de banda**: anchura del espectro, es decir, la diferencia entre la frecuencia más alta y la más baja con energía significativa.

# Tipos de transmision:

**Asincronos:** Los datos se transmiten enviando un caracter a la vez, con un metodo de inicio/parada. Los datos se transmiten a intervalos irregulares conforme se necesitan. Los bits de arranque/parada se agregan al inicio y al final de cada mensaje. La transmision asincrona es mas apropiada para la comunicacion de datos que comprende dispositivos de entrada/salida de baja velocidad, ejemplo: impresoras en serie.

**Sincronos:** La transmision es continua, los caracteres se envian uno tras otro por las lineas sin interrupcion. La transmision sincrona es mucho mas rapida debido a que no se tienen que enviar señales adicionales por las lineas para cada uno de los caracteres. La fuente y el destino operan con una sincronizacion para permitir la transmision de datos de alta velocidad. Este tipo de transmision no necesita bits de arranque/parada.

# Tecnicas de transmision de datos:

**Banda base:** La banda base es un tipo de transmisión digital en la que la señal se transmite sin modular y codificada. En ella no se permite la multiplexación en frecuencia. Se usa todo el ancho de banda que ofrece el sistema, todo un canal. Su problema es que las distancias máximas de empleo son de pocos kilómetros usando repetidores, y su ventaja es el ahorro de los aparatos de modulación/demodulación. Se emplea, por tanto, para cortas distancias debido a su bajo coste.

**Banda ancha:** La banda ancha es un tipo de transmisión de señales analógicas moduladas y multiplexadas en frecuencias. Cada canal lógico transporta información diferenciada sobre datos, sonido y video. Se puede transmitir a distintas velocidades en cada canal, con distancias elevadas de varias decenas de kilómetros y posible uso de amplificadores para aumentar la longitud total. Se necesita una ruta para transmitir y otra para recibir datos.
- Mid-split: Divide el canal en dos rangos de frecuencia, uno para transmitir y otro para recibir
- Dual-cable: Se utilizan dos cables diferentes, uno para transmitir y el otro para recibir informacion

# Dificultades en la transmision
Ninguna señal llega al receptor exactamente igual a como fue transmitida. Las tres causas principales de deterioro son:

**Atenuación**
La señal pierde intensidad (potencia) a medida que recorre el medio. Consideraciones prácticas:
- La señal recibida debe tener suficiente potencia para que el receptor la pueda detectar e interpretar correctamente.
- Debe mantenerse **suficientemente por encima del ruido** para que se reciba sin error.
- La atenuación es **mayor a frecuencias más altas**, lo que provoca **distorsión** (porque las distintas componentes de frecuencia de una señal compuesta se atenúan de forma desigual).

**Distorsión de retardo (delay distortion)**
Ocurre solo en medios **guiados** (cable, fibra), y es consecuencia de que **la velocidad de propagación de una señal varía con la frecuencia**. Esto tiene un efecto muy concreto: las distintas componentes de frecuencia de una señal llegan al receptor en **instantes ligeramente distintos**, aunque hayan sido enviadas al mismo tiempo.
Consecuencia práctica: en transmisión digital, esto provoca que parte de la energía de un bit se "derrame" sobre bits vecinos, generando **interferencia intersímbolo (ISI)**, uno de los factores limitantes principales de la tasa de bits que un canal puede soportar de forma confiable.

**Ruido**
- **Ruido térmico**: causado por la agitación térmica de los electrones en cualquier conductor; está presente en todos los dispositivos y medios de transmisión, es función de la temperatura, y se distribuye uniformemente en el espectro (por eso también se llama _ruido blanco_). No se puede eliminar, marca un límite físico inferior al desempeño del sistema.
- **Ruido de intermodulación**: aparece cuando señales de distintas frecuencias comparten el mismo medio y producen señales espurias en frecuencias suma/diferencia de las originales (o sus múltiplos), típicamente por no linealidades del transmisor/receptor o del medio.
- **Diafonía (crosstalk)**: acoplamiento no deseado entre líneas de transmisión cercanas (ej. se escucha una conversación de fondo en otra línea telefónica); también ocurre por acoplamiento electromagnético entre cables próximos.
- **Ruido impulsivo**: pulsos irregulares de corta duración pero alta amplitud, originados por fenómenos externos (descargas eléctricas, fallas en el sistema de comunicación). Es el más disruptivo para **datos digitales**, porque un solo pulso puede corromper varios bits consecutivos, mientras que en voz analógica suele ser tolerable (produce un "clic" o "chasquido" audible).

# Ancho de banda

Es la cantidad de informacion que se puede transmitir por un canal de transmision en un intervalo de tiempo dado, tambien se llama capacidad de canal y se mide en bits por segundo (bps). Una forma de aumentar el ancho de banda en un canal es incrementando el numero de cables paralelos. Otra forma es aumentar la velocidad del paso de informacion por el cable.

# Modulacion digital y multiplexacion

La multiplexión es la técnica que permite que **varias fuentes de datos compartan un mismo medio de transmisión**, aprovechando que la capacidad del medio suele ser mayor que la que necesita un solo usuario.

**Multiplexión por división de frecuencia (FDM – Frequency Division Multiplexing)**
- Se usa cuando el ancho de banda útil del medio excede el ancho de banda requerido por cada señal individual.
- Cada señal se modula sobre una portadora de frecuencia distinta, de modo que el espectro total se divide en canales (bandas de frecuencia) que no se superponen.
- Cada usuario tiene asignado su propio "hueco" de frecuencia y puede transmitir simultáneamente con los demás.
- Se necesitan **bandas de guarda** entre canales para evitar interferencia entre señales adyacentes (evitar solapamiento espectral).
- Ejemplo clásico: transmisión de radio FM, TV por cable analógica, telefonía analógica multicanal.
**Ventaja**: no requiere sincronización temporal entre canales.  
**Desventaja**: susceptible a intermodulación si el medio no es perfectamente lineal.

**Multiplexión por división de tiempo (TDM – Time Division Multiplexing)**
Se aplica a señales digitales (o analógicas convertidas a digital), y en lugar de dividir el espectro, se divide el tiempo de uso del medio en intervalos (slots).

**a) TDM síncrono (TDM síncrona / síncrona)**
- El medio se organiza en **tramas (frames)**, y cada trama se divide en un número fijo de **slots de tiempo**, uno por cada fuente/canal.
- Cada fuente tiene **su slot reservado**, se use o no en ese instante — si una fuente no tiene datos, ese slot se transmite vacío.
- Requiere que la **tasa de datos del medio sea mayor o igual a la suma de las tasas de todas las fuentes**.
- Ineficiente cuando alguna fuente tiene tráfico intermitente o "en ráfagas" (bursty), porque se desperdician slots.

**b) TDM estadístico (TDM asíncrono / por demanda)**
- Los slots se asignan **dinámicamente**, solo a las fuentes que realmente tienen datos para enviar en ese momento.
- Es más eficiente porque no reserva capacidad fija: aprovecha mejor el ancho de banda cuando el tráfico es variable.
- Como los slots ya no tienen una posición fija predecible, cada slot debe llevar una **etiqueta/dirección** que indique a qué fuente pertenece (a diferencia del síncrono, donde la posición del slot en la trama ya identifica la fuente).
- A cambio de esa eficiencia, se paga un costo de **overhead** (los bits de dirección) y de **complejidad** en el control de acceso.
 
**Multiplexión por división de código (CDM / CDMA – Code Division Multiple Access)**

A diferencia de FDM (que divide frecuencia) y TDM (que divide tiempo), en CDM todos los usuarios transmiten simultáneamente en la misma banda de frecuencia y todo el tiempo, y lo que los distingue es un **código único** asignado a cada uno.
**Ventajas**
- No requiere dividir el espectro en bandas fijas (como FDM) ni sincronizar slots de tiempo (como TDM).
- Es robusto frente a interferencia y ruido de banda angosta, precisamente porque la energía de cada señal está distribuida ("esparcida") sobre un ancho de banda amplio.
- Permite que varios usuarios compartan la misma banda de forma más flexible, típico en sistemas celulares (3G) y en el estándar original de WiFi.

# Medios de transmision
Los medios se agrupan en medios guiados (cables) y medios no guiados (transmision inalambrica)

# Medios guiados:

* ***Par Trenzado**
	Hecho de cables de cobre trenzados por parejas sobre un alma común en el centro, en número de 2/4 pares con aislamiento individual y un aislamiento común exterior. Los conductores son de cobre y tienen un diámetro entre 0,4 y 0,9 mm. Es débil a las perturbaciones, y suele aparecer apantallado. Hay tres tipos de par trenzado: UTP (sin blindaje), STP (con blindaje común), y FTP (blindaje común y otro para cada cable trenzado). Tienen bajo coste y ancho de banda reducido.

-  **Cable Coaxial**
	Formado por dos conductores concéntricos: uno interno de cobre y otro a modo de pantalla. El central es más ancho (1/5 mm). Usado para redes mixtas fibra óptica-cable coaxial. Poco sensible a interferencias. Es caro, rígido y grueso. Existe delgado (RG58) y grueso (RG59). Tiene menor atenuación por unidad de longitud, mayor respuesta en frecuencia, mejor inmunidad frente al ruido, coste más elevado y manejo más difícil.

* **Fibra Óptica**
	Está constituida por dos cilindros coaxiales de silicio de alta pureza, que por medio de la reflexión de la luz logra transmitir la información. Sus características son: transmite luz sin interferencias, gran capacidad de transmisión, no sufre interferencias por campos eléctricos y magnéticos, energía puesta en juego muy baja, gran ancho de banda, diámetro reducido, peso reducido, material totalmente dieléctrico.
	
	Las fibras ópticas se componen de un cilindro material dieléctrico llamado núcleo, rodeado por un revestimiento también dieléctrico con un índice de refracción ligeramente inferior al del núcleo. La forma de propagación de la señal se basa en las propiedades de refracción y reflexión de la luz.


# Medios NO guiados

- **Direccional**: la antena transmisora concentra la energía en un haz estrecho, por lo que transmisor y receptor deben estar alineados con precisión. Se usa en frecuencias más altas (microondas).
- **Omnidireccional**: la señal se dispersa en todas direcciones y puede ser captada por múltiples antenas receptoras. Típica de frecuencias más bajas (ondas de radio).

**Microondas terrestres**
- Antenas típicas: **parabólicas**, con diámetros de alrededor de 3 metros, montadas en torres altas para maximizar la distancia (línea de vista) y evitar obstáculos.
- Frecuencias: rango aproximado de **1 a 40 GHz**; a mayor frecuencia, mayor ancho de banda posible y mayores tasas de datos.
- Requiere **línea de vista** entre transmisor y receptor; por la curvatura terrestre, se necesitan **repetidores/relés** espaciados (históricamente cada ~40-50 km en enlaces intermunicipales), o bien torres muy altas.
- **Aplicación clásica**: enlaces punto a punto de larga distancia (antes de la fibra óptica, era la base de la telefonía de larga distancia), y también enlaces entre edificios como alternativa al cableado.
-  Problema particular: desvanecimiento por múltiples trayectorias (multipath fading) parte de la señal puede llegar al receptor por trayectos reflejados (agua, terreno) además del directo, y estas señales pueden interferir destructivamente entre sí, sobre todo en climas húmedos.
- También es sensible a la **atenuación por lluvia**, más marcada a frecuencias por encima de los 10 GHz.

**Microondas satelitales**
- El satélite actúa como una **estación repetidora** en el espacio: recibe en una frecuencia (**uplink**), amplifica/regenera la señal, y retransmite en otra frecuencia distinta (**downlink**), para evitar que la señal saliente interfiera con la entrante.
- La configuración más común es el **satélite geoestacionario**: orbita a una altura tal (~35.786 km) que su período orbital coincide con el de rotación de la Tierra, por lo que **parece fijo** respecto a un punto de la superficie terrestre. Esto simplifica enormemente el apuntado de las antenas terrestres.
- **Aplicaciones**: difusión de TV, transmisión telefónica de larga distancia, redes privadas de datos, sistemas de posicionamiento (GPS).
- **Limitación importante: retardo de propagación** — como la distancia es enorme, hay un retardo perceptible (~250 ms ida y vuelta para geoestacionarios), lo cual es problemático para aplicaciones interactivas o en tiempo real (ej. telefonía, videoconferencia).
- Comparte las mismas bandas de frecuencia que microondas terrestres, por lo que puede haber **interferencia** entre sistemas satelitales y terrestres si no se coordina bien la ubicación y el apuntado de antenas.

**Ondas de radio (radio broadcast)**
- Rango de frecuencia: aproximadamente **30 MHz a 1 GHz**, cubre buena parte de la banda de VHF y UHF.
- A diferencia de las microondas, las ondas de radio son **omnidireccionales**: no requieren antenas parabólicas alineadas, sino antenas más simples que emiten en todas direcciones.
- Por eso, el transmisor no necesita apuntar físicamente al receptor, lo que facilita la comunicación con receptores múltiples o móviles.
- **Aplicaciones típicas**: radio FM, televisión (VHF/UHF), y comunicaciones que no requieren un enlace punto a punto exclusivo.
- **Problema principal**: al ser omnidireccional, es más susceptible a **interferencia entre transmisores** que usan frecuencias cercanas, por lo que su uso está fuertemente regulado (asignación de bandas por organismos reguladores).
- También está sujeta a **atenuación multitrayecto** por reflexión en edificios, terreno, y la ionósfera (dependiendo de la frecuencia específica).

**Transmision infrarroja**
- Frecuencia: banda de **infrarrojo**, justo por debajo del espectro visible.
- Se genera y detecta mediante **transceptores** (emisores/receptores) que **no requieren licencia** del organismo regulador (a diferencia de radio y microondas), porque no atraviesa objetos sólidos.
- **Característica clave: no puede atravesar paredes ni objetos opacos.** Esto tiene dos consecuencias:
    - **Ventaja de seguridad**: un sistema infrarrojo en una habitación no interfiere con uno en la habitación contigua, ni puede ser interceptado desde afuera del recinto.
    - **Limitación de alcance/uso**: solo sirve para comunicación de **corto alcance y dentro de un mismo ambiente cerrado** (ej. controles remotos, comunicación IrDA entre dispositivos cercanos).
- No sufre interferencia entre sistemas en habitaciones distintas, pero dentro del mismo ambiente puede verse afectado por la **luz solar directa**, que satura el receptor infrarrojo.