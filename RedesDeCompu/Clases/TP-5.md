a)​ ¿Qué significa "establecer una conexión"? ¿Dónde "existe" una conexión TCP: en los cables, en los routers o en los extremos?

Establecer conexion es cuando dos aplicaciones o procesos en la red acuerdan comunicarse de manera coordinada. Para ello, sincronizan sus parametros iniciales mediante un intercambio formal de mensajes (three way handshake con paquetes SYN, SYN-ACK y ACK).
La conexion TCP existe unicamente en los extremos (los hosts de origen y destino). La estructura y el estado de la conexion existen dentro de la memoria y la pila del sistema operativo de los equipos que se comunican.
En los cables y en los routers la conexion no existe.

b)​ ¿Qué es un puerto? ¿Qué identifica el par (IP, puerto)?
Un puerto es una abstraccion numerida de 16 bits, con valores del 1 al 65535 en la capa de transporte (TCP/UDP) que sirve como punto final logico para direccionar datos hacia una aplicacion o proceso.
El par (IP, puerto) se conoce como socket. Identifica de manera univoca a una aplicacion/proceso activo en la red dentro de un host especifico.

c) ¿Qué significa que un proceso esté "escuchando" en un puerto?
Significa que un programa ha reservado un puerto en la interfaz de red mediante una llamada al sistema y se encuentra en un estado pasivo, a la espera de que lleguen solicitudes de conexion o paquetes dirigidos a ese numero de puerto especifico.
