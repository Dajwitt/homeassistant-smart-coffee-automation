### 🧪 Template-Sensoren in Home Assistant

Diese Sensoren werten Daten intelligent aus und erkennen zuverlässig, ob gerade ein Kaffee zubereitet wird oder ob sich die Maschine im Spülvorgang befindet.

---

#### 🟢 `binary_sensor.kaffeezubereitung_erkannt`

- **Typ:** Template Binary Sensor
- **Zustand:** `on`, wenn der gespeicherte Wert von `input_number.kaffee_letzter_wert` zwischen **15 und 45 Sekunden** liegt.
- **Zweck:** Erkennt eine gültige Kaffeezubereitung anhand der Dauer der Stromspitze über 1300 W.
- **Voraussetzung:** Der Wert muss zuvor durch eine Automation ermittelt und in den Helfer geschrieben worden sein.

```
template:
  - binary_sensor:
      - name: "Kaffeezubereitung erkannt"
        state: >
          {{ states('input_number.kaffee_letzter_wert') | int >= 15
             and states('input_number.kaffee_letzter_wert') | int <= 45 }}
```

---

#### 🟢 `binary_sensor.kaffeemaschine_ab_500w`

- **Typ:** Template Binary Sensor
- **Zustand:** `on`, sobald der **aktuelle Verbrauch über 500 Watt** liegt.
- **Zweck:** Erkennt, ob die Maschine aktiv ist – z. B. während Spülvorgang oder Aufheizen.
- **Hinweis:** Wird z. B. zur Filterung von Aktionen während Spülvorgängen verwendet.

```
template:
  - binary_sensor:
      - name: "Kaffeemaschine ab 500W"
        state: >
          {{ states('sensor.kaffeemaschine_led_switch_0_power') | float > 500 }}
```

---

> 📎 **Hinweis:** Falls du bereits einen `template:`-Eintrag in deiner `configuration.yaml` hast, **ergänze die** `binary_sensor`**-Abschnitte dort**, ohne den `template:`-Block doppelt zu definieren.