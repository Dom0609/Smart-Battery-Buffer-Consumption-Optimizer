# Smart Battery Buffer & Consumption Optimizer (v1.0.8)

⚠️ **STATUS: STABLE RELEASE (17. April 2026)**
Dieses Repository bietet eine hochstabile Steuerung für Marstek/Hame Speichersysteme via MQTT. Das System nutzt eine **Dynamic-Turbo-Strategie**, um Hardware-Latenzen und das Zusammenspiel mehrerer Wechselrichter (z.B. bei paralleler PV1-Einspeisung) perfekt auszugleichen.

> ### 🚀 Neu in v1.0.8: Der Stability-Patch
> Dieses Update adressiert kritische Hardware-Phänomene (wie den 500W-Fallback-Bug), die besonders bei schwacher WLAN-Anbindung (RSSI < -80dBm) auftreten.
> * **Anti-Bounce (40W Floor):** Verhindert unkontrollierte Leistungssprünge durch eine Mindestlast-Sicherung von 40W.
> * **WLAN-Resilience:** Optimierte Timeouts für stabile Steuerung bei schwierigen Empfangsbedingungen.
> * **Dynamic Turbo:** Adaptive Regelschritte (150W / 20W) für maximale Präzision ohne Aufschwingen.

---

## Über dieses Projekt
Ziel dieses Projekts von **Dom0609** ist eine robuste Steuerung des Marstek B2500 / HAME HMJ-2 Speichers. Im Fokus steht eine intelligente Zielwert-Steuerung, die Systemstabilität und Hardware-Langlebigkeit priorisiert.

## 🏗️ Das Szenario: Hybrid-Ready & Stand-alone
Die Logik ist für zwei Einsatzszenarien optimiert:
1. **Hybrid-Setup:** Speicher parallel zu einem zweiten, ungeregelten Wechselrichter (PV1).
2. **Stand-alone:** Hochstabile Einzelsteuerung für den Speicher.

| Feature | Legacy (V3.0 ESP32/BLE) | Aktuell (v1.0.8 Stable) |
| :--- | :--- | :--- |
| **Latenz** | ca. 14 Sekunden | **< 1 Sekunde** |
| **WLAN-Stabilität** | Funkabhängig (Bluetooth) | **Hoch (MQTT Keep-Alive)** |
| **Regelverhalten** | Träge & schwingungsanfällig | **Dynamic Turbo (Adaptive)** |
| **Min. Output** | 0W (Instabil / Bug-Gefahr) | **40W (Stable Floor)** |

---

## 💡 Regelungskonzept: Dynamic Turbo Strategy

### 1. Adaptive Regelschritte
* **Turbo-Modus:** Bei großen Abweichungen (>150W) regelt das System massiv nach.
* **Precision-Modus:** Im Zielbereich regelt das System in sanften **20W-Schritten**, um die Blindleistung des Shelly Pro 3EM auszugleichen.

### 2. Smart-Targeting & Offsets
* **40W Keep-Alive Floor:** Das System sendet niemals `0W`. Diese Mindestlast hält die Kommunikation aktiv und verhindert den berüchtigten 500W-Fallback-Bug der Firmware.
* **Zielwerte:** Nachts wird ein minimaler Netzbezug von +40W angestrebt, um Akku-Energie als Puffer zu halten. Tagsüber wird auf -100W geregelt, um PV1-Überschuss vorrangig in den Akku zu leiten.

---

## 🛠️ Installation & Setup
1. **MQTT Freischaltung:** Über den Marstek-Support (App-Feedback) beantragen.
2. **Bridge-Setup:** Installation des [hm2mqtt Add-ons](https://github.com/tomquist/hm2mqtt).
3. **Home Assistant:** Die Datei `/homeassistant/marstek_v1_0_8_dynamic_turbo.yaml` importieren.
4. **Helfer:** Folgende Entitäten müssen angelegt sein:
    * `input_boolean.marstek_hand_modus` (Manueller Stop)
    * `input_boolean.marstek_entlade_freigabe` (SOC-Schutz)
    * `sensor.netz_gesamtbezug` (Summe der Shelly-Phasen)

## 📁 Archiv & Legacy-Versionen
Falls du noch die alte Hardware-Anbindung via ESP32 und Bluetooth nutzt:
👉 [Legacy V3.0 (ESP32/BLE) Archiv](./homeassistant/legacy_v3_ble_esp32/)

---

## 🤝 Feedback & Mitarbeit
Optimierungsvorschläge sind jederzeit willkommen! Eröffne gerne ein **Issue** oder einen **Pull Request**.

**Credits:**
* MQTT-Brücke: `tomquist/hm2mqtt`
* Konzept & Entwicklung: **Dom0609**

*Dokumentationsstand: April 2026*
