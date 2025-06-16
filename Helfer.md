### Helfer in Home Assistant 🛠️

Diese Helfer sind essenziell, um den Zustand der Kaffeemaschine zu erkennen, zu speichern und Automationen auszulösen. Sie bilden die Grundlage für die Logik hinter der Verbrauchsauswertung und der Reaktion auf erkannte Abläufe.

#### 🔢 counter.kaffeezubereitungen

- **Typ:** Zähler (Counter)
- **Name:** Kaffeezubereitungen
- **Funktion:** Zählt die Anzahl der Kaffeezubereitungen seit der letzten Befüllung des Wassertanks.
- **Zweck:** Erkennt, wann eine bestimmte Anzahl an Kaffee erreicht wurde (z. B. 5), um eine Warnung vor leerem Tank auszulösen oder Automationen zu aktivieren.

---

#### 🔁 `input_boolean.kaffeemaschine_kaffeezubereitung`

- **Typ:** Schalter (Boolesche Eingabe) 
- **Name:** Kaffeemaschine - Kaffeezubereitung
- **Funktion:** Wird aktiviert, sobald eine Kaffeezubereitung erkannt wurde, und nach ca. 60 Sekunden wieder deaktiviert.
- **Zweck:** Dient als zentrales Steuersignal für Folge-Aktionen wie das Hochzählen des Zählers.

---

#### 🔢 `input_number.kaffeetimer`

- **Typ:** Zahl (Zahlenwert-Eingabe)
- **Name:** Kaffeetimer
- **Funktion:** Zählt in Sekunden, wie lange der Stromverbrauch über dem Schwellenwert von 1300 Watt liegt.
- **Zweck:** Dient zur Erkennung der Dauer der eigentlichen Kaffeezubereitung.

---

#### 🔢 `input_number.kaffee_letzter_wert`

- **Typ:** Zahl (Zahlenwert-Eingabe)
- **Name:** Kaffee Letzter Wert
- **Funktion:** Speichert den finalen Wert von `kaffeetimer` zur späteren Auswertung.
- **Zweck:** Wenn dieser Wert zwischen 15 und 45 liegt, wird eine gültige Zubereitung angenommen.

---

#### ⏱️ `timer.kaffeemaschine_15_min_timer`

- **Typ:** Timer
- **Name:** Kaffeemaschine - 15 min Timer
- **Funktion:** Startet nach Ablauf des 40-Minuten-Timers und führt zur Abschaltung der Maschine.
- **Zweck:** Verhindert das automatische Spülen der Maschine beim Übergang in den Standby.

---

#### ⏱️ `timer.kaffeemaschine_40_min_timer`

- **Typ:** Timer
- **Name:** Kaffeemaschine - 40 min Timer
- **Funktion:** Startet nach jeder Kaffeezubereitung oder jedem Spülvorgang.
- **Zweck:** Gibt der Maschine ausreichend Betriebszeit, bevor ein Abschaltvorgang eingeleitet wird.

---

#### ⏱️ `timer.kaffeemaschine_5_min_timer`

- **Typ:** Timer
- **Name:** Kaffeemaschine - 5 min Timer
- **Funktion:** Warnt den Nutzer bei leerem Tank durch eine Verzögerung von 5 Minuten.
- **Zweck:** Gibt die Möglichkeit, den Tank zu füllen, bevor automatisch abgeschaltet wird.

---

#### 🔁 input_boolean.kaffee_5_min_timer

- **Typ:** Schalter (Boolesche Eingabe) 
- **Name:** Kaffee 5 Min Timer
- **Funktion:** Steuert den `timer.kaffeemaschine_5_min_timer`
- **Zweck:** Unterstützt die zuverlässige Steuerung des Kaffeemaschine - 5 min Timer

---

#### 🔁 `input_boolean.spulen_erkannt`

- **Typ:** Schalter (Boolesche Eingabe) 
- **Name:** Spülen erkannt
- **Funktion:** Wird beim Einschalten oder Spülvorgang aktiviert und nach etwa 55 Sekunden wieder deaktiviert.
- **Zweck:** Verhindert, dass der Spülvorgang als Kaffeezubereitung erkannt wird.

---

> 💡 **Tipp:** Alle Helfer lassen sich bequem im Home Assistant unter *Einstellungen → Geräte & Dienste → Helfer* anlegen.
