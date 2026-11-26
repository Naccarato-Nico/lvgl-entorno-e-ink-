# lvgl-entorno-e-ink-




Entorno E-ink / RLCD: Notificaciones Calmas y Descanso Visual es un proyecto desarrollado en el IFTS N° 14 (asignatura Desarrollo de Aplicaciones para IoT, Prof. Germán Gabriel Velárdez, autor Nicolás Naccarato) para recibir y consultar notificaciones en red local sin luz azul emisiva ni dependencia de servicios externos en la nube.

El firmware está escrito en C sobre ESP-IDF v5.x y FreeRTOS, con tareas distribuidas entre núcleos (vTaskHttpServer en Core 0 y vTaskLVGLRender, vTaskPowerManager y vTaskSensorRead en Core 1), sincronización mediante cola (xQueueNotif) y mutex (xSemaphoreI2C), persistencia en NVS Flash, doble buffer parcial en PSRAM (300 × 50 px) sobre LVGL (LV_COLOR_DEPTH = 1) y refresco parcial por SPI hacia el controlador ST7305.

Incluye un servidor HTTP en el puerto 80 (GET /, POST /api/v1/notification, GET /api/v1/notifications, GET /api/v1/status) con validación de payloads mediante cJSON, filtro por lista blanca de hasta 10 aplicaciones, pulso de aviso para prioridad 5 y panel web local de alto contraste. Además, aplica el perfil energético CR-01 (escalado de CPU 80–240 MHz, Light Sleep tras 30 s de inactividad y celda Li-Ion 18650 de 3500 mAh) para mantener un consumo promedio ≤ 110 mA y autonomía ≥ 24 horas dentro del presupuesto aprobado de $520.000 ARS y plazo de 16 semanas.

 
