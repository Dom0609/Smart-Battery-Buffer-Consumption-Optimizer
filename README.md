# Smart Battery Buffer & Consumption Optimizer (v2.0 - 2026 Edition)

![Status](https://img.shields.io/badge/Status-Stable_Release-green)
![Hardware](https://img.shields.io/badge/Hardware-Marstek_B2500_%7C_Hame_HMJ--2-blue)
![Platform](https://img.shields.io/badge/Platform-Home_Assistant-orange)

Dieses Repository bietet eine hochstabile Steuerung für Marstek/Hame Speichersysteme. Nach der Entwicklung der manuellen MQTT-Steuerung (v1.0.8) wurde das System auf eine **Zähler-Emulation via AstraMeter** umgestellt, um Hardware-Latenzen zu eliminieren und den 1680W PV-Ausbau perfekt zu managen.

---

## 📢 DER STRATEGIEWECHSEL (April 2026)

Nach intensiven Langzeittests hat sich gezeigt: Die direkte Steuerung per Watt-Befehlen (v1.0.8) ist anfällig für den "500W-Fallback-Bug" und WLAN-Latenzen. 

**Die neue Gold-Standard-Lösung:** Wir nutzen weiterhin die MQTT-Anbindung (`hm2mqtt`), aber anstatt den Speicher aktiv zu "schieben", emulieren wir einen **Shelly Pro 3EM** (via AstraMeter Add-on). Der Marstek nutzt so seinen internen, nativen Regelalgorithmus, was die Stabilität massiv erhöht.

---

## 🏗️ Das Setup (Hybrid-Ready)

Mein System ist auf maximale Effizienz ausgelegt:
* **Speicher:** Marstek B2500 / Hame HMJ-2.
* **PV-Leistung:** 1680Wp (massive Überbelegung für Schlechtwetter-Performance).
* **Zusatz-WR:** Ein Deye Wechselrichter läuft parallel an PV1.
* **Messung:** Shelly Pro 3EM am Hausanschluss.

---

## 💡 Die Logik: Intelligente Offsets (v2.0)

Damit das System bei 1680W PV nicht "nervös" wird und nachts keinen Strom verschenkt, nutze ich ein spezialisiertes Home Assistant Template für den AstraMeter-Sensor:

```yaml
# Template für den emulierten Zählerwert
- sensor:
    - name: "Marstek Steuerung Input"
      unique_id: marstek_control_v2
      unit_of_measurement: "W"
      state: >
        {% set echt_netz = states('sensor.netz_gesamtbezug') | float(0) %}
        {% set pv_power = states('sensor.hame_energy_hma_1_18cedf9836c6_total_input_power') | float(0) %}
        {% set soc = states('sensor.hame_energy_hma_1_18cedf9836c6_host_battery_soc') | float(0) %}
        {% set akku_fluss = states('sensor.hame_energy_hma_1_18cedf9836c6_battery_power') | float(0) %}

        {% if pv_power > 850 or soc > 97 or akku_fluss > 10 %}
          {# TAG-MODUS: Aggressives Laden & Überschuss sichern #}
          {{ echt_netz + 100 }}
        {% else %}
          {# NACHT-MODUS: Akku-Schutz & Verbrauchs-Optimierung #}
          {{ echt_netz - 30 }}
        {% endif %}

🏗️ Hardware-Szenario: Hybrid-Ready
Das System von Dom0609 ist für den parallelen Einsatz optimiert:

Hybrid-Setup: Speicher parallel zu einem zweiten, ungeregelten Wechselrichter (Deye auf PV1).

Stand-alone: Hochstabile Einzelsteuerung für den Speicher.

PV-Power: 1680Wp Gesamteingang am Marstek für maximale Schlechtwetter-Performance.

🛠️ Installation & Setup
MQTT Freischaltung: Muss über den Marstek-Support (App-Feedback) beantragt werden.

Bridge-Setup: Installation des hm2mqtt Add-ons.

Emulation: AstraMeter installieren und das obige Template als Sensor-Quelle nutzen.

🤝 Credits & Mitarbeit
Optimierungsvorschläge sind jederzeit willkommen! Eröffne gerne ein Issue oder einen Pull Request.

MQTT-Brücke: tomquist/hm2mqtt

Emulations-Basis: tomquist/AstraMeter

Konzept & Entwicklung: Dom0609

Dokumentationsstand: April 2026
