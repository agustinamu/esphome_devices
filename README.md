# ESPHome Devices

Configuraciones ESPHome para dispositivos del hogar, usables como remote packages desde Home Assistant.

## Estructura

```
devices/
├── common/                        # Paquetes compartidos
│   └── guition-jc8048w550-base.yaml
├── humidificador/                 # ESP8266 - Control humidificador
├── guition-jc8048w550/            # ESP32-S3 - Ejemplo básico
└── guition-jc8048w550-bambu/      # ESP32-S3 - Dashboard Bambu Lab
    ├── core.yaml
    ├── sensors.yaml
    └── ui/                        # UI modular
```

Cada dispositivo tiene su propio README.md con detalles específicos.

## Instalación

1. Abre la carpeta del dispositivo
2. Copia el contenido de `esphome.yaml`
3. Pega en un nuevo dispositivo en ESPHome de Home Assistant
4. Ajusta los secretos y substitutions según el README del dispositivo
5. Compila e instala

## Test local

```bash
esphome config devices/humidificador/test.yaml
esphome config devices/guition-jc8048w550/test.yaml
esphome config devices/guition-jc8048w550-bambu/test.yaml
```

## Alternativas

- **[OpenHASP](https://www.openhasp.com/)** - Firmware con diseñador visual para displays táctiles

## Licencia

MIT
