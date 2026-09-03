# 🚨 Pruebas de Evitamiento de Obstáculos — Colisión con Pared

Durante las pruebas dinámicas del circuito, se identificó un **fallo de trayectoria** al intentar esquivar los bloques de señalización (regla WRO: 🟢 Verde a la izquierda / 🔴 Rojo a la derecha). El robot reacciona al obstáculo pero impacta posteriormente contra el límite exterior del circuito.

---

### 🟢 Caso 1: Detección de Bloque Verde

> **Resultado:** El robot detecta la señal verde pero realiza un giro con radio excesivo, perdiendo el centro del carril y colisionando contra la pared externa.

[![Prueba Bloque Verde](https://img.youtube.com/vi/wK5_0qYKZh0/0.jpg)](https://www.youtube.com/watch?v=wK5_0qYKZh0)

*🎬 Haz clic en la imagen para ver el vídeo del ensayo.*

---

### 🔴 Caso 2: Detección de Bloque Rojo

> **Resultado:** El robot identifica el bloque rojo, pero la maniobra de esquiva a la derecha resulta inestable y termina colisionando con el muro del circuito.

[![Prueba Bloque Rojo](https://img.youtube.com/vi/95K2SqlpfjY/0.jpg)](https://www.youtube.com/watch?v=95K2SqlpfjY)

*🎬 Haz clic en la imagen para ver el vídeo del ensayo.*

---

### 🛠 Puntos a corregir

- [ ] Ajustar el PID / radio de giro tras la detección de color.
- [ ] Recalibrar la lectura de distancia de los sensores ultrasónicos para evitar el impacto tras el esquive.
- [ ] Revisar el umbral de detección de color en la cámara/sensor.
