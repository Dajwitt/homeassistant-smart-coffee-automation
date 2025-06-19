## ⏱️ Delonghi Timer & Abschaltung

**Zweck:** Diese Automation verwaltet die automatische Abschaltung der Kaffeemaschine in zwei Schritten, um Energie zu sparen und den Spülvorgang beim Ausschalten zu verhindern.

```
alias: Delongi Timer & Abschaltung
trigger:
  - platform: state
    entity_id: binary_sensor.kaffeezubereitung_erkannt
    to: "on"
    id: Verbrauch erkannt
  - platform: state
    entity_id: timer.kaffeemaschine_40_min_timer
    to: idle
    id: 40 min abgelaufen
  - platform: state
    entity_id: timer.kaffeemaschine_15_min_timer
    to: idle
    id: 15 min abgelaufen
condition: []
action:
  - choose:
      - conditions:
          - condition: trigger
            id: Verbrauch erkannt
          - condition: or
            conditions:
              - condition: state
                entity_id: timer.kaffeemaschine_40_min_timer
                state: active
              - condition: state
                entity_id: timer.kaffeemaschine_40_min_timer
                state: idle
              - condition: state
                entity_id: timer.kaffeemaschine_15_min_timer
                state: active
        sequence:
          - service: timer.start
            target:
              entity_id: timer.kaffeemaschine_40_min_timer
          - service: timer.finish
            target:
              entity_id: timer.kaffeemaschine_15_min_timer
      - conditions:
          - condition: trigger
            id: 40 min abgelaufen
        sequence:
          - service: timer.start
            target:
              entity_id: timer.kaffeemaschine_15_min_timer
      - conditions:
          - condition: trigger
            id: 15 min abgelaufen
          - condition: state
            entity_id: timer.kaffeemaschine_40_min_timer
            state: idle
        sequence:
          - service: switch.turn_off
            target:
              entity_id: switch.kaffeemaschine    # Ersetze dies durch die Entity ID deines Switch!
          - delay:
              seconds: 2
          - service: switch.turn_on
            target:
              entity_id: switch.kaffeemaschine   # Ersetze dies durch die Entity ID deines Switch!
mode: restart
```

---

### Was passiert konkret? 

**Neue Kaffeezubereitung erkannt** (binary_sensor.kaffeezubereitung_erkannt geht auf on): 

- Der timer.kaffeemaschine_40_min_timer wird neu gestartet. 
- Falls der timer.kaffeemaschine_15_min_timer gerade läuft, wird dieser beendet.  Dies stellt sicher, dass die Maschine nicht unerwartet abschaltet, während sie noch aktiv genutzt wird.

**40 Minuten ohne Kaffee vorbei** (timer.kaffeemaschine_40_min_timer läuft ab): 

- Der timer.kaffeemaschine_15_min_timer wird gestartet.  Dieser fungiert als letzter Countdown vor der Abschaltung, um z.B. noch schnell einen weiteren Kaffee zu ermöglichen.

**15 Minuten auch vorbei** (timer.kaffeemaschine_15_min_timer läuft ab) UND der 40-Minuten-Timer ist nicht (mehr) aktiv: 

- Der Switch der Kaffeemaschine wird kurz ausgeschaltet und nach 2 Sekunden wieder eingeschaltet.  Dies bewirkt, dass die Maschine in den Standby-Modus geht, ohne den automatischen Spülvorgang beim kompletten Ausschalten auszulösen.

> Damit ist ein zuverlässiges, zeitgesteuertes Abschalten der Maschine möglich – ohne unnötige Spülvorgänge. 

---

### 🛠 Anpassung an deine Maschine

Damit diese Automation bei dir zuverlässig funktioniert, musst du folgende Punkte individuell anpassen:

🔌 Power-Switch 
  - Ersetze **switch.kaffeemaschine** durch die **entity_id deines eigenen schaltbaren Steckers oder Relais**, das deine Kaffeemaschine steuert (z.B. switch.shelly_plug_s_kaffee oder switch.zigbee_coffee_machine). Dieser Switch muss in Home Assistant verfügbar sein und die Maschine ein- und ausschalten können.
