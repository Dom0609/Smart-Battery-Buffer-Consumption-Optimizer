# Smart Battery Buffer & Consumption Optimizer (v2.0)

![Release](https://img.shields.io/badge/Release-v2.0--2026-gold?style=for-the-badge)
![Hardware](https://img.shields.io/badge/Hardware-Marstek%20|%20Hame-blue?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Home%20Assistant-integrated?style=for-the-badge)

Dieses Repository bietet eine hochstabile Steuerung für **Marstek B2500 / Hame HMJ-2** Speichersysteme. Nach der Entwicklung der manuellen MQTT-Steuerung (v1.0.8) wurde das System auf eine **Zähler-Emulation via AstraMeter** umgestellt, um Hardware-Latenzen zu eliminieren und den 1680Wp PV-Ausbau perfekt zu managen.

## 📢 DER STRATEGIEWECHSEL (April 2026)

Nach intensiven Langzeittests hat sich gezeigt: Die direkte Steuerung per Watt-Befehlen (v1.0.8) ist anfällig für den sogenannten "500W-Fallback-Bug" und WLAN-Latenzen.

**Die neue Gold-Standard-Lösung:** Wir nutzen weiterhin die MQTT-Anbindung (`hm2mqtt`), aber anstatt den Speicher aktiv zu "schieben", emulieren wir einen **Shelly Pro 3EM** (via AstraMeter Add-on). Der Marstek nutzt so seinen internen, nativen Regelalgorithmus, was die Stabilität massiv erhöht.

---

## 🏗️ Das Setup (Hybrid-Ready)

Mein System ist auf maximale Effizienz ausgelegt:

* **Speicher:** Marstek B2500 / Hame HMJ-2.
* **PV-Leistung:** 1680Wp (massive Überbelegung für starke Schlechtwetter-Performance).
* **Zusatz-WR:** Ein Deye Wechselrichter läuft parallel an PV1.
* **Messung:** Shelly Pro 3EM am Hausanschluss.

---


## 💡 Die Logik: Intelligente Offsets (v2.0)

Damit das System bei 1680W PV nicht "nervös" wird und nachts keinen Strom verschenkt, nutzt das Setup ein spezialisiertes Home Assistant Template für den AstraMeter-Sensor.

### Strategie-Modi:
* **TAG-MODUS:** Aggressives Laden und Sichern des Überschusses durch einen positiven Offset (+100W), sobald die PV-Leistung 850W übersteigt oder der Akku fast voll ist.
* **NACHT-MODUS:** Akku-Schutz und Verbrauchs-Optimierung durch einen negativen Offset (-30W), um Netzbezug durch Regelschwankungen abzufangen.

---

## 🛠️ Installation & Setup

1.  **MQTT Freischaltung:** Muss über den Marstek-Support (App-Feedback) beantragt werden.
2.  **Bridge-Setup:** Installation des `hm2mqtt` Add-ons.
3.  **Emulation:** AstraMeter installieren und das System-Template als Sensor-Quelle nutzen.
4.  **Integration:** Der emulierte Zähler wird vom Marstek-System wie ein physischer Shelly-Zähler behandelt.

---

## 🤝 Credits & Mitarbeit

Optimierungsvorschläge sind jederzeit willkommen! Eröffne gerne ein Issue oder einen Pull Request.

* **MQTT-Brücke:** tomquist/hm2mqtt
* **Emulations-Basis:** tomquist/AstraMeter
* **Konzept & Entwicklung:** Dom0609

---
**Dokumentationsstand:** April 2026


