# Guition JC8048W550 - Panel Bambu Lab

Dashboard táctil para impresoras Bambu Lab P1S/X1C con pantalla de 5 pulgadas.

## Características

### Página Home
- **Progreso de impresión** con gauge circular
- **Temperaturas** de nozzle y bed con indicador de calentamiento (parpadeo naranja)
- **Nombre del archivo** imprimiéndose
- **Tiempo restante** y hora estimada de finalización
- **Capas** actual/total
- **Ventiladores** (cooling, aux, chamber) - lectura
- **Botones** Pause/Resume/Stop con confirmación
- **Botón LIGHT** (reemplaza Stop cuando idle)
- **Limpieza automática** - oculta elementos cuando no está imprimiendo

### Página Controls
- Ajuste de temperatura **nozzle** (+/-5, +/-10)
- Ajuste de temperatura **bed** (+/-5, +/-10)
- **Presets** de materiales: PLA, PETG, ABS, Enfriar
- Toggle **luz de carcasa**
- Acceso a página **Fans**

### Página Fans
- Control individual de 3 ventiladores:
  - Part Cooling Fan
  - Auxiliary Fan
  - Chamber Fan
- **Arco visual** de velocidad por ventilador
- Toggle **on/off**
- Botones **+/-10%**

### Página Motion
- Control **XY** del cabezal (+/-1mm, +/-10mm, Home)
- Control **Z** de la cama (posiciones absolutas: 250, 150, 50mm, Home)
- *Página Filament desactivada temporalmente*

### Página Info
- **Estado** de conexión de la impresora
- **Errores** HMS y de impresión
- **SD Card** - estado
- **WiFi** - señal en dBm
- **IP** del dispositivo
- **Uptime** del ESP
- Botón **Reiniciar** ESP
- Toggle **Pantalla siempre encendida**

### Sistema
- **Auto-dim** - brillo al 15% tras 60s sin tocar
- **Screensaver** - pantalla con reloj tras 5min de inactividad
- **Touch wake** - protección contra toques accidentales al despertar
- **Indicador de error** - icono parpadea en sidebar cuando hay error
- **Modo siempre encendida** - desactiva auto-dim y screensaver

## Requisitos

1. **Hardware**: Guition JC8048W550 (ESP32-S3 + Display 5" 800x480)
2. **Home Assistant** con ESPHome Add-on
3. **[ha-bambulab](https://github.com/greghesp/ha-bambulab)** instalado via HACS
4. Impresora Bambu Lab P1S o X1C conectada a Home Assistant

## Configuración

### 1. Obtener datos de la impresora

En Home Assistant:
1. Ve a **Configuración** → **Dispositivos y servicios** → **Bambu Lab**
2. Click en el dispositivo de tu impresora
3. Anota:
   - **Device ID**: visible en la URL o info del dispositivo
   - **Prefijo de entidades**: busca cualquier sensor y toma la parte después de `sensor.` y antes del nombre (ej: `p1s_01p00c581002790`)

### 2. Crear dispositivo en ESPHome

1. Abre ESPHome en Home Assistant
2. Click **+ NEW DEVICE** → **Continue** → nombre → **Skip this step**
3. Pega el siguiente contenido:

```yaml
substitutions:
  device_name: "guition-bambu"
  friendly_name: "Panel Bambu"
  bambu_device_id: "TU_DEVICE_ID"           # Del paso 1
  bambu_prefix: "p1s_01p00c581002790"       # Del paso 1

packages:
  remote:
    url: https://github.com/agustinamu/esphome_devices
    ref: develop
    files: [devices/guition-jc8048w550-bambu/package.yaml]
    refresh: 1h

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

api:
  encryption:
    key: !secret api_key
```

4. Configura tus secretos en ESPHome:
```yaml
wifi_ssid: "TuRedWiFi"
wifi_password: "TuPassword"
api_key: "GeneraUnaClaveAleatoria"  # ESPHome puede generarla
```

5. Click **INSTALL** → **Plug into this computer** (primera vez)

## Estructura de Archivos

```
guition-jc8048w550-bambu/
├── esphome.yaml          # Ejemplo de config para HA
├── package.yaml          # Orquestador principal
├── core.yaml             # Hardware + auto-dim + touch wake
├── sensors.yaml          # Sensores de Home Assistant (Bambu)
├── ui.yaml               # Orquestador de UI
├── test.yaml             # Para validación local
└── ui/
    ├── fonts.yaml        # Montserrat + Material Design Icons
    ├── styles.yaml       # Estilos reutilizables (cards, buttons)
    ├── top_layer.yaml    # Sidebar + touch blocker
    ├── lvgl.yaml         # Orquestador LVGL
    ├── intervals.yaml    # Actualizaciones periódicas
    └── pages/
        ├── home.yaml           # Página principal
        ├── controls.yaml       # Control de temperaturas
        ├── presets.yaml        # Presets de materiales
        ├── fans.yaml           # Control de ventiladores
        ├── motion.yaml         # Menú de movimiento
        ├── motion_xy.yaml      # Control XY
        ├── motion_z.yaml       # Control Z
        ├── filament.yaml       # Gestión de filamento (WIP)
        ├── confirm_pause.yaml  # Diálogo confirmación pause
        ├── confirm_stop.yaml   # Diálogo confirmación stop
        ├── info.yaml           # Info del sistema
        └── screensaver.yaml    # Screensaver con reloj
```

## Solución de Problemas

### La impresora no aparece en Home Assistant
- Verifica que ha-bambulab esté instalado y configurado
- La impresora debe estar en modo LAN y conectada a la misma red

### Las temperaturas no se actualizan
- Verifica el `bambu_prefix` en substitutions
- Comprueba que los sensores existen en Home Assistant

### Error "Permitir acciones de Home Assistant"
- En ESPHome, habilita la opción en la configuración del dispositivo
- O añade `allow_other_uses: true` en la sección `api:`

### La pantalla parpadea durante OTA
- Es normal. El display muestra artefactos durante la actualización OTA

## Referencia Técnica

### Entidades Principales

| Función | Entity ID |
|---------|-----------|
| Temperatura nozzle | `sensor.${bambu_prefix}_temperatura_de_la_boquilla` |
| Temperatura bed | `sensor.${bambu_prefix}_temperatura_de_la_cama` |
| Progreso | `sensor.${bambu_prefix}_progreso_de_la_impresion` |
| Tiempo restante | `sensor.${bambu_prefix}_tiempo_restante` |
| Estado | `sensor.${bambu_prefix}_estado_actual` |
| Luz | `light.${bambu_prefix}_luz_de_la_carcasa` |
| Cooling fan | `fan.${bambu_prefix}_ventilador_de_enfriamiento` |
| Aux fan | `fan.${bambu_prefix}_ventilador_auxiliar` |
| Chamber fan | `fan.${bambu_prefix}_ventilador_de_la_carcasa` |

### M-codes Útiles

| Comando | Descripción |
|---------|-------------|
| `M104 Sxxx` | Temp nozzle (no espera) |
| `M109 Sxxx` | Temp nozzle (espera) |
| `M140 Sxxx` | Temp cama (no espera) |
| `M190 Sxxx` | Temp cama (espera) |
| `M106 P1 Sxxx` | Part cooling (0-255) |
| `M106 P2 Sxxx` | AUX fan (0-255) |
| `M106 P3 Sxxx` | Chamber fan (0-255) |
| `G28 X Y` | Home XY |
| `G28 Z` | Home Z |

## Alternativas

- **[OpenHASP](https://www.openhasp.com/0.7.0/hardware/guition/jc8048w550/)** - Firmware con diseñador visual, más fácil para diseños simples
