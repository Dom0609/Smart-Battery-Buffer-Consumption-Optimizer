# Smart Battery Buffer & Consumption Optimizer (v1.0.0 - Stable Release)

⚠️ **STATUS: STABLE RELEASE (April 2026)**
Dieses Setup nutzt eine **Smart-Offset-Strategie** zur Eigenverbrauchsoptimierung. Im Gegensatz zur klassischen Nulleinspeisung wird hier bewusst mit Zielwert-Verschiebungen gearbeitet, um Systemstabilität und maximale Batterieladung zu garantieren.

> ### 🛑 ENTWICKLUNGS-HINWEIS
> Die Steuerung via MQTT bietet gegenüber der alten ESP32/BLE-Lösung (V3.0) eine drastische Latenzreduzierung und ist die Basis für dieses Release.

---

## Über dieses Projekt
Dieses Repository dokumentiert das Setup von **Dom0609**. Ziel ist die intelligente Steuerung eines Marstek/Hame Speichersystems (B2500 / HMJ-2) in einer Hybrid-Umgebung mit zwei Energiequellen.

Der Fokus liegt nicht auf einer mathematischen "Punkt-Null-Landung", sondern auf einer robusten **Eigenverbrauchs-Maximierung**, die Hardware-Latenzen und schwankende Lasten durch intelligente Puffer (Offsets) ausgleicht.

## Die Architektur: Performance & Transparenz
Dank des Add-ons **[hm2mqtt](https://github.com/tomquist/hm2mqtt)** von **TomQuist** arbeitet das System lokal und nahezu in Echtzeit.

| Feature | Legacy (V3.0 ESP32/BLE) | Aktuell (v1.0.0 Stable) |
| :--- | :--- | :--- |
| **Latenz** | ca. 14 Sekunden | < 1 Sekunde |
| **Stabilität** | Funkabhängig (Bluetooth) | Hoch (Lokal/IP) |
| **Regelverhalten** | Träge & schwingungsanfällig | **Robust & Balanced** |

---

## Das Szenario (Hybrid-PV)
Optimiert für Haushalte mit parallelen Erzeugern:
* **Speicher-System:** Marstek B2500 / HAME Energy HMJ-2 (Geregelt via `hm2mqtt`).
* **Zusatz-PV (PV1):** Unabhängiger Wechselrichter, der als dynamische Störgröße fungiert.
* **Sensorik:** Shelly 3EM (Netzanschluss) & Shelly 1PM (PV1-Überwachung).

---

## Regelungskonzept: Smart Offset Strategy

Um ein ständiges "Aufschaukeln" der Wechselrichter zu verhindern, arbeitet die v1.0.0 mit gezielten Zielwerten:

### 1. Dynamische Zielwerte (Offsets)
* **Ertrags-Phase (Tag): Zielwert `-200W` (Einspeisung)**
    * **Grund:** Priorisierung der Batterieladung. Durch den negativen Zielwert wird sichergestellt, dass PV1 den Hausverbrauch deckt und der Marstek-Input zu 100% in den Akku fließt, statt im Haus verbraucht zu werden.
* **Bezugs-Phase (Nacht): Zielwert `+100W` (Netzbezug)**
    * **Grund:** Sicherheitspuffer. Da das System auf Lastspitzen reagieren muss, verhindert dieser Puffer, dass bei plötzlichem Abschalten von Geräten wertvolle Akkenergie ins Netz verschenkt wird.

### 2. Stabilitäts-Features
* **100W Hysterese (Deadzone):** Korrekturen erfolgen erst bei Abweichungen > 100W vom Zielwert. Dies schont die Hardware und verhindert nervöse Regelzyklen.
* **Adaptive 100W-Steps:** Schnelle Reaktion bei Großverbrauchern (Herd, Wasserkocher) ohne Überschwingen der Regelung.
* **Deep Discharge Protection:** Automatischer Lademodus bei < 11% SoC zum Schutz der Akkuzellen.

---

## Installation & Setup
1.  **MQTT Freischaltung:** Muss zuvor über den Marstek-Support (App-Feedback) beantragt werden.
2.  **Bridge-Setup:** Installation des [hm2mqtt Add-ons](https://github.com/tomquist/hm2mqtt).
3.  **Home Assistant:** Die Automation (`/homeassistant/marstek_v1_0_0_stable.yaml`) importieren und die `deviceId` sowie Sensor-Entitäten anpassen.

## Feedback & Mitarbeit
Optimierungsvorschläge zur weiteren Effizienzsteigerung sind willkommen!

---

## Credits
* **MQTT-Brücke:** [tomquist/hm2mqtt](https://github.com/tomquist/hm2mqtt)
* **Konzept:** Dom0609

*Dokumentationsstand: April 2026*

