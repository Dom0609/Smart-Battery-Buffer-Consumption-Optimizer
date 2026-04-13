# Smart Battery Buffer & Consumption Optimizer (v1.0.0 - Stable Release)

⚠️ **STATUS: STABLE RELEASE (April 2026)**
Dieses Setup hat die Validierungsphase erfolgreich abgeschlossen. Nach der Migration auf die MQTT-Architektur und intensiven Feldtests mit Hybrid-PV-Systemen ist die Version 1.0.0 nun der empfohlene Standard für maximale Stabilität und Hardwareschonung.

> ### 🛑 ENTWICKLUNGS-HINWEIS
> Die bisherige **ESP32/BLE-Steuerung (V3.0)** wird nicht mehr aktiv weiterentwickelt. Die MQTT-Lösung bietet die notwendige Latenzfreiheit für eine präzise Regelung. Der alte Code bleibt für Community-Projekte im Archiv erhalten.

---

## Über dieses Projekt
Dieses Repository dokumentiert das optimierte Setup von **Dom0609**. Ziel ist die intelligente Eigenverbrauchsoptimierung eines Marstek/Hame Speichersystems (B2500 / HMJ-2) in einer dynamischen Umgebung.

Durch den Einsatz einer **lokalen MQTT-Architektur** und einer **Hysterese-basierten Regelungslogik** wurde ein System geschaffen, das trotz Cloud-Latenzen und schwankender Lasten eine zuverlässige Nulleinspeisung ermöglicht.

## Die Architektur: Performance & Transparenz
Dank des Add-ons **[hm2mqtt](https://github.com/tomquist/hm2mqtt)** von **TomQuist** konnte die Systemperformance massiv gesteigert werden.

| Feature | Legacy (V3.0 ESP32/BLE) | Aktuell (v1.0.0 Stable) |
| :--- | :--- | :--- |
| **Latenz** | ca. 14 Sekunden | < 1 Sekunde |
| **Stabilität** | Funkabhängig (Bluetooth) | Hoch (Lokal/IP) |
| **Transparenz** | Basis-Daten | Vollzugriff (inkl. Einzelzellen) |
| **Regelverhalten** | Träge & schwingungsanfällig | **Robust & Balanced** |

---

## Das Szenario (Hybrid-PV)
Das System ist speziell für Haushalte mit mehreren Energiequellen optimiert:
* **Speicher:** Marstek B2500 / HAME Energy HMJ-2 (Geregelt via `hm2mqtt`).
* **Zusatz-PV (PV1):** Ein zweiter, unabhängiger Wechselrichter (Balkonkraftwerk), dessen Ertrag als dynamische Störgröße einfließt.
* **Sensorik:**
    * **Shelly 3EM:** Referenzwert am Hausanschluss (`sensor.netz_gesamtbezug`).
    * **Shelly (diverse):** Überwachung der PV1-Einspeisung (`sensor.pv_1_einspeissung`).

---

## Regelungskonzept: "Balanced Hybrid"

Die Logik v1.0.0 setzt auf Ruhe im System statt nervösem Nachregeln.

### 1. Adaptive Zustandssteuerung
* **Ertrags-Phase (Tag):**
    * **Zielwert:** `-200W` am Netzanschluss. Dies priorisiert die Batterieladung und nutzt den Ertrag von PV1 optimal aus.
* **Bezugs-Phase (Nacht):**
    * **Zielwert:** `+100W` (Netzbezug). Dient als Sicherheitspuffer, um bei plötzlichen Lastwechseln keine unnötige Netzeinspeisung aus dem Akku zu provozieren.

### 2. Die v1.0.0 Stabilitäts-Features
* **100W Hysterese (Deadzone):** Das System reagiert erst, wenn die Abweichung vom Zielwert mehr als 100W beträgt. Dies schont die Relais und die Leistungselektronik des Speichers massiv.
* **Adaptive 100W-Steps:** Bei großen Lastsprüngen (z. B. Wasserkocher) korrigiert das System in 100W-Schritten. Das ermöglicht ein zügiges Erreichen des Zielwerts ohne "Überschwingen".
* **Latenz-Management:** Durch die Reduktion der MQTT-Schreibbefehle (nur bei Bedarf außerhalb der Deadzone) wird die Kommunikation stabilisiert und ESP-Hänger vermieden.

### 3. Deep Discharge Protection
* **Rescue-Trigger:** Bei einem SoC < 11% wird die Entladung sofort gestoppt und der Lademodus erzwungen, um das System vor einer Notabschaltung zu schützen.

---

## Installation & Setup
1.  **MQTT Bridge:** Installation des [hm2mqtt Add-ons](https://github.com/tomquist/hm2mqtt). **Wichtig:** MQTT muss zuvor über den Marstek-Support (App-Feedback) freigeschaltet werden!
2.  **Home Assistant:** Die finale Automation findet sich im Ordner `/homeassistant/`.
3.  **Anpassung:** Ersetze die Platzhalter-Entitäten durch deine individuelle `deviceId`.

## Feedback & Mitarbeit
Dieses Projekt ist auf Langlebigkeit ausgelegt (Ziel: 10 Jahre stabiler Betrieb). Optimierungsvorschläge zur weiteren Effizienzsteigerung sind willkommen!
* Eröffne ein **Issue** für Diskussionen.
* Teile deine Ergebnisse via **Pull Request**.

---

## Credits & Quellen
* **MQTT-Brücke:** [tomquist/hm2mqtt](https://github.com/tomquist/hm2mqtt)
* **Inspiration:** Dank an die gesamte Community, die an der Entschlüsselung der Hame/Marstek-Protokolle mitgewirkt hat.

*Erstellt von Dom0609 - 2026*


