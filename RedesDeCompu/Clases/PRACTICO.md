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



