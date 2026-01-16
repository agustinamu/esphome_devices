# UI - Interfaz Bambu Lab

Componentes de interfaz LVGL para el panel Bambu.

## Archivos

| Archivo | Descripción |
|---------|-------------|
| `fonts.yaml` | Montserrat (14-48) + Material Design Icons |
| `lvgl.yaml` | Estilos, sidebar, todas las páginas |
| `intervals.yaml` | Actualizaciones periódicas de datos |

## Páginas

1. **main_page** - Home con progreso y temperaturas
2. **controls_page** - Ajuste de temperaturas
3. **controls_presets_page** - Presets PLA/PETG/ABS
4. **motion_page** - Menú motion
5. **motion_xy_page** - Control tool head XY
6. **motion_z_page** - Control bed Z
7. **info_page** - Información del sistema

## Paleta de colores

```
Negro fondo:    0x121212
Gris sidebar:   0x1A1A1A
Gris cards:     0x1E1E1E
Gris botones:   0x2A2A2A
Verde Bambu:    0x00AE42
Rojo peligro:   0xE53935
```

## Recursos

- [LVGL en ESPHome](https://esphome.io/components/lvgl/)
- [Material Design Icons](https://pictogrammers.com/library/mdi/)
