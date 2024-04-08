# Sistem-Operasi
## Task  : How do the Task No. x behaves by setting and modifying the Priority if we have the same delay and the same duration

Source from : [Wokwi](https://wokwi.com/projects/394199274175749121)
```ino
/*
  The original code:
  https://microcontrollerslab.com/use-freertos-arduino/
  Modified by Barbu Vulc!
  January 4th, 2023
*/
//RTOS = Real-time operating system
//FreeRTOS site: https://www.freertos.org/

//FreeRTOS library:
#include <Arduino_FreeRTOS.h>

//Variables for buzzer & first 3 LEDs!
const int buzzer = 7;    //Buzzer
const int LED1 = 8;      //Red LED
const int LED2 = 9;      //Yellow LED
const int LED3 = 10;     //Green LED
/*
 * I named this 'extender' (see below) because I can optionally...
 * ...put a 4th task to this LED! Now is task-free! :))
 */
const int extender = 11; //Blue LED

void setup() {
  Serial.begin(9600);

  //LEDs' initialization!
  pinMode(LED1, OUTPUT);
  pinMode(LED2, OUTPUT);
  pinMode(LED3, OUTPUT);
  pinMode(extender, OUTPUT);

  //Buzzer initialization!
  pinMode(buzzer, OUTPUT);

  /*
    Create 3 tasks with labels 'Task_1', 'Task_2' and 'Task_3' and
    assign the priority as 1, 2 and 3 respectively.
  */
  //'Neutral_Task' - the task-free function!
  xTaskCreate(Task_1,       "Task no.  1!",  100, NULL, 4, NULL);
  xTaskCreate(Task_2,       "Task no.  2!",  100, NULL, 2, NULL);
  xTaskCreate(Task_3,       "Task no.  3!",  100, NULL, 3, NULL);
  xTaskCreate(Neutral_Task, "Neutral_Task!", 100, NULL, 0, NULL);
}

//The following function is Task1. We display the task label on Serial monitor.
static void Task_1(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, HIGH);
    digitalWrite(LED2, LOW);
    digitalWrite(LED3, LOW);
    digitalWrite(extender, LOW);
    tone(buzzer, 200, 1000 / portTICK_PERIOD_MS);
    Serial.println(F("Task no. 1!"));
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

//Task 2
static void Task_2(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, HIGH);
    digitalWrite(LED3, LOW);
    digitalWrite(extender, LOW);
    tone(buzzer, 300, 1000 / portTICK_PERIOD_MS);
    Serial.println(F("Task no. 2!"));
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

//Task 3
static void Task_3(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, LOW);
    digitalWrite(LED3, HIGH);
    digitalWrite(extender, LOW);
    tone(buzzer, 400, 1000 / portTICK_PERIOD_MS);
    Serial.println(F("Task no. 3!"));
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

//Task 4 (the last one)!
//This is an extension which can be task-free!
static void Neutral_Task(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, LOW);
    digitalWrite(LED3, LOW);
    digitalWrite(extender, HIGH);
    tone(buzzer, 500, 1000);
    Serial.println(F("Neutral_Task"));
    delay(1000);
  }
}

//We don't need to use "loop" function here!
void loop() {}

```

- Then the output will be as below
```
Task no. 1!
Task no. 3!
Task no. 2!
Neutral_Task
```
<!-- Different Priorities  -->

- The behavior of each Task depends on the priority set via the `xTaskCreate()` function, even if they have the same time lag and the same duration.

`Different Priorities:`
- When priorities of different tasks are given, the task with the higher priority will have a higher execution priority than the others.
In the code example
- Task_1 has the highest priority (priority 4)
- Followed by Task_3 with priority 3
- Then Task_2 with priority 2
- And Neutral_Task which has the lowest priority (priority 0).
- Therefore, even though all tasks have the same lag time and duration
- The order of execution will be based on the assigned priority
- So that Task_1 will be executed first, followed by Task_3, then Task_2, and finally Neutral_Task.

<!-- Modify Priority 2, 3, 4 -->



- If the priority is changed to order into 3 functions then (example below)
```ino
  xTaskCreate(Task_1, "Task no. 1!", 100, NULL, 2, NULL);
  xTaskCreate(Task_2, "Task no.  2!", 100, NULL, 3, NULL);
  xTaskCreate(Task_3, "Task no.  3!", 100, NULL, 4, NULL);
  xTaskCreate(Neutral_Task, "Neutral_Task!", 100, NULL, 0, NULL);

... 

//Task 1
static void Task_1(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, HIGH);
    digitalWrite(LED2, LOW);
    digitalWrite(LED3, LOW);
    digitalWrite(extender, LOW);
    tone(buzzer, 200, 1000 / portTICK_PERIOD_MS);
    Serial.println(F("Task no. 1!"));
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

//Task 2
static void Task_2(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, HIGH);
    digitalWrite(LED3, LOW);
    digitalWrite(extender, LOW);
    tone(buzzer, 300, 1100 / portTICK_PERIOD_MS);
    Serial.println(F("Task no. 2!"));
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

//Task 3
static void Task_3(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, LOW);
    digitalWrite(LED3, HIGH);
    digitalWrite(extender, LOW);
    tone(buzzer, 400, 1200 / portTICK_PERIOD_MS);
    Serial.println(F("Task no. 3!"));
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

//Task 4 (the last one)!
//This is an extension which can be task-free!
static void Neutral_Task(void* pvParameters) {
  while (1) {
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, LOW);
    digitalWrite(LED3, LOW);
    digitalWrite(extender, HIGH);
    tone(buzzer, 500, 1000);
    Serial.println(F("Neutral_Task"));
    delay(1000);
  }
}
```

- Then the output will be as below 
```
Task no. 3!
Task no. 2!
Task no. 1!
Neutral_Task
```

<!-- Modify Priority Changed To Same -->
- If the priority is changed to 1 for all functions then (example below)
```ino
xTaskCreate(Task_1, "Task no. 1!", 100, NULL, 1, NULL);
xTaskCreate(Task_2, "Task no.  2!", 100, NULL, 1, NULL);
xTaskCreate(Task_3, "Task no.  3!", 100, NULL, 1, NULL);
xTaskCreate(Neutral_Task, "Neutral_Task!", 100, NULL, 0, NULL);
```
- Then the output will be as below
```
Task no. 1!
Task no. 2!
Task no. 3!
Neutral_Task
```

- From the example above we can conclude that if the `xTaskCreate` priority argument is set to be greater then that argument will have the greatest priority and that argument will be executed first
- If the priority is `0` it will be an idle task that will be only run when idle `(no other work to do)`.
