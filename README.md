# ESPHome Devices

Configuraciones ESPHome para dispositivos del hogar, usables como remote packages desde Home Assistant.

## Dispositivos

| Dispositivo | Hardware | Descripción |
|-------------|----------|-------------|
| [humidificador](devices/humidificador/) | ESP-01 (ESP8266) | Control por pulsos GPIO |
| [guition-jc8048w550](devices/guition-jc8048w550/) | ESP32-S3 + Display 5" | Ejemplo básico con LVGL |
| [guition-jc8048w550-bambu](devices/guition-jc8048w550-bambu/) | ESP32-S3 + Display 5" | Dashboard completo para Bambu Lab P1S/X1C |

## Guía de Inicio Rápido

### Requisitos previos

1. **Home Assistant** con [ESPHome Add-on](https://esphome.io/guides/getting_started_hassio.html) instalado
2. **Hardware compatible** (ver tabla de dispositivos)
3. Para Bambu Lab: [ha-bambulab](https://github.com/greghesp/ha-bambulab) instalado via HACS

### Instalación en Home Assistant

1. Abre ESPHome en Home Assistant
2. Click en **+ NEW DEVICE**
3. Selecciona **Continue** → escribe un nombre → **Skip this step** (para editar manualmente)
4. Reemplaza el contenido con el `esphome.yaml` del dispositivo deseado
5. Ajusta los `substitutions` según el README del dispositivo:
   ```yaml
   substitutions:
     device_name: "mi-dispositivo"
     # ... otros valores específicos del dispositivo
   ```
6. Click en **INSTALL** → selecciona método (USB primera vez, OTA después)

### Primera instalación (USB)

Para la primera instalación necesitas conexión USB:

1. Conecta el dispositivo al ordenador
2. En ESPHome, click **INSTALL** → **Plug into this computer**
3. Selecciona el puerto COM/ttyUSB
4. Espera a que compile y flashee

### Actualizaciones posteriores (OTA)

Una vez instalado, puedes actualizar via WiFi:

1. En ESPHome, click **INSTALL** → **Wirelessly**
2. El dispositivo debe estar en la misma red

## Estructura del Repositorio

```
devices/
├── common/                        # Hardware compartido
│   └── guition-jc8048w550-base.yaml
├── humidificador/                 # ESP8266 - Control humidificador
├── guition-jc8048w550/            # ESP32-S3 - Ejemplo básico
└── guition-jc8048w550-bambu/      # ESP32-S3 - Dashboard Bambu Lab
    ├── core.yaml                  # Hardware + auto-dim
    ├── sensors.yaml               # Entidades de Home Assistant
    └── ui/                        # Interfaz LVGL modular
        ├── fonts.yaml
        ├── styles.yaml
        ├── lvgl.yaml
        ├── intervals.yaml
        └── pages/                 # Páginas individuales
```

## Desarrollo Local

### Validar configuración

```bash
# Instalar ESPHome CLI
pip install esphome
# o con uv
uv tool install esphome

# Validar sin compilar
esphome config devices/humidificador/test.yaml
esphome config devices/guition-jc8048w550/test.yaml
esphome config devices/guition-jc8048w550-bambu/test.yaml
```

### Compilar localmente

```bash
esphome compile devices/guition-jc8048w550-bambu/test.yaml
```

## Alternativas

- **[OpenHASP](https://www.openhasp.com/)** - Firmware con diseñador visual para displays táctiles

## Licencia

MIT
