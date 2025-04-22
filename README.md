# 🛡️ SOC Automation Lab – Home Lab Setup mit Wazuh, Shuffle & TheHive

Willkommen zu meinem SOC Automation Projekt, das ich als **vollautomatisiertes Home Lab** entwickelt habe. Das Ziel war, eine Umgebung zu bauen, die sowohl **SIEM**, **SOAR**, als auch **Case Management** integriert – ideal zur Simulation und Analyse echter Security Incidents (z. B. Mimikatz-Angriffe).

---
## 📌 Ziel des Projekts

Aufbau eines funktionierenden SOC-Workflows:

➡️ **Windows VM (Incident)** → **Wazuh (SIEM)** → **Shuffle (SOAR)** → **TheHive (Case Management)** → **Email an Analyst**

- Detection von Angriffen (z. B. Mimikatz)
- Automatisierte Weiterleitung und Eskalation
- Fallanlage in TheHive mit Kontext-Informationen
- Darstellung der Architektur via Diagramm (Draw.io)

---

## 🚀 Resultat

Ein funktionierender Security Workflow, der von der **Erkennung über Reaktion bis zum Fall-Management vollautomatisch** abläuft.

Das vollständige Architekturdiagramm ist hier abrufbar:

➡️ [[Draw.io Workflow](#)](https://app.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&dark=auto#Hhansleonp%2FSOC-Automation-Lab%2Fmain%2FWorkflow.drawio) *

![image](https://github.com/user-attachments/assets/6da447ea-0b42-49d4-9b79-ef8990486910)


---

## 📺 Tutorials & Referenzen

YouTube Playlist zum Projekt 🔄 *(Link hier einfügen)*

---

## 🔧 1. Windows 10 VM installieren (für Incident Simulation)

- Installiere **VirtualBox**, **Parallels**, **UTM** o. ä. auf deinem Mac oder PC
- Richte eine **Windows 10 VM** ein – sie wird später für Mimikatz-Simulationen verwendet

---

## ☁️ 2. Cloud Set-Up (Empfohlen: DigitalOcean)

- [DigitalOcean](https://www.digitalocean.com) bietet $200 kostenloses Guthaben
- **Empfehlung für Mac-User (M1, M2, M3):** Nutze Cloud-Ressourcen, um Kompatibilitätsprobleme mit lokalen VMs zu vermeiden  
  ⚠️ Parallels kann auf ARM-Macs zu unerwarteten Fehlern führen

  ![image](https://github.com/user-attachments/assets/4e4f1197-63ed-4512-8e4b-a49157a2ecc8)


---

### 📺 Windows Server auf DigitalOcean installieren oder Parallels nutzen (für Mac-Nutzer)

Da DigitalOcean kein Windows als Standard-OS anbietet, folge diesem Guide, um Windows Server trotzdem zu installieren:

▶️ Video: [Windows auf DigitalOcean installieren](https://www.youtube.com/watch?v=wtHBDEl5DIw)

<img width="1021" alt="image" src="https://github.com/user-attachments/assets/49007d82-f7aa-49fd-b48a-6ef7c918eb7f" />

---

### 🛠 Sysmon installieren (für Windows VM)

- Lade Sysmon herunter:  
  🔗 [Sysmon Download](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- Nutze Olaf Hartongs Konfiguration:  
  🔗 [sysmonconfig.xml](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml)
- Setup-Anleitung:  
  ▶️ Video (ab Minute 08:35): [Sysmon installieren & konfigurieren](https://www.youtube.com/watch?v=YxpUx0czgx4)

---

## 📈 3. Wazuh Setup (SIEM) in der Cloud

**Systemanforderungen:**
- RAM: **8 GB+**
- HDD: **50 GB+**
- OS: **Ubuntu 22.04 LTS**

### Installation:

```bash
apt-get update && apt-get upgrade -y
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

### Zugangsdaten extrahieren:

```bash
sudo tar -xvf wazuh-install-files.tar
```
<img width="1429" alt="image" src="https://github.com/user-attachments/assets/a1e34516-84eb-4ac9-ad65-2a100d076536" />

---

## 🐝 4. TheHive Setup (Case Management) in der Cloud

**Systemanforderungen:**
- RAM: **8 GB+ (empfohlen: 16 GB)**
- HDD: **50 GB+**
- OS: **Ubuntu 22.04 LTS**

### 4.1 Abhängigkeiten installieren:

```bash
apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl software-properties-common python3-pip lsb-release
```

### 4.2 Java installieren:

```bash
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" | sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
```

### 4.3 Cassandra installieren:

```bash
wget -qO - https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra
```

### 4.4 Elasticsearch installieren:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```

#### Optional: JVM-Konfiguration für Elasticsearch

```bash
sudo mkdir -p /etc/elasticsearch/jvm.options.d
sudo nano /etc/elasticsearch/jvm.options.d/custom.options
```

Inhalt der Datei:

```
-Dlog4j2.formatMsgNoLookups=true
-Xms2g
-Xmx2g
```

### 4.5 TheHive installieren:

```bash
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
sudo apt-get update
sudo apt-get install -y thehive
```

🖥 Zugriff über Port `9000`

**Standard-Zugangsdaten:**
- Benutzer: `admin@thehive.local`
- Passwort: `secret`

<img width="1438" alt="image" src="https://github.com/user-attachments/assets/c7cf4023-60b3-44f5-9415-b8ce8ea8a8b9" />

---

---

## 🧩 5. Windows VM: Agent & Mimikatz vorbereiten

- Installiere den **Wazuh-Agent** auf deiner Windows VM
- Konfiguriere **Sysmon** für erweiterte Event Logs
- Simuliere einen **Mimikatz-Angriff**
- Passe deine **ossec.conf** und **Rule Files** an, damit Mimikatz-Events im Dashboard sichtbar werden

<img width="918" alt="image" src="https://github.com/user-attachments/assets/2489bac7-a999-45d3-af79-bc9ed7d2c31f" />


![Pasted Graphic 1](https://github.com/user-attachments/assets/6d8c554f-323a-41c4-ac3d-ba3baea5b204)

---

## 🤖 6. Shuffle (SOAR) Integration

- Registrierung unter: [https://shuffler.io](https://shuffler.io)
- Erstellung eines **Webhook-basierten Workflows**
- Anbindung über `<integration>`-Tag in der `ossec.conf` auf dem Wazuh-Manager

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/972e3213-5a50-4b23-8e89-e674d63c93a0" />


---

## 🧠 7. TheHive als Case Management System anbinden

- Erstellung eines neuen **Cases via Shuffle bei jedem Mimikatz-Event**
- Optionale **Anreicherung mit VirusTotal Hash Lookup**

---

## 📬 8. Automatische Benachrichtigung

Der Workflow triggert bei erfolgreichem Alert:

- 📂 Case-Erstellung in **TheHive**
- ✉️ **Email an SOC Analyst**

---

## ✅ Nächste Schritte

- [ ] Wazuh & TheHive Dienste starten und testen
- [ ] Windows VM mit Sysmon ausstatten
- [ ] Shuffle für SOAR-Prozesse integrieren (wird im nächsten Abschnitt behandelt)

---

## 🛠️ Tools & Komponenten

| **Komponente**       | **Zweck**                            |
|----------------------|--------------------------------------|
| **Wazuh**            | SIEM – Detection Engine              |
| **Shuffle**          | SOAR – Automatisierung               |
| **TheHive**          | Case Management                      |
| **Windows 10 VM**    | Simulation von Angriffen             |
| **VirusTotal API**   | IOC Enrichment                       |




