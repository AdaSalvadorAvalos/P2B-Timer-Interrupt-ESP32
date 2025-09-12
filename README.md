# Practice 2-B: Timer Interrupt on ESP32 (English Version)

## Materials
- ESP32

## Introduction
In this practice, for my Digital Processors course, I learned how to use timer interrupts on the ESP32. Timer interrupts allow code to execute at precise intervals, independently of the main loop.


## Code Explanation (with comments explaining line by line):
```cpp
#include <Arduino.h>

// Number of interrupts that have occurred
volatile int interruptCounter;
int totalInterruptCounter;

// Create a pointer to the timer
hw_timer_t * timer = NULL;

// Variable to synchronize the loop and the ISR
portMUX_TYPE timerMux = portMUX_INITIALIZER_UNLOCKED;

void IRAM_ATTR onTimer() { 
  // IRAM_ATTR ensures the function runs from the ESP32's internal RAM (IRAM)
  // This is the Interrupt Service Routine (ISR) called on timer events
  portENTER_CRITICAL_ISR(&timerMux); // Temporarily disables global interrupts
  interruptCounter++;
  portEXIT_CRITICAL_ISR(&timerMux);  // Re-enables global interrupts
}

void setup() {
  Serial.begin(115200);

  // Initialize the timer
  timer = timerBegin(0, 80, true); 
  // 0 = timer number (ESP32 has 4 timers: 0,1,2,3)
  // 80 = prescaler (ESP32 main clock is 80 MHz, so timer tick = 1/(80MHz/80) = 1 µs)
  // true = count up

  timerAttachInterrupt(timer, &onTimer, true); 
  // Attach the ISR function to the timer

  timerAlarmWrite(timer, 1000000, true); 
  // Set the alarm to 1,000,000 ticks (~1 second)
  // true = auto-reload the timer

  timerAlarmEnable(timer); 
  // Enable the timer
}

void loop() {
  if (interruptCounter > 0) {
    portENTER_CRITICAL(&timerMux);
    interruptCounter--;
    portEXIT_CRITICAL(&timerMux);

    totalInterruptCounter++;

    Serial.print("ON ");
    Serial.println(totalInterruptCounter);
  }

  // The loop increments a counter for each interrupt and prints it via Serial
}
```

## How to Run

1. **Install PlatformIO**
   - Install [VS Code](https://code.visualstudio.com/)
   - Install the [PlatformIO extension](https://platformio.org/install/ide?install=vscode)

2. **Create a New Project**
   - Open PlatformIO in VS Code
   - Create a new project and select your board (e.g., ESP32 Dev Module)
   - Choose **Arduino framework**

3. **Add the Code**
   - Replace the contents of `src/main.cpp` with the code provided above

4. **Connect the Hardware**
   - Connect the ESP32 board to your computer via USB

5. **Build and Upload**
   - Click the **Build (✓)** button to compile the code
   - Click the **Upload (→)** button to flash the code to your ESP32

6. **Observe the Output**
   - Open the Serial Monitor at **115200 baud**
   - Watch the interrupt counter increment every second

### Resources
- **Video Demonstration in Spanish:** [Watch video](assets/practica2Bvideo.mp4)
- **ESP32 Documentation:** [Espressif ESP32](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/index.html)  
- **ESP32 Timer API Documentation:** [ESP32 Timers](https://docs.espressif.com/projects/esp-idf/en/v4.3/esp32/api-reference/peripherals/timer.html)  
- **Arduino Timer Interrupt Reference:** [Arduino TimerInterrupt](https://docs.arduino.cc/libraries/timerinterrupt/)  

# Práctica 2-B: Interrupción por Timer en ESP32 (Versión en Español)

## Materiales
- ESP32

## Introducción
En esta práctica, para mi curso de Procesadores Digitales, aprendí cómo utilizar interrupciones por timer en el ESP32. Las interrupciones por timer permiten que el código se ejecute a intervalos precisos, independientemente del bucle principal.


## Explicación del código (con comentarios línea a línea):

```cpp
#include <Arduino.h>
//cuantas interrupciones han ocurrido
volatile int interruptCounter;
int totalInterruptCounter;
 //creamos un puntero al timer
hw_timer_t * timer = NULL;
//variable para sincronizar el loop y el ISR
portMUX_TYPE timerMux = portMUX_INITIALIZER_UNLOCKED;
 
void IRAM_ATTR onTimer() { // IRAM_ATTR=  hará que use la memoria RAM interna (IRAM) del ESP32
//El código que queremos ejecutar es una rutina de servicio de interrupción (ISR) en este caso le llamamos OnTimer
  portENTER_CRITICAL_ISR(&timerMux); //dejará las interrupciones deshabilitadas globalmente.
  interruptCounter++;
  portEXIT_CRITICAL_ISR(&timerMux); //quitará estas interrupciones globales
 
}
 
 void setup() {
 
   Serial.begin(115200);
 
   timer = timerBegin(0, 80, true); 
   // 0=el timer  que usamos ESP32 tiene 4 (0,1,2,3), 80= es el divisor, el reloj principal de ESP32 es de 80MHz, 
   //por lo que tendremos T=1/(80MHz/80)=1ms que son 1000000 tics por 1 segundo.
   //y si es true= es que el timer contará
   timerAttachInterrupt(timer, &onTimer, true);
   //sirve para detener, &onTimer= es una función que se llamará, true=si es verdaderp una alarma generará una interrupción
   timerAlarmWrite(timer, 1000000, true); 
   //1000000=número que hemos calculado previamente usando timerBegin(), true= si es verdadero el timer se repetirá
   timerAlarmEnable(timer); 
   //habilita el timer
 }

void loop() { //bucle que usaremos
 
  if (interruptCounter > 0) {
 
    portENTER_CRITICAL(&timerMux);
    interruptCounter--;
    portEXIT_CRITICAL(&timerMux);
 
    totalInterruptCounter++;
 
    Serial.print("ON ");
    Serial.println(totalInterruptCounter);
 
  }
  //incrementa el contador con el numero total de interrupciones que ocurren y lo imprime por un puerto serie.
}
```
## Cómo Ejecutar

1. **Instalar PlatformIO**
   - Instala [VS Code](https://code.visualstudio.com/)
   - Instala la [extensión PlatformIO](https://platformio.org/install/ide?install=vscode)

2. **Crear un Nuevo Proyecto**
   - Abre PlatformIO en VS Code
   - Crea un nuevo proyecto y selecciona tu placa (ejemplo: ESP32 Dev Module)
   - Elige el **framework Arduino**

3. **Agregar el Código**
   - Reemplaza el contenido de `src/main.cpp` con el código proporcionado arriba

4. **Conectar el Hardware**
   - Conecta la placa ESP32 a tu computadora mediante USB

5. **Compilar y Subir**
   - Haz clic en el botón **Build (✓)** para compilar el código
   - Haz clic en el botón **Upload (→)** para cargar el código en tu ESP32

6. **Observar el Resultado**
   - Abre el Monitor Serial a **115200 baudios**
   - Observa cómo se incrementa el contador de interrupciones cada segundo

### Recursos
- **Video demostrativo en español:** [Ver video](assets/practica2Bvideo.mp4)
- **Documentación del ESP32:** [Espressif ESP32](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/index.html)  
- **Documentación de temporizadores en ESP32:** [ESP32 Timers](https://docs.espressif.com/projects/esp-idf/en/v4.3/esp32/api-reference/peripherals/timer.html)  
- **Referencia de interrupciones por timer en Arduino:** [Arduino TimerInterrupt](https://docs.arduino.cc/libraries/timerinterrupt/)  
