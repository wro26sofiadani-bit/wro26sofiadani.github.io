---

## 💻 Algoritmo y Objetivo del Programa Principal

Esta sección describe la lógica de control programada en el sistema para guiar de forma autónoma el desplazamiento del robot en la pista de pruebas.

> [!NOTE]
> **Propósito Fundamental:** 
> Conseguir que el robot mantenga una trayectoria recta constante mientras monitoriza activamente su entorno mediante la triangulación de los sensores de ultrasonido, evadiendo colisiones en tiempo real.

### 🧠 Flujo de Toma de Decisiones (Evasión de Obstáculos)

El bucle principal del software ejecuta continuamente la siguiente secuencia lógica:

1.  **Monitoreo en Línea Recta:** Los motores traseros se activan en sincronía para avanzar hacia adelante, mientras el sensor ultrasónico frontal mide la distancia hacia cualquier superficie.
2.  **Umbral de Alerta ($42\text{ cm}$):** Si el sensor frontal detecta un obstáculo o pared a una distancia **menor a 42 cm**, el robot detiene su marcha de inmediato para evitar el impacto.
3.  **Evaluación de Flancos:** El sistema lee los valores de los sensores ultrasónicos laterales (izquierdo y derecho) para contrastar los espacios disponibles.
4.  **Giro Evasivo Inteligente:** 
    *   El robot compara las distancias de ambos lados.
    *   Envía la señal de ángulo correspondiente al servomotor delantero para **girar hacia el flanco que tenga el camino más libre**.
5.  **Reanudación del Ciclo:** Una vez completada la maniobra de giro y despejado el frente, las ruedas directrices vuelven a alinearse y el robot continúa su avance recto.

Gracias a este bucle de control de bucle cerrado, el robot es capaz de navegar de forma cíclica por todo el tablero, sorteando esquinas y obstáculos dinámicos sin necesidad de intervención humana.


---

## 💻 Programación y Arquitectura del Firmware (Arduino)

El control de bajo nivel, la gestión de la potencia de los motores, el posicionamiento del servo y la lectura directa de los sensores están gestionados de forma dedicada por el **Arduino UNO R3**. El código se divide estructuralmente en tres bloques: **Configuración inicial, Subprogramas modulares y el Bucle principal**.

---

### 🧱 1. Librerías, Variables Globales y Setup

En este bloque inicial cargamos las dependencias del sistema, mapeamos la distribución de pines y declaramos el espacio en memoria para las variables críticas de control.

#### 📦 Librerías del Sistema
*   #include <Wire.h>: Habilita el bus de comunicaciones **I2C**, canal por el cual se conecta el sensor de orientación.
*   #include <Adafruit_Sensor.h> & <Adafruit_BNO055.h> & <utility/imumaths.h>: Dependencias oficiales de Adafruit para realizar el control y álgebra vectorial del sensor de orientación **BNO055**.
*   #include <Servo.h>: Librería para el control por modulación de ancho de pulso (PWM) del servomotor de dirección.
*   #include <EEPROM.h>: Permite leer y escribir en la memoria no volátil del microcontrolador, reteniendo datos tras apagar el robot.

#### 📊 Configuración de Variables y Mapeo de Pines

```arduino
// ========== CONFIGURACIÓN BNO055 ==========
Adafruit_BNO055 bno = Adafruit_BNO055(55, 0x28);
float heading_offset = 0;
const float rotation_tolerance = 2.0; // Precisión de giro en grados

// Estructura para almacenar calibración en la EEPROM
struct CalibrationData {
  adafruit_bno055_offsets_t bnoOffsets;
  bool valid = false;
} calibrationData;

// ========== CONFIGURACIÓN SERVOMOTOR ==========
Servo steeringServo;
const int SERVO_PIN = 8;
float current_steering = 81.5;  // Posición neutra calibrada para línea recta

// ========== CONFIGURACIÓN MOTOR TRACCIÓN ==========
const int MOTOR_IN2 = 10;
const int MOTOR_IN1 = 12;
const int MOTOR_PWM = 11;
const int STBY_PIN = 9;

// ========== CONFIGURACIÓN ULTRASÓNICOS ==========
const int TRIG_LEFT = 6, ECHO_LEFT = 7;
const int TRIG_FRONT = 4, ECHO_FRONT = 5;
const int TRIG_RIGHT = 3, ECHO_RIGHT = 2;
unsigned long lastUltrasonicTime = 0;
const int ULTRASONIC_INTERVAL = 300;  // Tiempo de refresco entre lecturas (ms)

// ========== VARIABLES DE CONTROL ==========
float Kp_straight = 0.8;    // Ganancia proporcional para movimiento recto
float Kp_rotation = 1.2;    // Ganancia proporcional para giros ajustados
bool isCalibrated = false;

int angulo_a_conseguir = 65;       // Umbral angular objetivo para giros mecánicos
int angulo_a_conseguirCamara = 70; // Umbral angular objetivo asistido por cámara

void setup() {
  Serial.begin(115200); // Apertura del canal de comunicación serial con la Raspberry Pi
 
  // Inicializar servo dirección
  steeringServo.attach(SERVO_PIN);
  setSteering(current_steering);  // Configurar ruedas en posición neutra
 
  // Inicializar motor tracción
  pinMode(MOTOR_IN1, OUTPUT);
  pinMode(MOTOR_IN2, OUTPUT);
  pinMode(MOTOR_PWM, OUTPUT);
  pinMode(STBY_PIN, OUTPUT);
  digitalWrite(STBY_PIN, HIGH);  // Levantar pin Standby para activar el chip TB6612FNG
 
  // Inicializar sensores ultrasónicos
  pinMode(TRIG_LEFT, OUTPUT);   pinMode(ECHO_LEFT, INPUT);
  pinMode(TRIG_FRONT, OUTPUT);  pinMode(ECHO_FRONT, INPUT);
  pinMode(TRIG_RIGHT, OUTPUT);  pinMode(ECHO_RIGHT, INPUT);
 
  // Inicializar BNO055
  if (!bno.begin()) {
    while(1); // Bucle de bloqueo de seguridad si el hardware no responde
  }
  delay(1000);
  bno.setExtCrystalUse(true);
 
  // Gestión de calibración inteligente mediante EEPROM
  if (loadCalibration()) {
    isCalibrated = true;
  } else {
    calibrateBNO();
    isCalibrated = true;
  }
 
  resetHeadingZero(); // Restablece el rumbo relativo a 0° al iniciar en el tapete
}

void calibrateBNO() {
  Serial.println("Calibrando BNO055. Siga estas instrucciones:");
  Serial.println("1. Deje el robot inmóvil por 5 segundos");
  Serial.println("2. Incline el robot 45° en todos los ejes (pausando en cada posición)");
  Serial.println("3. Mueva el robot en 'figura de 8' para calibrar magnetómetro");

  uint8_t system, gyro, accel, mag;
  
  while (!isFullyCalibrated()) {
    bno.getCalibration(&system, &gyro, &accel, &mag);
    Serial.print("Progreso [Sis,Giro,Acel,Mag]: ");
    Serial.print(system); Serial.print(", ");
    Serial.print(gyro); Serial.print(", ");
    Serial.print(accel); Serial.print(", ");
    Serial.println(mag);
    delay(500);
  }
  Serial.println("Calibracion completada con exito!");
  saveCalibration();
}

bool isFullyCalibrated() {
  uint8_t system, gyro, accel, mag;
  bno.getCalibration(&system, &gyro, &accel, &mag);
  return (system >= 3 && gyro >= 3 && accel >= 3 && mag >= 3); // Nivel 3 = Calibración óptima
}

bool loadCalibration() {
  EEPROM.get(0, calibrationData);
  if (calibrationData.valid) {
    bno.setSensorOffsets(calibrationData.bnoOffsets);
    return true;
  }
  return false;
}

void saveCalibration() {
  adafruit_bno055_offsets_t offsets;
  bno.getSensorOffsets(offsets);
  calibrationData.bnoOffsets = offsets;
  calibrationData.valid = true;
  EEPROM.put(0, calibrationData);
}

void resetHeadingZero() {
  sensors_event_t event;
  bno.getEvent(&event);
  heading_offset = event.orientation.x;
}

float getHeading() {
  sensors_event_t event;
  bno.getEvent(&event);
  float heading = event.orientation.x - heading_offset; // Aplicar desviación inicial
  
  // Normalización matemática en el plano de [-180°, 180°]
  if (heading > 180) heading -= 360;
  if (heading < -180) heading += 360;
  return heading;
}

void setMotorSpeed(int speed) {
  speed = constrain(speed, -255, 255); // Restringir velocidad al rango PWM de 8 bits
  
  if (speed > 0) {         // Cinemática de avance
    digitalWrite(MOTOR_IN1, HIGH);
    digitalWrite(MOTOR_IN2, LOW);
  } else if (speed < 0) {  // Cinemática de retroceso
    digitalWrite(MOTOR_IN1, LOW);
    digitalWrite(MOTOR_IN2, HIGH);
  } else {                 // Frenado activo / Bloqueo
    digitalWrite(MOTOR_IN1, LOW);
    digitalWrite(MOTOR_IN2, LOW);
  }
  analogWrite(MOTOR_PWM, abs(speed)); // Inyección de potencia mediante PWM
}

void setSteering(int angle) {
  angle = constrain(angle, 45, 135); // Límite mecánico de seguridad para proteger el servo
  current_steering = angle;
  steeringServo.write(angle);
}

void correction(int target_angle) {
  setSteering(target_angle);
  Serial.println("OK"); // Confirmación de ejecución para el controlador de alto nivel
}

void rotateToAngle(float target_angle) {
  float angulo_actual = getHeading();
  setSteering(target_angle);
  unsigned long startTime = millis(); // Temporizador de seguridad (Watchdog software)

  if(target_angle < angulo_a_conseguir){
    do {
        delay(50);
    } while((float(getHeading()) >= angulo_actual - angulo_a_conseguir) && (millis() - startTime < 2000));
    
    if (float(getHeading()) < angulo_actual - angulo_a_conseguir ){
      setSteering(81);      // Enderezar eje delantero
      resetHeadingZero();  // Fijar nueva línea base de referencia de rumbo
      Serial.println("Finalizado");
    } else {
      Serial.println("Error no se ha realizado el giro");
    }
  }
  // El flujo lógico continúa evaluando giros en sentido inverso...
