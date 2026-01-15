# ESPHome Devices

Configuraciones ESPHome para dispositivos del hogar, usables como remote packages desde Home Assistant.

## Dispositivos disponibles

| Dispositivo | Archivo | Descripción |
|-------------|---------|-------------|
| Humidificador | `devices/humidificador.yaml` | Control via pulsos GPIO (ESP8266) |
| Guition 5" Básico | `devices/guition-jc8048w550.yaml` | Reloj, WiFi, botón de prueba |
| Guition 5" Bambu | `devices/guition-jc8048w550-bambu.yaml` | Dashboard para impresora Bambu Lab P1S |

## Configuraciones para ESPHome en Home Assistant

### Humidificador (ESP8266)

```yaml
esphome:
  name: humidificador
  friendly_name: Humidificador

packages:
  - url: https://github.com/agustinamu/esphome_devices
    ref: develop
    files: [devices/humidificador.yaml]
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
    ssid: "Humidificador Fallback"
    password: !secret fallback_password
```

### Guition 5" - Ejemplo básico (reloj, wifi, botón)

```yaml
esphome:
  name: guition-pantalla
  friendly_name: Pantalla Guition

packages:
  - url: https://github.com/agustinamu/esphome_devices
    ref: develop
    files: [devices/guition-jc8048w550.yaml]
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
    ssid: "Guition Fallback"
    password: !secret fallback_password
```

### Guition 5" - Dashboard Bambu Lab P1S

```yaml
esphome:
  name: guition-bambu
  friendly_name: Panel Bambu

packages:
  - url: https://github.com/agustinamu/esphome_devices
    ref: develop
    files: [devices/guition-jc8048w550-bambu.yaml]
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
    ssid: "Guition Bambu Fallback"
    password: !secret fallback_password
```

## Guition JC8048W550

Pantalla táctil 5" 800x480 con ESP32-S3.

### Especificaciones
- **CPU**: ESP32-S3 dual-core 240MHz
- **Memoria**: 16MB Flash, 8MB PSRAM
- **Display**: IPS 800x480, driver ST7262
- **Touch**: Capacitivo GT911

### Dashboard Bambu Lab

Funcionalidades:
- Temperaturas en tiempo real (nozzle, cama)
- Progreso de impresión con barra
- Tiempo restante
- Controles: pausar, reanudar, cancelar
- Accesos rápidos: home, filamento, precalentar, enfriar, luz

## Requisitos

- Home Assistant con ESPHome addon
- Para Bambu Lab: integración [ha-bambulab](https://github.com/greghesp/ha-bambulab) via HACS

## Licencia

MIT
