### 📅 2025-06-18

1. 🆕 **Changelog-Datei erstellt** für zukünftige Nachverfolgung von Änderungen.

2. ✍️ **Hinweis zur Maschinenabhängigkeit in der Spül-Erkennung ergänzt** 
   → Die verwendete entity_id (Verbrauchssensor) und die delay-Zeit können je nach Kaffeemaschine variieren und müssen ggf. angepasst werden.

  
 3. ✅ **Kaffeezählung verbessert** Eine zusätzliche Bedingung verhindert, dass ein Spülvorgang fälschlich als Kaffee gezählt wird.  
   Die input_boolean.spulen_erkannt muss mindestens 5 Sekunden auf off stehen, bevor gezählt wird:

  ```yaml
  condition:
    - condition: state
      entity_id: input_boolean.spulen_erkannt
      state: "off"
      for: "00:00:05"
  ```

4. 📌 **Kaffeezubereitung-Erkennung überarbeitet**

- Beschreibung und Abläufe klarer erläutert
- 15-Sekunden-Verzögerung nach dem Zählen erklärt (zur Filterung von Verbrauchsschwankungen)
- Neuer Abschnitt "Anpassung an deine Maschine" mit Hinweisen zu Sensor, Schwelle, Mindestzeit und Brühzeit-Erkennung ergänzt

