# Azzaiteros C15

"Si funciona, no se toca"

---

# 1. DISEÑO Y ESTRUCTURA

Somos Azzaiteros C15, un equipo formado por estudiantes del instituto Az-Zait de Jaén, que participa en la competición WRO Future Engineers 2026.

El objetivo principal del proyecto es diseñar y construir un vehículo autónomo desde cero, aplicando principios reales de ingeniería.

## Objetivo del diseño

El robot está pensado para:

- Cumplir las restricciones oficiales de la competición  
- Ser estable en movimiento  
- Tener una distribución de peso equilibrada  
- Facilitar el mantenimiento y reparación  
- Permitir mejoras modulares  

## Chasis

El robot, llamado “Leotardo”, ha evolucionado desde estructuras básicas hasta un chasis modular impreso en PLA.

Características del diseño:

- Estructura de doble placa (superior e inferior)  
- Uniones mediante barras metálicas  
- Soportes integrados en el propio diseño CAD  
- Acceso sencillo a componentes internos  
- Optimización de espacio y peso  

## Restricciones del diseño

El robot debe cumplir:

- 300 × 200 × 300 mm máximo  
- 1.5 kg de peso máximo  
- 4 ruedas obligatorias  
- Sin control remoto

<p align="center">
  <a href="./diseño y estructura">
    <img src="https://img.shields.io/badge/diseño y estructura-Ver más pulsar aquí-blue">
  </a>

---

# 2. MECÁNICA

## Sistema de tracción

- Motores TT con reductora en eje trasero  
- Sincronización de ambos motores para trayectoria estable  
- Transmisión directa sin pérdidas innecesarias  

## Dirección

- Servomotor MG996R de alta potencia  
- Sistema de dirección en eje delantero  
- Barra mecánica para giro de ruedas  

## Ruedas

- Delanteras reutilizadas de sistema mecánico tipo Meccano  
- Traseras adaptadas a motores TT  
- Ajuste de altura para evitar desalineaciones  

## Problemas y soluciones

Durante el montaje se detectaron interferencias en los motores con la base.

Solución aplicada:

- Elevación mediante separadores estructurales  
- Reajuste de la base para evitar fricción
 
<p align="center">
  <a href="./mecanica">
    <img src="https://img.shields.io/badge/mecanica-Ver más pulsar aquí-yellow">
  </a>

---

# 3. ELECTRÓNICA

## Unidades principales

- Arduino UNO como controlador de bajo nivel  
- Raspberry Pi 3 B+ como unidad de procesamiento principal  
- Sensor Shield para distribución de conexiones  

## Alimentación

Sistema dividido en dos partes:

- Lógica: Arduino y sensores  
- Alto consumo: motores y Raspberry Pi  

Baterías utilizadas:

- 2 celdas Li-Ion 18650 en serie (7.4V)  
- Powerbank independiente para Raspberry Pi  

Motivo del cambio:

Las baterías de 9V no proporcionaban suficiente corriente para los motores.

## Driver de motores

Se utiliza el TB6612FNG en lugar del L298N debido a:

- Menor consumo energético  
- Menor tamaño  
- Mejor eficiencia térmica  
- Mayor rendimiento en PWM  

## Sensores

- 3 sensores ultrasónicos HC-SR04  
- Sensor central y dos laterales  
- Sensor de inicio tipo joystick (botón)

<p align="center">
  <a href="./electronica">
    <img src="https://img.shields.io/badge/electronica-Ver más pulsar aquí-yellow">
  </a>

---

# 4. PROGRAMACIÓN

## Arquitectura del sistema

El sistema está dividido en dos niveles:

- Raspberry Pi: decisiones principales  
- Arduino: control directo de sensores y actuadores  

## Funciones principales

- Lectura de sensores ultrasónicos  
- Control de dirección mediante servo  
- Control de motores mediante driver TB6612FNG  
- Comunicación entre Raspberry Pi y Arduino  
- Sistema de espera de inicio mediante botón  

## Lógica de navegación

El robot está programado para:

- Detectar obstáculos  
- Calcular distancias en tiempo real  
- Tomar decisiones de giro  
- Mantener estabilidad en línea recta  
- Ejecutar maniobras de esquiva
  
<p align="center">
  <a href="./programacion">
    <img src="https://img.shields.io/badge/programacion-Ver más pulsar aquí-red">
  </a>

---

# 5. CONCLUSIONES

El desarrollo de este robot ha sido un proceso de aprendizaje continuo.

A lo largo del proyecto:

- Se han probado diferentes estructuras  
- Se han rediseñado sistemas mecánicos  
- Se han sustituido componentes electrónicos  
- Se han optimizado decisiones de diseño  

El resultado final es un sistema autónomo funcional, modular y preparado para competir en la categoría Future Engineers.

Más allá de la competición, el proyecto ha permitido aprender:

- Diseño mecánico real  
- Electrónica aplicada  
- Programación de sistemas embebidos  
- Resolución de problemas técnicos  
- Trabajo en equipo

<p align="center">
  <a href="./conclusiones">
    <img src="https://img.shields.io/badge/conclusiones-Ver más pulsar aquí-orange">
  </a>

---

Fin del documento.
