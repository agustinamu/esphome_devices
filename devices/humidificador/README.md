# Humidificador - ESP8266

Control de humidificador mediante pulsos GPIO que simulan botones físicos.

## Hardware

- **Placa**: ESP-01 1M (ESP8266)
- **GPIO14**: Power (pulso para encender/apagar)
- **GPIO16**: Luz (pulso para cambiar)

## Uso

```yaml
# En ESPHome de Home Assistant
packages:
  - url: https://github.com/agustinamu/esphome_devices
    ref: develop
    files: [devices/humidificador/package.yaml]
```

## Estructura

```
humidificador/
├── esphome.yaml   # Config para Home Assistant
├── package.yaml   # Lógica del dispositivo
└── test.yaml      # Validación local
```
