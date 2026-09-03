---

##  Conclusiones: ¿Qué nos ha enseñado este reto?

Este proyecto ha representado un punto de inflexión en nuestro aprendizaje técnico y personal. Diseñar y construir un vehículo autónomo desde cero nos obligó a expandir nuestros conocimientos en robótica, electrónica y desarrollo de software. Tuvimos que activar nuestra creatividad para fabricar soportes y adaptar el chasis a las necesidades de los sensores y motores.

A nivel humano, el trabajo en equipo fortaleció nuestra cohesión. Nos permitió identificar las habilidades y debilidades individuales de cada integrante, coordinando esfuerzos bajo un objetivo común y estricto. Descubrimos el valor de la resiliencia técnica ante fallos inesperados y la importancia de la sinergia colectiva.

---

##  Propuestas de Mejora y Trabajo Actual

Si tuviéramos la oportunidad de reiniciar el proceso de desarrollo desde el principio, implementaríamos las siguientes mejoras estructurales:

*   **Gestión del Tiempo y Planificación:** Nuestro principal obstáculo organizativo fue la procrastinación. Al disponer de varios meses, la carga de trabajo real se concentró en la fase final, obligándonos a trabajar bajo altos niveles de presión. Para futuras iteraciones, redactaríamos un **cronograma de trabajo semanal con entregables claros**. Esto garantizaría un avance fluido, reservando el último mes de forma exclusiva a optimizaciones, calibración fina y limpieza de trayectorias sobre el tapete.
*   **Optimización del Algoritmo de Navegación:** En las primeras versiones, el servomotor ejecutaba giros automáticos simples cuando el sensor de ultrasonido frontal detectaba el marco del tablero a una distancia fija de 42 cm. 

###  Líneas de Desarrollo Actuales
Actualmente nos encontramos rediseñando el firmware para mejorar drásticamente el rendimiento del vehículo en el tablero a través de dos implementaciones:
1.  **Contador de Giros Automatizado:** Integración de una variable de control que registra el número de virajes completados, forzando la detención absoluta del robot al alcanzar los 12 giros reglamentarios.
2.  **Lectura Predictiva Inicial:** Al arrancar, el robot ejecuta un análisis tridimensional mediante sus sensores ultrasónicos laterales para determinar la distancia relativa a las paredes de la pista. Con estos datos, el algoritmo decide de forma autónoma si la primera maniobra de evasión debe ejecutarse hacia la izquierda o hacia la derecha.

---

##  Problemas Detectados y Soluciones Implementadas

A lo largo de las fases de diseño, montaje, programación y documentación, surgieron múltiples desafíos técnicos. A continuación, se detallan las incidencias y las soluciones de ingeniería aplicadas para resolverlas:

###  1. Diseño, Estructura y Mecánica

*   **Fricción en el Sistema de Tracción:** 
    *   *Problema:* Al distribuir los componentes en el chasis, los motores rozaban directamente con la estructura principal del robot, limitando el movimiento y arriesgando el bloqueo de los ejes.
    *   *Solución:* Se sobreelevó la posición del chasis unos milímetros mediante el uso de espaciadores de nylon, garantizando la rotación libre de las ruedas.
*   **Holgura en la Dirección:** 
    *   *Problema:* El servomotor encargado de la dirección presenta un juego mecánico (holgura) en su eje piñón. Esto causa pequeñas desviaciones en las trayectorias e impide que el vehículo mantenga una línea recta perfecta de forma pasiva durante el *Obstacle Challenge*. Es un problema remanente en vías de optimización mediante correcciones por software con el giroscopio.
*   **Distribución de Cableado y Ergonomía:**
    *   *Problema:* Ubicar el módulo controlador de motores en la parte inferior del vehículo provocó que las líneas de datos y potencia quedaran expuestas y colgando, dificultando las tareas de mantenimiento y poniendo en riesgo la integridad física del circuito. Se identificó la falta de un interruptor general (botón de encendido) en el diseño inicial.

###  2. Electrónica y Hardware de Visión

*   **Conflicto de Voltajes en la Alimentación:**
    *   *Problema:* La Raspberry Pi y el Arduino UNO requerían tensiones eléctricas y corrientes distintas. Alimentar ambos sistemas con una única fuente genérica provocaba que la Raspberry se reiniciara por subvoltaje o que el Arduino entrara en estados de inestabilidad.
    *   *Solución:* Se rediseñó la etapa de potencia independizando las fuentes de alimentación mediante reguladores de voltaje (`Step-Down`) dedicados y calibrados para los requerimientos específicos de cada placa de control.
*   **Incompatibilidad Óptica de la Cámara:**
    *   *Problema:* Inicialmente reutilizamos una cámara web doméstica sin percatarnos de que incorporaba un filtro de visión nocturna (IR). Bajo condiciones de luz natural en el laboratorio, el sensor era incapaz de segmentar correctamente la gama cromática, invalidando los algoritmos de detección de color.
    *   *Solución:* Tras una intensa búsqueda en foros técnicos y hojas de especificaciones, adquirimos un sensor de alta definición con un lente gran angular (`Fish-eye`) capaz de escanear el tapete completo en una sola captura y con soporte nativo para espacios de color RGB.
*   **Sincronización Dinámica de los Motores:**
    *   *Problema:* En pruebas estáticas la cámara procesaba las imágenes perfectamente, pero en movimiento el robot arrollaba los elementos de la pista. Los motores giraban a una velocidad superior a la tasa de refresco y procesamiento de los chips de visión artificial de la Raspberry Pi. Al intentar bajar el ciclo de trabajo (`Duty Cycle`) del PWM, el motor perdía el torque mínimo y no avanzaba.
    *   *Solución:* Analizando la documentación del driver de motores, descubrimos que el chip contaba con dos modos de configuración de pines: uno de velocidad fija y torque máximo, y otro con control de velocidad por ancho de pulso con retención de fuerza. Reconfigurando el mapeo de pines logramos ralentizar el coche sin perder la fuerza necesaria para vencer la fricción estática.

###  3. Programación y Comunicación de Datos

*   **Curva de Aprendizaje y Arquitectura Híbrida:**
    *   *Problema:* Tuvimos que aprender desde cero la sintaxis de nuevos lenguajes de programación. Además, la arquitectura de control combinaba Python (alto nivel en Raspberry Pi) con C++ (bajo nivel en Arduino IDE), duplicando la dificultad de integración.
*   **Corrupción de Datos en el Bus Serial:**
    *   *Problema:* Los primeros paquetes enviados por el puerto serie (`UART`) entre Arduino y Raspberry Pi se recibían corruptos, con caracteres invertidos o con ruido electromagnético. No conocíamos la tasa de baudios óptima para esta arquitectura de hardware.
    *   *Solución:* Sincronizamos ambos dispositivos a una velocidad estándar de **115200 baudios** y optimizamos el firmware del Arduino para que transmitiera estrictamente cadenas de texto (`strings`) con estructuras limpias y delimitadores específicos, eliminando impresiones basura en el búfer.
*   **Falsos Positivos con Ultrasonidos en el Obstacle Challenge:**
    *   *Problema:* El algoritmo base interpretaba las columnas y pilares del reto de obstáculos como si fueran las esquinas o paredes perimetrales de la pista principal, provocando que el coche girase fuera de zona de manera errática.
    *   *Solución:* Se programó un software completamente nuevo desde cero para esta prueba, aislando las lecturas del sensor ultrasónico frontal y delegando la toma de decisiones críticas al giroscopio **BNO055** (el cual requirió un arduo proceso de aprendizaje y calibración de sus registros internos de orientación).

###  4. Gestión de Documentación y Recursos

*   **Incidencias de Autenticación en Plataformas:**
    *   *Problema:* Al intentar subir las evidencias en video del progreso técnico a YouTube utilizando cuentas personales de los integrantes, los sistemas de seguridad de Google bloqueaban los accesos y arrojaban errores de configuración.
    *   *Solución:* Se optó por centralizar el proyecto creando una cuenta de Google institucional exclusiva para el equipo de robótica, lo que permitió desplegar el canal oficial y alojar los videos sin conflictos de permisos.

---

> **Balance del Estado del Proyecto:** Debido a las fallas en la gestión del tiempo, falta de organización interna en el calendario y la complejidad técnica de los bugs encontrados a última hora, el equipo reconoce que el margen de tiempo restante compromete el desarrollo completo de la totalidad de los retos de la competencia. El esfuerzo actual se concentra en consolidar un desempeño excelente y limpio en las pruebas core de navegación antes que entregar sistemas incompletos.

