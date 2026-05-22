---

## ⚡ Gestión de la Energía y Alimentación

La elección del sistema de energía fue uno de los desafíos más críticos del proyecto. Basándonos en la experiencia de equipos de años anteriores, sabíamos que una mala gestión de la alimentación podía convertirse en el mayor obstáculo para mover el robot.

### ⚠️ El problema de las pilas de 9V tradicionales

En las primeras fases del prototipado, intentamos utilizar una **pila de 9V convencional**, e incluso se probaron alternativas de 9V recargables. 

> [!WARNING]
> **Falta de intensidad (Corriente):** Aunque estas pilas ofrecen un voltaje nominal alto (9V), **no tienen la capacidad de corriente (amperaje) necesaria** para romper la inercia de los motores. El robot ni siquiera podía moverse y el sistema sufría caídas de tensión constantes.

---

### 🔋 La Solución: Baterías de Litio Recargables (3.7V x 2)

Para garantizar un funcionamiento estable, sustituimos la pila por un portapilas con **dos baterías recargables de 3.7V cada una**, conectadas en serie para ofrecer un total de **7.4V**.

Esta decisión técnica transformó por completo el rendimiento de **Leotardo** gracias a sus ventajas:

*   **Alta capacidad de corriente:** A diferencia de las pilas de 9V, estas celdas proporcionan los picos de intensidad que demandan los motores de tracción y el servo de dirección al mismo tiempo.
*   **Mayor autonomía:** Permiten sesiones de prueba mucho más largas sin caídas notables en el rendimiento de los sensores.
*   **Sostenibilidad y eficiencia:** Al ser recargables de forma sencilla, evitamos el reemplazo constante de consumibles, reduciendo costes y residuos durante el desarrollo del reto.

*   
---

## 🔌 Esquema de Conexiones Electrónicas (Fase Provincial)

Para garantizar la estabilidad del sistema y evitar interferencias, centralizamos las conexiones utilizando un *shield* (escudo) sobre el **Arduino Uno** y unificamos la masa (`GND`) de todos los componentes.

### 📊 Resumen de Distribución de Pines

| Componente | Tipo de Pin | Conexión en Arduino | Propósito Técnico |
| :--- | :--- | :--- | :--- |
| **Línea de Alimentación** | Voltaje / Masa | `5V` y `GND` | Energía común para sensores, shield y servo |
| **Sensor Ultrasónico HC-SR04** | Digital (Salida) | `Pin Digital (Trig)` | Disparo de la onda ultrasónica para medir distancia |
| **Sensor Ultrasónico HC-SR04** | Digital (Entrada) | `Pin Digital (Echo)` | Recepción del eco para el cálculo de obstáculos |
| **Servomotor** | Digital (PWM) | `Pin Digital (Señal)` | Control del ángulo del sistema de dirección |
| **Driver de Motores L298N** | Digitales / PWM | `IN1`, `IN2`, `ENA` | Señales de sentido de giro y velocidad (PWM) |

---

### 🔍 Detalle de Cableado por Componente

<details>
<summary><b>1. Sistema de Alimentación General</b></summary>

*   **Línea de 5V:** Conectada directamente al pin `VCC` (positivo) de los sensores ultrasónicos, al *shield* de expansión y al servomotor.
*   **Línea de GND (Tierra):** Funciona como el punto de referencia común. **Todos** los componentes del robot comparten esta masa para cerrar el circuito correctamente.
</details>

<details>
<summary><b>2. Sensores Ultrasónicos (HC-SR04)</b></summary>

Cada sensor dispone de 4 pines de conexión:
*   **VCC / GND:** Conectados a las líneas de 5V y masa del Arduino a través del *shield*.
*   **Trig (Trigger):** Recibe un pulso digital del Arduino para emitir la onda de sonido.
*   **Echo:** Envía una señal de retorno al Arduino para calcular el tiempo de vuelo del pulso y determinar la distancia física.
</details>

<details>
<summary><b>3. Servomotor de Dirección</b></summary>

Dispone de un conector de 3 hilos:
*   **VCC (Rojo/Marrón claro) y GND (Negro/Marrón oscuro):** Van asignados a las salidas de alimentación del Arduino.
*   **Señal (Naranja/Amarillo):** Conectado a un pin digital del Arduino para recibir las órdenes de posición angular que orientan el eje delantero.
</details>

<details>
<summary><b>4. Módulo Driver de Motor (L298N) y Motores de Tracción</b></summary>

El driver actúa como intermediario de potencia para no dañar las salidas lógicas del microcontrolador:
*   **Pines de Control (IN1, IN2, ENA):** Se conectan a las salidas digitales del Arduino para gestionar el encendido, apagado y la dirección de los canales.
*   **Alimentación del Driver:** Los bornes de potencia `VCC` y `GND` reciben la energía de forma directa desde el bloque de baterías recargables.
*   **Salidas de Potencia (OUT1/OUT2 y OUT3/OUT4):** Van cableadas directamente a los terminales de cada motor, transfiriendo la corriente necesaria para el movimiento de las ruedas traseras.
</details>



---

## 🛠️ Inventario Detallado de Componentes y Hardware

A continuación se describen todos los elementos de hardware que integran a **Leotardo**, detallando sus especificaciones técnicas, funciones asignadas y las modificaciones que tuvimos que realizar en el taller.

---

### 🧠 Unidades de Procesamiento y Expansión

#### 🔷 Elegoo Arduino UNO R3
*   **Función:** Actúa como controlador de bajo nivel e intermediario del sistema.
*   **Operación:** Recibe las lecturas de toda la sensórica (salvo el botón de inicio) y las transmite a la unidad central. A su vez, recibe las instrucciones de la Raspberry Pi para coordinar físicamente los actuadores (motores y servos). Funciona como un puente de comunicación en tiempo real.

#### 🍓 Raspberry Pi 3 Model B+
*   **Función:** Unidad central de procesamiento y toma de decisiones lógicas de alto nivel.
*   **Modificación Térmica:** Se le instalaron **dos disipadores de calor pasivos** (uno sobre el SoC principal y otro sobre el chip de red/comunicaciones). 
> [!TIP]
> La adición de disipadores previene el *thermal throttling* (pérdida de rendimiento por sobrecalentamiento), asegurando una tasa de procesamiento estable durante la competencia.

#### 🛡️ Sensor Shield V5.0
*   **Función:** Placa de expansión acoplada directamente sobre el Arduino UNO.
*   **Ventaja de Diseño:** Duplica las salidas de líneas de `5V` y `GND` junto a cada pin de señal. Elimina por completo la necesidad de usar una *protoboard* o cableados flotantes para distribuir la energía, logrando un acabado limpio y profesional.

---

### ⚙️ Actuadores, Tracción y Dirección

#### 🦾 Servomotor Digital MG996R
*   **Función:** Control de la geometría de dirección del eje delantero.
*   **Materiales:** Piñonería y engranajes completamente metálicos de alta resistencia.

| Parámetro Técnico | Rendimiento a 4.8V | Rendimiento a 6.0V |
| :--- | :--- | :--- |
| **Torque de retención** | ~9.4 kg·cm | ~11.0 kg·cm |
| **Velocidad de respuesta** | 0.17 - 0.19 s / 60° | 0.14 - 0.16 s / 60° |
| **Rango de giro / Voltaje** | 180° / 4.8V a 7.2V | Rango térmico: -30°C a +60°C |

#### 🏎️ Motores Amarillos "TT" con Reductora
*   **Función:** Sistema de tracción trasera del robot.
*   **Especificaciones:** Voltaje de operación de 3V a 6V (velocidad nominal de ~200 RPM). La caja reductora reduce la velocidad angular para maximizar el par torsor.
*   **Sincronización:** Ambos motores se unieron eléctricamente mediante una **ficha de empalme** para garantizar que giren de forma unísona, estabilizando la trayectoria en línea recta. 

#### 🛞 Sistema de Ruedas
*   **Ruedas Delanteras (Dirección):** Reutilizadas de un antiguo set de *Meccano*. Una alternativa económica, funcional y sostenible que encajó milimétricamente en el diseño de dirección.
*   **Ruedas Traseras (Tracción):** Llantas específicas para motor TT, elegidas tras varias pruebas en pista por su óptimo coeficiente de adherencia y tracción sobre el tapete oficial.

---

### ⚡ Sistema de Potencia y Control de Motores

#### 🧠 Driver de Motores TB6612FNG
Sustituyó al clásico puente en H L298N debido a estrictos criterios de eficiencia:
*   **Eficiencia Térmica:** Resistencia interna de conducción de solo $0.5\ \Omega$ (frente a los $1.8\ \Omega$ del L298N), reduciendo drásticamente las pérdidas por calor.
*   **Potencia Dinámica:** Soporta hasta 1.2 A continuos por canal (picos de 3.2 A) y frecuencias PWM de hasta 100 kHz para un control fino de velocidad.
*   **Activación:** Requiere señal `HIGH` constante en el pin `STBY` (Standby) para habilitar los puentes.

> [!CAUTION]
> **Complejidad de Montaje:** A diferencia del L298N, este módulo no posee terminales de tornillo. Requirió soldar de manera manual hilos específicos en un espacio muy denso, incrementando la precisión obligatoria en el conexionado.

#### 🔋 Celdas de Litio Li-Ion 18650
*   **Función:** Alimentación del driver de potencia TB6612FNG y electrónica de Arduino.
*   **Voltaje:** 3.7V nominales por celda (hasta 4.2V a plena carga). Dispuestas en serie (7.4V) dentro de un portapilas modificado con interruptor integrado. El cable de salida se bifurcó mediante soldadura para alimentar simultáneamente la lógica y la etapa de potencia.

#### 🔌 Powerbank SyN de 5000 mAh
*   **Función:** Alimentación exclusiva y aislada para la Raspberry Pi 3 B+.
*   **Criterio de Selección:** Se descartaron opciones de 10000 mAh para ahorrar peso muerto en el chasis, ya que 5000 mAh ofrecen autonomía de sobra.
*   **Modificación de Masa:** Se intervino el cable de salida original para soldar derivaciones extra hacia el `GND` de motores y Arduino, unificando así la tierra de todo el ecosistema eléctrico.

---

### 📡 Sensores e Interfaz de Usuario

#### 🦇 Sensores de Distancia Ultrasonido (HC-SR04 x3)
*   **Configuración:** Un sensor central delantero y dos sensores en los flancos laterales.
*   **Misión:** Triangular distancias mediante rebote de ondas de alta frecuencia para evadir obstáculos en trayectorias autónomas complejos.

> [!CAUTION]
> **Defecto del Soporte Comercial:** Empleamos unas placas de soporte facilitadas para el HC-SR04, pero detectamos que tienen un fallo de diseño que orienta los sensores con una ligera inclinación hacia el suelo, afectando la lectura. Se desaconseja su uso para futuras iteraciones del robot debido a su fragilidad.

#### 🕹️ Módulo Joystick (Modo Pulsador)
*   **Función:** Botón de inicio seguro del robot.
*   **Operación:** Aunque cuenta con lecturas analógicas para dos ejes, se cablearon únicamente los pines `5V`, `GND` y `SW` (Switch) a la Raspberry Pi. El software mantiene al robot en bucle de espera bloqueante hasta que el operador presiona físicamente el botón, garantizando un arranque seguro en pista.
