#### 🌀 Delongi Spülen erkennen

**Zweck:** Diese Automation erkennt einen Spülvorgang der Kaffeemaschine beim Einschalten (z. B. morgens). Der Spülvorgang verbraucht kurzzeitig Strom, wird aber nicht als Kaffeezubereitung gezählt.

```
alias: Delongi Spülen erkennen
trigger:
  - platform: numeric_state
    entity_id: sensor.kaffeemaschine_led_switch_0_power
    above: 500
condition:
  - condition: state
    entity_id: timer.kaffeemaschine_15_min_timer
    state: idle
  - condition: state
    entity_id: timer.kaffeemaschine_40_min_timer
    state: idle
action:
  - service: input_boolean.turn_on
    target:
      entity_id: input_boolean.spulen_erkannt
  - delay:
      seconds: 55
  - service: input_boolean.turn_off
    target:
      entity_id: input_boolean.spulen_erkannt
  - service: timer.start
    target:
      entity_id: timer.kaffeemaschine_40_min_timer
mode: restart
```

**Was passiert konkret?**

- Wenn der Stromverbrauch beim Einschalten kurz über **500 Watt** steigt und **keine Timer aktiv** sind, wird angenommen, dass es sich um einen Spülvorgang handelt.
- Der **Schalter** `input_boolean.spulen_erkannt` wird für **55 Sekunden** aktiviert.
- Dadurch wird in anderen Automationen vermieden, dass der Spülvorgang als Kaffeezubereitung erkannt wird.
- Danach wird der Spülen-Schalter wieder deaktiviert.
- Zusätzlich wird der **40-Minuten-Timer** gestartet, damit die Maschine nicht sofort wieder abgeschaltet wird.