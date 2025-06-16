#### 💧 Delongi Wassertank – Zähler zurücksetzen

**Zweck:** Setzt den Kaffeezähler zurück, wenn der Wassertank für über 10 Sekunden entnommen wird. Erkennt außerdem, wenn mehr als 5 Kaffee zubereitet wurden – in dem Fall wird gewarnt, ein Timer gestartet und ggf. die Maschine neu gestartet.

```
alias: Delongi Wassertank - Zähler zurücksetzen
description: >-
  Setzt den Zähler zurück, wenn der Wassertank länger als 10 Sekunden entnommen wurde.
trigger:
  - platform: state
    entity_id: binary_sensor.wassertank_contact
    from: "off"
    to: "on"
    for: "00:00:10"
    id: tank entnommen nachfüllen
  - platform: numeric_state
    entity_id: counter.kaffeezubereitungen
    above: 5
    id: zähler über 5
  - platform: numeric_state
    entity_id: sensor.kaffeemaschine_power
    above: 50
    for: "00:00:05"
    id: verbrauch erkannt
  - platform: numeric_state
    entity_id: counter.kaffeezubereitungen
    below: 1
    id: zähler unter 1
  - platform: state
    entity_id: input_boolean.kaffee_5_min_timer
    from: "on"
    to: "off"
    id: Helfer aus
  - platform: state
    entity_id: switch.kaffeemaschine_switch
    to: "off"
    id: Maschine aus
action:
  - choose:
      - conditions:
          - condition: trigger
            id: tank entnommen nachfüllen
        sequence:
          - service: counter.reset
            target:
              entity_id: counter.kaffeezubereitungen
          - service: timer.cancel
            target:
              entity_id: timer.kaffeemaschine_5_min_timer
      - conditions:
          - condition: trigger
            id: zähler über 5
        sequence:
          - service: input_boolean.turn_on
            target:
              entity_id: input_boolean.kaffee_5_min_timer
          - service: timer.start
            target:
              entity_id: timer.kaffeemaschine_5_min_timer
          - service: notify.alexa_media_echo
            data:
              message: Das Wasser reicht nicht mehr für einen weiteren Kaffee!
              title: Kaffeequeen
              target: media_player.echo
              data:
                type: announce
                method: all
      - conditions:
          - condition: trigger
            id: 5 min Timer abgelaufen
        sequence:
          - service: switch.turn_off
            target:
              entity_id: switch.kaffeemaschine_switch
          - delay: "00:00:05"
          - service: switch.turn_on
            target:
              entity_id: switch.kaffeemaschine_switch
          - service: timer.finish
            target:
              entity_id: timer.kaffeemaschine_15_min_timer
      - conditions:
          - condition: trigger
            id: zähler unter 1
        sequence:
          - service: timer.cancel
            target:
              entity_id: timer.kaffeemaschine_5_min_timer
      - conditions:
          - condition: trigger
            id: Helfer aus
        sequence:
          - service: timer.cancel
            target:
              entity_id: timer.kaffeemaschine_5_min_timer
      - conditions:
          - condition: trigger
            id: Maschine aus
        sequence:
          - service: timer.cancel
            target:
              entity_id: timer.kaffeemaschine_5_min_timer
          - service: input_boolean.turn_off
            target:
              entity_id: input_boolean.kaffee_5_min_timer
mode: single
```

**Was passiert konkret?**

- Wird der **Wassertank für mehr als 10 Sekunden entnommen**, wird davon ausgegangen, dass Wasser nachgefüllt wurde → der Zähler `counter.kaffeezubereitungen` wird **auf 0 zurückgesetzt**.
- **Wurden mehr als 5 Kaffee** gezählt:
  - Alexa gibt eine Warnung („Das Wasser reicht nicht mehr für einen weiteren Kaffee!“),
  - der **Helfer** `input_boolean.kaffee_5_min_timer` wird aktiviert,
  - ein **5-Minuten-Timer** startet.
- **Wird der Tank für 10 Sekunden entnommen**, bevor der Timer abläuft:
  - Der Timer wird gestoppt,
  - die Maschine bleibt eingeschaltet.
- **Läuft der Timer vollständig ab**, ohne dass Wasser nachgefüllt wurde:
  - Die Kaffeemaschine wird **ausgeschaltet**,
  - nach 5 Sekunden automatisch **wieder eingeschaltet**, um eine erneute Zubereitung zu blockieren.
- Fällt der Zähler **unter 1**, wird der Timer ebenfalls gestoppt.
- Wird die Maschine eingeschaltet, der Zähler ist immer noch über 5 und es wurde kein Wasser nachgefüllt, gibt Alexa erneut eine Warnung („Das Wasser reicht nicht mehr für einen weiteren Kaffee!“) aus.

> Damit wird eine zuverlässige Tanküberwachung möglich, ohne Sensor im Inneren – allein durch Beobachtung von Entnahmezeit und Zählerstand.

