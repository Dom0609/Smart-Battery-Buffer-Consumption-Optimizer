# Smart Battery Buffer & Consumption Optimizer (v2.1)

Dieses Repository bietet eine hochstabile und präzise Steuerung für **Marstek B2500 / Hame HMJ-2** Speichersysteme in Home Assistant. Es ist speziell darauf optimiert, Hand in Hand mit zusätzlichen Wechselrichtern (z. B. **Deye**) zu arbeiten, um eine perfekte Nulleinspeisung zu erreichen.

## 📢 Der "BMS-Fix": Die Hardware-Basis (April 2026)
Der wichtigste Durchbruch in der Entwicklung war die Erkenntnis, dass viele Regelungsprobleme (MPPT-Abschaltungen, "Ruckeln", Ignorieren von Befehlen) auf ein dekalibriertes BMS zurückzuführen sind. 

**Bevor die Automation genutzt wird, wird dieser Kalibrierungsweg dringend empfohlen:**
1. **Volladen:** Den Akku auf 100 % laden.
2. **Reset:** In der Marstek-App den **Hardware-Reset** auslösen (während der Akku bei 100 % steht).
3. **Entladen:** Den Akku kontrolliert auf **0 %** entladen lassen.
4. **Laden:** Den Akku wieder auf **100 %** aufladen, **ohne dabei Energie zu entnehmen** (kein aktiver Output).

Erst durch diesen Reset-Zyklus kennt das BMS die Kapazitätsgrenzen wieder korrekt, was die Voraussetzung für eine stabile MQTT-Steuerung ist.

## 🚀 Projekt-Evolution
* **v1.0.8:** Erste stabile MQTT-Direktsteuerung.
* **v2.0 (AstraMeter):** Strategiewechsel zur Zähler-Emulation (Shelly Pro 3EM Fake).
* **v2.1 "The Precision Update":** Rückkehr zur Direktsteuerung. Dank Software-Dämpfung und der BMS-Kalibrierung bietet dieser Weg nun die höchste Präzision und Stabilität.

## 💡 Warum v2.1 (Automation v1.1)?
Im Gegensatz zur "Blackbox" der internen Marstek-Logik erlaubt diese Automation volle Kontrolle:
* **Gezielte Dämpfung:** Ein P-Regler (Faktor 0.7) verhindert das Aufschwingen des Systems bei schnellen Lastwechseln.
* **80W Deadzone (Totzone):** Das System bleibt bei kleinen Schwankungen ruhig. Dies schont die Hardware, entlastet das WLAN und sorgt für ein sauberes Schriftbild im Dashboard.
* **Anti-Shutdown Logik:** Bei einem SOC > 98 % erzwingt das System einen kleinen Bypass-Strom (-150W Offset), um die MPPT-Eingänge aktiv zu halten und ein Abschalten der PV-Produktion bei vollem Akku zu verhindern.
* **Smart State-Check:** Es werden nur Werte an den MQTT-Broker gesendet, wenn eine tatsächliche Änderung vorliegt.

## 🏗️ Das Setup
* **Speicher:** Marstek B2500 / Hame HMJ-2.
* **Solar:** 1680Wp (Massive Überbelegung für starke Performance bei Bewölkung).
* **Hybrid-Betrieb:** Ein Deye Wechselrichter läuft parallel an PV1 zur Deckung der Grundlast.
* **Messung:** Shelly Pro 3EM am Netzübergangspunk.

## 🛠️ Installation & Konfiguration
1.  **MQTT-Freischaltung:** Über den Marstek-Support (App-Feedback) beantragen ("Local MQTT enable").
2.  **Bridge:** `hm2mqtt` Add-on von *tomquist* installieren.
3.  **Automation:** Die `Marstek_v1.1.yaml` importieren und deine Entitäten anpassen.


## ❤️ Credits
Ein riesiges Dankeschön geht an **tomquist** für seine Arbeit an `hm2mqtt` und `AstraMeter`. Dieses Projekt baut auf seinem Fundament auf und ergänzt es um eine spezialisierte Regelungslogik.

**Dokumentationsstand:** 27. April 2026  
**Entwickler:** Dom0609

