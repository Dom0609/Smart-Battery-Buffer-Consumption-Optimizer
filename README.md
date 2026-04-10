# Smart Battery Buffer & Consumption Optimizer

Dieses Repository enthält die Konfiguration für einen **ESP32-basierten Controller** (ESPHome) und eine **Home Assistant Automation**, um den Eigenverbrauch eines Marstek B2500 Speichersystems in einem komplexeren Umfeld zu optimieren.

## Projekthintergrund & Status
Dies ist ein privates Dokumentationsprojekt. Da ich langfristig auf ein System mit einem **Hoymiles HMT-2250** umsteige, dient dieser Stand als Archivierung meiner Erfahrungen mit der Marstek-Plattform. Die hier dokumentierte Logik löst die Trägheit der Bluetooth-Steuerung durch ein intelligentes Regelkonzept.

## Das Szenario
Das System arbeitet in einer Umgebung mit zwei Energiequellen:
* **Marstek B2500 (Gen 1):** Dynamisch geregelt via ESP32/ESPHome (BLE).
* **Zweiter Wechselrichter (PV1):** Ungeregelt (liefert eine konstante Grund-Einspeisung).
* **Messung:** Ein **Shelly 3EM** am Hausanschluss liefert den Referenzwert für die Regelung. Ein **Shelly 1PM** überwacht den Ertrag des ungeregelten PV1-Wechselrichters als zusätzliche Störgröße.

## Regelungskonzept: Zustandsbasierter P-Regler (V3.0)

Die Automation arbeitet wie ein industrieller Zustandsautomat, um die systembedingte Latenz der Bluetooth-Strecke (ca. 14s) auszugleichen.

### 1. Adaptive Zustandssteuerung
Die Logik wechselt automatisch zwischen zwei Modi, basierend auf der verfügbaren PV-Leistung:

* **Zustand: Ertrags-Phase (Tag)**
    * **Trigger:** PV-Ertrag > 250W oder aktiver interner PV-Input.
    * **Zielwert:** `-200W` (Einspeisung).
    * **Logik:** Während der Solarproduktion wird eine leichte Einspeisung toleriert. Dies priorisiert die Batterieladung und verhindert, dass der Regler bei wechselnder Bewölkung gegen den Ladestrom arbeitet.
* **Zustand: Bezugs-Phase (Akku-Betrieb / Nacht)**
    * **Trigger:** PV-Leistung seit > 20 Min. unter 5W.
    * **Zielwert:** `+100W` (Netzbezug).
    * **Logik:** Um ungewollte Netzeinspeisung bei Lastwechseln (z.B. Abschalten von Geräten) trotz der 14s-Latenz zu vermeiden, wird ein bewusster Puffer zum Nullpunkt gehalten.

### 2. Variable Schrittweiten (Adaptive Gain)
Um Schwingungen zu vermeiden, reagiert die Korrekturgeschwindigkeit (`max_step`) dynamisch auf die Fehlergröße:
* **Grobausgleich (150W Steps):** Bei Fehlern > 300W oder starker Einspeisung.
* **Feinausgleich (40W Steps):** Im Nahbereich des Zielwerts zur Materialschonung.
* **Hysterese (Deadzone 10W):** Verhindert unnötiges Regeln bei minimalen Rauschwerten der Sensoren.

## Hardware-Anforderungen
* **ESP32 DevKit:** Geflasht mit ESPHome (Arduino Framework für stabile BLE-Treiber).
* **Shelly 3EM:** Als zentraler Netz-Sensor (`sensor.netz_gesamtbezug`).
* **Shelly 1PM:** Zur Überwachung der Störgröße (PV1-Einspeisung).

## Installation

1. `secrets.yaml` lokal anlegen (nicht im Repo hochladen!).
2. ESP32 mit der bereitgestellten [b2500-controller.yaml](./esphome/b2500-controller.yaml) flashen (Details siehe [ESPHome Guide](./esphome/README_ESP32.md)).
3. Automation in Home Assistant importieren (Details zur Logik siehe [Automations Guide](./homeassistant/README_Automation.md)) und die Entitäten an die eigenen Shelly-IDs anpassen.

## Credits & Quellen
* **BLE-Integration:** Basiert auf der Vorarbeit von [tomquist/esphome-b2500](https://github.com/tomquist/esphome-b2500).
* **Framework:** ESPHome & Home Assistant Community.
