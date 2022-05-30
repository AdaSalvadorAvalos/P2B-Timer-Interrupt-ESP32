MATERIALES:
·ESP32

Objetivo: interrupciones usando el timer.

Comentario y explicación del código:
```
#include <Arduino.h>
//cuantas interrupciones han ocurrido
volatile int interruptCounter;
int totalInterruptCounter;
 //creamos un puntero al timer
hw_timer_t * timer = NULL;
//variable para sincronizar el loop y el ISR
portMUX_TYPE timerMux = portMUX_INITIALIZER_UNLOCKED;
 
void IRAM_ATTR onTimer() { IRAM_ATTR=  hará que use la memoria RAM interna (IRAM) del ESP32
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
  //incrementa el contador con el numero total de interrupciones que ocurren y lo imprime por un puero serie.
}
```
//salidas explicadas en el video
