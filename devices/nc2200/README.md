# Cargador SkyRC NC2200

Lee las 4 ranuras del cargador NiMH SkyRC NC2200 por el puerto USB-A donde va el dongle
Bluetooth (SK-600135), sin el dongle. Por ranura publica voltaje, corriente, capacidad,
temperatura, resistencia interna, tiempo y estado.

Estado: prueba de concepto con un Wemos D1 mini. Lee las ranuras y los ajustes, y cambia el
programa de cada ranura. **No puede parar**: el cargador no tiene ese comando (la app tampoco).

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

Comandos del NC2200, sacados de la app oficial SkyRC 2.94 (`com.skyrc.q200`, descompilada):

| cmd | qué | datos | respuesta |
|---|---|---|---|
| `0x91` | estado de ranura | índice 0-3 | la de arriba |
| `0x93` | leer programa | índice | índice, modo, carga mA, descarga mA, capacidad mAh (16 bits) |
| `0x94` | escribir programa **y empezar** | índice, modo, carga, descarga, capacidad, relleno a 17 bytes | índice, 1 = OK |
| `0x96` | leer ajustes | `04` | `04`, ΔV mV, corte descarga V/100, corte carga V/100 (0 = off), goteo mA |
| `0x95` | escribir ajustes | `04`, mismos campos, relleno a 17 | 1 = OK |
| `0x92` | turbo | `04` | 1 = OK |
| `0x97` | reset de fábrica | `04` | sin probar |

Modo: 1 carga, 2 descarga, 3 refresh, 4 break-in. La app pone a 0 los campos que el modo no
usa. Rangos de la app: carga 200-2000 mA, descarga 100-1000 mA, capacidad 500-3200 mAh (de 100
en 100). `0x94` reinicia la ranura aunque esté en marcha (probado). `0xFE` (parar en el MC5000)
no hace nada en el NC2200.

En Home Assistant, por ranura: programa, corrientes y capacidad, leídos del cargador. Un
cambio se manda al momento (y reinicia la ranura) solo si afecta al modo elegido; el resto se
guarda para cuando se cambie de modo. No hay botón de reiniciar a secas. El campo «Enviar trama» manda cualquier `cmd datos` en hex.

## Pendiente

- Pasar a ESP32-C3 (UART libre del USB, logs por USB).
- El D1 mini pierde la WiFi a veces al conectar RX/TX: sin diagnosticar.
