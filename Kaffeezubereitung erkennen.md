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
    for: "00:00:05"
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
- Wenn der gemessene Zeitraum zwischen **15 und 45 Sekunden** liegt, wird ein Kaffee als erkannt gewertet: 
  - Der **Zähler** `counter.kaffeezubereitungen` wird erhöht.
  - Weitere Automationen wie **Abschaltung** und **Wassertanküberwachung** reagieren auf diesen Zählerstand.
- Nach weiteren 10 Sekunden wird `kaffee_letzter_wert` wieder auf 0 gesetzt, um für den nächsten Vorgang bereit zu sein.

🛠️ **Warum gibt es eine 15-Sekunden-Verzögerung?**

Während der Kaffeezubereitung schwankt der Stromverbrauch – z. B. beim Mahlen, Erhitzen und Ausgeben des Kaffees. Dabei kann der Verbrauch **kurzzeitig unter die definierte Schwelle** (z. B. 1300 Watt) fallen.

Ohne diese Verzögerung würde die Erkennung abbrechen, sobald der Verbrauch ein einziges Mal unter die Grenze fällt – obwohl der Brühvorgang noch läuft.

👉 Die 15 Sekunden sorgen dafür, dass die Automation **nicht sofort abbricht**, sondern nur dann den Zähler erhöht, wenn der Verbrauch **über 1300 Watt liegt**.

> Diese Erkennung arbeitet rein auf Basis von Energieverbrauch und Zeit – ohne direkte Kommunikation mit dem Gerät. Sie funktioniert zuverlässig, solange man die Verbräuche seines Geräts kennt und typische Muster beobachtet.

### 🔧 Anpassung an deine Maschine – *Kaffeezubereitung erkennen*

Diese Automation basiert auf den typischen Verbrauchswerten meiner DeLonghi-Maschine. Damit sie bei dir ebenfalls zuverlässig funktioniert, musst du folgende Punkte individuell anpassen:

#### 🔌 1. Power-Sensor (`sensor.kaffeemaschine_power`)

Trage hier den Stromsensor deiner Maschine ein – z. B. von einem **Shelly Plug S**, **mit Verbrauchsmessung**.

```
sensor.kaffeemaschine_power
```

#### ⚡ 2. Verbrauchsschwelle (`above: 1300`)

Die Automation reagiert nur auf Verbrauchswerte über einem bestimmten Watt-Wert.  
Meine Maschine verbraucht während der Brühphase **über 1300 Watt**.

👉 Ermittle für deine Maschine den typischen Verbrauch beim **reinen Brühvorgang wenn der Kaffee ausgegeben wird** (nicht beim Einschalten oder Spülen) und passe diesen Wert ggf. an. 

```
above: 1300
```

#### 

#### 3. Brühzeit-Erkennung (Kaffeezählerbedingung)

Am Ende der Automation wird geprüft, wie lange der Brühvorgang über der Verbrauchsschwelle lag: Passe die Range zwischen 15 und 45 ggf. an. 

```
- condition: template
  value_template: |
    {{ states('input_number.kaffee_letzter_wert') | int >= 15
       and states('input_number.kaffee_letzter_wert') | int <= 45 }}
```

👉 Dieser Bereich basiert auf den typischen Brühzeiten deiner Maschine.  
Miss, wie lange der Verbrauch **während einer vollständigen Kaffeezubereitung** über dem Schwellenwert liegt (z. B. 34 Sekunden).  
Ein sinnvoller Bereich liegt meist zwischen **20 und 60 Sekunden**, je nach Modell und Tassengröße.

