# Lab-04-Robotica-2025-2
Laboratorio 4 de Robótica 2025-2s, realizado por Jeison Diaz y Mateo Ramos

# Integrantes
1. Jeison Nicolás Diaz Arciniegas [jediazar@unal.co](JeisonD0819)
2. Mateo Ramos Cujer [mramoscu@unal.edu.co](MateoKGR)

# Informe

Indice:
1. [Objetivos](#objetivos)
2. [Procedimientos realizados](#procedimientos_realizados)
3. [Funcionamiento general y decisiones de diseño](#funcionamiento_y_diseño)
4. [Diagrama de flujo](#diagrama_de_flujo)
5. [Conclusiones](#conclusiones)


## Objetivos

- Familiarizarse con la arquitectura de ROS2 (nodos, tópicos, mensajes y servicios).
- Implementar un nodo en Python capaz de publicar mensajes del tipo `Twist` para controlar el simulador *turtlesim*.
- Utilizar el servicio `/reset` de turtlesim para reiniciar la posición y orientación de la tortuga.
- Desarrollar un sistema de lectura de teclado en tiempo real que permita el control manual de la tortuga (flechas) y la ejecución de trayectorias predefinidas para dibujar letras.
- Consolidar en un mismo programa la interacción entre tópicos, servicios y entrada del usuario.

## Procedimientos realizados

Para desarrollar este laboratorio comenzamos por preparar todo el entorno de trabajo necesario para ejecutar ROS2 Humble. Primero instalamos **Ubuntu 22.04**, ya que es la distribución compatible con esta versión de ROS. Una vez dentro del sistema, seguimos cuidadosamente las guías proporcionadas por el laboratorio para familiarizarnos con Linux y con los fundamentos de ROS2. Estas guías fueron esenciales, especialmente las de instalación de ROS2 Humble y la del paquete *turtlesim*, disponibles en los siguientes repositorios:

- https://github.com/labsir-un/ROB_Intro_Linux.git  
- https://github.com/labsir-un/ROB_Intro_ROS2_Humble.git  
- https://github.com/labsir-un/ROB_Intro_ROS2_Humble_Turtlesim.git  

Con estas referencias configuramos las variables de entorno, añadimos ROS2 al `bashrc` y verificamos que los comandos básicos estuvieran funcionando. Luego instalamos *turtlesim* y realizamos pruebas iniciales ejecutando el nodo gráfico para asegurarnos de que todo estuviera funcionando correctamente. También exploramos sus tópicos, servicios e interfaces para entender cómo íbamos a controlarlo desde nuestro propio nodo.

Una vez preparado el entorno, comenzamos a desarrollar nuestro nodo principal en Python, llamado **TurtleController**. Lo primero fue importar `rclpy` y crear la estructura básica del nodo. Después configuramos un **publisher** que envía mensajes del tipo `Twist` al tópico `/turtle1/cmd_vel`, que es el encargado de controlar la velocidad de la tortuga. Paralelamente configuramos un **cliente** del servicio `/reset` usando `std_srvs/srv/Empty` para poder reiniciar la posición y orientación de la tortuga cada vez que se selecciona una letra.

Con la parte de ROS funcionando, pasamos al siguiente reto: crear un sistema que nos permitiera leer el teclado en tiempo real sin necesidad de presionar Enter. Para lograr esto utilizamos las librerías `sys`, `termios` y `tty`, con las cuales desarrollamos la función `get_key()`. Gracias a esto fue posible capturar teclas especiales como las flechas y también las letras que activan cada rutina.

Cuando ya podíamos leer teclas, empezamos a construir las funciones encargadas de dibujar cada letra (M, A, R, C, J, N, D). Para esto usamos cinemática básica: si conocemos la velocidad y el tiempo, podemos controlar la distancia recorrida. Así, cada rutina crea un mensaje `Twist`, ajusta componentes lineales o angulares y lo envía por un tiempo determinado usando `time.sleep()`. Ajustamos valores varias veces hasta que los trazos fueran más legibles dentro del simulador.

Además de dibujar letras, implementamos también un pequeño sistema de control manual. Creamos funciones dedicadas para mover la tortuga hacia arriba, abajo y rotar en ambos sentidos según la flecha presionada. Estas funciones simplemente publican valores constantes al tópico de velocidad, logrando movimientos directos e inmediatos.

Con todas estas piezas listas, estructuramos el **bucle principal del programa**. Este `while True` se encarga de leer continuamente la tecla presionada. Dependiendo de la entrada:
- Si es una letra válida, se llama primero al servicio `/reset` y luego a la rutina correspondiente.
- Si es una flecha, se ejecuta el movimiento manual.
- Si es la tecla `s`, el programa finaliza.

Finalmente, realizamos las pruebas del sistema completo. Ejecutamos el nodo y *turtlesim* simultáneamente, verificando lectura de teclado, dibujos de letras, funcionamiento del servicio y control manual. Durante estas pruebas ajustamos valores de velocidad y tiempos de ejecución para mejorar la claridad de las figuras.

## Funcionamiento general y decisiones de diseño.
Para desarrollar este laboratorio tomamos una serie de decisiones técnicas que definieron la estructura final del programa y la manera en que interactuamos con *turtlesim* usando ROS2. Todo el funcionamiento del código se construyó alrededor de un objetivo principal: permitir que el usuario controle la tortuga mediante el teclado y que pueda dibujar diferentes letras en pantalla, cada una con su propia secuencia de movimientos.

1. Arquitectura general del nodo

La primera decisión clave fue implementar un único nodo llamado `turtle_controller`. Dentro de este nodo se integrarían todas las tareas necesarias: publicar velocidades, llamar servicios y capturar las teclas del usuario. No utilizamos suscriptores u otros nodos porque el laboratorio se centra principalmente en el envío de comandos a *turtlesim*, no en recibir información de él.

Para controlar la tortuga utilizamos un **publisher** hacia el tópico `/turtle1/cmd_vel`, enviando mensajes `Twist`. La velocidad lineal y angular se manejan dentro de este tipo de mensaje, por lo que fue la elección natural para mover la tortuga en ROS2.

 2. Uso del servicio `/reset`
Otra decisión importante fue aprovechar el servicio nativo `/reset` del simulador. En lugar de crear un servicio propio, utilizamos este ya disponible para devolver la tortuga a una posición conocida antes de dibujar cualquier letra. Esto garantizaba consistencia en la forma final de las figuras, ya que todas partían desde la misma orientación inicial.

Para esto, el nodo creó un **cliente** del servicio `/reset`, y decidimos llamar al servicio de manera asíncrona (`call_async`) para no bloquear el programa mientras se realizaba el reinicio.

Cada vez que el usuario elige dibujar una letra, la tortuga se reinicia automáticamente usando esta función.

3. Lectura de teclado sin presionar Enter
Para hacer el programa interactivo, tomamos la decisión de leer las teclas directamente de la terminal sin requerir Enter. Para lograrlo, usamos las librerías `sys`, `tty` y `termios`. Aunque existen alternativas más simples, preferimos no agregar dependencias externas y mantener el programa compatible con cualquier terminal Linux.

Esta elección permite detectar incluso teclas especiales como las flechas (`↑`, `↓`, `→`, `←`). La función `get_key()` interpreta secuencias ESC para reconocer correctamente estas teclas.

4. Estrategia para dibujar letras
En lugar de implementar cinemática compleja o calcular trayectorias matemáticamente, optamos por la estrategia más directa: controlar la distancia a partir de la relación: **posición = velocidad × tiempo**

Cada letra tiene su propia función (`move_turtle_onceJ()`, `move_turtle_onceN()`, etc.), donde:
1. Se construye un mensaje `Twist`
2. Se ajustan sus campos `linear.x` y/o `angular.z`
3. Se publica en el tópico
4. Se espera un tiempo con `time.sleep()`

Este método resultó suficiente para dibujar letras reconocibles dentro del simulador. Ajustamos tiempos y velocidades manualmente hasta lograr formas que visualmente se acercaran a las letras deseadas.

5. Movimientos manuales con las flechas
Decidimos complementar el dibujo automático con control manual de la tortuga. Para esto, se implementaron funciones que responden inmediatamente a:
- flecha arriba → avanzar  
- flecha abajo → retroceder  
- flecha derecha → girar horario  
- flecha izquierda → girar antihorario  

Estas funciones también publican un `Twist`, pero sin pausas largas, para que el movimiento se sienta natural y fluido.

6. Bucle principal y estructura de ejecución

Todo el flujo del programa está organizado alrededor de un `while True`. Esta decisión permitió centralizar la lectura del teclado y la selección de acciones, sin repartir la lógica por todo el archivo.

El bucle funciona así:
1. Llama a `get_key()`  
2. Evalúa qué tecla presionó el usuario  
3. Ejecuta una acción:
- letras → se reinicia la tortuga y se dibuja la figura  
- flechas → control manual inmediato  
- `s` → salir del programa  
De esta manera logramos una estructura clara, fácil de entender y mantener.

7. Finalización ordenada del nodo

Finalmente, cuando el usuario presiona `s`, destruimos el nodo y ejecutamos `rclpy.shutdown()`. Esta decisión asegura un cierre seguro y limpio del entorno ROS2, evitando procesos bloqueados o nodos sin cerrar.

En conjunto, todas estas decisiones —desde el uso del servicio `/reset`, pasando por el diseño de las trayectorias con velocidades y tiempos, hasta la lectura directa del teclado— permitieron construir un código compacto, comprensible y funcional que cumple con los objetivos del laboratorio: interactuar con ROS2, manipular tópicos y servicios, y controlar un robot (en este caso, *turtlesim*) de manera programada e interactiva.

## Diagrama de flujo

```mermaid
flowchart TD
A([Inicio del programa])
B[Inicializar rclpy]
C[Crear nodo TurtleController]
D[Crear publisher /turtle1/cmd_vel]
E[Crear cliente servicio /reset]
F[Esperar servicio /reset]
A --> B
B --> C
C --> D
D --> E
E --> F

G{¿Tecla presionada?}
F --> G

H[reset_turtle]
I[Ejecutar rutina de letra]
J[Movimiento manual: Arriba / Abajo / Giro horario / Giro antihorario]
K([Salir del programa])

G -->|Letra J,N,D,A,M,R,C| H
H --> I
I --> G

G -->|Flechas| J
J --> G

G -->|s| K
```




