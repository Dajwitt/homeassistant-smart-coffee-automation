Dashboard zur Überwachung ob alles funktioniert.

```
views:
  - type: sections
    max_columns: 4
    title: Kaffeemaschine
    path: tablet
    sections:
      - type: grid
        cards:
          - type: entities
            entities:
              - entity: counter.kaffeezubereitungen
              - entity: timer.kaffeemaschine_5_min_timer
          - type: tile
            entity: timer.kaffeemaschine_40_min_timer
            grid_options:
              columns: 12
              rows: 1
            features_position: bottom
            vertical: false
          - type: tile
            entity: timer.kaffeemaschine_15_min_timer
            grid_options:
              columns: 12
              rows: 1
            features_position: bottom
            vertical: false
          - type: tile
            entity: binary_sensor.wassertank_contact
            grid_options:
              columns: 12
              rows: 1
            features_position: bottom
            vertical: false
            name: Wassertank
      - type: grid
        cards:
          - type: entities
            entities:
              - entity: switch.kaffeemaschine_switch
              - entity: sensor.kaffeemaschine_power
      - type: grid
        cards:
          - type: heading
            icon: mdi:fridge
            heading: Start & Spülen
            heading_style: title
          - type: tile
            entity: input_boolean.spulen_erkannt
            features_position: bottom
            vertical: false
      - type: grid
        cards:
          - type: entities
            entities:
              - entity: binary_sensor.kaffeezubereitung_erkannt
              - entity: input_number.kaffeetimer
              - entity: input_number.kaffee_letzter_wert
            title: 20 - 60 Sekunden - Kaffee erkannt
    cards: []
```