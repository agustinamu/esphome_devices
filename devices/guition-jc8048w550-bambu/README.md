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

### Secuencia de Purga
```gcode
G1 X60 Y265 F12000  ; Ir a zona de residuos
M109 S[temp]        ; Esperar temperatura
G92 E0              ; Reset extrusor
G1 E20 F200         ; Purgar 20mm
G1 E10 F200         ; Purgar 10mm más
M106 P1 S255        ; Fan para enfriar
G1 E5 F300          ; Purgar 5mm
G1 E-1.0 F300       ; Retracción anti-ooze
; Movimientos de limpieza X65-X165
```

### Notas

- El cortador requiere el nozzle caliente (>180°C) para funcionar
- Las coordenadas Y negativas están fuera del área de impresión
- Zona de residuos/calentamiento: X60-X70 Y265
- Fuentes:
  - [BambuStudio Issues](https://github.com/bambulab/BambuStudio/issues/271)
  - [Community gcode gist](https://gist.github.com/codeincontext/4efc5820e7fd4167b231dffcc4d9ccd6)
  - [BBL P1S organized start/end gcode](https://forum.bambulab.com/t/bbl-p1s-organized-start-and-end-gcode/38795/7)

## Entidades de Ventiladores

La integración ha-bambulab expone los ventiladores como entidades `fan.*`:

| Ventilador | Entity ID | M-code directo |
|------------|-----------|----------------|
| Part Cooling | `fan.{prefix}_ventilador_de_enfriamiento` | `M106 P1 S0-255` |
| Auxiliar | `fan.{prefix}_ventilador_auxiliar` | `M106 P2 S0-255` |
| Carcasa/Chamber | `fan.{prefix}_ventilador_de_la_carcasa` | `M106 P3 S0-255` |

Control mediante `fan.set_percentage` con valores 0-100.

## M-codes Útiles P1S

| Comando | Descripción |
|---------|-------------|
| `M104 Sxxx` | Setear temp nozzle (no espera) |
| `M109 Sxxx` | Setear temp nozzle (espera) |
| `M140 Sxxx` | Setear temp cama (no espera) |
| `M190 Sxxx` | Setear temp cama (espera) |
| `M106 P1 Sxxx` | Part cooling fan (0-255) |
| `M106 P2 Sxxx` | AUX fan (0-255) |
| `M106 P3 Sxxx` | Chamber fan (0-255) |
| `M412 S1` | Activar detección filamento |
| `M975 S1` | Activar supresión vibración |
| `M620 S255` | Preparar unload a AMS |
| `T255` | Ejecutar unload a AMS |
| `M83` | Extrusión relativa |
| `M82` | Extrusión absoluta |
| `G28 X Y` | Home ejes X e Y |
| `G28 Z` | Home eje Z |
| `G91` | Movimiento relativo |
| `G90` | Movimiento absoluto |

## Alternativas

- **[OpenHASP](https://www.openhasp.com/)** - Firmware con diseñador visual
  - [Guition en OpenHASP](https://www.openhasp.com/0.7.0/hardware/guition/jc8048w550/)
  - Más fácil para diseños simples, menos flexible para lógica compleja
