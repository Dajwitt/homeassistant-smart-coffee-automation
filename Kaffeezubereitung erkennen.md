#### ☕️ Delongi Kaffeezubereitung erkennen

**Zweck:** Diese Automation erkennt anhand der Verbrauchsdauer und des Stromflusses, ob gerade ein Kaffee zubereitet wird. Sie arbeitet eng mit Helfer und Template-Sensoren zusammen. Die Automation wird nur getriggert wenn der Helfer Spülen erkannt aus ist.

```
alias: Delongi Kaffeezubereitung erkennen
triggers:
  - entity_id:
      - sensor.kaffeemaschine_power    #hier deinen Power Sensor eintragen!
    above: 1300
    trigger: numeric_state
conditions:
  - condition: state
    entity_id: input_boolean.spulen_erkannt
    state: "off"
actions:
  - repeat:
      while:
        - condition: numeric_state
          entity_id: sensor.kaffeemaschine_power   #hier deinen Power Sensor eintragen!
          above: 1300
      sequence:
        - target:
            entity_id: input_number.kaffeetimer
          data:
            value: "{{ states('input_number.kaffeetimer') | int + 1 }}"
          action: input_number.set_value
        - delay:
            seconds: 1
  - delay:
      hours: 0
      minutes: 0
      seconds: 15
      milliseconds: 0
  - target:
      entity_id: input_number.kaffee_letzter_wert
    data:
      value: "{{ states('input_number.kaffeetimer') | int }}"
    action: input_number.set_value
  - target:
      entity_id: input_number.kaffeetimer
    data:
      value: 0
    action: input_number.set_value
  - condition: template
    value_template: |
      {{ states('input_number.kaffee_letzter_wert') | int >= 15
         and states('input_number.kaffee_letzter_wert') | int <= 45 }}
  - target:
      entity_id:
        - counter.kaffeezubereitungen
    action: counter.increment
    data: {}
  - delay:
      hours: 0
      minutes: 0
      seconds: 10
      milliseconds: 0
  - target:
      entity_id: input_number.kaffee_letzter_wert
    data:
      value: 0
    action: input_number.set_value
mode: restart

```

#### **Was passiert konkret?**

- Sobald der Stromverbrauch der Maschine **über 1300 Watt** steigt und **kein Spülvorgang aktiv ist**, beginnt die Überwachung.
- Solange der Verbrauch über diesem Wert bleibt, wird der interne Helfer `input_number.kaffeetimer` **jede Sekunde um 1 erhöht**.
- Nach einer kurzen Wartezeit von **15 Sekunden** wird der gemessene Zubereitungswert in `input_number.kaffee_letzter_wert` gespeichert und `kaffeetimer` auf 0 zurückgesetzt.
- Wenn der gemessene Zeitraum zwischen **15 und 45 Sekunden** liegt, wird ein Kaffee als erkannt gewertet:
  - Der **Zähler** `counter.kaffeezubereitungen` wird erhöht.
  - Weitere Automationen wie **Abschaltung** und **Wassertanküberwachung** reagieren auf diesen Zählerstand.
- Nach weiteren 10 Sekunden wird `kaffee_letzter_wert` wieder auf 0 gesetzt, um für den nächsten Vorgang bereit zu sein.


> Diese Erkennung arbeitet rein auf Basis von Energieverbrauch und Zeit – ohne direkte Kommunikation mit dem Gerät. Sie funktioniert zuverlässig, solange man die Verbräuche seines Geräts kennt und typische Muster beobachtet.

