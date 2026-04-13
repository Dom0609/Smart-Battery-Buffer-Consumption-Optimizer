# Smart Battery Buffer & Consumption Optimizer (V4.0 - MQTT Edition)

> ### ⚠️ STATUS: AKTIVE BETA-TESTPHASE
> **Projekt-Update (April 2026):** Migration von ESP32/BLE auf MQTT-Bridge abgeschlossen. 
> Dieses Setup befindet sich in der Validierung. Die Nutzung erfolgt auf eigene Gefahr.

---

## Über dieses Projekt
Dieses Repository dokumentiert das persönliche Setup von **Dom0609**. Ziel ist die intelligente Eigenverbrauchsoptimierung eines Marstek/Hame Speichersystems. 

Nach intensiven Tests mit ESP32-basierten Bluetooth-Lösungen wurde das System auf eine **MQTT-Architektur** umgestellt. Dieser Durchbruch ermöglichte erstmals eine nahezu latenzfreie Steuerung und eine zuverlässige Fernwartung über große Distanzen (erfolgreich getestet auf 40 km Entfernung).

## Die Architektur: Von BLE zu MQTT
Ein zentraler Dank geht an **[TomQuist](https://github.com/tomquist)**. Sein Add-on **[hm2mqtt](https://github.com/tomquist/hm2mqtt)** bildet das Rückgrat der Version 4.0.

| Feature | Legacy (V3.0 ESP32/BLE) | Aktuell (V4.0 MQTT) |
| :--- | :--- | :--- |
| **Latenz** | ca. 14 Sekunden | < 1 Sekunde |
| **Stabilität** | Funkabhängig (Bluetooth) | Hoch (Lokal/IP) |
| **Transparenz** | Basis-Daten | Vollzugriff (inkl. 14 Einzelzellen) |
| **Control** | Träge | Echtzeit-Befehle |

---

## Das Szenario
Das System optimiert den Eigenverbrauch in einer Umgebung mit zwei Energiequellen:
* **Speicher:** Marstek B2500 / HAME Energy HMJ-2 (Geregelt via `hm2mqtt`).
* **Zusatz-PV (PV1):** Ungeregelter Wechselrichter (Konstante Grund-Einspeisung).
* **Sensorik:** * **Shelly 3EM:** Referenzwert am Hausanschluss (`sensor.netz_gesamtbezug`).
    * **Shelly 1PM:** Überwachung der PV1-Störgröße.

---

## Regelungskonzept: Zustandsbasierter P-Regler

Die Logik arbeitet als Zustandsautomat in Home Assistant, um trotz hoher Lastwechsel eine Nulleinspeisung zu erreichen.

### 1. Adaptive Zustandssteuerung
* **Zustand: Ertrags-Phase (Tag)**
    * **Trigger:** PV-Ertrag > 250W oder aktiver interner PV-Input.
    * **Zielwert:** `-200W` (Bewusste leichte Einspeisung zur Priorisierung der Batterieladung).
* **Zustand: Bezugs-Phase (Nacht)**
    * **Trigger:** PV-Leistung seit > 20 Min. unter 5W.
    * **Zielwert:** `+100W` (Netzbezug als Sicherheitspuffer gegen Lastspitzen).

### 2. Dynamische Sicherheitsregeln (Deep Discharge Protection)
Ein kritischer Bestandteil der V4.0 ist die automatische Rettungslogik:
* **Trigger:** SoC < 10%.
* **Aktion:** Erzwingt `Discharge Depth = 0`, um das System vor einer Notabschaltung zu schützen.

### 3. Variable Schrittweiten (Adaptive Gain)
* **Grobausgleich (150W Steps):** Bei Fehlern > 300W für schnelle Reaktion.
* **Feinausgleich (40W Steps):** Im Nahbereich zur Materialschonung (Relais/Komponenten).
* **Hysterese:** 10W Deadzone zur Vermeidung von unnötigem "Regel-Rauschen".

---

## Installation & Setup

1.  **MQTT Bridge:** Installation des [hm2mqtt Add-ons von TomQuist](https://github.com/tomquist/hm2mqtt).
2.  **Home Assistant:** Import der Automationen aus dem Ordner `/v4_mqtt_control/`.
3.  **Anpassung:** Entitäten in der `secrets.yaml` oder direkt in der Automation auf die eigene `deviceId` anpassen.

---

## Credits & Quellen
* **MQTT-Brücke:** [tomquist/hm2mqtt](https://github.com/tomquist/hm2mqtt) - Ohne dieses Tool wäre die Fernwartung und Latenz-Optimierung nicht möglich gewesen.
* **Legacy-Inspiration:** [tomquist/esphome-b2500](https://github.com/tomquist/esphome-b2500).

---
*Erstellt von Dom0609 - 2026*
