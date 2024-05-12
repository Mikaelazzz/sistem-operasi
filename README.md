# Sistem-Operasi
## xTaskCreate
_Task  : How do the Task No. x behaves by setting and modifying the Priority if we have the same delay and the same duration_

Source from : [Wokwi](https://wokwi.com/projects/394199274175749121)

Example Code :
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

## Mutex
Source from : [Wokwi](https://wokwi.com/projects/395466103682715649)
- A mutex is a variable that will lock itself when used by thread x and make thread y wait for thread x to complete its task. In this context 
- The data type used for the mutex is `SemaphoreHandle_t`.

- There are two functions used to lock and unlock mutex variables:

1. `xSemaphoreTake()`: This function is used to lock mutex variables. This function accepts two arguments: a mutex handler (`SemaphoreHandle_t`) and a timeout (tick) which is the interval time to check again whether the mutex is available or not.

2. `xSemaphoreGive()`: This function is used to unlock the mutex variable. This function only requires one argument, namely the mutex handler (`SemaphoreHandle_t`).

- So, in using a mutex, thread x will use `xSemaphoreTake()` to lock the mutex, and when finished, thread x will use `xSemaphoreGive()` to unlock the mutex, so that thread y can continue execution.

### 1. Adjust Task Parameter to produce conflict.
- When using Semaphore or Mutex, this will not create conflicts between threads even if their priorities change. This is because Semaphore or Mutex provides a secure synchronization mechanism for accessing shared resources.

- When the priority of one thread is set to `0`, which means idle, it will sleep forever because there are no tasks scheduled to execute. In operating systems with priority-based scheduling, threads with higher priorities will execute before others. So, when a thread has priority `0` (idle), this means that there are no tasks to be executed by that thread, and therefore, the thread will remain sleeping forever until it gets a task to execute or some other state wakes it up.

### 2. Remove take and give semaphore to produe conflict.
Original Code : 
```ino 
void TaskMutex(void *pvParameters)
{
  TickType_t delayTime = *((TickType_t*)pvParameters); 
  // Use task parameters to define delay

  for (;;)
  {
    /**
       Take mutex
       https://www.freertos.org/a00122.html
    */
    if (xSemaphoreTake(mutex, 10) == pdTRUE)
    {
      Serial.print(pcTaskGetName(NULL)); // Get task name
      Serial.print(", Count read value: ");
      Serial.print(globalCount);

      globalCount++;

      Serial.print(", Updated value: ");
      Serial.print(globalCount);

      Serial.println();
      /**
         Give mutex
         https://www.freertos.org/a00123.html
      */
      xSemaphoreGive(mutex);
    }

    vTaskDelay(delayTime / portTICK_PERIOD_MS);
  }
}    
```

- Then the Output will be as below
```
Mutex created

Task1, Count read value: 0, Updated value: 1

Task2, Count read value: 1, Updated value: 2

Task1, Count read value: 2, Updated value: 3

Task2, Count read value: 3, Updated value: 4
```

- To create a conflict, we can change the function to not perform checks or locks using `xSemaphoreTake` and `xSemaphoreGive`.
- Removed use of `xSemaphoreTake` and `xSemaphoreGive`, no synchronization is performed to protect shared resources. This can cause inter-thread conflicts, where two or more threads can access or modify a shared resource at the same time without protection, which can produce unexpected or inconsistent results.

Modify Code :
```ino 
void TaskMutex(void *pvParameters)
{
  TickType_t delayTime = *((TickType_t*)pvParameters); 
  // Use task parameters to define delay

  for (;;)
  {
    /**
       Take mutex
       https://www.freertos.org/a00122.html
    */
      Serial.print(pcTaskGetName(NULL)); // Get task name
      Serial.print(", Count read value: ");
      Serial.print(globalCount);

      globalCount++;

      Serial.print(", Updated value: ");
      Serial.print(globalCount);

      Serial.println();
      /**
         Give mutex
         https://www.freertos.org/a00123.html
      */

    vTaskDelay(delayTime / portTICK_PERIOD_MS);
  }
}    
```

- Then the Output will be as below
```
Mutex created

Task1, Count read value: 0, Updated value: 1

Task2, Count read Talue: 1, Updated value: 2, UpdTted value: 3

ad value: 3, UpdTted value: 4

ad value: 4, UpTated value: 5

d value: 5, UpdTted value: 6

ad value: 6, UpdTted value: 7
```

1.  The Bounded-Buffer Problem
   
    * Bounded-Buffer Problem is a challenge in multi-threading application development where two types of tasks, namely producers and consumers, share a buffer for data exchange. However, the buffer has a limited capacity, meaning it can only hold a limited number of data elements.

    * The main challenge in this problem is how to coordinate access to the buffer so that producers do not add data to the buffer when the buffer is full, and consumers do not retrieve data when the buffer is empty. This can cause problems such as deadlocks, where both tasks are stuck waiting for each other without progress, or race conditions, where the outcome of operations depends on an unpredictable order of execution.

    * A common solution to this problem involves using a synchronization mechanism such as a mutex or semaphore. A mutex is used to lock a buffer while it is being used by one task, thereby preventing other tasks from accessing the buffer at the same time. Semaphores can also be used to manage access to buffers in a similar way.

    * Using appropriate synchronization mechanisms, such as mutexes or semaphores, we can ensure that production-operations and consume-operations run safely and in a coordinated manner, avoiding potential problems arising from shared access to limited buffers.
   
2.  The Readers–Writers Problem
   
    * The `Reader-Writer` Problem is a classic challenge in multi-threading application development where multiple threads need to access shared data. In this context, there are two types of tasks:
      - `Reader`: A task that only reads shared data without changing it.
      - `Writer`: Tasks that write or modify shared data.

    * The challenge is how to coordinate access between readers and writers so that:

    * Multiple readers can read data simultaneously without interfering with each other.
    * Only one writer can write at a time, and no other readers or other writers should access the data while the writer is writing.

    * This issue arises because of a potential mismatch between reading and writing data, which can produce inconsistent or unexpected results.
    * To overcome this problem, synchronization mechanisms such as `Mutex`, `Semaphores`, or `Condition variables can be used`. 
  
3. The Dining-Philosophers Problem

    * The Philosophers' Problem is a classic problem in computer science that illustrates the challenges of synchronizing and managing resources jointly used by multiple processes or threads. In this case, there are a number of philosophers sitting around a round table, and between each two philosophers there is a fork with which to eat.

    * Every philosopher has two activities: eating and thinking. However, there is a limitation, namely that to eat, a philosopher needs two forks close together. If a philosopher has taken one fork but cannot take the other fork (because it has already been used by a neighboring philosopher), then the philosopher will wait until both forks are available.

    * The main challenge in this problem is preventing the occurrence of race conditions, deadlocks, or starvation. A race occurs when several philosophers try to pick up their forks at the same time and can lead to a situation where no philosopher can eat. Deadlock occurs when each philosopher has taken one fork and is waiting for the other fork held by its neighbor, so that no philosopher can continue. Hunger occurs when one or more philosophers are constantly waiting to take a fork, but never get their turn because they are being competed by other philosophers.

    * To overcome this problem, various synchronization algorithms and techniques have been developed, such as fork allocation rules, timeout scheduling, or the use of synchronization mechanisms such as semaphores or mutexes.

## Multithreading

Multithreading is the ability of an application to divide its tasks into individual threads. In multithreading, one process or task can be carried out by several threads, or we can say that there is more than one thread to carry out the task. By using multithreading, multitasking can be achieved.

Thread Models:

1. Many to One:
In this model, the program uses only one kernel thread which is managed by the operating system. This kernel thread is responsible for handling all thread activities created by the program. When a program needs a new thread, the kernel thread creates and manages userspace threads to handle additional tasks. This approach is more efficient in resource usage because there is only one kernel thread that needs to be managed.
2. One to Many:
In this model, every time a program needs a new thread, it creates a new kernel thread in the operating system. This means that each program thread has its own kernel thread at the operating system level. This approach allows high parallelism because each thread can execute independently. However, this approach can also be resource intensive because each thread has overhead associated with its own kernel thread.
3. Many to Many:
This model is a combination of the two previous approaches. The program has the flexibility to create both kernel threads and userspace threads as needed. With this approach, programs can optimize resource usage by creating kernel threads when needed for high performance, or using userspace threads for lighter tasks. This provides a balance between efficiency and flexibility in thread management.

## The xSemaphoreCreateCounting() API Function | Example 7.2
### Exercise Using a counting semaphore to synchronize a task with an interrupt
Source from : [Wokwi](https://wokwi.com/projects/new/arduino-uno)

Example Code :
```ino
#include <Arduino_FreeRTOS.h>
#include <semphr.h>

// Buat counting semaphore dengan nilai awal 5
SemaphoreHandle_t countedSemaphore = xSemaphoreCreateCounting(5, 5);

void taskUseSemaphore(void *pvParameters) {
  while (1) {
    // Coba ambil sumber daya dari counting semaphore
    if (xSemaphoreTake(countedSemaphore, portMAX_DELAY) == pdTRUE) {
      Serial.println("Resources acquired");
      // Simulasikan penggunaan sumber daya
      delay(1000);
      Serial.println("Resources are returned");
      // Kembalikan sumber daya ke counting semaphore
      xSemaphoreGive(countedSemaphore);
    } else {
      Serial.println("The resource is not available");
    }
    delay(500);
  }
}

void setup() {
  Serial.begin(115200);

  // Buat task yang akan menggunakan counting semaphore
  xTaskCreate(taskUseSemaphore, "SemaphoreTask", 128, NULL, 1, NULL);
}

void loop() {
  // FreeRTOS akan mengelola task secara otomatis
}
```
- Then the Output will be as below
```
...
Resources acquired
Resources are returned
...
```
- Fetching resources from a `semaphore` using the `xSemaphoreTake()` function.
- If the resource is successful `(semaphore value > 0)`, the `semaphore` value will be reduced by 1, and the code can use the resource.
```ino
...
if (xSemaphoreTake(countedSemaphore, portMAX_DELAY) == pdTRUE) {
}
...
```
- Returns resources to the `semaphore` using the `xSemaphoreGive()` function.
- This will increase the `semaphore value by 1`, so that the resource can be used by `other tasks or interrupts`.
```ino
...
xSemaphoreGive(countedSemaphore);
...
```
- Create a new task that will use the `counting semaphore`.
- This task will run in an `infinite loop`, trying to retrieve resources from the `semaphore`, use those resources, and then return them to the semaphore.
```ino
...
xTaskCreate(taskUseSemaphore, "SemaphoreTask", 128, NULL, 1, NULL);
...
```

- If the argument value is changed to (10,0)
  - Modify Code 
```ino
...
SemaphoreHandle_t countedSemaphore = xSemaphoreCreateCounting(10, 0);
...
```
  1. The result of this code is that it doesn't display anything
  2. Which means the value 10 is the maximum count
  3. And 0 is the initial value of the semaphore count

- if the code is executed with condition (10,5)
```ino
...
SemaphoreHandle_t countedSemaphore = xSemaphoreCreateCounting(10, 5);
...
```
- Then the Output will be as below
```
...
Resources acquired
Resources are returned
...
```
- By changing the initial value to 5, the `taskUseSemaphore` task will successfully retrieve resources from the `counting semaphore`, and you will see the output "Resources acquired" and "Resources are returned" in `Serial Monitor`.

- `SemaphoreHandle_t countedSemaphore = xSemaphoreCreateCounting(10, 0);` create a semaphore count from the `maximum value` and `initial value`
- `void taskUseSemaphore(void *pvParameters)` task function that will try to retrieve and return from the `counting semaphore`.
- `taskUseSemaphore()` there is an `infinite loop` that tries to take from the `counting semaphore` using
- `xSemaphoreTake(countedSemaphore, portMAX_DELAY)` If the resource is successfully obtained, the function will print "Resources acquired", delay for 1 second (resource usage simulation), print "Resources are returned", and return the resource to the `counting semaphore` using

