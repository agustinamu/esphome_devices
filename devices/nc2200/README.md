# Cargador SkyRC NC2200

Lee las 4 ranuras del cargador NiMH SkyRC NC2200 por el puerto USB-A donde va el dongle
Bluetooth (SK-600135), sin el dongle. Por ranura publica voltaje, corriente, capacidad,
temperatura, resistencia interna, tiempo y estado.

Estado: prueba de concepto con un Wemos D1 mini; solo lectura.

## Conexión

El puerto USB-A del cargador **no es USB**: es un UART de 3,3 V a 115200 8N1.

| Pin USB-A | Color típico | Señal | D1 mini |
|---|---|---|---|
| 1 | rojo | VBUS 5 V | **sin conectar** |
| 2 | blanco | TX del cargador (D−) | RX (GPIO3) |
| 3 | verde | RX del cargador (D+) | TX (GPIO1) |
| 4 | negro | GND | G |

- **VBUS no da corriente** para un ESP: sin carga mide 5 V, con el D1 mini cae a ~1 V. El
  dongle oficial declara ≤20 mA. Alimentar el ESP aparte.
- **Conectar RX/TX con los dos aparatos encendidos.** Con uno apagado, el TX del otro lo
  alimenta a medias por el pin (el cargador no llega a arrancar).
- Primera instalación por USB con el D1 mini **desconectado del cargador** (el UART0 es el
  mismo que el del conversor USB).
- Cuando el cargador recibe tramas válidas muestra el icono de Bluetooth.

## Protocolo

El dongle oficial (módulo Tuya BT3L, BLE `FFE0`/`FFE1`) es un puente transparente: las
tramas del cable son las mismas que las de la app SkyCharger, documentadas en
[srThibaultP/ha-skyrc-charger](https://github.com/srThibaultP/ha-skyrc-charger).

```
0F | len | cmd | datos... | suma(cmd..datos) & 0xFF        len = bytes tras len
```

Info (`0x57`), respuesta real:

```
>>> 0F 13 57 00 (17 x 00) 57
<<< 0F 13 57 00 31 30 30 31 38 31 02 00 00 00 00 01 0F 02 00 C1 57
               "100181" = NC2200, firmware 1.15
```

Estado de una ranura (`0x91` + índice 0-3), respuesta real de la ranura 1 descargando:

```
>>> 0F 03 91 00 91
<<< 0F 12 91 00 | 00 35 | 04 69 | 00 19 | 00 02 | 00 DD | 00 00 | 08 | 02 | 00 | 35
                  53 mA  1129 mV  25 °C   2 mAh   221 s   0 mΩ   st  modo err chk
```

Big-endian. Estado: 0 en espera, 1-6 preparando, 7 cargando, 8 descargando, 9 enfriando,
10 terminado. Modo: 1 carga, 2 descarga, 3 refresh, 4 break-in, 5 turbo.

## Pendiente

- Pasar a ESP32-C3 (UART libre del USB, logs por USB).
- El D1 mini pierde la WiFi a veces al conectar RX/TX: sin diagnosticar.
- Arrancar y parar ranuras (`0xFE` para parar, según la librería; sin probar).
