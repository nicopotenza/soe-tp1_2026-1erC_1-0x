#Paso 2

##¿Cómo FreeRTOS asigna tiempo de procesamiento a cada Tarea en una aplicación?
En el caso preemptive, asigna el tiempo en partes iguales entre las tareas de la máxima prioridad que se encuentren Ready.
En caso de no encontrar tareas Ready en la máxima prioridad, pasa a la prioridad siguiente.

##¿Cómo FreeRTOS elige qué Tarea debe ejecutarse en un momento dado?
Verificando la siguiente tarea Ready, priorizando siempre a la de más alta prioridad.

##¿Cómo la prioridad relativa de cada Tarea afecta el comportamiento del sistema?
Las tareas se ejecutan desde la prioridad más alta a la más baja. Si las tareas de la prioridad mayor consumen todo el tiempo de ejecución, las tareas de menor prioridad nunca se ejecutarán.

##¿Cuáles son los estados en los que puede encontrarse una Tarea?
Los estados son:
- Running
- Ready
- Blocked
- Suspended
- Deleted

##¿Cómo implementar Tareas?
Para implementar una tarea debe realizarse una función, la cual contiene un esquema como el siguiente:

void task_name(void *parameters) {
  // Setup de la tarea
  
  for(;;) {
    // Acciones que deben repetirse
  }
}

##¿Cómo crear una o más instancias de una Tarea?

Mediante la función xTaskCreate(), la cual recibe:
- Puntero a la tarea (función de la contiene),
- Nombre de la tarea,
- Tamaño del Stack,
- Argumentos que recibe la tarea,
- Nivel de prioridad,
- Puntero al Handle de la tarea.

##¿Cómo eliminar una Tarea?
Mediante la función xTaskDelete(), la cual recibe el puntero al Handle de la tarea a eliminar.

#Paso 3
##Prueba 1
Prioridades:
- task_btn: (tskIDLE_PRIORITY + 2ul)
- task_led: (tskIDLE_PRIORITY + 1ul)

Resultado: La tarea del led nunca se ejecuta.

[info]  soe-tp0_03-application: Demo Code
[info]  
[info] Task BTN is running - Tick [mS] =   0
[info]  Task BTN - BTN PRESSED
[info]  Task BTN - BTN HOVER
[info]  Task BTN - BTN PRESSED
[info]  Task BTN - BTN HOVER

##Prueba 2
Prioridades:
- task_btn: (tskIDLE_PRIORITY + 1ul)
- task_led: (tskIDLE_PRIORITY + 2ul)

Resultado: La tarea del botón nunca se ejecuta.

[info]  soe-tp0_03-application: Demo Code
[info]  
[info] Task LED is running - Tick [mS] =   0


#Paso 4
Se crearon 3 instancias de task_btn, con nombres task_btn_1, task_btn_2 y task_btn_3.
En la primera prueba, la creación de la instancia task_btn_3 retornó error por falta de memoria para hacerla.
Se redujo el stack de las instancias de (2 * configMINIMAL_STACK_SIZE) a (1 * configMINIMAL_STACK_SIZE) y se ejecutó correctamente. Las tres instancias compiten entre si para leer el estado del botón y enviar la orden a task_led para que blinkee.
Luego se agregó la eliminación de task_btn_2 desde task_led. Para eso se declaró:
extern TaskHandle_t h_task_btn_2;
en task_led.c y se invocó a la función xTaskDelete() desde task_led_statechart() en el caso ST_LED_XX_BLINK cuando cambia el estado para volver a ST_LED_XX_OFF. Luego de realizado eso se verificó que task_btn_2 desapareció de "FreeRTOS Task List".