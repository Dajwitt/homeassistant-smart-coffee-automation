#### 💧 Delongi Wassertank – Zähler zurücksetzen

**Zweck:** Setzt den Kaffeezähler zurück, wenn der Wassertank für über 10 Sekunden entnommen wird. Erkennt außerdem, wenn mehr als 5 Kaffee zubereitet wurden – in dem Fall wird gewarnt, ein Timer gestartet und ggf. die Maschine neu gestartet.

```
alias: 💧 Wassertank überwachen – Zähler zurücksetzen neu
description: ""
triggers:
  - entity_id: binary_sensor.wassertank_contact
    from: "off"
    to: "on"
    for: "00:00:10"
    id: tank_entnommen
    trigger: state
  - entity_id: counter.kaffeemaschine_zubereitungen
    above: 5
    id: zaehler_ueber_max
    trigger: numeric_state
  - entity_id: sensor.kaffeemaschine_power
    above: 50
    for: "00:00:05"
    id: verbrauch_erkannt
    trigger: numeric_state
  - entity_id: counter.kaffeemaschine_zubereitungen
    below: 1
    id: zaehler_unter_1
    trigger: numeric_state
  - entity_id: timer.kaffeemaschine_wasser_nachfuell_prompt
    from: active
    to: idle
    id: wasser_timer_abgelaufen
    trigger: state
actions:
  - choose:
      - conditions:
          - condition: trigger
            id: tank_entnommen
        sequence:
          - wait_for_trigger:
              - entity_id: binary_sensor.wassertank_contact
                to: "off"
                trigger: state
            timeout: "00:05:00"
            continue_on_timeout: true
          - target:
              entity_id: counter.kaffeemaschine_zubereitungen
            action: counter.reset
            data: {}
      - conditions:
          - condition: trigger
            id: zaehler_ueber_max
        sequence:
          - target:
              entity_id: timer.kaffeemaschine_wasser_nachfuell_prompt
            action: timer.start
            data: {}
          - target:
              entity_id: input_boolean.sprachbenachrichtigung_ausloeser_wassertank
            action: input_boolean.turn_on
            data: {}
          - delay:
              seconds: 5
          - target:
              entity_id: input_boolean.sprachbenachrichtigung_ausloeser_wassertank
            action: input_boolean.turn_off
            data: {}
      - conditions:
          - condition: trigger
            id: zaehler_unter_1
          - condition: state
            entity_id: timer.kaffeemaschine_wasser_nachfuell_prompt
            state: active
        sequence:
          - target:
              entity_id: timer.kaffeemaschine_wasser_nachfuell_prompt
            action: timer.cancel
            data: {}
      - conditions:
          - condition: and
            conditions:
              - condition: trigger
                id: verbrauch_erkannt
              - condition: numeric_state
                entity_id: counter.kaffeemaschine_zubereitungen
                above: 5
              - condition: state
                entity_id: timer.kaffeemaschine_idle_ausschalt_timer
                state: idle
              - condition: state
                entity_id: timer.kaffeemaschine_standby_vorwarnung
                state: idle
        sequence:
          - target:
              entity_id: input_boolean.sprachbenachrichtigung_ausloeser_wassertank
            action: input_boolean.turn_on
            data: {}
          - delay:
              seconds: 5
          - target:
              entity_id: input_boolean.sprachbenachrichtigung_ausloeser_wassertank
            action: input_boolean.turn_off
            data: {}
      - conditions:
          - condition: trigger
            id: wasser_timer_abgelaufen
        sequence:
          - target:
              entity_id: switch.kaffeemschine
            action: switch.turn_off
            data: {}
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

