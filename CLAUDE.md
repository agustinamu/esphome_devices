# ESPHome Devices Repository

## Descripción
Repositorio de configuraciones ESPHome para dispositivos del hogar, diseñado para usarse con Home Assistant mediante remote packages.

## Estructura

```
├── devices/                    # Configuraciones de dispositivos
│   ├── humidificador.yaml     # Control de humidificador via GPIO pulsos
│   ├── guition-jc8048w550.yaml         # Pantalla 5" - ejemplo básico (reloj, wifi, botón)
│   └── guition-jc8048w550-bambu.yaml   # Pantalla 5" - dashboard Bambu Lab P1S
├── secrets.yaml.example        # Template de secretos
└── .gitignore
```

## Dispositivos

### Humidificador (ESP8266)
- **Placa**: ESP-01 1M
- **Función**: Control de humidificador mediante pulsos en GPIO
- **Pines**: GPIO14 (power), GPIO16 (luz)

### Guition JC8048W550 (ESP32-S3)
- **Pantalla**: 5" IPS 800x480
- **Touch**: GT911 capacitivo (I2C)
- **Driver**: ST7262 RGB paralelo
- **PSRAM**: 8MB Octal
- **Flash**: 16MB

#### Pines importantes:
- **Backlight**: GPIO2
- **I2C**: SDA=GPIO19, SCL=GPIO20
- **Display**: DE=GPIO40, HSYNC=GPIO39, VSYNC=GPIO41, PCLK=GPIO42

## Uso desde Home Assistant

Los dispositivos se usan como remote packages. Ejemplo:

```yaml
esphome:
  name: mi-dispositivo
  friendly_name: Mi Dispositivo

packages:
  - url: https://github.com/agustinamu/esphome_devices
    ref: develop
    files: [devices/nombre-archivo.yaml]
    refresh: 1d

api:
  encryption:
    key: !secret api_key

ota:
  - platform: esphome
    password: !secret ota_password

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  ap:
    ssid: "Fallback"
    password: !secret fallback_password
```

## Integración Bambu Lab

El dashboard `guition-jc8048w550-bambu.yaml` está configurado para una **P1S** con serial `01P00C581002790`.

Para otra impresora, cambiar el prefijo de entity_id:
- Actual: `p1s_01p00c581002790_`
- Buscar y reemplazar por el prefijo de tu impresora

### Entidades usadas:
- `sensor.*_nozzle_temperature`
- `sensor.*_bed_temperature`
- `sensor.*_print_progress`
- `sensor.*_remaining_time`
- `sensor.*_current_stage`
- `sensor.*_task_name`
- `number.*_nozzle_target_temperature`
- `number.*_bed_target_temperature`
- `button.*_pause_printing`
- `button.*_resume_printing`
- `button.*_stop_printing`
- `button.*_home`
- `light.*_chamber_light`

## Notas técnicas

### Framework
Los dispositivos ESP32-S3 con pantalla RGB usan **ESP-IDF** (no Arduino) por requisitos de rendimiento del display.

### Compilación
Primera compilación: ~15-25 min en RPi
Compilaciones incrementales: ~2-5 min

### LVGL
Se usa LVGL para la interfaz gráfica. Acciones disponibles:
- `lvgl.label.update` - actualizar texto
- `lvgl.bar.update` - actualizar barra de progreso
- `lvgl.page.show` - cambiar de página
- `homeassistant.action` - llamar acciones de HA
