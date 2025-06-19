### 📅 2025-06-19

* **Template Sensoren**
    * **Code-Verbesserung:** `unique_id` hinzugefügt.
    * **Verbesserte Text-Erläuterungen:** Die Abschnitte "💡 **Erklärungen zu den erweiterten Parametern** und ▶️ **Nächste Schritte** wurden hinzugefügt


* **Automation: Delongi Spülen erkennen**
    * Den Trigger robuster gemacht, um Fehlalarme durch kurze Leistungsspitzen zu vermeiden. 
    * `for:`-Dauer zum Trigger hinzugefügt, um sicherzustellen, dass der erhöhte Stromverbrauch für eine bestimmte Zeit anhält, bevor die Automation auslöst.
      
        ```yaml
        trigger:
          - platform: numeric_state
            entity_id: sensor.dein_power_sensor_hier # Ersetze dies mit der echten ID deines Sensors (z.B. sensor.shelly_plug_power)
            above: 500
            for: # <--- Neu: Hier eine Dauer hinzufügen!
              seconds: 2 # Oder 3, je nachdem wie kurz/lang die Spitze deiner Spülung ist.
        ```
    * Klarstellung im Text, dass es um einen *Anstieg* des Verbrauchs geht, der für eine gewisse Zeit gehalten wird, nicht um eine kurzzeitige "Spitze".
    * Beispielcode für den Trigger aktualisiert, um die Verwendung von `for:` zu demonstrieren.

---

### 📅 2025-06-18

* 🆕 **Changelog-Datei erstellt**
   * für zukünftige Nachverfolgung von Änderungen.


* ✍️ **Hinweis zur Maschinenabhängigkeit in der Spül-Erkennung ergänzt** 
   * Die verwendete entity_id (Verbrauchssensor) und die delay-Zeit können je nach Kaffeemaschine variieren und müssen ggf. angepasst werden.

  
* ✅ **Kaffeezählung verbessert**
   * Eine zusätzliche Bedingung verhindert, dass ein Spülvorgang fälschlich als Kaffee gezählt wird.  
   * Die input_boolean.spulen_erkannt muss mindestens 5 Sekunden auf off stehen, bevor gezählt wird.

        ```yaml
        condition:
          - condition: state
            entity_id: input_boolean.spulen_erkannt
            state: "off"
            for: "00:00:05"
        ```

* 📌 **Kaffeezubereitung-Erkennung überarbeitet**

    * Beschreibung und Abläufe klarer erläutert
    * 15-Sekunden-Verzögerung nach dem Zählen erklärt (zur Filterung von Verbrauchsschwankungen)
    * Neuer Abschnitt "Anpassung an deine Maschine" mit Hinweisen zu Sensor, Schwelle, Mindestzeit und Brühzeit-Erkennung ergänzt

