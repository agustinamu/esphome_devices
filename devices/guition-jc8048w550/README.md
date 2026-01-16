# Guition JC8048W550 - Ejemplo básico

Ejemplo minimalista con reloj y botón de prueba.

## Hardware

- **Placa**: ESP32-S3 N16R8
- **Display**: 5" IPS 800x480 RGB (ST7262)
- **Touch**: GT911 I2C

## Uso

```yaml
# En ESPHome de Home Assistant
packages:
  - url: https://github.com/agustinamu/esphome_devices
    ref: develop
    files: [devices/guition-jc8048w550/package.yaml]
```

## Estructura

```
guition-jc8048w550/
├── esphome.yaml   # Config para Home Assistant
├── package.yaml   # Lógica (usa common/base)
└── test.yaml      # Validación local
```

## Alternativas

- **[OpenHASP](https://www.openhasp.com/latest/hardware/sunton/esp32-8048s050c/)** - Si prefieres diseñador visual
