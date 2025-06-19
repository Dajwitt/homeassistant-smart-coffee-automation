### 🧪 Template-Sensoren in Home Assistant

Diese Sensoren werten Daten intelligent aus und erkennen zuverlässig, ob gerade ein Kaffee zubereitet wird oder ob sich die Maschine im Spülvorgang befindet.

---

#### 🟢 binary_sensor.kaffeezubereitung_erkannt

- Typ: Template Binary Sensor
- Zustand: on, wenn der gespeicherte Wert von input_number.kaffee_letzter_wert zwischen 15 und 45 Sekunden liegt.
- Zweck: Erkennt eine gültige Kaffeezubereitung anhand der Dauer der Stromspitze über 1300 W.
- Voraussetzung: Der Wert muss zuvor durch eine Automation ermittelt und in den Helfer geschrieben worden sein.

```
template:
  - binary_sensor:
      - name: "Kaffeezubereitung erkannt"
        unique_id: coffee_brewing_detected
        state: >
          {{ states('input_number.kaffee_letzter_wert') | int(0) >= 15
             and states('input_number.kaffee_letzter_wert') | int(0) <= 45 }}
```

---

#### 🟢 binary_sensor.kaffeemaschine_ab_500w

- Typ: Template Binary Sensor
- Zustand: on, sobald der aktuelle Verbrauch über 500 Watt liegt.
- Zweck: Erkennt, ob die Maschine aktiv ist – z. B. während Spülvorgang oder Aufheizen.
- Hinweis: Wird z.B. zur Filterung von Aktionen während Spülvorgängen verwendet.

```
template:
  - binary_sensor:
      - name: "Kaffeemaschine ab 500W"
        unique_id: coffee_machine_active_above_500w
        state: >
          {{ states('sensor.kaffeemaschine_led_switch_0_power') | float(0) > 500 }}
```

---

> 📎 Hinweis: Falls du bereits einen template:-Eintrag in deiner configuration.yaml hast, ergänze die binary_sensor -Abschnitte dort\*\*, ohne den template:-Block doppelt zu definieren.

---

### 💡 Erklärungen zu den erweiterten Parametern:

- unique_id: 
  - Eine eindeutige Kennung für den Sensor. Diese ID macht den Sensor in der Home Assistant Benutzeroberfläche (unter "Einstellungen" > "Geräte & Dienste" > "Entitäten") bearbeitbar, so dass du ihn später einfach umbenennen oder anpassen kannst, ohne direkt in der YAML-Datei suchen zu müssen.
- | int(0) / | float(0): 
  - Dies sind sogenannte Jinja2-Filter. Sie wandeln den Wert des Sensors in eine ganze Zahl (int) oder eine Dezimalzahl (float) um.
  - Der Zusatz (0) ist hier eine wichtige Verbesserung: 
  - Falls der Quellsensor (z.B. input_number.kaffee_letzter_wert oder sensor.kaffeemaschine_led_switch_0_power) vorübergehend nicht verfügbar ist oder keinen numerischen Wert liefert (z.B. "unavailable"), wird standardmäßig 0 angenommen. 
  - Dies verhindert, dass dein Template-Sensor selbst den Zustand "unavailable" annimmt und sorgt für eine robustere Funktionalität.

---

### ▶️ Nächste Schritte

Nachdem du diese Sensoren in deiner configuration.yaml Datei hinzugefügt oder aktualisiert hast:

- Speichere deine configuration.yaml Datei.
- Gehe in Home Assistant zu Einstellungen > Entwicklerwerkzeuge > YAML.
- Klicke auf "Prüfe Konfiguration", um Syntaxfehler zu finden. Das ist ein extrem wichtiger Schritt!
- Klicke anschließend auf "YAML-Konfiguration neu laden" oder starte Home Assistant neu.

Sobald die Sensoren geladen sind, kannst du ihre Zustände in deinen Home Assistant Entitäten finden.
