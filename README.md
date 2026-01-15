# ESPHome Devices

Configuraciones ESPHome para dispositivos del hogar, usables como remote packages desde Home Assistant.

## Estructura

```
devices/
├── humidificador/
│   ├── esphome.yaml      # ← Copiar a Home Assistant
│   └── package.yaml      # ← Se carga automáticamente
├── guition-jc8048w550/
│   ├── esphome.yaml      # ← Copiar a Home Assistant
│   └── package.yaml      # ← Se carga automáticamente
└── guition-jc8048w550-bambu/
    ├── esphome.yaml      # ← Copiar a Home Assistant
    └── package.yaml      # ← Se carga automáticamente
```

## Dispositivos

### Humidificador (ESP8266)
Control de humidificador mediante pulsos GPIO.
- **Placa**: ESP-01 1M
- **Funciones**: Encender/apagar, control de luz

### Guition JC8048W550 - Básico
Pantalla táctil 5" con ejemplo básico.
- **Funciones**: Reloj, estado WiFi, botón de prueba

### Guition JC8048W550 - Bambu Lab
Dashboard para impresora Bambu Lab P1S.
- **Funciones**: Temperaturas, progreso, control de impresión
- **Requiere**: Integración [ha-bambulab](https://github.com/greghesp/ha-bambulab)

## Instalación

1. Abre la carpeta del dispositivo que quieres instalar
2. Copia el contenido de `esphome.yaml`
3. Pega en un nuevo dispositivo en ESPHome de Home Assistant
4. Ajusta los secretos en tu `secrets.yaml` de ESPHome
5. Compila e instala

## Guition JC8048W550 - Especificaciones

| Componente | Especificación |
|------------|----------------|
| CPU | ESP32-S3 dual-core 240MHz |
| Flash | 16MB |
| PSRAM | 8MB Octal |
| Display | 5" IPS 800x480 RGB |
| Touch | GT911 capacitivo I2C |
| Backlight | GPIO2 (PWM) |

### Pinout

| Función | GPIO |
|---------|------|
| Backlight | 2 |
| I2C SDA | 19 |
| I2C SCL | 20 |
| Display DE | 40 |
| Display HSYNC | 39 |
| Display VSYNC | 41 |
| Display PCLK | 42 |

## Integración Bambu Lab

El dashboard está configurado para P1S con entidades en español.

Para otra impresora, busca y reemplaza el prefijo `p1s_01p00c581002790_` por el de tu impresora en `package.yaml`.

### Funcionalidades

- Temperaturas en tiempo real (nozzle, cama)
- Progreso de impresión con barra y porcentaje
- Capas actual / total
- Tiempo restante
- Botones: pausar, reanudar, cancelar
- Presets: PLA, PETG, enfriar
- Control de luz de cámara
- Auto-dim después de 60 segundos

## Requisitos

- Home Assistant con ESPHome addon
- Para Bambu Lab: integración ha-bambulab via HACS

## Licencia

MIT
