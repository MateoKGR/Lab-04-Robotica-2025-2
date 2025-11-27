# Lab-04-Robotica-2025-2
Laboratorio 4 de Robótica 2025-2s, realizado por Jeison Diaz y Mateo Ramos

# Integrantes
1. Jeison Nicolás Diaz Arciniegas [jediazar@unal.co](JeisonD0819)
2. Mateo Ramos Cujer [mramoscu@unal.edu.co](MateoKGR)

# Informe

Indice:
1. [Objetivos](#objetivos)
2. [Procedimientos realizados](#procedimientos_realizados)
3. [Decisiones de diseño](#decisiones_de_diseño)
4. [Funcionamiento general](#funcionamiento_general)
5. [Diagrama de flujo](#diagrama_de_flujo)


## Objetivos

- Familiarizarse con la arquitectura de ROS2 (nodos, tópicos, mensajes y servicios).
- Implementar un nodo en Python capaz de publicar mensajes del tipo `Twist` para controlar el simulador *turtlesim*.
- Utilizar el servicio `/reset` de turtlesim para reiniciar la posición y orientación de la tortuga.
- Desarrollar un sistema de lectura de teclado en tiempo real que permita el control manual de la tortuga (flechas) y la ejecución de trayectorias predefinidas para dibujar letras.
- Consolidar en un mismo programa la interacción entre tópicos, servicios y entrada del usuario.

## Procedimientos realizados
## Decisiones de diseño
## Funcionamiento general
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




