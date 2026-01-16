# Guition JC8048W550 - Panel Bambu Lab

Dashboard táctil para impresoras Bambu Lab P1S/X1C.

## Funcionalidades

- **Home**: Progreso, temperaturas, ventiladores, pause/resume/stop
- **Controls**: Ajuste temperaturas, presets materiales, luz, ventiladores
- **Motion**: Control manual XYZ, homing
- **Filament**: Purge zone, extruir/retraer, load/unload, cortar
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

## Operaciones de Filamento

### Coordenadas del Cortador P1S

El cortador de filamento está ubicado fuera del área de impresión:

| Posición | Coordenadas |
|----------|-------------|
| Aproximación | X20 Y50 |
| Cortador | X20 Y-3 |
| Zona purga | X5 Y5 Z50 |

### Secuencias G-code

#### UNLOAD (Descargar filamento)
```gcode
M104 S240          ; Calentar nozzle
M109 S240          ; Esperar temperatura
M83                ; Extrusión relativa
G1 E-20 F200       ; Retracción inicial
G28 X Y            ; Home XY
G1 X20 Y50 F20000  ; Aproximar al cortador
G1 Y-3 F3000       ; Enganchar cortador
G4 P500            ; Pausa 0.5s
G1 E-80 F1500      ; Retracción completa
M82                ; Extrusión absoluta
G28 X Y            ; Home XY
M104 S0            ; Apagar nozzle
```

#### LOAD (Cargar filamento)
```gcode
M104 S220          ; Calentar nozzle (PLA)
M109 S220          ; Esperar temperatura
M83                ; Extrusión relativa
G1 E18 F200        ; Extruir lento
G1 E2 F20          ; Extruir muy lento (prime)
G1 E50 F300        ; Extruir rápido
M82                ; Extrusión absoluta
```

#### CUT (Cortar filamento)
```gcode
G28 X Y            ; Home XY
G1 X20 Y50 F20000  ; Aproximar al cortador
G1 Y-3 F3000       ; Enganchar cortador
G4 P500            ; Pausa 0.5s
G28 X Y            ; Home XY
```

### Notas

- El cortador requiere el nozzle caliente (>180°C) para funcionar
- Las coordenadas Y negativas están fuera del área de impresión
- Estas secuencias fueron obtenidas de [BambuStudio source](https://github.com/bambulab/BambuStudio/issues/271) y [community gcode](https://gist.github.com/codeincontext/4efc5820e7fd4167b231dffcc4d9ccd6)

## Entidades de Ventiladores

La integración ha-bambulab expone los ventiladores como entidades `fan.*`:

| Ventilador | Entity ID |
|------------|-----------|
| Part Cooling | `fan.{prefix}_ventilador_de_enfriamiento` |
| Auxiliar | `fan.{prefix}_ventilador_auxiliar` |
| Carcasa/Chamber | `fan.{prefix}_ventilador_de_la_carcasa` |

Control mediante `fan.set_percentage` con valores 0-100.

## Alternativas

- **[OpenHASP](https://www.openhasp.com/)** - Firmware con diseñador visual
  - [Guition en OpenHASP](https://www.openhasp.com/0.7.0/hardware/guition/jc8048w550/)
  - Más fácil para diseños simples, menos flexible para lógica compleja
