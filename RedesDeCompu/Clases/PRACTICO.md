![](../imagenes/Pasted%20image%2020260813190810.png)
formula del efecto doppler


nosotros podemos mandar una  serie de bits asi 010100101000101001000 y pueden ser infinitos, pero nosotros solo quisimos mandar 8bits, para eso se crean los protocolos que sirven para tener un bit de inicio y un bit final


ultimo ejercicio 
![](../imagenes/Pasted%20image%2020260813194851.png)


# Clase practica del 20
vimos protocolos TCP, UDP, QUIC, MATT, SSH, FTP, HTTP

# Clase del 27
presento el TP3 y se entrega el 

usamos udp par ahacer transmisiones de videos o cosas asi ya que puede habre perdida de paquetes
y perdemos paquetes porque cuando mando paquetes a una direccion, no puedo saber la capacidad de ese canal, o sea que saturo el canal y pierdo los paquetes

# Clase del 10 sept

## Overview

Repaso de conceptos fundamentales sobre redes de área local (LAN) y su evolución en tamaño (MAN, WAN), junto con la configuración práctica en Packet Tracer. La clase se centró fuertemente en el entendimiento y configuración de Redes de Área Local Virtuales (VLANs), el funcionamiento de máscaras de subred, puertas de enlace predeterminadas (default gateways), y el uso del software de simulación para modelar casos de uso de la vida real.

## Puntos clave técnicos tratados

- Las redes se clasifican por su tamaño geográfico, escalando de LAN a CAN, MAN y finalmente WAN, siendo esta última la internet misma.
    
- La máscara de subred utiliza una operación lógica para determinar si una dirección IP de destino se encuentra dentro de la misma red local o si el paquete debe ser derivado hacia afuera a través del enrutador.
    
- El "Default Gateway" es la dirección IP del enrutador de salida por el cual un dispositivo enviará el tráfico destinado a redes externas.
    
- Los servidores DNS se encargan de traducir nombres de dominio en strings legibles a direcciones IP, simplificando la memorización para los usuarios.
    
- Las VLANs permiten dividir lógicamente un único switch físico en múltiples redes virtuales aisladas, ahorrando los costos de adquirir hardware adicional.
    
- El protocolo 802.1Q modifica el paquete agregando una etiqueta (tag) de 32 bits, la cual incluye un identificador de VLAN de 12 bits que soporta la creación de hasta 4000 redes virtuales en un switch.
    
- Los switches pueden realizar comunicaciones "unicast" (dirigidas a un solo dispositivo) o "broadcast" (dirigidas a todos los dispositivos en la misma VLAN para, por ejemplo, averiguar quién posee una IP específica).
    

## Ejemplos prácticos revisados en clase

- Conexión simulada en Packet Tracer utilizando un cable de consola desde una laptop al puerto serie del switch, accediendo mediante la terminal para ejecutar comandos de configuración de administrador (`enable`, cambio de nombre, contraseñas, asignación de puertos).
    
- Simulación de la red interna de un avión: división en distintas clases de pasajeros mediante VLANs para asignar privilegios y servicios específicos.
    
- Creación de un servidor HTTP en la red del avión para proveer una página HTML de entretenimiento a bordo, limitando el acceso a internet únicamente a la primera clase y a los administradores del sistema.
    

## Preguntas y clarificaciones destacadas (con respuestas)

- ¿Las computadoras de la red necesitan conocer a qué VLAN pertenecen? — No, a las computadoras no les interesa la etiqueta VLAN; el etiquetado del puerto es un trabajo de segmentación que se realiza por software íntegramente en el switch.
    
- ¿Qué ocurre si una computadora tiene más de una interfaz de red? — Se puede configurar cuál de las interfaces actuará como el "Default Gateway" principal para tratar de enrutar la salida a internet.
    

## Decisiones y acción a seguir

- Metodología de calificación de trabajos prácticos: El docente parte de una nota de 10, restando puntos por mala organización en repositorios compartidos (commits únicos masivos, un solo miembro aportando) y sumando puntos por creatividad y reflexión propia.
    
- El servidor montado por el profesor para las pruebas de los alumnos se dará de baja la próxima semana para frenar el consumo en su tarjeta de crédito personal.
    

## Tareas y responsables

- Profesor (Santiago Martin Henn): Enviar un correo electrónico avisando sobre la inminente baja del servidor y continuar cargando las calificaciones del laboratorio número uno para quienes ya entregaron.
    
- Alumnos: Apurar la interacción y terminar las pruebas en el servidor antes de que sea apagado, además de resolver el ejercicio del avión en Packet Tracer aplicando los comandos compartidos.
    

## Observaciones finales

- Es altamente recomendable utilizar VLANs con números distintos a 1 (asignada por defecto a todo) y evitar los extremos 0 y 4095 para prevenir problemas de configuración.
    
- Para enriquecer visualmente el modelo práctico en Packet Tracer, se sugirió cargar una imagen física de un avión como fondo y posicionar las computadoras en los asientos correspondientes.





# TP 4

2)
![](../imagenes/Pasted%20image%2020260919172914.png)

Primero hay que entender que el sistema operativo de cisco se divide en niveles de acceso, cada uno con mas permisos que el anterior. El modo Usuario (>) solo permite ver informacion basica, mientras que el Privilegiado (#) permite ver toda la configuracion y el modo Global ( (config)# ) permite modificar los parametros de funcionamiento del equipo

Para configurar la tabla de ruteo que nos dieron, en cada pc abrimos la configuracion de IP y ahi reemplazamos con los datos dados

a)​ Desde cada computadora, ingresar a la terminal y configurar los switch. Nombrar a los mismos sw1 y sw2 respectivamente. Ayuda: investigar los comandos necesarios online si no te acordás, por ejemplo, para cambiar el nombre del switch:​

Para la PC-A
![](../imagenes/Pasted%20image%2020260919173822.png)
Para la PC-B
![](../imagenes/Pasted%20image%2020260919173929.png)

b)​ Asignar contraseñas privilegiadas, de consola y vty. Ayuda:​
​
	enable secret contrasena_exec
	line console 0
	password contrasena_consola
	login
	exit
	line vty 0 15
	password contrasena_vty
	login
	exit
Las líneas **VTY** (del inglés _Virtual Teletype_ o **Teletipo Virtual**) son **interfaces de línea de comando (CLI) lógicas y virtuales** en dispositivos de red como enrutadores y switches, utilizadas para la **administración remota** mediante protocolos como **Telnet** o **SSH**.

Para la PC-A
![](../imagenes/Pasted%20image%2020260919174414.png)

Para la PC-B
![](../imagenes/Pasted%20image%2020260919174803.png)


c)​ Encriptar las contraseñas (Ayuda: utilizar service password-encryption)
Para PC-B
![](../imagenes/Pasted%20image%2020260919175411.png)

d)​ Configurar las redes VLAN para ambos switch según la tabla de direcciones provista. Ayuda:​
​
	interface vlan 1
	ip address <IP_address> <subnet_mask>
	no shutdown
	exit​
Para PC-A , SW1
![](../imagenes/Pasted%20image%2020260919202647.png)

Para PC-B , SW2
![](../imagenes/Pasted%20image%2020260919202901.png)

e)​ Desconectar todas las interfaces que no estén siendo utilizadas (Ayuda: podés ver las interfaces utilizando show ip interface brief)

PC-A antes de desconectar las interfaces no usadas
![](../imagenes/Pasted%20image%2020260919203232.png)
Desconectamos en un rango 
![](../imagenes/Pasted%20image%2020260919203545.png)

Luego queda
![](../imagenes/Pasted%20image%2020260919203607.png)

PC-B antes de desconectar
![](../imagenes/Pasted%20image%2020260919203739.png)

Despues de desconectar
![](../imagenes/Pasted%20image%2020260919203916.png)


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
h)​ Crear VLANs en ambos switches. Ayuda:
	sw1(config)# vlan 10 ​
	sw1(config-vlan)# name Laboratorio
	sw1(config-vlan)# vlan 20
	sw1(config-vlan)# name Bar
	sw1(config-vlan)# vlan 99
	sw1(config-vlan)# name Management
	sw1(config-vlan)# end
	2Trabajo Práctico N°4

PC-A
![](../imagenes/Pasted%20image%2020260919204701.png)

PC-B
![](../imagenes/Pasted%20image%2020260919204755.png)

i)​ Utilizar show vlan brief para visualizar la lista de VLANs en alguno de los switch. ¿Cuál es la VLAN utilizada por defecto?. Colocar el output en el informe.

PC-A
![](../imagenes/Pasted%20image%2020260919221552.png)
La respuesta es la **VLAN 1**. Como puedes observar en tu propia captura, lleva el nombre `default` y es la que agrupa absolutamente todos los puertos físicos del switch de fábrica (desde el Fa0/1 hasta el Gig0/2).

Además, el output confirma que creaste correctamente las VLANs 10 (laboratorio), 20 (bar) y 99 (Management). Es completamente normal que en este punto aparezcan sin ningún puerto asignado a la derecha, ya que esa es justamente la tarea del siguiente inciso.


j)​ Asignar la PC-A a la VLAN Laboratorio. Ayuda:
	sw1(config)# interface f0/6
	sw1(config-if)# switchport mode access
	sw1(config-if)# switchport access vlan 10
![](../imagenes/Pasted%20image%2020260919221924.png)

k)​ Desde la VLAN 1, remover la ip de Management y configurarla para funcionar en la VLAN 99 (que configuramos como Management). Ayuda:
	sw1(config)# interface vlan 1
	sw1(config-if)# no ip address
	sw1(config-if)# interface vlan 99
	sw1(config-if)# ip address IP MASCARA
	sw1(config-if)# end
![](../imagenes/Pasted%20image%2020260919222232.png)

l)​ Verificar el estado de la VLAN utilizando show vlan brief y el estado de las interfaces
utilizando show ip interface brief. Colocar los output en el informe e interpretar.
![](../imagenes/Pasted%20image%2020260919222352.png)

m)​ Asignar la PC-B a la VLAN Laboratorio en el sw2. Repetir el inciso k) pero para el sw2.
![](../imagenes/Pasted%20image%2020260919224247.png)

n)​ Verificar la conectividad entre PC-A y PC-B utilizando pings. Verificar conectividad entre sw1 y sw2 utilizando pings. Interpretar los resultados

Ponemos que configurar el perto como trunk 
![](../imagenes/Pasted%20image%2020260919224431.png)

Ping de pc-a a pc-b
![](../imagenes/Pasted%20image%2020260919224528.png)

Ping del SW2 hacia SW1
![](../imagenes/Pasted%20image%2020260919225310.png)