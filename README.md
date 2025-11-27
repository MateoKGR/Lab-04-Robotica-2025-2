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
## Procedimientos realizados
## Decisiones de diseño
## Funcionamiento general
## Diagrama de flujo

```mermaid
flowchart TD

    %% Bloque inicial
    A([Inicio del programa]) --> B[Inicializar rclpy]
    B --> C[Crear nodo TurtleController]
    C --> D[Crear publisher /turtle1/cmd_vel]
    D --> E[Crear cliente del servicio /reset]
    E --> F[Esperar disponibilidad del servicio]

    %% Loop principal
    F --> G{Leer tecla\n(get_key())}

    %% Acciones según tecla
    G -->|Letra J,N,D,A,M,R,C| H[reset_turtle()]
    H --> I[Ejecutar función de letra\n(move_turtle_onceX)]

    G -->|Flechas| J[Ejecutar movimiento manual:\nArriba/Abajo/Horario/Antihorario]

    G -->|Tecla s| K([Salir del programa])

    %% Retorno al loop
    I --> G
    J --> G
```




