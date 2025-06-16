## Geräte für das smarte Kaffee-Setup 🛠️

Damit dein Kaffeevollautomat künftig nicht nur brüht, sondern auch „spricht“, brauchst du ein paar unterstützende Helferlein. Die hier vorgestellten Geräte sind erprobt und erfüllen ihren Zweck – du kannst sie aber natürlich mit ähnlichen Geräten ersetzen, sofern sie dieselben Funktionen abdecken.

### ☕ Mein Setup (Beispiel)

| Komponente        | Funktion                                                                 |
|-------------------|--------------------------------------------------------------------------|
| **Kaffeevollautomat** | Delonghi ECAM 20.116.B – klassisches Modell ohne smarte Funktionen       |
| **Shelly 1PM Gen3**   | Misst präzise den Stromverbrauch und kann die Maschine ein-/ausschalten  |
| **Aqara Türsensor**   | Erkennt, ob der Wassertank entnommen wurde                               |
| **Amazon Echo**       | Gibt Sprachausgaben bei leerem Wassertank oder erfolgreicher Zubereitung |

> 💡 **Wichtig:** Dein Automat muss nicht smart sein – entscheidend ist, dass er sich *vorhersagbar* verhält: Spült beim Einschalten, verbraucht Strom beim Brühen, spült wieder beim Ausschalten.

---

### **🔌 Shelly 1PM – Warum?**

Er misst kontinuierlich den Stromverbrauch – das Herzstück der Erkennung:

- Spülen → kurze Last über 500 W
- Kaffee → stabiler Verbrauch 1300–1600 W

---

### **🚰 Türsensor – Warum?**

Er erkennt, wenn der Wassertank entnommen wurde. Wird er z. B. länger als 10 Sekunden entfernt, geht Home Assistant davon aus: **Tank wurde nachgefüllt** → Zähler wird zurückgesetzt.

So kannst du dich z. B. nach 5 Kaffeezubereitungen daran erinnern lassen:

> „Das Wasser reicht nicht mehr für einen weiteren Kaffee! 😱“

---

### 🛠️ Geräte-Alternativen

- Statt Shelly 1PM: z. B. Tasmota-Steckdose mit Power-Messung
- Stromsensor via ESP32 (Eigenbau)
- Türsensor: Aqara, Sonoff, Tuya – alle, die Home Assistant unterstützen