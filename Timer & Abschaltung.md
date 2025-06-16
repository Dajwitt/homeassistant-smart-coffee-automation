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
              entity_id: switch.kaffeemaschine
          - delay:
              seconds: 2
          - service: switch.turn_on
            target:
              entity_id: switch.kaffeemaschine
mode: restart
```

**Was passiert konkret?**

- **Neue Kaffeezubereitung erkannt:** Startet den 40-Minuten-Timer neu. Falls der 15-Minuten-Timer gerade läuft, wird dieser beendet.
- **40 Minuten ohne Kaffee vorbei:** Der 15-Minuten-Timer wird gestartet – dieser fungiert als Countdown zur Abschaltung.
- **15 Minuten auch vorbei:** Wenn der 40-Minuten-Timer nicht (mehr) aktiv ist, wird der Switch kurz ausgeschaltet und nach 2 Sekunden wieder eingeschaltet – dadurch geht die Maschine in den Standby, ohne erneut zu spülen

::: info
Damit ist ein zuverlässiges, zeitgesteuertes Abschalten der Maschine möglich – ohne unnötige Spülvorgänge.

:::
