# Bambu P1S Dashboard - Roadmap

## Funcionalidades Actuales

| Pagina | Funcionalidad | Estado |
|--------|--------------|--------|
| **Home** | Temperaturas nozzle/bed | OK |
| | Indicador calentando (iconos parpadean) | OK |
| | Ventiladores (lectura) | OK |
| | Gauge circular progreso | OK |
| | Capas actual/total | OK |
| | Tiempo restante | OK |
| | Nombre del archivo | OK |
| | Hora estimada de fin | OK |
| | Pause/Resume/Stop con confirmacion | OK |
| | Boton LIGHT cuando idle | OK |
| | **Limpieza cuando idle** (oculta elementos) | OK |
| **Controls** | Ajuste temp nozzle +/-5/+/-10 | OK |
| | Ajuste temp bed +/-5/+/-10 | OK |
| | Presets (PLA/PETG/ABS/Enfriar) | OK |
| | Toggle luz carcasa | OK |
| | Acceso a pagina Fans | OK |
| **Fans** | Arco visual por ventilador | OK |
| | Toggle on/off | OK |
| | Botones +/-10% | OK |
| **Motion** | Movimiento XY (+/-1, +/-10, Home) | OK |
| | Movimiento Z (posiciones absolutas) | OK |
| | Filament (desactivado) | PENDIENTE |
| **Info** | WiFi signal, IP, Uptime | OK |
| | Estado impresora online | OK |
| | Errores HMS/Print | OK |
| | Estado SD Card | OK |
| | Boton reiniciar | OK |
| | **Toggle pantalla siempre encendida** | OK |
| **Sidebar** | Indicador error parpadeante | OK |
| **Sistema** | Auto-dim 60s | OK |
| | Touch wake | OK |
| | Screensaver 5min | OK |
| | **Modo siempre encendida** (runtime) | OK |
| **Codigo** | **Refactor UI en archivos modulares** | OK |

---

## Funcionalidades Deseables

### Prioridad Alta

1. **Filament load/unload funcional**
   - Requiere ingenieria inversa MQTT
   - Capturar comandos desde Bambu Studio
   - Ciclo completo: calentar, cortar, retraer

2. **Velocidad de impresion**
   - Entidad: `select.p1s_01p00c581002790_velocidad_de_impresion`
   - Valores: Silent, Standard, Sport, Ludicrous
   - UI propuesta: 4 botones en Controls o dropdown en Home
   - Implementacion:
     ```yaml
     # En sensors.yaml
     text_sensor:
       - platform: homeassistant
         id: print_speed_mode
         entity_id: select.${bambu_prefix}_velocidad_de_impresion

     # En lvgl.yaml - botones de seleccion
     on_press:
       - homeassistant.action:
           action: select.select_option
           data:
             entity_id: select.${bambu_prefix}_velocidad_de_impresion
             option: "Standard"
     ```

3. ~~**Notificacion visual de errores**~~ ✅ COMPLETADO
   - Icono parpadeante en sidebar cuando hay error

4. ~~**Nombre del archivo imprimiendose**~~ ✅ COMPLETADO
   - Visible en Home page con icono

### Prioridad Media

5. **Camara**
   - Snapshot de la camara via Home Assistant
   - Requiere integracion de camara en HA

6. ~~**Tiempo estimado de fin**~~ ✅ COMPLETADO
   - Muestra "Fin estimado HH:MM" en Home

7. **AMS status**
   - Estado de los slots de filamento
   - Solo si se usa AMS

8. **Historial de errores HMS**
   - Ver ultimos errores
   - Limitacion: la integracion no expone historial

9. **Temperatura chamber**
   - Si el sensor esta disponible
   - Entidad: `sensor.xxx_temperatura_de_la_camara` (verificar)

10. ~~**Gauge circular de progreso**~~ ✅ COMPLETADO
    - Arco visual reemplaza barra de progreso
    - Porcentaje y hora fin dentro del gauge

11. ~~**Indicador visual de calentando**~~ ✅ COMPLETADO
    - Iconos nozzle/bed parpadean naranja cuando calentando
    - Verde cuando a temperatura

### Prioridad Baja

12. **Temas de color**
    - Cambiar esquema de colores
    - Modo claro/oscuro
    - Requiere parametrizar todos los colores (mucho trabajo)

13. ~~**Screensaver**~~ ✅ COMPLETADO
    - Pantalla negra con reloj tras 5 min inactividad
    - Toca para despertar

14. **Sonidos**
    - Feedback tactil/sonoro
    - Requiere buzzer (no disponible en este hardware)

15. ~~**Boton luz en Home**~~ ✅ COMPLETADO
    - LIGHT reemplaza STOP cuando idle
    - Toggle con feedback visual

16. ~~**Modo pantalla siempre encendida**~~ ✅ COMPLETADO
    - Toggle en pagina Info
    - Desactiva auto-dim y screensaver en runtime

17. ~~**Home limpio cuando idle**~~ ✅ COMPLETADO
    - Oculta filename, fans, progreso cuando no imprime
    - UI mas limpia en estado idle/offline

---

## Propuesta Refactor UX

### Problemas Actuales

- Demasiadas paginas -> navegacion confusa
- Informacion dispersa entre paginas
- No hay indicadores de estado global visibles
- Subpaginas innecesarias (Presets, Fans como paginas separadas)
- Inconsistencia en tamanos de fuente y espaciado

### Nueva Estructura Propuesta

```
+---------------------------+---------------------------------+
| SIDEBAR (80px)            |  CONTENT (720px)                |
+---------------------------+---------------------------------+
|                           |                                 |
|  [HOME]      <- activo    |  Area principal con scroll      |
|                           |                                 |
|  [CONTROLS]               |                                 |
|                           |                                 |
|  [MOTION]                 |                                 |
|                           |                                 |
|  -----------              |                                 |
|  [ERROR]    <- parpadea   |                                 |
|  [INFO]                   |                                 |
+---------------------------+---------------------------------+
```

### Cambios por Pagina

#### Home Page Rediseñada

```
+----------------------------------------------------------+
| BAMBU P1S    printing...    archivo.gcode         12:34  |
+----------------------------------------------------------+
|                                                          |
|     +-------------+                  +---------+         |
|     |             |                  | 220°C   |  Nozzle |
|     |    75%      |                  | /220°C  |         |
|     |   [====]    |                  +---------+         |
|     |             |                  | 60°C    |  Bed    |
|     +-------------+                  | /60°C   |         |
|      Gauge grande                    +---------+         |
|                                                          |
|  Layer: 45/120        Remaining: 2:34        Speed: 100% |
|                                                          |
|  +------------+  +------------+  +------------+          |
|  |   PAUSE    |  |    STOP    |  |   LIGHT    |          |
|  +------------+  +------------+  +------------+          |
+----------------------------------------------------------+
```

#### Controls Unificada con Tabs

```
+----------------------------------------------------------+
| < Back        CONTROLS                                   |
+----------------------------------------------------------+
| [TEMP]  [FANS]  [SPEED]                                  |
+----------------------------------------------------------+
|                                                          |
|  Contenido segun tab seleccionada                        |
|  - TEMP: Cards de nozzle y bed con +/-5/+/-10            |
|  - FANS: Los 3 ventiladores con arcos                    |
|  - SPEED: Slider de velocidad (cuando este disponible)  |
|                                                          |
+----------------------------------------------------------+
```

#### Motion Simplificada

```
+----------------------------------------------------------+
| < Back        MOTION                                     |
+----------------------------------------------------------+
|                                                          |
|  +------------------+    +------------------+            |
|  |    TOOL HEAD     |    |       BED        |            |
|  |                  |    |                  |            |
|  |   [Y+10][Y+1]    |    |    [Z=250]       |            |
|  | [X-10][H][X+10]  |    |    [Z=150]       |            |
|  |   [Y-1][Y-10]    |    |    [Z=50]        |            |
|  |                  |    |    [HOME Z]      |            |
|  +------------------+    +------------------+            |
|                                                          |
+----------------------------------------------------------+
```

### Consistencia Visual

| Elemento | Valor |
|----------|-------|
| Fuente titulos | montserrat_28 |
| Fuente contenido | montserrat_22 |
| Fuente secundaria | montserrat_18 |
| Color primario | 0x00AE42 (verde Bambu) |
| Color fondo | 0x121212 |
| Color cards | 0x1E1E1E |
| Color texto | 0xFFFFFF |
| Color secundario | 0x888888 |
| Color error | 0xFF4444 |
| Color warning | 0xFFAA00 |
| Border radius cards | 12px |
| Padding cards | 12px |

---

## Plan MQTT para Filament

### Objetivo
Replicar la funcionalidad de carga/descarga de filamento de Bambu Studio.

### Preparacion

1. **Obtener datos de conexion**
   - IP de la impresora (ej: 192.168.1.XX)
   - Access code: Ajustes > Red > LAN Access Code
   - Serial number: visible en la impresora/app
   - Puerto: 8883 (MQTT sobre TLS)
   - Usuario: `bblp`
   - Password: el access code

2. **Extraer certificado TLS**
   ```bash
   # Guardar el certificado de la impresora
   openssl s_client -showcerts -connect IP_IMPRESORA:8883 </dev/null \
     | sed -n -e '/-.BEGIN/,/-.END/ p' > bambu.pem
   ```

3. **Conectar al broker MQTT**
   ```bash
   # Suscribirse a los reportes de la impresora
   mosquitto_sub -d -h IP_IMPRESORA -p 8883 \
     -u bblp -P ACCESS_CODE \
     --cafile bambu.pem \
     --insecure \
     -t "device/SERIAL/report"
   ```

### Captura de comandos

1. **Topics relevantes**
   - `device/SERIAL/report` - Estado de la impresora (lectura)
   - `device/SERIAL/request` - Envio de comandos (escritura)

2. **Acciones a capturar desde Bambu Studio**
   - Iniciar carga de filamento
   - Iniciar descarga de filamento
   - Cortar filamento
   - Anotar todos los mensajes JSON

3. **Formato tipico de comandos**
   ```json
   {
     "print": {
       "command": "gcode_line",
       "param": "G-code aqui"
     }
   }
   ```

### Implementacion en ESPHome

Una vez capturados los comandos, replicar via:
```yaml
on_press:
  - homeassistant.action:
      action: bambu_lab.send_command
      data:
        device_id: "DEVICE_ID"
        command: "gcode_line"
        param: "M104 S200"  # ejemplo
```

### Riesgos
- El ciclo de cambio de filamento es complejo
- Involucra el cutter, sensing, temperatura
- Comandos G-code simples probablemente no funcionan
- Puede requerir M-codes propietarios de Bambu

### Archivos locales (no en repo)
- `bambu.pem` - Certificado TLS de la impresora
- `mqtt_connect.sh` - Script con datos de conexion

---

## Historial de Cambios

### 2026-01-20 (sesion 3)
- **Refactor UI modular**: lvgl.yaml dividido en archivos individuales
  - `styles.yaml` - Estilos reutilizables
  - `top_layer.yaml` - Sidebar y touch blocker
  - `pages/*.yaml` - 12 archivos de paginas individuales
- **Toggle "Pantalla siempre encendida"** en pagina Info
  - Desactiva auto-dim y screensaver en runtime
- **Home limpio cuando idle**: oculta filename, fans, progreso cuando no imprime
- Documentacion completa actualizada

### 2026-01-20 (sesion 2)
- Nombre del archivo en Home page
- Hora estimada de fin ("Fin estimado HH:MM")
- Indicador de error parpadeante en sidebar
- Refactor layout Home para mejor distribucion vertical
- Gauge circular de progreso (reemplaza barra)
- Indicador de calentando (iconos parpadean naranja)
- Boton LIGHT en Home cuando idle (reemplaza STOP)
- Screensaver con reloj tras 5 min inactividad
- Documentacion velocidad de impresion (select: Silent/Standard/Sport/Ludicrous)

### 2026-01-20 (sesion 1)
- Documento inicial
- Funcionalidades base completadas
- Pagina Fans rediseñada con arcos y toggles
- Pagina Info con errores HMS/SD
- Confirmacion para Pause/Stop
- Play/Pause dinamico segun estado
