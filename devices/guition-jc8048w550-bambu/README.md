# Guition JC8048W550 - Panel Bambu Lab

Dashboard táctil para impresoras Bambu Lab P1S/X1C.

## Funcionalidades

- **Home**: Progreso, temperaturas, pause/resume/stop
- **Controls**: Ajuste temperaturas, presets materiales
- **Motion**: Control manual XYZ, homing
- **Info**: WiFi, IP, uptime, estado

## Requisitos

1. [ha-bambulab](https://github.com/greghesp/ha-bambulab) instalado en HACS
2. Impresora Bambu Lab conectada a Home Assistant

## Configuración

```yaml
# esphome.yaml en Home Assistant
substitutions:
  bambu_device_id: "TU_DEVICE_ID"      # Configuración > Dispositivos > Bambu
  bambu_prefix: "p1s_01p00c581002790"  # Prefijo de entidades
```

## Estructura

```
guition-jc8048w550-bambu/
├── esphome.yaml      # Config HA + substitutions
├── package.yaml      # Orquestador
├── core.yaml         # Hardware + auto-dim
├── sensors.yaml      # Entidades Bambu
├── ui.yaml           # Orquestador UI
└── ui/
    ├── fonts.yaml    # Montserrat + MDI
    ├── lvgl.yaml     # Estilos + páginas
    └── intervals.yaml # Actualizaciones
```

## Alternativas

- **[OpenHASP](https://www.openhasp.com/)** - Firmware con diseñador visual
  - [Guition en OpenHASP](https://www.openhasp.com/latest/hardware/sunton/esp32-8048s050c/)
  - Más fácil para diseños simples, menos flexible para lógica compleja
