
1)
**a) 
Investigar como se clasifican las redes segun su alcance. Mencionar brevemente las caracteristicas de cada una y colocar en cada cuadro de la figura el acronimo de red que corresponda.**

* **Red de area personal (PAN):*** Es una red informatica de pocos metros, algo parecido a la distancia que necesita el bluetooth del celular para intercambiar datos, Sirven para espacios reducidos
* **Red de area local (LAN):** Abarcan desde los 200 metros hasta 1 kilometro de cobertura, suele instalarse en la mayoria de lasa empresas y permite conectar ordenadores, impresoras, escaneres, fotocopiadoras y mas perifericos.
* **Red de area de campus (CAN):** Abarca un área geográfica delimitada mayor que una LAN pero menor que una ciudad, como el predio de una universidad, un hospital o una base militar. Se encarga de interconectar múltiples redes LAN dentro de ese espacio.
* **Red de area metropolitana (MAN):** Su alcance se ubica entre las redes LAN y las WAN. Están diseñadas para proporcionar gran capacidad a coste reducido en áreas relativamente grandes, interconectando distintas ubicaciones dentro de una misma ciudad o municipio.
* **Red de area amplia (WAN):** Tienen un alcance geográfico extenso, cruzando ciudades, países o continentes. Interconectan múltiples redes locales o metropolitanas a través de grandes distancias utilizando tecnologías como conmutación de circuitos o paquetes. Internet es el ejemplo más representativo de una red WAN.

**b) ¿Que es una VLAN? ¿Como se clasifican?**
Una VLAN es una agrupacion logica de dispositivos dentro de una o mas redes fisicas (LAN) que permite que estos nodos se comuniquen entre si como si estuvieran conectados al mismo switch, sin importar su ubicacion fisica real.
Las VLANs se dividen tradicionalmente en 3 categorias principales:
* **VLAN basadas en puertos:** El administrador de la red asigna manualmente cada puerto fisico del switch a una red especifica
* **VLAN basadas en direcciones MAC:** La pertenencia a la red virtual se basa en la direccion fisica unica (MAC) de la tarjeta de red del dispositivo
* **VLAN basadas en protocolo o red:** El switch inspecciona la carga de los datos para determinar la pertenencia a la VLAN basándose en el tipo de protocolo que el dispositivo está utilizando o según la dirección IP lógica del equipo, enrutando el tráfico de manera más inteligente.

**c) Investigar y resumir el protocolo IEEE 802.1Q. ¿Como se relaciona con las VLAN?**
El estándar IEEE 802.1Q, comúnmente conocido como Dot1q, es el protocolo fundamental que permite la implementación de múltiples Redes de Área Local Virtuales (VLAN) sobre una misma infraestructura física Ethernet. Su función principal es modificar las tramas de datos estándar para incluir información que indique a qué red virtual pertenece cada paquete antes de que viaje a través de enlaces compartidos.

La relación entre el protocolo 802.1Q y las redes virtuales es de absoluta dependencia en arquitecturas de red complejas. Mientras que el concepto de VLAN existe para separar lógicamente los departamentos dentro de un mismo switch, el 802.1Q es la herramienta técnica que permite expandir esa separación a través de todo un edificio o campus.

- **Enlaces Troncales (trunks):** El protocolo 802.1Q añade una etiqueta a los paquetes para que el tráfico de múltiples VLANs pueda viajar mezclado a través de un único cable entre switches.
- **Aislamiento:** El switch de destino lee la etiqueta para identificar la red, la borra y entrega el paquete exclusivamente a los puertos correspondientes, garantizando que las redes permanezcan separadas e invisibles entre sí.

**d) En el contexto de los dos items anteriores ¿Que es el tagging?**
El Tagging es el proceso mediante el cual un switch inserta una marca digital (la etiqueta 802.1Q) dentro de un paquete de datos común antes de enviarlo por un cable compartido (enlace troncal).

- **Propósito:** Al tener varias redes virtuales viajando por un mismo cable físico, el tagging actúa como una "matrícula" que le indica al switch receptor exactamente a qué VLAN pertenece cada paquete para que no se mezclen.
- **Resolución:** Una vez que el switch de destino lee esta etiqueta, la recorta para devolver el paquete a su estado original y lo entrega únicamente a los puertos que pertenecen a esa VLAN específica, garantizando el aislamiento.

2)

![697](../imagenes/Pasted%20image%2020260919172914.png)

Primero hay que entender que el sistema operativo de cisco se divide en niveles de acceso, cada uno con mas permisos que el anterior. El modo Usuario (>) solo permite ver informacion basica, mientras que el Privilegiado (#) permite ver toda la configuracion y el modo Global ( (config)# ) permite modificar los parametros de funcionamiento del equipo

Para configurar la tabla de ruteo que nos dieron, en cada pc abrimos la configuracion de IP y ahi reemplazamos con los datos dados

a)​ Desde cada computadora, ingresar a la terminal y configurar los switch. Nombrar a los mismos sw1 y sw2 respectivamente.

Para la PC-A
![](../imagenes/Pasted%20image%2020260919173822.png)

Para la PC-B
![](../imagenes/Pasted%20image%2020260919173929.png)

b)​ Asignar contraseñas privilegiadas, de consola y vty. 

Las líneas **VTY** (del inglés _Virtual Teletype_ o **Teletipo Virtual**) son **interfaces de línea de comando (CLI) lógicas y virtuales** en dispositivos de red como enrutadores y switches, utilizadas para la **administración remota** mediante protocolos como **Telnet** o **SSH**.

Para la PC-A
![](../imagenes/Pasted%20image%2020260919174414.png)

Para la PC-B
![](../imagenes/Pasted%20image%2020260919174803.png)


c)​ Encriptar las contraseñas (Ayuda: utilizar service password-encryption)

Para PC-B
![](../imagenes/Pasted%20image%2020260919175411.png)

d)​ Configurar las redes VLAN para ambos switch según la tabla de direcciones provista. 

Para PC-A , SW1
![](../imagenes/Pasted%20image%2020260919202647.png)

Para PC-B , SW2
![](../imagenes/Pasted%20image%2020260919202901.png)

e)​ Desconectar todas las interfaces que no estén siendo utilizadas (Ayuda: podés ver las interfaces utilizando show ip interface brief)

PC-A antes de desconectar las interfaces no usadas
![457](../imagenes/Pasted%20image%2020260919203232.png)

Desconectamos en un rango 
![477](../imagenes/Pasted%20image%2020260919203545.png)

Luego queda
![468](../imagenes/Pasted%20image%2020260919203607.png)

PC-B antes de desconectar
![473](../imagenes/Pasted%20image%2020260919203739.png)

Despues de desconectar
![496](../imagenes/Pasted%20image%2020260919203916.png)


f)​ Guardar la configuración (write memory)
PC-B
![](../imagenes/Pasted%20image%2020260919204055.png)

PC-A
![](../imagenes/Pasted%20image%2020260919204116.png)

g)​ Testear comunicación usando pings entre las computadoras.

Ping de PC-A hacia PC-B
![](../imagenes/Pasted%20image%2020260919204327.png)

Ping de PC-B hacia PC-A
![](../imagenes/Pasted%20image%2020260919204415.png)

h)​ Crear VLANs en ambos switches.

PC-A
![](../imagenes/Pasted%20image%2020260919204701.png)

PC-B
![](../imagenes/Pasted%20image%2020260919204755.png)

i)​ Utilizar show vlan brief para visualizar la lista de VLANs en alguno de los switch. ¿Cuál es la VLAN utilizada por defecto?. Colocar el output en el informe.

PC-A
![](../imagenes/Pasted%20image%2020260919221552.png)
La respuesta es la **VLAN 1** lleva el nombre `default` y es la que agrupa absolutamente todos los puertos físicos del switch de fábrica (desde el Fa0/1 hasta el Gig0/2).


j)​ Asignar la PC-A a la VLAN Laboratorio.

![572](../imagenes/Pasted%20image%2020260919221924.png)

k)​ Desde la VLAN 1, remover la ip de Management y configurarla para funcionar en la VLAN 99
	
![](../imagenes/Pasted%20image%2020260919222232.png)

l)​ Verificar el estado de la VLAN utilizando show vlan brief y el estado de las interfaces
utilizando show ip interface brief. Colocar los output en el informe e interpretar.
![444](../imagenes/Pasted%20image%2020260919222352.png)
En el primer output vemos que el puerto F0/6 ya no esta en VLAN 1, si no que aparece en VLAN 10 (laboratorio).
En el segundo ouput me olvide de sacar captura a la parte de abajo donde dice "More" pero si aparecia que la interfaz vlan1 ya no tiene una ip asignada, mientras que la vlan99 tiene la ip 192.168.1.11 y su estado dice up

m)​ Asignar la PC-B a la VLAN Laboratorio en el sw2. Repetir el inciso k) pero para el sw2.
![](../imagenes/Pasted%20image%2020260919224247.png)

n)​ Verificar la conectividad entre PC-A y PC-B utilizando pings. Verificar conectividad entre sw1 y sw2 utilizando pings. Interpretar los resultados

Ponemos que configurar el perto como trunk 
![](../imagenes/Pasted%20image%2020260919224431.png)

Ping de pc-a a pc-b
![](../imagenes/Pasted%20image%2020260919224528.png)

Ping del SW2 hacia SW1
![](../imagenes/Pasted%20image%2020260919225310.png)
El resultado exitoso del comando ping demuestra que existe conectividad de extremo a extremo entre la PC-A y la PC-B. Aunque el resultado visual es idéntico al del inciso 'g', este ping valida que la nueva arquitectura lógica funciona correctamente.

3)
Vamos a simular una red LAN a bordo de una aeronave segmentada en tres clases (Turista, Business y Administracion), utilizando tecnicas de enrutamiento inter-VLAN y configuraciones de VLANs, NAT para la salida del internet y listas de control de acceso para la seguridad (ACL).
![538](../imagenes/Pasted%20image%2020260925000103.png)

**CONFIGURACION DEL SWITCH 1**
Aca creamos las VLANs 10, 20 y 99 en el switch central con sus respectivos puertos.
![432](../imagenes/Pasted%20image%2020260924235823.png)

**CONFIGURACION DEL ROUTER AVION**
Se configuró el dispositivo principal implementando enrutamiento Inter-VLAN (Router-on-a-Stick) con subinterfaces y encapsulación 802.1Q para segmentar las redes Turista, Business y Administración. Para proveer conectividad a Internet, se estableció una ruta estática hacia el ISP y se activó NAT con sobrecarga (PAT) en la interfaz externa. Finalmente, se aseguró la red aplicando una Lista de Control de Acceso (ACL) en la subinterfaz Turista, restringiendo su comunicación exclusivamente al servidor local y bloqueando cualquier otro destino.
![494](../imagenes/Pasted%20image%2020260924235731.png)


CONFIGURACION ROUTER ISP
Se configuró el Router ISP inicializando y direccionando sus interfaces físicas. Se asignó la dirección IP `200.0.0.2` (máscara /30) a la interfaz `g0/1` para establecer el enlace WAN directo con el router de la aeronave. Posteriormente, se configuró la interfaz `g0/0` con la IP `8.8.8.1` (máscara /24) para dar conectividad a la red que simula Internet, activando ambos puertos exitosamente mediante el comando no shutdown.
![517](../imagenes/Pasted%20image%2020260924235927.png)

ping desde turista hacia el server entretenimiento 

![](../imagenes/Pasted%20image%2020260925000758.png)

![504](../imagenes/Pasted%20image%2020260925001307.png)


No nos deja hacer ping, justo lo que queriamos (pc turista)
![](../imagenes/Pasted%20image%2020260925001356.png)

Desde pc businees
![513](../imagenes/Pasted%20image%2020260925001911.png)

ping a internet desde business
![](../imagenes/Pasted%20image%2020260925002041.png)

Tuve que cambiar la configuracion del router avion porque no habia ping entre pc admin y las demas
![498](../imagenes/Pasted%20image%2020260925002756.png)

ping desde admin hacia las otras PCs e internet
![317](../imagenes/Pasted%20image%2020260925002933.png)