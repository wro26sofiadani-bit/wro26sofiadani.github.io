#  Algoritmo de evasión de obstáculos: fundamento y arquitectura

El sistema de evasión de obstáculos se basa en un esquema de percepción por visión artificial combinado con control reactivo proporcional, ejecutado de forma concurrente al lazo de navegación principal mediante un hilo de procesamiento independiente.

---

##  Etapa de percepción

La adquisición de imagen se realiza en el espacio de color **HSV (Matiz, Saturación, Valor)** en lugar de RGB, dado que este modelo desacopla la crominancia de la luminancia, ofreciendo mayor robustez frente a variaciones de iluminación ambiental —un factor crítico considerando que las condiciones lumínicas del recinto de competencia difieren sustancialmente de las del entorno de desarrollo.

1. **Segmentación cromática:** Sobre cada fotograma se aplica una segmentación por umbralización de color (*thresholding*), generando máscaras binarias independientes para los rangos cromáticos correspondientes a los obstáculos objetivo.
2. **Filtrado morfológico:** Se ejecuta un proceso de erosión seguida de dilatación para atenuar el ruido de alta frecuencia y eliminar falsos positivos de baja área.
3. **Extracción de contornos:** Sobre la máscara resultante se extraen los contornos mediante el **algoritmo de Suzuki-Abe**, seleccionando el de mayor área como candidato principal, bajo la premisa de que corresponde al obstáculo relevante más próximo.
4. **Cálculo del centroide:** A partir de sus momentos de imagen se calcula el centroide, cuya coordenada horizontal determina la posición angular relativa del obstáculo respecto al eje óptico de la cámara.

---

##  Etapa de decisión y control

La estrategia de evasión responde a una regla determinista impuesta por el reglamento:

- **🔴 Bloques rojos:** Exigen sorteo por el lado derecho.
- **🟢 Bloques verdes:** Requieren sorteo por el lado izquierdo.

El sistema traduce esta regla en una corrección angular de dirección, cuya magnitud no es constante sino **proporcional al área del contorno detectado** —empleada como variable *proxy* de la distancia al obstáculo, dada la relación inversa entre distancia y tamaño aparente en proyección perspectiva.

>  **Comportamiento Proporcional:** Obstáculos distantes generan correcciones de baja intensidad, mientras que la proximidad incrementa progresivamente la magnitud de la maniobra evasiva (controlador proporcional de ganancia variable).

---

##  Integración con el sistema de navegación

Esta corrección no opera de forma aislada, sino que se superpone aditivamente a la señal de control generada por el **subsistema de seguimiento de pared**, sustentado en telemetría de sensores ultrasónicos.
