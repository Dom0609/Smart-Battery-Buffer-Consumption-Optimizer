# Smart Battery Buffer & Consumption Optimizer (v1.0.0 - Stable Release)

⚠️ **STATUS: STABLE RELEASE (April 2026)**
Dieses Repository bietet eine hochstabile Steuerung für Marstek/Hame Speichersysteme via MQTT. Das System nutzt eine **Smart-Offset-Strategie** zur Eigenverbrauchsoptimierung, um Hardware-Latenzen und das Zusammenspiel mehrerer Wechselrichter perfekt auszugleichen.

> ### 🛑 ENTWICKLUNGS-HINWEIS
> Die Steuerung via MQTT ersetzt die veraltete ESP32/BLE-Lösung (V3.0). Durch den lokalen Zugriff wird die Latenz von ~14 Sekunden auf unter 1 Sekunde reduziert.

---

## Über dieses Projekt
Ziel dieses Projekts von **Dom0609** ist eine robuste Steuerung des Marstek B2500 / HAME HMJ-2 Speichers. Im Fokus steht nicht die mathematische "Nulleinspeisung", sondern eine intelligente **Zielwert-Steuerung (Smart Offset)**, die Systemstabilität und maximale Batterieladung über kurzfristige Schwankungen priorisiert.

## 🏗️ Das Szenario: Hybrid-Ready & Stand-alone
Die Logik ist für zwei Einsatzszenarien optimiert:

1. **Hybrid-Setup (Zwei Wechselrichter):** Speziell entwickelt für den Betrieb des Speichers parallel zu einem **zweiten, ungeregelten Wechselrichter (PV1)**. Das System nutzt PV1 als Basis und regelt den Speicher als dynamischen Puffer dazu.
2. **Stand-alone:** Die Automation funktioniert gleichermaßen als hochstabile Einzelsteuerung für den Speicher (hierzu einfach den PV1-Sensor in der Konfiguration ignorieren oder auf 0 setzen).

| Feature | Legacy (V3.0 ESP32/BLE) | Aktuell (v1.0.0 Stable) |
| :--- | :--- | :--- |
| **Latenz** | ca. 14 Sekunden | **< 1 Sekunde** |
| **Stabilität** | Funkabhängig (Bluetooth) | **Hoch (Lokal/IP)** |
| **Regelverhalten** | Träge & schwingungsanfällig | **Robust & Balanced** |

---

## 💡 Regelungskonzept: Smart Offset Strategy

Um ein gegenseitiges "Aufschaukeln" der Wechselrichter zu verhindern und die Hardware zu schonen, arbeitet die v1.0.0 mit gezielten Offsets:

### 1. Dynamische Zielwerte
* **Ertrags-Phase (Tag): Zielwert `-200W` (Einspeisung)**
    * **Grund:** Priorisierung der Batterieladung. Durch den negativen Zielwert wird sichergestellt, dass PV1 den Hausverbrauch deckt und der Marstek-Input zu 100% in den Akku fließt.
* **Bezugs-Phase (Nacht): Zielwert `+100W` (Netzbezug)**
    * **Grund:** Sicherheitspuffer. Verhindert, dass bei plötzlichem Abschalten von Geräten wertvolle Akkenergie ungewollt ins Netz verschenkt wird.

### 2. Stabilitäts-Features
* **100W Hysterese (Deadzone):** Korrekturen erfolgen erst bei Abweichungen > 100W vom Zielwert. Dies verhindert nervöse Regelzyklen bei kleinen Lastschwankungen.
* **Adaptive 100W-Steps:** Schnelle Reaktion bei Großverbrauchern (Herd, Wasserkocher) in 100W-Schritten, um den Zielwert zügig ohne Überschwingen zu erreichen.
* **Deep Discharge Protection:** Automatischer Lademodus bei < 11% SoC zum Schutz der Akkuzellen.

---

## 🛠️ Installation & Setup
1. **MQTT Freischaltung:** Muss zuvor über den Marstek-Support (App-Feedback) beantragt werden.
2. **Bridge-Setup:** Installation des [hm2mqtt Add-ons](https://github.com/tomquist/hm2mqtt) von TomQuist.
3. **Home Assistant:** Die Datei `/homeassistant/marstek_v1_0_0_stable.yaml` importieren.
4. **Anpassung:** In der YAML die `deviceId` sowie die Sensor-Entitäten (`sensor.netz_gesamtbezug` und optional `sensor.pv_1_einspeissung`) anpassen.

## 🤝 Feedback & Mitarbeit
 Optimierungsvorschläge sind jederzeit willkommen! Eröffne gerne ein **Issue** oder einen **Pull Request**.

---

## Credits
* **MQTT-Brücke:** [tomquist/hm2mqtt](https://github.com/tomquist/hm2mqtt)
* **Konzept & Entwicklung:** Dom0609

*Dokumentationsstand: April 2026*
