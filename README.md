# lvgl-entorno-e-ink-


Firmware embebido en C (**ESP-IDF v5.x / FreeRTOS**) para un nodo basado en **ESP32-S3** y pantalla **Reflective LCD (RLCD) de 4.2" (300 x 400 px, controlador SPI ST7305)**, diseñado para mostrar notificaciones en red local sin luz azul directa ni dependencia de servicios externos en la nube.

## Características principales
- **Visualización de bajo impacto visual:** Pantalla RLCD monocroma de 4.2" alimentada por luz ambiental con refresco parcial mediante SPI DMA y buffers en **PSRAM** (`MALLOC_CAP_SPIRAM`).
- **Concurrencia determinista:** Tareas de FreeRTOS distribuidas entre núcleos (`vTaskHttpServer`, `vTaskLVGLRender`, `vTaskPowerManager` y `vTaskSensorRead`), sincronizadas con colas (`xQueueNotif`) y mutex (`xSemaphoreI2C`).
- **Servidor HTTP y panel web local:** API REST en el puerto 80 (`POST /api/v1/notification`, `GET /api/v1/notifications`, `GET /api/v1/status`) con validación JSON (`cJSON`), filtro por lista blanca (hasta 10 aplicaciones), aviso sonoro breve para prioridad 5 y panel web local (`/`) de alto contraste.
- **Conectividad y persistencia:** Modo Wi-Fi STA (2.4 GHz, 802.11 b/g/n) con fallback a punto de acceso (`ESP32-EINK-SETUP`) y almacenamiento en **NVS Flash**.
- **Gestión energética `CR-01`:** Escalado de frecuencia de CPU (80–240 MHz), **Light Sleep** con escucha Wi-Fi DTIM tras 30 segundos de inactividad y soporte para celda Li-Ion 18650 (3500 mAh), orientado a un consumo promedio ponderado `<= 110 mA` y autonomía `>= 24 horas`.

## Estructura del repositorio
- `CMakeLists.txt`, `sdkconfig.defaults` y `partitions.csv`: configuración de compilación, PSRAM, administración de energía y particiones para Flash de 16 MB.
- `main/main.c`: inicialización de NVS, colas, mutex y creación de tareas FreeRTOS.
- `main/wifi_manager.h` y `main/wifi_manager.c`: gestión de conexión STA, reintentos y modo AP.
- `main/http_server.h` y `main/http_server.c`: servidor HTTP, validación JSON, whitelist, historial y panel web local.
- `main/gui_rlcd.h` y `main/gui_rlcd.c`: buffers en PSRAM, refresco parcial sobre ST7305 y actualización de pantalla.
- `main/peripherals.h` y `main/peripherals.c`: lectura de sensores/RTC/batería con mutex I2C y pulso de alerta para prioridad 5.
- `main/power_mgmt.h` y `main/power_mgmt.c`: control de inactividad y perfil energético `CR-01`.
- `web/index.html`: interfaz web local de alto contraste para enviar alertas y consultar el historial.
- `scripts/test_notify.sh`: pruebas con `curl` para payloads válidos (`HTTP 200`), inválidos (`HTTP 400`) y consulta de estado.

## Compilación y flasheo (ESP-IDF v5.x)
```bash
idf.py set-target esp32s3
idf.py build
idf.py -p /dev/ttyUSB0 flash monitor
