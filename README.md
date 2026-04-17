# Smart Battery Buffer & Consumption Optimizer (v1.0.8)

## ⚠️ STATUS: STABLE RELEASE (17. April 2026)
Dieses Repository bietet eine hochstabile Steuerung für Marstek/Hame Speichersysteme via MQTT. Das System nutzt eine **Dynamic-Turbo-Strategie**, um Hardware-Latenzen und das Zusammenspiel mehrerer Wechselrichter (z.B. bei paralleler PV1-Einspeisung) perfekt auszugleichen.

---

## 🚀 Neu in v1.0.8: Der Stability-Patch
Dieses Update adressiert kritische Hardware-Phänomene, die bei vielen Usern zu unerklärlichen Leistungssprüngen führten.

* **Anti-Bounce (40W Floor):** Das System sendet niemals den Befehl `0W`. Eine Mindestlast von **40W** verhindert, dass der Speicher in den Standby/Fehler-Modus verfällt oder unkontrolliert auf 500W hochspringt.
* **WLAN-Resilience:** Optimierte Timeouts für Installationen mit schwierigen Empfangsbedingungen (RSSI < -80dBm).
* **Dynamic Turbo Integration:** Ablösung der statischen 100W-Schritte durch eine adaptive Regelung (150W/20W).

---

## 🏗️ Das Szenario: Hybrid-Ready & Stand-alone
Die Logik ist für zwei Einsatzszenarien optimiert:

1.  **Hybrid-Setup (Zwei Wechselrichter):** Speziell entwickelt für den Betrieb des Speichers parallel zu einem zweiten, ungeregelten Wechselrichter (**PV1**). Das System nutzt PV1 als Basis und regelt den Speicher als dynamischen Puffer dazu.
2.  **Stand-alone:** Die Automation funktioniert gleichermaßen als hochstabile Einzelsteuerung für den Speicher.

| Feature | Legacy (V3.0 ESP32/BLE) | Aktuell (v1.0.8 Stable) |
| :--- | :--- | :--- |
| **Latenz** | ca. 14 Sekunden | **< 1 Sekunde** |
| **WLAN-Stabilität** | Funkabhängig (BT) | **Robust (MQTT Keep-Alive)** |
| **Regelverhalten** | Träge & schwingend | **Dynamic Turbo (Adaptive)** |
| **Min. Output** | 0W (Instabil) | **40W (Stable Floor)** |

---

## 💡 Regelungskonzept: Dynamic Turbo Strategy

Um ein gegenseitiges "Aufschaukeln" der Wechselrichter zu verhindern und die Hardware zu schonen, arbeitet die v1.0.8 mit zwei Geschwindigkeiten:

### 1. Adaptive Schritte
* **Turbo-Modus:** Bei großen Abweichungen (>150W) regelt das System massiv nach, um Netzbezug sofort zu stoppen.
* **Precision-Modus:** Im Zielbereich regelt das System in sanften **20W-Schritten**, um ein Aufschwingen der Wechselrichter zu verhindern und die Blindleistung des Shelly Pro 3EM auszugleichen.

### 2. Smart-Targeting & Offsets
* **Ertrags-Phase (Tag):** Zielwert ca. **-100W** (Einspeisung). Dies stellt sicher, dass PV1 den Hausverbrauch deckt und der Marstek-Input primär in den Akku fließt.
* **Bezugs-Phase (Nacht):** Zielwert **+40W** (Mindestlast). Dies hält die Kommunikation zum Speicher aktiv ("Keep-Alive") und verhindert den berüchtigten 500W-Fallback-Bug der Firmware.

---

## 🛠️ Installation & Setup

1.  **MQTT Freischaltung:** Muss zuvor über den Marstek-Support (App-Feedback) beantragt werden.
2.  **Bridge-Setup:** Installation des `hm2mqtt` Add-ons (z.B. von TomQuist).
3.  **Home Assistant:** Die Datei `/homeassistant/marstek_v1_0_8_dynamic_turbo.yaml` importieren.
4.  **Anpassung:** In der YAML die `deviceId` sowie die Sensor-Entitäten (`sensor.netz_gesamtbezug`) anpassen.

---

## 🤝 Feedback & Mitarbeit
Optimierungsvorschläge sind jederzeit willkommen! Da dieses Projekt aus der Praxis für die Praxis entstand (besonders für schwierige WLAN-Umgebungen), freue ich mich über jeden Pull Request.

**Credits:**
* MQTT-Brücke: `tomquist/hm2mqtt`
* Konzept & Entwicklung: **Dom0609**
