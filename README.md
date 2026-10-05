# Proyecto PCB

Proyecto de Diseño PCB **TerraVisionIA**

## Descripción

TerraVision es un sistema embebido para el monitoreo y la clasificación de suelos, evolución del proyecto SueloSmart. El objetivo es que el sistema vaya más allá de lo que una persona puede apreciar a simple vista en la tierra, combinando visión artificial en el borde (*edge AI*) con medición de variables físico-químicas del suelo.

Este repositorio contiene el diseño de la PCB (esquemáticos y placa) desarrollado en **EasyEDA**.

## Arquitectura

El sistema se compone de dos nodos basados en ESP32-S3, interconectados mediante pistas dentro de la misma PCB (sin cables ni BLE):

| Nodo | Función |
|------|---------|
| **ESP32-S3-CAM** (módulo con OV5640) | Captura de imágenes y clasificación del suelo con un modelo de Edge Impulse. Se enchufa a la PCB mediante pines hembra. |
| **ESP32-S3 (Sensores)** | Lectura de sensores, gestión de energía, almacenamiento, GPS, reloj, pantalla e interfaz de usuario. Usa un ESP32-S3-WROOM-1 integrado directamente en la placa. |

La comunicación entre ambos nodos se hace por las redes `TXD0` y `RXD0`.

## Características del hardware

- **Alimentación:** celda solar de 12 V + batería de 12 V, con regulación a 3.3 V mediante convertidor buck AP63203WU-7.
- **Protecciones de entrada:** fusible PTC, diodo contra polaridad inversa (SS34) y TVS (SMBJ15A).
- **Programación:** USB-C con CH340C y circuito de auto-reset (DTR/RTS hacia EN/Boot) para ambos nodos.
- **Sensor de suelo:** sensor NPK "7 en 1" por RS485, con conector industrial M12.
- **Reloj en tiempo real:** DS3231N con pila de respaldo CR1220.
- **Almacenamiento:** socket microSD en modo SPI.
- **Geolocalización:** módulo GPS para asociar cada lectura a su ubicación.
- **Pantalla:** ePaper.
- **Botones:** EN, Boot, ON/OFF, Iniciar (captura) y Subir (envío de datos a la nube).

## Funcionalidades previstas

- Clasificación del tipo de suelo mediante IA embebida (Edge Impulse).
- Sistema de recomendación de cultivos o plantas apropiadas según el suelo detectado.
- Envío de lecturas geolocalizadas a Huawei Cloud y mapa de datos históricos.

## Validación de la PCB (EVT y DVT)

Las pruebas siguen las etapas vistas en clase (Énfasis II): primero **EVT**, para comprobar que el diseño cumple los requisitos funcionales básicos con prototipos iniciales, y luego **DVT**, para comprobar confiabilidad, normas y desempeño con prototipos cercanos al producto final.

### Engineering Validation Testing (EVT)

Objetivo: validar que la PCB funciona según la especificación, identificar errores de ingeniería (eléctricos, mecánicos, firmware) y reducir riesgos antes de pasar a DVT.

| # | Prueba EVT | Aplicación en TerraVision |
|---|------------|---------------------------|
| 1 | **Prueba funcional básica** | Arranque de ambos ESP32-S3, programación por USB-C (CH340C con auto-reset), captura de imagen con OV5640, lectura del sensor NPK por RS485, hora del RTC DS3231N, escritura y lectura en microSD, posición del GPS, refresco de la pantalla ePaper y respuesta de los botones (EN, Boot, ON/OFF, Iniciar, Subir). |
| 2 | **Medición de energía** | Corriente y consumo en reposo, captura de imagen, inferencia de IA, lectura de sensores, GPS y envío de datos. Sirve para dimensionar la batería de 12 V y el panel solar. |
| 3 | **Calidad de la señal** | Intensidad y estabilidad del Wi-Fi hacia Huawei Cloud, tiempo hasta obtener posición del GPS (*fix*), integridad del enlace UART `TXD0`/`RXD0` entre nodos y de la línea RS485 con el sensor NPK. |
| 4 | **Prueba de conformidad** | Revisar que el diseño cumpla los estándares aplicables: módulos ESP32-S3 ya certificados para radio, protección de la caja (referencia IP67 para uso en campo) y uso de componentes conformes a RoHS. |
| 5 | **Preescaneo EMI** | Medición preliminar de ruido y emisiones del buck AP63203WU-7 (12 V a 3.3 V), del RS485, del GPS y de la radio del ESP32, para detectar interferencias antes de fabricar más unidades. |
| 6 | **Prueba térmica y de 4 esquinas** | Operar la placa en temperatura mínima y máxima esperadas en campo (por ejemplo, exposición directa al sol), con carga baja y alta (cámara, IA y transmisión activas), vigilando regulador, módulo ESP32-S3 y estabilidad de las lecturas. |
| 7 | **Mediciones paramétricas y validación de especificaciones** | Tensión de 3.3 V y rizado, tensión de entrada, corriente máxima, precisión del RTC, valores del NPK frente a referencia, resolución de imagen y tiempo de inferencia del modelo. |

**Lista de chequeo EVT aplicada al proyecto**

- [ ] Sensores: precisión y calibración del NPK.
- [ ] Muestreo y latencia de los sensores.
- [ ] Actuadores y control: respuesta de botones, pantalla ePaper y activación de la cámara.
- [ ] Lógica de control (firmware) en ambos nodos.
- [ ] Comunicaciones: Wi-Fi, GPS, UART entre nodos y RS485.
- [ ] Integridad del PCB y de los componentes (continuidad, cortos, soldaduras, polaridad).
- [ ] Gestión de energía y consumo.
- [ ] Almacenamiento de datos y registro local en microSD.
- [ ] Manejo de errores y recuperación (fallo de sensor, SD ausente, pérdida de Wi-Fi o GPS).
- [ ] Interfaz de usuario local (botones y ePaper).
- [ ] Firmware: actualización OTA.
- [ ] Seguridad básica (acceso y configuración).
- [ ] Documentación técnica EVT (fallos encontrados y correcciones).

### Design Validation Testing (DVT)

Objetivo: validar el desempeño frente a las especificaciones finales, garantizar seguridad, confiabilidad y cumplimiento de normas, y detectar problemas de manufacturabilidad con prototipos cercanos al producto final.

| # | Prueba DVT | Aplicación en TerraVision |
|---|------------|---------------------------|
| 1 | **Pruebas de funciones completas** | Recorrer todo el flujo: captura, clasificación del suelo, lectura NPK, geolocalización, guardado en microSD, envío a Huawei Cloud, mapa histórico y recomendación de cultivos. |
| 2 | **Pruebas de rendimiento** | Tiempo de captura, inferencia, lectura de sensores y subida de datos; precisión de la clasificación de suelos con muestras reales. |
| 3 | **Estrés ambiental y envejecimiento (ESS)** | Temperaturas extremas, humedad alta, cambios rápidos de temperatura y vibración, con la placa dentro de su caja. |
| 4 | **Pruebas de confiabilidad** | Operación continua durante un período prolongado con ciclos de carga y descarga del panel solar y la batería, buscando bloqueos, reinicios o pérdida de datos. |
| 5 | **Pruebas EMI/EMC** | Verificar que la placa no interfiera con otros dispositivos y que RS485, GPS y Wi-Fi no se degraden entre sí. |
| 6 | **Certificación de seguridad** | Seguridad eléctrica y protecciones de entrada (fusible PTC, diodo SS34 contra polaridad inversa, TVS SMBJ15A) y seguridad de la batería. |

**Lista de chequeo DVT aplicada al proyecto**

- [ ] Pruebas ambientales (temperatura y humedad).
- [ ] Vibración y choque (transporte e instalación).
- [ ] Compatibilidad electromagnética (EMC/EMI).
- [ ] Seguridad eléctrica y normas.
- [ ] Durabilidad y vida útil (MTTF/MTBF).
- [ ] Usabilidad en campo (ergonomía de los botones y legibilidad del ePaper al sol).
- [ ] Instalación y mantenimiento (cambio de pila CR1220, microSD y sensor NPK).
- [ ] Calidad de la carcasa y resistencia física.
- [ ] Conformidad normativa local.
- [ ] Interoperabilidad y escalabilidad (varios dispositivos enviando a la nube).
- [ ] Documentación de usuario y certificaciones.
- [ ] Validación energética y eficiencia (autonomía con energía solar).

### Resumen EVT vs. DVT

| Aspecto | EVT | DVT |
|---------|-----|-----|
| Foco | Funcionalidad básica | Confiabilidad y estándares |
| Prototipos | Iniciales | Cercanos al final |
| Riesgo | Alto | Medio |
| Salida | Corrección de diseño | Aprobación para producción |

## Herramientas

- EasyEDA (diseño de esquemáticos y PCB)
- Autodesk Fusion (Enclosure)
- Edge Impulse

## Estructura del repositorio

```
├── schematics/   # Esquemáticos
├── pcb/          # Diseño de la placa
├── gerbers/      # Archivos de fabricación
└── README.md
```

> Ajusta esta sección a las carpetas reales de tu repositorio.

## Autor

Edison — Ingeniería en Electrónica y Telecomunicaciones, Universidad del Cauca (FIET).
