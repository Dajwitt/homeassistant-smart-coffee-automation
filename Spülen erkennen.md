#### 🌀 Delongi Spülen erkennen

**Zweck:** Diese Automation erkennt einen Spülvorgang der Kaffeemaschine beim Einschalten (z. B. morgens). Der Spülvorgang verbraucht kurzzeitig Strom, wird aber nicht als Kaffeezubereitung gezählt.

```
alias: Delongi Spülen erkennen
trigger:
  - platform: numeric_state
    entity_id: sensor.kaffeemaschine_power   #hier deinen Power Sensor eintragen!
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
- Zusätzlich wird der **40-Minuten-Timer** gestartet, damit du genau weisst, wann die Maschine wieder abgeschaltet wird.

### 🛠 Anpassung an deine Maschine

Die Werte in dieser Automation sind auf **meine DeLonghi-Maschine** abgestimmt – insbesondere der verwendete **Verbrauchssensor** und der **Watt-Schwellenwert** zum Erkennen des Spülvorgangs.

Damit die Automation auch bei dir zuverlässig funktioniert, musst du folgende Punkte **individuell anpassen**:

- 🔌 **Power-Sensor**  
  Ersetze `sensor.kaffeemaschine_power` durch die `entity_id` deines eigenen Stromsensors – z. B. `sensor.shelly_plug_power` oder ein Zigbee-Gerät mit Leistungsmessung.

- ⚡ **Watt-Wert (`above`)**  
  Passe die Schwelle an den typischen Verbrauch deiner Maschine **während des Spülvorgangs** an. Bei DeLonghi liegt dieser meist bei ca. 500–600 Watt. Bei anderen Geräten kann das stark variieren.

- ⏱ **Delay-Zeit**  
  Der `delay`-Wert (z. B. `00:00:55`) orientiert sich an der **Dauer des Spülvorgangs**. Wenn deine Maschine kürzer oder länger spült, passe den Wert entsprechend an.

---

💡 **Tipp zur Analyse**  
Starte deine Maschine manuell und beobachte den Stromverbrauch im Energie-Diagramm von Home Assistant.  
So erkennst du genau, wann der Spülvorgang beginnt und wie lange er dauert – das hilft dir bei der optimalen Anpassung.

