# 📑 YAML-Dokumentation: Marstek_Direct_Control_v1.1

Diese Automation ist das Herzstück der v2.1-Steuerung. Sie nutzt einen gedämpften Regelalgorithmus, um Netzbezug und PV-Einspeisung in Einklang zu bringen.

---

## 🔧 Benötigte Helfer (Voraussetzung)

Bevor die Automation importiert wird, müssen folgende **Helfer** in Home Assistant angelegt werden (*Einstellungen -> Geräte & Dienste -> Helfer -> Helfer erstellen -> Schalter*):

| Entität | Name | Funktion |
| :--- | :--- | :--- |
| `input_boolean.marstek_hand_modus` | Marstek Hand-Modus | **AN:** Pausiert die Automatik (für Wartung/Handbetrieb). |
| `input_boolean.marstek_entlade_freigabe` | Marstek Entladefreigabe | Wird von der Logik via SOC gesteuert (Hysterese). |

---

## 🛠️ Konfigurations-Bereich (User-Variables)

In der YAML-Datei müssen im Block `variables` die folgenden Entitäten an dein System angepasst werden:

| Variable | Funktion |
| :--- | :--- |
| `regler_id` | Die Entität für den Marstek-Output (`number...output_value`). |
| `soc_sensor` | Der aktuelle Ladestand des Speichers in %. |
| `scene_sensor` | Erkennt Tageszeit/Lichtstatus (für Zielwert-Offset). |
| `netz_sensor` | Die Datenquelle deines Stromzählers (z.B. Shelly 3EM). |
| `freigabe_id` | Verweist auf den oben erstellten Helfer `marstek_entlade_freigabe`. |

---

## 🧠 Funktionsweise der v1.1 Logik

### 1. P-Regelung mit Dämpfungsfaktor (0.7)
Statt den Speicher sofort auf den vollen Korrekturwert zu zwingen, rechnet die Automation:
`raw_step = Fehler * 0.7`
* **Vorteil:** Das System nähert sich dem Zielwert sanft an. Dies verhindert das gefürchtete "Überschießen" und Pendeln der Regelung bei schnellen Lastwechseln (z.B. wenn der Wasserkocher ausschaltet).

### 2. Die 80W Deadzone (Totzone)
Um die Hardware und das WLAN-Modul des Marstek zu schonen, greift die Regelung erst ein, wenn die Abweichung vom Zielwert mehr als **80 Watt** beträgt. 
* **Ergebnis:** Kleine Schwankungen werden ignoriert, was für eine extrem ruhige Kurve im Dashboard sorgt.

### 3. Dynamische Korrektur-Limits
Die Automation passt ihre Korrekturgeschwindigkeit automatisch an die Last an:
* **Grob-Modus:** Bei Fehlern > 200W darf der Marstek mit bis zu **200W pro Schritt** regeln.
* **Fein-Modus:** Bei kleinen Abweichungen korrigiert er in vorsichtigen **40W-Schritten**.

### 4. Strategische Offsets (Hybrid-Optimierung)
* **Tag-Modus (-100W):** Zielwert ist eine leichte Einspeisung. Dies schafft "Platz" für parallel arbeitende Wechselrichter (z.B. **Deye**), damit deren Energie priorisiert genutzt wird.
* **Nacht-Modus (40W):** Ein minimaler Netzbezug wird toleriert, um unnötige Zyklen bei Kleinstverbrauchern zu vermeiden.

---

## ⏱️ Zeitsteuerung & Intervall
Die Automation triggert fest alle **3 Sekunden**. In Kombination mit dem Dämpfungsfaktor ergibt das eine Reaktionszeit, die schnell genug für den Alltag ist, aber träge genug, um die Hardware und den MQTT-Bus nicht zu überlasten.

## ⚠️ Sicherheit: SOC-Hysterese
Die Entladefreigabe schützt den Akku vor schnellen Ein-/Ausschaltzyklen an der Kapazitätsgrenze:
* **Stopp:** Wenn SOC < 15% (Schutz vor Tiefentladung).
* **Start:** Erst wieder ab SOC > 20% (Hysterese-Puffer).

---

**Hinweis:** Diese Automation wurde für die Firmware-Stände von April 2026 optimiert und setzt ein via Hardware-Reset kalibriertes BMS voraus.
