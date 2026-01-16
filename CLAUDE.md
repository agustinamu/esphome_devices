# ESPHome Devices - Contexto para Claude

## Descripción
Repositorio de configuraciones ESPHome para dispositivos del hogar. Usa remote packages para cargar desde GitHub.

## Estructura de carpetas

```
devices/
├── humidificador/
│   ├── esphome.yaml    # Config para copiar en HA (secretos + package ref)
│   └── package.yaml    # Lógica del dispositivo (se carga desde GitHub)
├── guition-jc8048w550/
│   ├── esphome.yaml
│   └── package.yaml
└── guition-jc8048w550-bambu/
    ├── esphome.yaml
    └── package.yaml
```

## Dispositivos

### 1. Humidificador
- **Placa**: ESP8266 ESP-01 1M
- **Pines**: GPIO14 (power), GPIO16 (luz)
- **Función**: Pulsos para simular botones físicos

### 2. Guition JC8048W550 (básico)
- **Placa**: ESP32-S3 N16R8
- **Display**: 5" IPS 800x480 RGB (ST7262)
- **Touch**: GT911 I2C
- **Función**: Ejemplo con reloj y botón

### 3. Guition JC8048W550 Bambu
- **Igual que anterior**
- **Función**: Dashboard para Bambu Lab P1S
- **Entidades**: En español (HA configurado en español)
- **Prefijo entidades**: `p1s_01p00c581002790_`

## Pinout Guition JC8048W550

| Función | GPIO |
|---------|------|
| Backlight | 2 |
| I2C SDA | 19 |
| I2C SCL | 20 |
| Touch INT | 18 (no conectado por defecto) |
| Display DE | 40 |
| Display HSYNC | 39 |
| Display VSYNC | 41 |
| Display PCLK | 42 |
| Red | 45, 48, 47, 21, 14 |
| Green | 5, 6, 7, 15, 16, 4 |
| Blue | 8, 3, 46, 9, 10 |

## Configuración display

```yaml
pclk_frequency: 16MHz
hsync_front_porch: 40
hsync_pulse_width: 48
hsync_back_porch: 40
vsync_front_porch: 13
vsync_pulse_width: 3
vsync_back_porch: 32
```

## Notas técnicas

### Framework
ESP32-S3 con display RGB requiere **ESP-IDF** (no Arduino).

### Colores RGB565
Evitar grises intermedios (causan dithering/parpadeo). Usar colores puros:
- Blanco: 0xFFFFFF ✓
- Negro: 0x000000 ✓
- Verde Bambu: 0x00AE42 ✓
- Grises: evitar o usar tonos de verde

### Iconos MDI
Se definen fuera del bloque `lvgl:` con el componente `font:`.

### Auto-dim
Brillo baja al 15% después de 60s sin tocar. Al tocar vuelve al 100%.

## Integración Bambu Lab

### Requisitos
- Integración ha-bambulab via HACS
- Habilitar "Permitir acciones de Home Assistant" en ESPHome

### Entidades principales (español)
- `sensor.*_temperatura_de_la_boquilla`
- `sensor.*_temperatura_de_la_cama`
- `sensor.*_progreso_de_la_impresion`
- `sensor.*_tiempo_restante`
- `sensor.*_estado_actual`
- `sensor.*_nombre_de_la_tarea`
- `sensor.*_capa_actual`
- `sensor.*_cantidad_total_de_capas`
- `number.*_temperatura_objetivo_de_la_boquilla`
- `number.*_temperatura_objetivo_de_la_cama`
- `button.*_pausar_la_impresion`
- `button.*_continuar_la_impresion`
- `button.*_detener_la_impresion`
- `light.*_luz_de_la_carcasa`

### Acciones
Las acciones de HA se llaman con `homeassistant.action` en LVGL:
```yaml
on_press:
  - homeassistant.action:
      action: light.toggle
      data:
        entity_id: light.xxx
```

## Test local antes de commit

Cada dispositivo tiene un `test.yaml` para validar la configuración antes de pushear:

```bash
# Instalar esphome (una vez)
uv tool install esphome

# Validar configuración
esphome config devices/humidificador/test.yaml
esphome config devices/guition-jc8048w550/test.yaml
esphome config devices/guition-jc8048w550-bambu/test.yaml
```

Los `test.yaml` usan `!include` para cargar el `package.yaml` local, simulando lo que haría el remote package en HA.

## Compilación

- Primera vez: 15-25 min en RPi
- Incremental: 2-5 min
- Durante OTA la pantalla muestra artefactos (normal)
