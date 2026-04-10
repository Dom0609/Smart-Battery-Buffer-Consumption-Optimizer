# Smart Battery Buffer & Consumption Optimizer (V3.0)

> ### ⚠️ STATUS: EXPERIMENTELLE TESTPHASE (Work in Progress)
> **Wichtiger Hinweis:** Dieses Projekt befindet sich in einer aktiven Entwicklungs- und Testphase. Die Regelungslogik wird täglich im Live-Betrieb verfeinert.
>
> * **Einschränkungen:** Es kann vorkommen, dass die Regelung in bestimmten Szenarien (noch) nicht sauber reagiert, Schwingungen erzeugt oder die Bluetooth-Verbindung kurzzeitig verliert.
> * **Haftungsausschluss:** Die Nutzung erfolgt auf eigene Gefahr. Ich übernehme keinerlei Haftung für Fehlfunktionen, Hardware-Schäden am BMS/Akku oder unerwartete Stromkosten.

---

## Über dieses Projekt
Dieses Repository dokumentiert das persönliche Setup von **Dom0609**. Da ich langfristig auf ein System mit einem **Hoymiles HMT-2250** umsteige, dient dieser Stand als Archivierung meiner Erfahrungen mit der Marstek-Plattform. Die hier dokumentierte Logik löst die systembedingte Trägheit der Bluetooth-Steuerung (ca. 14s Latenz) durch ein intelligentes Regelkonzept.

## Das Szenario
Das System arbeitet in einer Umgebung mit zwei Energiequellen:
* **Marstek B2500 (Gen 1):** Dynamisch geregelt via ESP32/ESPHome (BLE).
* **Zweiter Wechselrichter (PV1):** Ungeregelt (liefert eine konstante Grund-Einspeisung).
* **Messung:** Ein **Shelly 3EM** am Hausanschluss liefert den Referenzwert für die Regelung. Ein **Shelly 1PM** überwacht den Ertrag des ungeregelten PV1-Wechselrichters als zusätzliche Störgröße.

## Regelungskonzept: Zustandsbasierter P-Regler

Die Automation arbeitet wie ein industrieller Zustandsautomat, um die Latenz der Bluetooth-Strecke auszugleichen.

### 1. Adaptive Zustandssteuerung
Die Logik wechselt automatisch zwischen zwei Modi:

* **Zustand: Ertrags-Phase (Tag)**
    * **Trigger:** PV-Ertrag > 250W oder aktiver interner PV-Input.
    * **Zielwert:** `-200W` (Einspeisung).
    * **Logik:** Während der Solarproduktion wird eine leichte Einspeisung toleriert. Dies priorisiert die Batterieladung und verhindert, dass der Regler gegen den Ladestrom arbeitet.
* **Zustand: Bezugs-Phase (Akku-Betrieb / Nacht)**
    * **Trigger:** PV-Leistung seit > 20 Min. unter 5W.
    * **Zielwert:** `+100W` (Netzbezug).
    * **Logik:** Um ungewollte Netzeinspeisung bei Lastwechseln trotz der 14s-Latenz zu vermeiden, wird ein bewusster Puffer zum Nullpunkt gehalten.

### 2. Variable Schrittweiten (Adaptive Gain)
Um Schwingungen zu vermeiden, reagiert die Korrekturgeschwindigkeit (`max_step`) dynamisch auf die Fehlergröße:
* **Grobausgleich (150W Steps):** Bei Fehlern > 300W oder starker Einspeisung.
* **Feinausgleich (40W Steps):** Im Nahbereich des Zielwerts zur Materialschonung.
* **Hysterese (Deadzone 10W):** Verhindert unnötiges Regeln bei minimalen Rauschwerten der Sensoren.

## Hardware-Anforderungen
* **ESP32 DevKit:** Geflasht mit ESPHome (Arduino Framework empfohlen).
* **Shelly 3EM:** Als zentraler Netz-Sensor (`sensor.netz_gesamtbezug`).
* **Shelly 1PM:** Zur Überwachung der Störgröße (PV1-Einspeisung).

## Installation & Guides

1. **Secrets:** `secrets.yaml` lokal anlegen (nicht im Repo hochladen!).
2. **ESPHome:** ESP32 mit der [b2500-controller.yaml](./esphome/b2500-controller.yaml) flashen. 
    * *Details siehe [ESPHome Guide](./esphome/README_ESP32.md)*
3. **Home Assistant:** Automation importieren und Entitäten anpassen.
    * *Details zur Logik siehe [Automations Guide](./homeassistant/README_Automation.md)*

## Feedback & Mitarbeit
Da sich das System noch in der Validierung befindet, ist Feedback sehr willkommen! Falls du Optimierungen an den Schrittweiten oder der Tag/Nacht-Erkennung vorgenommen hast, eröffne gerne ein **Issue** oder einen **Pull Request**.

## Credits & Quellen
* **BLE-Integration:** Basiert auf der großartigen Vorarbeit von [tomquist/esphome-b2500](https://github.com/tomquist/esphome-b2500).
* **Framework:** ESPHome & Home Assistant Community.

---
*Erstellt von Dom0609 - 2026*
