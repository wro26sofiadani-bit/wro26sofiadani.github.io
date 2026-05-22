# 🤖 Proceso de Montaje del Robot

Este documento detalla el paso a paso del ensamblaje de nuestro robot, destacando las decisiones técnicas y las soluciones aplicadas a los desafíos encontrados.

---

## 🏗️ Fase 1: Estructura Base y Cerebro
### 1. La Placa Arduino
Comenzamos con la placa de **Arduino**, el corazón del sistema. 
* **Preparación:** Antes de fijarla, imprimimos las piezas y verificamos que no hubiera deformaciones.
* **Instalación:** Se atornilló sobre la base buscando estabilidad total. 
> [!IMPORTANTE]
> Una placa mal nivelada afectaría la alineación de motores, sensores y la fiabilidad de las conexiones.

### 2. Sistema de Dirección (Servo)
Instalamos el **servo** que controla el giro.
* **Prueba previa:** Comprobamos que el rango de movimiento fuera completo y sin obstáculos.
* **Estrategia:** Se colocó en esta etapa para ajustar su altura respecto a la base y la placa Arduino, evitando futuros desmontajes.

---

## ⚡ Fase 2: Alimentación y Tracción
### 3. Sistema de Energía
Incorporamos las **pilas y su soporte**.
* **Equilibrio:** Esto permitió verificar la distribución de pesos desde el inicio.
* **Seguridad:** Aseguramos que la energía llegue correctamente tanto a Arduino como a la Raspberry Pi, manteniendo un cableado ordenado.

### 4. Motores y Solución de Problemas 🛠️
Durante la instalación de los motores, detectamos que rozaban con la base.
* **El Problema:** Los motores quedaban demasiado bajos, impidiendo el giro libre.
* **La Solución:** Añadimos una **lámina de Lego como separador**. Esta elevación garantizó un giro sin interferencias de forma sencilla y efectiva.

### 5. Ruedas Delanteras
Alineamos las ruedas de dirección con el eje trasero para asegurar un desplazamiento estable. 
> [!TIP]
> Una alineación precisa previene derivas indeseadas en la navegación y errores en los sensores de distancia.

---

## 🧠 Fase 3: Integración de Sistemas y Sensores
### 6. Soporte de Raspberry Pi
Montamos los soportes superiores y barras de fijación.
* **Estabilidad:** Fundamental para evitar desconexiones por movimiento.
* **Gestión de cables:** Definimos un espacio interno limpio para evitar enredos.

### 7. Giroscopio y Ultrasonidos
* **Giroscopio:** Instalado en posición central para obtener lecturas de equilibrio y orientación precisas.
* **Ultrasonidos:** Debido a una avería en la impresora 3D, realizamos los orificios **manualmente**. Se calculó la ubicación para optimizar la detección de obstáculos.

---

## 🔌 Fase 4: Conectividad y Finalización
### 8. Gestión de Cableado
Conectamos la **Raspberry Pi** y el **Arduino**.
* **Dimensionamiento:** Ajustamos la longitud de los cables al tamaño del robot para evitar tensiones.
* **Fiabilidad:** Un cableado ordenado reduce interferencias y facilita el mantenimiento.

### 9. Periféricos Finales
Conectamos la **cámara** y el **botón** directamente a la Raspberry Pi.
* **Objetivo:** Acceso directo para programación y control, minimizando latencias en el software.

---

### 👥 Créditos y Autoría
* **Daniel Bautista Cortijo:** Fotografías y montaje (Leotardo nacional, construcción, robot arriba, montaje ultras).
* **Sofía Romero Luque:** Fotografías de alimentación (Pilas GIF) y motores.
* *Licencia de imágenes: Creative Commons Zero (CC0).*



---

## ❓ ¿Por qué Leotardo es así?

El diseño y la estructura final de **Leotardo** no fueron casualidad: el robot se construyó minuciosamente de esta manera para cumplir estrictamente con el **reglamento oficial de la competencia**. 

Desde la fase de diseño en papel, el tamaño, el peso y la distribución de cada componente se calcularon para garantizar la homologación del robot sin sacrificar rendimiento.

### 📋 Restricciones Técnicas Oficiales

A continuación se detallan las normas clave que dieron forma a nuestro robot:

| Parámetro | Límite Permitido | Estado de Leotardo |
| :--- | :--- | :--- |
| **📏 Dimensiones** | Máx. 300 x 200 x 300 mm | **Cumple** (Optimizado) |
| **⚖️ Peso** | Máximo 1.5 Kilogramos | **Cumple** (Ligero) |

---

### 🔍 Especificaciones del Reglamento

> [!NOTE]
> **Dimensiones máximas del robot (Regla 10.1)**
> *“El vehículo no puede superar los 300 mm de largo, 200 mm de ancho y 300 mm de alto.”*

> [!WARNING]
> **Peso máximo permitido (Regla 10.2)**
> *“El peso del vehículo no puede superar los 1.5 kilogramos.”*

Gracias a estas limitaciones, cada decisión —como la elección de materiales ligeros para el chasis y la distribución central de la batería— se tomó con el objetivo de exprimir al máximo el espacio disponible sin registrar penalizaciones.
