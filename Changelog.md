## Änderungsprotokoll – Smarte Kaffeemaschine

### 📅 2025-06-18

- 🆕 **Changelog-Datei erstellt** für zukünftige Nachverfolgung von Änderungen.
- ✍️ **Hinweis zur Maschinenabhängigkeit** in der Spül-Erkennung ergänzt:  
   → Die verwendete `entity_id` (Verbrauchssensor) und die `delay`-Zeit können je nach Kaffeemaschine variieren und **müssen ggf. angepasst werden**.  
  
- ✅ **Kaffeezählung verbessert**: Eine zusätzliche Bedingung verhindert, dass ein Spülvorgang fälschlich als Kaffee gezählt wird.  
   Die `input_boolean.spulen_erkannt` muss mindestens **5 Sekunden auf** `off` stehen, bevor gezählt wird:

  ```yaml
  condition:
    - condition: state
      entity_id: input_boolean.spulen_erkannt
      state: "off"
      for: "00:00:05"
  ```
