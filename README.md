# Smart Battery Buffer & Consumption Optimizer (v2.0)

![Release](https://img.shields.io/badge/Release-v2.0--2026-gold?style=for-the-badge)
![Hardware](https://img.shields.io/badge/Hardware-Marstek%20|%20Hame-blue?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Home%20Assistant-integrated?style=for-the-badge)

Dieses Repository bietet eine hochstabile Steuerung für **Marstek B2500 / Hame HMJ-2** Speichersysteme. Nach intensiver Arbeit mit der manuellen MQTT-Steuerung (v1.0.8) wurde das System auf **Zähler-Emulation via AstraMeter** umgestellt.

---

## 📢 DER STRATEGIEWECHSEL (April 2026)

Nach Langzeittests steht fest: Die direkte Steuerung per Watt-Befehlen via `hm2mqtt` stößt an die Grenzen der **internen Marstek-Logik**. 

**Das Problem:** Bei direkter Steuerung neigt die Hardware dazu, bei vollem Akku die PV-Eingänge komplett abzuschalten, anstatt den Überschuss intelligent durchzureichen. Zudem führen WLAN-Latenzen oft zu einer verzögerten oder "ruckeligen" Regelung.

**Die Lösung:** Wir emulieren einen **Shelly Pro 3EM** (via AstraMeter Add-on). Anstatt dem Speicher starre Werte vorzugeben, liefern wir ihm nur die Verbrauchsdaten. Der Marstek nutzt so seinen **nativen Regelalgorithmus**. Das Ergebnis ist eine butterweiche Leistungsabgabe und ein stabiles Management der PV-Eingänge.

---

## ⚠️ Lessons Learned: Warum dieser Weg?

Ich habe dieses Repository erstellt, um anderen den Frust zu ersparen, den ich beim Versuch der direkten Hardware-Manipulation hatte:

* **Eingangs-Abschaltung:** Die interne Logik schaltet bei vollem Akku oft die Eingänge ab; externe Watt-Befehle werden in diesem Zustand schlichtweg nicht mehr verarbeitet. 
* **Regel-Harmonie:** Durch die Emulation arbeitet man *mit* der Hardware, nicht gegen sie. Der Speicher "denkt", er kommuniziert mit einem echten Shelly, was die stabilste Betriebsart darstellt.
* **Effizienz:** Die 1680Wp werden durch den nativen Algorithmus viel sauberer verarbeitet, besonders im Übergang von Akku-Laden zu Netzeinspeisung.

---

## 🏗️ Das Setup (Hybrid-Ready)

* **Speicher:** Marstek B2500 / Hame HMJ-2.
* **PV-Leistung:** 1680Wp (Massive Überbelegung für starke Performance bei Bewölkung).
* **Zusatz-WR:** Ein Deye Wechselrichter läuft parallel an PV1.
* **Messung:** Shelly Pro 3EM (Datenquelle für die Emulation).

---

## 💡 Die Logik: Intelligente Offsets (v2.0)

Das System nutzt ein spezialisiertes Home Assistant Template, um das Verhalten des Speichers zu optimieren:
* **TAG-MODUS (+100W Offset):** Priorisiert das Laden bei hoher PV-Leistung (>850W) oder hohem SoC.
* **NACHT-MODUS (-30W Offset):** Bildet einen Puffer, um Netzbezug bei Lastspitzen zu minimieren.

> 📄 **Konfiguration:** Das passende Template findest du hier: [Marstek_template.yaml](./Marstek_template.yaml)

---

## 🛠️ Installation & Setup

1. **MQTT Freischaltung:** Über den Marstek-Support (App-Feedback) beantragen ("Local MQTT enable").
2. **Bridge-Setup:** [hm2mqtt](https://github.com/tomquist/hm2mqtt) Add-on installieren.
3. **Emulation:** [AstraMeter](https://github.com/tomquist/AstraMeter) installieren und das Template als Quelle wählen.
4. **Kopplung:** In der Marstek App den emulierten Shelly als Smart Meter hinzufügen.

---

## 🤝 Credits & Mitarbeit

* **MQTT-Brücke:** [tomquist/hm2mqtt](https://github.com/tomquist/hm2mqtt)
* **Emulations-Basis:** [tomquist/AstraMeter](https://github.com/tomquist/AstraMeter)
* **Konzept & Entwicklung:** Dom0609

---
**Dokumentationsstand:** April 2026

