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

## Herramientas

- EasyEDA (diseño de esquemáticos y PCB)
- ESP32-S3 / Arduino
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
