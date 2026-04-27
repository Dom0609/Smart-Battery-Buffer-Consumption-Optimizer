# 🔋 Smart Battery Buffer & Consumption Optimizer (v2.1)

Dieses Repository bietet eine stabile Steuerung für **Marstek B2500 / Hame HMJ-2** Speichersysteme in Home Assistant. Optimiert für Hybrid-Systeme mit paralleler Einspeisung (z.B. durch einen zusätzlichen Deye-Wechselrichter).

---

### 🛠️ Der Weg zu v2.1 (Learning by Doing)
Dieses Projekt war ein ziemlicher Prozess. Von der ersten MQTT-Steuerung über Versuche mit Zähler-Emulationen bis hin zur jetzigen Version war es ein holpriger Weg mit vielen Korrekturen und Frustmomenten. 

Am Ende stellte sich heraus: Die stabilste Software bringt wenig, wenn das BMS der Hardware nicht sauber kalibriert ist. Die v2.1 kombiniert nun die Hardware-Erkenntnisse (BMS-Fix) mit einer gedämpften Software-Regelung, die auch bei Lastwechseln Ruhe ins System bringt.

---

## 📢 DER "BMS-FIX": Die Hardware-Basis (April 2026) 🛠️
Ein dekalibriertes BMS war bei meinen Tests die Hauptursache für MPPT-Abschaltungen und instabile Regelungen. Bevor die Automation genutzt wird, empfehle ich dringend diesen Kalibrierungsweg:

1. 📈 **Volladen:** Den Akku auf 100% laden.
2. 🔄 **Reset:** Hardware-Reset in der Marstek-App bei 100% SOC auslösen.
3. 📉 **Entladen:** Kontrolliert auf 0% entladen lassen (idealerweise ohne extreme Lastspitzen).
4. 🔌 **Laden:** Ohne Entnahme wieder auf 100% laden lassen.

---

## 🚀 PROJEKT-EVOLUTION
* **v1.0.8** ➔ Erste MQTT-Direktsteuerung.
* **v2.0** ➔ Versuch der Zähler-Emulation via AstraMeter.
* **v2.1** ➔ **"The Precision Update"**: Rückkehr zur Direktsteuerung nach erfolgreichem BMS-Fix. Höchste Stabilität durch Software-Dämpfung.

---

## 💡 WARUM v2.1 (v1.1 AUTOMATION)?
* 🎯 **Gezielte Dämpfung:** Ein P-Regler (Faktor 0.7) verhindert das Aufschwingen des Systems.
* 💤 **80W Deadzone:** Hält das System bei kleinen Schwankungen ruhig und schont die Hardware.
* ⚡ **Anti-Shutdown Logik:** Bei hohem SOC wird die Regelung stabilisiert, um MPPT-Tracker aktiv zu halten.
* 📡 **Smart State-Check:** Sendet Befehle nur bei tatsächlicher Wertänderung (schont WLAN/MQTT-Broker).

---

## 🏗️ DAS SETUP
* **Speicher:** Marstek B2500 / Hame HMJ-2
* **Solar:** 1680Wp (Massive Überbelegung für starke Performance auch bei Bewölkung)
* **Wechselrichter 1:** Deye (Parallelbetrieb zur Grundlastdeckung)
* **Wechselrichter 2:** Marstek MI800 (Direkt am B2500 Speicher)
* **Messung:** Shelly Pro 3EM am zentralen Netzübergang

---

## 🤝 MITARBEIT & FEEDBACK
Das System läuft bei mir jetzt hervorragend, aber jedes Hausnetz und jedes Setup ist individuell. 
* Wenn dir Fehler auffallen oder du eine bessere Idee für die Regel-Logik hast: **Immer her damit!**
* Eröffne gerne ein **Issue** oder beteilige dich an den **Discussions**.
* Besonders Optimierungen für Reaktionszeiten oder smartere Offsets sind jederzeit willkommen.

---

## 🛠️ INSTALLATION & SETUP
1. **MQTT:** Local MQTT via Marstek-Support freischalten lassen.
2. **Bridge:** [hm2mqtt](https://github.com/tomquist/hm2mqtt) Add-on installieren.
3. **Automation:** Die Datei `/homeassistant/Marstek_Direct_Control_v1.1.yaml` importieren und Entitäten anpassen.
4. **Doku:** Beachte die spezifische [YAML-Dokumentation im Unterordner](/homeassistant/).

---

## ❤️ CREDITS
Ein riesiges Dankeschön geht an **tomquist** für die herausragende Arbeit an `hm2mqtt` und `AstraMeter`. Dieses Projekt baut auf seinem Fundament auf.

**📅 Stand:** 27. April 2026  
**👨‍💻 Entwickler:** Dom0609
