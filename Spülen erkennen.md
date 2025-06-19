#### 🌀 Delongi Spülen erkennen

Zweck: Diese Automation erkennt einen Spülvorgang deiner Kaffeemaschine, typischerweise beim Einschalten (z.B. morgens). Der Spülvorgang verbraucht kurzzeitig Strom, wird aber bewusst nicht als Kaffeezubereitung gezählt, um deine Statistik sauber zu halten.

```
alias: Delongi Spülen erkennen
trigger:
  - platform: numeric_state
    entity_id: sensor.kaffeemaschine_led_switch_0_power   # Ersetze dies durch die Entity ID deines Power Sensors!
    above: 500
    for: # <--- Neu: Sorgt dafür, dass der Wert für eine bestimmte Zeit über der Schwelle bleibt
      seconds: 2 # Passe diese Dauer an die typische Länge deiner Spülspitze an (z.B. 2-3 Sekunden)
conditions:
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
      seconds: 55 # Passe diese Dauer an die tatsächliche Länge des Spülvorgangs deiner Maschine an
  - service: input_boolean.turn_off
    target:
      entity_id: input_boolean.spulen_erkannt
  - service: timer.start
    target:
      entity_id: timer.kaffeemaschine_40_min_timer
mode: restart
```

Was passiert konkret?

- Wenn der Stromverbrauch deiner Kaffeemaschine kurz über 500 Watt steigt und diesen Wert für mindestens 2 Sekunden hält (dank for: { seconds: 2 }).
  
- UND wenn deine beiden Timer (timer.kaffeemaschine_15_min_timer und timer.kaffeemaschine_40_min_timer) gerade nicht aktiv sind (also idle).
  
- Dann wird angenommen, dass es sich um einen Spülvorgang handelt.
  
- Der Helfer-Schalter input_boolean.spulen_erkannt wird für 55 Sekunden aktiviert.
  
- Das ist wichtig: Dadurch wird in anderen Automationen (z.B. "Delongi Kaffeezubereitung erkennen") verhindert, dass der Spülvorgang fälschlicherweise als Kaffeezubereitung gezählt wird.
  
- Nach diesen 55 Sekunden wird der input_boolean.spulen_erkannt wieder deaktiviert.
  
- Zusätzlich wird der 40-Minuten-Timer gestartet. Das ist ein wichtiger Teil deiner Gesamtlogik, damit du genau weißt, wann die Maschine später automatisch abgeschaltet wird. 

---

### 🛠 Anpassung an deine Maschine

Die Werte in dieser Automation sind auf meine DeLonghi-Maschine abgestimmt – insbesondere der verwendete Verbrauchssensor und der Watt-Schwellenwert zum Erkennen des Spülvorgangs. 

Damit die Automation auch bei dir zuverlässig funktioniert, musst du folgende Punkte individuell anpassen:

- 🔌 Power-Sensor Ersetze sensor.kaffeemaschine_led_switch_0_power (im Code-Beispiel) durch die exakte entity_id deines eigenen Stromsensors. Das könnte z.B. sensor.shelly_plug_power oder die ID eines Zigbee-Geräts mit Leistungsmessung sein.
  
- ⚡ Watt-Wert (above) Passe die Schwelle above: 500 an den typischen Verbrauch deiner Maschine während des Startvorgangs an.  Bei vielen DeLonghi-Modellen liegt dieser oft bei ca. 500–600 Watt, aber bei anderen Geräten kann das stark variieren.
  
- ⏱ Delay-Zeit (seconds) Der delay-Wert (im Beispiel 55 Sekunden) orientiert sich an der Dauer des Spülvorgangs deiner Kaffeemaschine.  Wenn deine Maschine kürzer oder länger spült, passe den Wert entsprechend an, damit der input_boolean.spulen_erkannt für die gesamte Dauer aktiv ist.
  
- ⏱ for: Dauer im Trigger Überlege, wie lange die Leistungsspitze beim Spülen mindestens anhält. Ein Wert von 2 oder 3 Sekunden ist oft ein guter Start, um sehr kurze, irrelevante Ausschläge zu filtern.

---

💡 Tipp zur Analyse Starte deine Maschine manuell (z.B. nach dem Einschalten) und beobachte den Stromverbrauch im Energie-Diagramm deines Leistungssensors in Home Assistant.  So erkennst du genau, wann der Spülvorgang beginnt, wie hoch die Spitze ist und wie lange sie dauert – das hilft dir bei der optimalen Anpassung aller Werte!
