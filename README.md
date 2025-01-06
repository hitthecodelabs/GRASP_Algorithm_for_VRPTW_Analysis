# GRASP para el Problema de Ruteo de Vehículos con Ventanas de Tiempo (VRPTW)

Este repositorio contiene la implementación en Wolfram Language (Mathematica) de un algoritmo GRASP (Greedy Randomized Adaptive Search Procedure) para resolver el Problema de Ruteo de Vehículos con Ventanas de Tiempo (VRPTW).

## Descripción del Problema

El VRPTW es un problema de optimización combinatoria que busca encontrar el conjunto óptimo de rutas para una flota de vehículos que deben visitar un conjunto de clientes, cada uno con una demanda específica y una ventana de tiempo en la cual debe ser atendido. El objetivo es minimizar el costo total del ruteo, que generalmente se compone de la distancia total recorrida y/o el número de vehículos utilizados.

**Características del problema:**

*   **Clientes:** Cada cliente tiene una ubicación geográfica, una demanda de bienes y una ventana de tiempo durante la cual se debe realizar la entrega o recolección.
*   **Depósito:** Los vehículos parten y regresan a un depósito central.
*   **Vehículos:** La flota de vehículos puede ser homogénea (todos los vehículos tienen la misma capacidad) o heterogénea (diferentes capacidades). En esta implementación, se considera una flota homogénea.
*   **Ventanas de tiempo:** Cada cliente especifica un intervalo de tiempo (ventana de tiempo) en el que puede ser atendido. Si un vehículo llega antes, debe esperar. No se permiten llegadas después del cierre de la ventana.
*   **Objetivo:** Minimizar el costo total de las rutas, que en este caso se considera como la distancia total recorrida. Como objetivo secundario, se busca minimizar el número de vehículos utilizados.

## Metodología: GRASP

GRASP (Greedy Randomized Adaptive Search Procedure) es una metaheurística que combina un procedimiento de construcción de soluciones voraz (greedy) y aleatorio con una búsqueda local para mejorar las soluciones encontradas.

**Fases de GRASP:**

1.  **Construcción:**
    *   Se inicializan las rutas con un conjunto de "clientes semilla" seleccionados estratégicamente.
    *   Los clientes restantes se asignan a las rutas de forma iterativa utilizando un criterio voraz que considera el costo de inserción y un factor de aleatorización.
    *   Se aplica un costo de penalización para priorizar las decisiones que tienen menos impacto negativo en el sistema general.
2.  **Búsqueda Local:**
    *   Se aplican operadores de búsqueda local para mejorar la solución construida. En esta implementación, se utilizan:
        *   **2-opt intra-ruta:** Mejora una ruta individual intercambiando arcos.
        *   **(Desactivado en la versión actual) Mover un cliente entre rutas:** Intenta mover un cliente de una ruta a otra si esto reduce el costo total.
    * La búsqueda local se repite hasta que no se encuentran mejoras.
3.  **Eliminacion de rutas (Desactivado en la versión actual):** Se intenta eliminar rutas, reubicando a los clientes en otras rutas existentes.

**Detalles de la implementación:**

*   **Inicialización de rutas:** Se implementa la estrategia descrita en el paper, utilizando el "Convex Hull" y una lista ordenada de clientes restantes para seleccionar los clientes semilla.
*   **Costo de inserción:** Se considera el aumento en la distancia total y el impacto en las ventanas de tiempo.
*   **Costo de penalización:** Se calcula como la diferencia entre el costo de la segunda mejor inserción y el costo de la mejor inserción para un cliente.
*   **Actualización de l_t, MAC y MDC:** Se implementan funciones para actualizar dinámicamente los valores de:
    *   **l_t:** Último tiempo de llegada posible a un cliente.
    *   **MAC:** Máxima capacidad disponible en una ruta al llegar a un cliente.
    *   **MDC:** Mínima capacidad disponible en una ruta al salir de un cliente.

## Estructura del Repositorio

*   **`vrptw_grasp.nb`:**  Notebook de Wolfram Language (Mathematica) que contiene la implementación del algoritmo GRASP.
    *   **`PARTE 1: Importar r102.txt`:**  Importa los datos del problema.
    *   **`Inicialización de rutas con clientes semilla`:** Funciones para calcular el Convex Hull (`ConvexHull`) e inicializar las rutas (`InitializeRoutes`).
    *   **`Cálculo de Tiempo/Factibilidad en una ruta`:** Función `RouteCostAndFeasibility` para calcular el costo y la factibilidad de una ruta.
    *   **`Costo de toda la solución (múltiples rutas)`:** Función `SolutionCostVRPTW` para calcular el costo total de una solución.
    *   **`Inserción Greedy con Capacidad y Ventanas`:**  Funciones para calcular la capacidad usada en una ruta (`CapacityUsed`), actualizar `l_t`, `MAC` y `MDC` (`UpdateRouteProperties`), calcular el costo de penalización (`PenaltyCost`) e insertar un cliente en la mejor posición posible (`InsertClientBestPosition`).
    *   **`Heurística de Construcción (Greedy+Múltiples Rutas)`:** Función `ConstructSolutionVRPTW` que implementa la fase de construcción de GRASP.
    *   **`Búsqueda Local: ejemplos sencillos`:** Funciones para realizar la búsqueda local 2-opt intra-ruta (`TwoOptIntraRoute`) y mover clientes entre rutas (`MoveOneClientBetweenRoutes`) (actualmente desactivada).
    *   **`Búsqueda Local Compuesta`:** Función `LocalSearchVRPTW` que combina las diferentes estrategias de búsqueda local.
    *   **`Procedimiento de Eliminación de Rutas`:** Función `EliminateRoutes` para eliminar rutas (actualmente desactivada).
    *   **`Fase GRASP`:** Función `GRASPVRPTW` que implementa el algoritmo GRASP completo.
*   **`r102.txt`:** Fichero de datos de ejemplo (instancia del problema VRPTW).

## Instrucciones de Uso

1.  **Requisitos:**
    *   Wolfram Mathematica (versión 12.0 o superior).
2.  **Ejecución:**
    *   Abre el notebook `vrptw_grasp.nb` en Mathematica.
    *   Ejecuta todas las celdas del notebook (Cell -> Evaluate Notebook).
    *   La solución y el costo se imprimirán en la salida.
    *   Los parámetros `nSeeds` (número de clientes semilla) y `maxIter` (número máximo de iteraciones) se pueden ajustar al final del notebook.
3.  **Datos de entrada:**
    *   El código está configurado para leer la instancia `r102.txt`.
    *   Para utilizar otro conjunto de datos, modifica la variable `data` en la primera celda del notebook con la ruta o URL del archivo de datos. Asegúrate de que el archivo de datos siga el mismo formato que `r102.txt`.

## Consideraciones Importantes

*   **Versión actual simplificada para reporte rápido:** La versión actual del código tiene algunas funciones desactivadas y usa un conjunto de datos reducido (solo 15 clientes más el depósito) para obtener una solución rápida con fines de demostración.  Esto **no representa la implementación completa** del algoritmo GRASP descrito en el paper.
*   **Funciones desactivadas:**
    *   `EliminateRoutes` (Eliminación de rutas).
    *   `MoveOneClientBetweenRoutes` (Mover clientes entre rutas en la búsqueda local).
*   **Limitaciones:**
    *   La calidad de la solución obtenida con la versión simplificada está lejos de ser óptima.
    *   El tiempo de ejecución sigue siendo un factor limitante para instancias más grandes del problema.

## Trabajo Futuro

*   **Reactivar las funciones desactivadas:** Implementar y probar las funciones `EliminateRoutes` y `MoveOneClientBetweenRoutes` para mejorar la calidad de la solución.
*   **Optimización del código:**  Perfilado y optimización del código para reducir el tiempo de ejecución.
*   **Implementación de otras estrategias de búsqueda local:** Explorar el uso de metaheurísticas como Simulated Annealing o Búsqueda Tabú para mejorar la búsqueda local.
*   **Implementar el cálculo de los límites inferiores:** Agregar el cálculo de los límites inferiores (`LB1`, `LB2`, `LB3`) para evaluar la calidad de la solución.
*   **Parametrización:** Permitir la configuración de los parámetros del algoritmo  (`γ1`, `γ2`, `δ1`, `δ2`, `δ3`, `μ`, `λ`) para ajustar su comportamiento.
*   **Interfaz gráfica:** Desarrollar una interfaz gráfica para facilitar la visualización de las rutas y la interacción con el algoritmo.

## Referencias

*   **Artículo:**  [Inserta aquí la referencia completa al artículo en el que se basa la implementación]
*   **GRASP:** Feo, T. A., & Resende, M. G. (1995). Greedy randomized adaptive search procedures. Journal of global optimization, 6(2), 109-133.

## Licencia

Este proyecto está bajo la Licencia [Nombre de la licencia, por ejemplo, MIT]. Consulta el archivo `LICENSE` para más detalles.
