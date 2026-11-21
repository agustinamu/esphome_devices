# Humidificador - ESP32-C3 Super Mini

Control de humidificador mediante pulsos GPIO que simulan botones físicos.

## Hardware

- **Placa**: ESP32-C3 Super Mini (4MB flash integrada, ESP-IDF)
- **GPIO5**: Power, en paralelo al pulsador
- **GPIO6**: Luz, en paralelo al pulsador
- Pines open-drain bidireccionales: pulsar = tirar a GND 500 ms; además leen
  las pulsaciones físicas para mantener el estado.
- **Las líneas de pulsador están a 5 V** (pull-up del humidificador; pulsar = 0 V).
  Corriente de cortocircuito a GND por debajo de la resolución del multímetro
  (pull-up grande, decenas de kΩ o más) → conexión directa al C3, sin resistencia.
  Fuera de especificación (máx. ~3,6 V) pero la corriente al diodo de protección es de µA.
  Si falla, poner un conversor de nivel BSS138 (HV=5 V, LV=3,3 V) o un Schottky en serie
  (cátodo al humidificador + `pullup: true`).
- Alimentación: 5 V del propio humidificador al pin `5V` del Super Mini (GND común).
- Al recibir corriente el aparato arranca apagado → switches `ALWAYS_OFF`.
- WiFi con `min_auth_mode: WPA3`. Si la señal es mala (antena defectuosa en algunos
  lotes de C3 Super Mini), probar `output_power: 8.5dB`.

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
