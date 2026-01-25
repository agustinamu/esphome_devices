# Guión de depuración - Carga/Descarga MQTT Bambu

## Datos de conexión

```
Host: <IP_IMPRESORA>
Puerto: 8883
Usuario: bblp
Password: <ACCESS_CODE>
Serial: <SERIAL>
```

---

## Preparación

Terminal 1: Captura MQTT (dejar corriendo todo el tiempo)
```bash
mosquitto_sub -h <IP_IMPRESORA> -p 8883 \
  -u bblp -P <ACCESS_CODE> \
  --cafile bambu.pem --insecure \
  -t "device/<SERIAL>/report" | tee bambu_debug.log
```

---

## Test 1: Descarga (estado: filamento cargado)

### Requisitos previos
- [ ] Hotend frío
- [ ] Filamento cargado y detectado por sensor

### Comando
```bash
mosquitto_pub -h <IP_IMPRESORA> -p 8883 \
  -u bblp -P <ACCESS_CODE> \
  --cafile bambu.pem --insecure \
  -t "device/<SERIAL>/request" \
  -m '{"print":{"sequence_id":"0","command":"ams_change_filament","target":254,"curr_temp":250,"tar_temp":250}}'
```

### Verificar
- [ ] ¿Calentó?
- [ ] ¿Descargó?
- [ ] ¿Esperó a retirar filamento?
- [ ] Estado final del sensor (vacío/detectado)

### Resultado
```
stg_cur observado:
ams_status progresión:
Comportamiento:
```

---

## Test 2: Carga (estado: sin filamento, fuera del sensor)

### Requisitos previos
- [ ] Filamento completamente retirado (fuera del extrusor)
- [ ] Sensor NO detecta filamento

### Comando (mismo que Test 1)
```bash
mosquitto_pub -h <IP_IMPRESORA> -p 8883 \
  -u bblp -P <ACCESS_CODE> \
  --cafile bambu.pem --insecure \
  -t "device/<SERIAL>/request" \
  -m '{"print":{"sequence_id":"0","command":"ams_change_filament","target":254,"curr_temp":250,"tar_temp":250}}'
```

### Verificar
- [ ] ¿Calentó?
- [ ] ¿Esperó inserción de filamento?
- [ ] ¿Cargó al insertar?
- [ ] ¿Purgó?

### Resultado
```
stg_cur observado:
ams_status progresión:
Comportamiento:
```

---

## Test 3: Carga (estado: filamento tocando sensor pero no cargado)

### Requisitos previos
- [ ] Filamento insertado hasta que sensor lo detecte
- [ ] Pero NO empujado completamente (no en el hotend)

### Comando (mismo)
```bash
mosquitto_pub -h <IP_IMPRESORA> -p 8883 \
  -u bblp -P <ACCESS_CODE> \
  --cafile bambu.pem --insecure \
  -t "device/<SERIAL>/request" \
  -m '{"print":{"sequence_id":"0","command":"ams_change_filament","target":254,"curr_temp":250,"tar_temp":250}}'
```

### Verificar
- [ ] ¿Saltó el paso de espera?
- [ ] ¿Cargó directamente?

### Resultado
```
stg_cur observado:
ams_status progresión:
Comportamiento:
```

---

## Test 4 (opcional): Probar target:255

### Comando alternativo
```bash
mosquitto_pub -h <IP_IMPRESORA> -p 8883 \
  -u bblp -P <ACCESS_CODE> \
  --cafile bambu.pem --insecure \
  -t "device/<SERIAL>/request" \
  -m '{"print":{"sequence_id":"0","command":"ams_change_filament","target":255,"curr_temp":250,"tar_temp":250}}'
```

### Resultado
```
Comportamiento con filamento:
Comportamiento sin filamento:
```

---

## Comandos útiles adicionales

### Home
```bash
mosquitto_pub -h <IP_IMPRESORA> -p 8883 \
  -u bblp -P <ACCESS_CODE> \
  --cafile bambu.pem --insecure \
  -t "device/<SERIAL>/request" \
  -m '{"print":{"sequence_id":"0","command":"gcode_line","param":"G28\n"}}'
```

---

## Resumen de campos MQTT observados

| Campo | Descarga (stg_cur:22) | Carga (stg_cur:24) | Idle |
|-------|----------------------|-------------------|------|
| stg_cur | 22 | 24 | 0 |
| ams_status | 258→259→260 | 258→261→262→263 | 0 |
| mc_print_sub_stage | 0→1→2→0 | 1→2→0 | 0 |

### Significado ams_status

**Descarga:**
- 258: Calentando
- 259: Retrayendo
- 260: Esperando retirar filamento

**Carga:**
- 258: Calentando
- 261: Esperando insertar filamento
- 262: Filamento detectado, cargando
- 263: Completado/purgando

---

## Conclusiones

```
Comando único para carga/descarga: ams_change_filament target:254
- Con filamento cargado → Descarga
- Sin filamento → Carga
- ¿Espera inserción manual?: [PENDIENTE]
```
