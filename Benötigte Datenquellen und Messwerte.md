## Benötigte Datenquellen und Messwerte 📊

Damit Home Assistant zuverlässig zwischen **Kaffeezubereitung**, **Spülvorgang**, **Standby** und **leerem Wassertank** unterscheiden kann, werden gezielt bestimmte Werte erfasst oder geschätzt. Diese bilden die Grundlage aller Helfer, Sensoren und Automationen:

---

### 🌀 Spülvorgang erkennen

- **Ziel:** Den Spülvorgang beim Einschalten erkennen.
- **Benötigt:** Verbrauch in Watt + Dauer in Sekunden.

 
> **Beispiel Delonghi:** Nach dem Einschalten steigt der Verbrauch kurz auf **über 500 Watt** und bleibt dort für rund **55 Sekunden**.

---

### ☕ Kaffeezubereitung erkennen

- **Ziel:** Dauer und Stromverbrauch der Zubereitung identifizieren.
- **Benötigt:** Typischer Stromverbrauch in Watt + Dauer in Sekunden.

  > **Beispiel Delonghi:** Während der Zubereitung liegt der Verbrauch stabil zwischen **1400 und 1600 Watt**, insbesondere beim Pressen und Ausgeben des Kaffees. Typisch sind **30–36 Sekunden über 1300 W**. Diese Kombination deutet zuverlässig auf eine Kaffeezubereitung hin..

---

### 💧 Wassertank-Überwachung

- **Ziel:** Erkennen, wann der Wassertank (basierend auf Anzahl der Zubereitungen) leer ist.
- **Benötigt:** Anzahl möglicher Kaffees mit einer Tankfüllung.

 > **Beispiel Delonghi:** **Beispiel Delonghi:** Etwa **5 Tassen pro Tankfüllung** – Home Assistant erkennt frühzeitig einen möglichen Engpass, noch bevor die Maschine selbst eine Warnung anzeigt.

---

### ⏱️ Abschaltlogik / Standby-Verhalten

- **Ziel:** Energie sparen & automatisches Spülen vermeiden.
- **Benötigt:** Standby-Zeit nach letzter Aktivität.

> **Beispiel Delonghi:** Die Maschine bleibt nach der letzten Aktion ca. **60 Minuten aktiv**. Home Assistant schaltet sie nach **55 Minuten gezielt aus und wieder ein**, um die automatische Spülung zu verhindern.
