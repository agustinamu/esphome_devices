# UI - Interfaz Bambu Lab

Componentes de interfaz LVGL para el panel Bambu, organizados en archivos modulares.

## Estructura

```
ui/
├── fonts.yaml        # Fuentes: Montserrat (14-48) + MDI icons
├── styles.yaml       # Estilos reutilizables (cards, buttons, etc.)
├── top_layer.yaml    # Sidebar de navegación + touch blocker
├── lvgl.yaml         # Orquestador principal LVGL
├── intervals.yaml    # Actualizaciones periódicas de datos
└── pages/            # Páginas individuales
    ├── home.yaml           # Progreso, temperaturas, controles
    ├── controls.yaml       # Ajuste de temperaturas
    ├── presets.yaml        # Presets de materiales
    ├── fans.yaml           # Control de ventiladores
    ├── motion.yaml         # Menú de movimiento
    ├── motion_xy.yaml      # Control XY del cabezal
    ├── motion_z.yaml       # Control Z de la cama
    ├── filament.yaml       # Gestión de filamento (WIP)
    ├── confirm_pause.yaml  # Confirmación de pausa
    ├── confirm_stop.yaml   # Confirmación de stop
    ├── info.yaml           # Info del sistema + toggle pantalla
    └── screensaver.yaml    # Screensaver con reloj
```

## Archivos Principales

| Archivo | Descripción |
|---------|-------------|
| `fonts.yaml` | Montserrat en tamaños 14, 18, 22, 28, 36, 48 + MDI 32, 48, 64, 96 |
| `styles.yaml` | Estilos: `style_card`, `style_btn`, `style_btn_dark`, `style_btn_red` |
| `top_layer.yaml` | Sidebar con botones de navegación + indicador de error + touch blocker |
| `lvgl.yaml` | Configura LVGL e incluye estilos, top_layer y todas las páginas |
| `intervals.yaml` | Actualiza labels cada 250ms, gestiona auto-dim y screensaver |

## Paleta de Colores

```
Fondo principal:  0x121212
Sidebar:          0x1A1A1A
Cards:            0x1E1E1E
Botones:          0x2A2A2A
Verde Bambu:      0x00AE42
Rojo peligro:     0xE53935
Naranja (heat):   0xFFAA00
Gris texto:       0x888888
Blanco:           0xFFFFFF
```

## Widgets IDs Importantes

### Home Page
- `lbl_status` - Estado de impresión
- `lbl_nozzle`, `lbl_bed` - Temperaturas
- `icon_nozzle`, `icon_bed` - Iconos (parpadean cuando calientan)
- `progress_arc`, `lbl_progress` - Gauge circular de progreso
- `lbl_filename` - Nombre del archivo
- `lbl_remaining`, `lbl_finish_time` - Tiempos
- `lbl_layers` - Capas actual/total
- `btn_pause`, `btn_resume`, `btn_stop` - Botones de control
- `btn_light_home` - Toggle luz (visible cuando idle)
- `home_filename_row`, `home_fans_row`, `home_progress_section` - Contenedores (se ocultan cuando idle)

### Sidebar
- `icon_error` - Indicador de error (parpadea cuando hay error)

### Info Page
- `sw_screen_always_on` - Toggle pantalla siempre encendida

### Screensaver
- `screensaver_clock` - Reloj grande en screensaver

## Recursos

- [LVGL en ESPHome](https://esphome.io/components/lvgl/)
- [Material Design Icons](https://pictogrammers.com/library/mdi/)
- [Paleta de colores Bambu](https://www.bambulab.com/en)
