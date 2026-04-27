# 🔋 Smart Battery Buffer & Consumption Optimizer (v2.1)

Dieses Repository bietet eine hochstabile und präzise Steuerung für **Marstek B2500 / Hame HMJ-2** Speichersysteme in Home Assistant. Optimiert für Hybrid-Systeme mit paralleler Einspeisung (z.B. Deye).

---

## 📢 DER "BMS-FIX": Die Hardware-Basis (April 2026) 🛠️
Ein dekalibriertes BMS war die Hauptursache für MPPT-Abschaltungen und instabile Regelungen. 

**Empfohlener Kalibrierungsweg vor Start der Automation:**
1. 📈 **Volladen:** Den Akku auf 100% laden.
2. 🔄 **Reset:** Hardware-Reset in der Marstek-App bei 100% SOC auslösen.
3. 📉 **Entladen:** Kontrolliert auf 0% entladen lassen (idealerweise ohne extreme Lastspitzen).
4. 🔌 **Laden:** Ohne Entnahme wieder auf 100% laden lassen.

---

## 🚀 PROJEKT-EVOLUTION
* **v1.0.8** ➔ Erste stabile MQTT-Direktsteuerung.
* **v2.0** ➔ Strategiewechsel: Zähler-Emulation via AstraMeter.
* **v2.1** ➔ **"The Precision Update"**: Rückkehr zur Direktsteuerung nach erfolgreichem BMS-Fix. Höchste Stabilität durch Software-Intelligenz.

---

## 💡 WARUM v2.1 (v1.1 AUTOMATION)?
* 🎯 **Gezielte Dämpfung:** P-Regler (Faktor 0.7) verhindert das Aufschwingen des Systems.
* 💤 **80W Deadzone:** Hält das System bei kleinen Schwankungen ruhig und schont die Hardware.
* ⚡ **Anti-Shutdown Logik:** Bei SOC > 98% wird ein kleiner Bypass-Strom erzwungen, um die MPPT-Tracker aktiv zu halten.
* 📡 **Smart State-Check:** Minimiert WLAN-Traffic durch Senden nur bei tatsächlicher Wertänderung.

---

## 🏗️ DAS SETUP
* **Speicher:** Marstek B2500 / Hame HMJ-2
* **Solar:** 1680Wp (Massive Überbelegung für starke Performance auch bei Bewölkung)
* **Wechselrichter 1:** Deye (Parallelbetrieb zur Grundlastdeckung)
* **Wechselrichter 2:** Marstek MI800 (Direkt am B2500 Speicher)
* **Messung:** Shelly Pro 3EM am zentralen Netzübergang

---

## 🛠️ INSTALLATION & SETUP
1.  **MQTT:** Local MQTT via Marstek-Support freischalten lassen.
2.  **Bridge:** [hm2mqtt](https://github.com/tomquist/hm2mqtt) Add-on installieren.
3.  **Automation:** `Marstek_v1.1.yaml` importieren und Entitäten anpassen.
4.  **Dashboard:** Empfohlen wird die **Lovelace Horizon Card** für den Sonnenverlauf.

---

## ❤️ CREDITS
Ein riesiges Dankeschön geht an **tomquist** für die herausragende Arbeit an `hm2mqtt` und `AstraMeter`. Dieses Projekt baut auf seinem Fundament auf.

**📅 Stand:** 27. April 2026  
**👨‍💻 Entwickler:** Dom0609
