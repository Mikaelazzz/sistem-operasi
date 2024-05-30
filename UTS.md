## The xSemaphoreCreateCounting() API Function | Example 7.2
### xSemaphoreCreateCounting() fungsi API dalam FreeR-
TOS yang digunakan untuk membuat sebuah count-
ing semaphore. Fungsi ini memiliki dua parameter:
nilai maksimum semaphore dan nilai awal semaphore.
Counting semaphore adalah jenis semaphore khusus
yang memungkinkan beberapa tugas (task) atau in-
terupsi untuk mengakses sumber daya yang terbatas
secara bersamaan, di mana jumlah sumber daya yang
tersedia direpresentasikan oleh nilai counting semaphore.
### Memahami penggunaan counting semaphore dalam FreeRTOS
Counting semaphore digunakan untuk mengontrol akses sumber daya yang terbatas oleh beberapa tugas (task) atau interupsi. Jumlah sumber daya yang tersedia direpresentasikan oleh nilai counting semaphore.

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

