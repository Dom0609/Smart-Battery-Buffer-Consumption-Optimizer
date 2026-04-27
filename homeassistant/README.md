# 📑 YAML-Dokumentation: Marstek_Direct_Control_v1.1

Diese Automation ist das Herzstück der v2.1-Steuerung. Sie nutzt einen gedämpften Regelalgorithmus, um Netzbezug und PV-Einspeisung in Einklang zu bringen.

---

## 🛠️ Konfigurations-Bereich (User-Variables)

In der YAML-Datei müssen folgende Entitäten im Block `variables` angepasst werden:

| Variable | Funktion |
| :--- | :--- |
| `regler_id` | Die Entität für den Marstek-Output (`number...output_value`). |
| `soc_sensor` | Der aktuelle Ladestand des Speichers in %. |
| `scene_sensor` | Erkennt Tag/Nacht (für unterschiedliche Zielwerte). |
| `netz_sensor` | Die Datenquelle deines Stromzählers (z.B. Shelly 3EM). |
| `freigabe_id` | Ein Helfer-Schalter (`input_boolean`) für den Automatik-Betrieb. |

---

## 🧠 Funktionsweise der v1.1 Logik

### 1. P-Regelung mit Dämpfungsfaktor (0.7)
Statt den Speicher sofort auf den vollen Korrekturwert zu zwingen, rechnet die Automation:
`raw_step = Fehler * 0.7`
* **Vorteil:** Das System nähert sich dem Zielwert sanft an. Dies verhindert das gefürchtete "Überschießen", wenn große Verbraucher (z.B. Herd) plötzlich ausschalten.

### 2. Die 80W Deadzone (Totzone)
Um die Hardware und das WLAN-Modul des Marstek zu schonen, greift die Regelung erst ein, wenn die Abweichung vom Zielwert mehr als **80 Watt** beträgt. 
* **Ergebnis:** Kleine Schwankungen werden ignoriert, was für eine extrem ruhige Kurve im Dashboard sorgt.

### 3. Dynamische Korrektur-Limits
Die Automation passt ihre "Geschwindigkeit" der Situation an:
* **Grob-Modus:** Bei Fehlern > 200W darf der Marstek mit bis zu **200W pro Schritt** regeln.
* **Fein-Modus:** Bei kleinen Abweichungen korrigiert er in vorsichtigen **40W-Schritten**.

### 4. Strategische Offsets
* **Tag-Modus (-100W):** Wir zielen auf eine leichte Einspeisung ab. Das schafft Puffer für den parallel laufenden **Deye-Wechselrichter**, damit dieser seine Energie immer priorisiert ins Hausnetz abgeben kann.
* **Nacht-Modus (40W):** Ein minimaler Netzbezug wird toleriert, um zu verhindern, dass der Speicher bei jedem kleinen Standby-Verbraucher anspringt.

---

## ⏱️ Zeitsteuerung & Intervall
Die Automation triggert fest alle **3 Sekunden**. In Kombination mit dem Dämpfungsfaktor ergibt das eine Reaktionszeit, die schnell genug für den Alltag ist, aber träge genug, um die Relais und den MQTT-Bus nicht zu überlasten.

## ⚠️ Sicherheit: SOC-Hysterese
Die Entladefreigabe nutzt eine Hysterese-Logik:
* **Stopp:** Wenn SOC < 15% (Schutz vor Tiefentladung).
* **Start:** Erst wieder ab SOC > 20% (Verhindert ständiges Ein-/Ausschalten an der Kapazitätsgrenze).

---

**Hinweis:** Diese Automation wurde für die Firmware-Stände von April 2026 optimiert und setzt ein via Hardware-Reset kalibriertes BMS voraus.
