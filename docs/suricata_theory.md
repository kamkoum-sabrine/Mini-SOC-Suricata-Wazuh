# Suricata – Théorie et principes de fonctionnement (NIDS)

## 1. Introduction

Dans le cadre du projet Mini-SOC, Suricata est utilisé comme **Network Intrusion Detection System (NIDS)**.  
Son rôle est d’analyser le trafic réseau en temps réel afin de détecter des activités malveillantes telles que :
- scans réseau,
- tentatives d’exploitation,
- attaques par déni de service,
- communications suspectes ou malveillantes.

Suricata est un moteur open source performant, capable de fonctionner à haut débit et largement utilisé dans les environnements SOC professionnels.

---

## 2. IDS / IPS : rappels fondamentaux

### 2.1 IDS (Intrusion Detection System)
Un IDS est un système de **détection** qui :
- surveille le trafic réseau ou l’activité d’un hôte,
- identifie des comportements suspects ou malveillants,
- génère des **alertes** sans bloquer automatiquement le trafic.

### 2.2 IPS (Intrusion Prevention System)
Un IPS est une extension de l’IDS qui :
- détecte les attaques,
- **bloque ou modifie** le trafic en temps réel (drop).

### 2.3 Différence IDS vs IPS

| Critère | IDS | IPS |
|------|----|----|
| Action | Détection | Détection + Blocage |
| Impact réseau | Aucun | Peut impacter le trafic |
| Mode courant | Passif | Actif |
| Usage Mini-SOC | Oui | Optionnel |

👉 Dans ce projet, **Suricata est utilisé en mode IDS (NIDS)**.

---

## 3. HIDS vs NIDS

### 3.1 NIDS (Network IDS)
- Analyse le **trafic réseau**
- Placé sur un point stratégique (SPAN / TAP)
- Détecte :
  - scans (Nmap)
  - exploits réseau
  - anomalies protocolaires

### 3.2 HIDS (Host IDS)
- Analyse l’**activité locale d’un hôte**
- Surveille :
  - logs système
  - connexions SSH
  - intégrité des fichiers

👉 **Suricata = NIDS**  
👉 **Wazuh = HIDS**

---

## 4. Présentation de Suricata

### 4.1 Qu’est-ce que Suricata ?
Suricata est un moteur de détection réseau open source développé par l’OISF (Open Information Security Foundation).

Il permet :
- l’inspection profonde des paquets (DPI),
- la détection basée sur signatures et anomalies,
- l’export de logs structurés (JSON).

### 4.2 Avantages principaux
- Multi-threading (performant)
- Support IPv4 / IPv6
- Compatible Elastic Stack / OpenSearch
- Large base de règles communautaires

---

## 5. Fonctionnement interne de Suricata

### 5.1 Capture du trafic
Suricata capture le trafic via :
- `af-packet`
- `pcap`
- interfaces en mode **promiscuous**

### 5.2 Analyse du trafic
Le trafic est analysé selon :
- protocoles (TCP, UDP, ICMP)
- couches applicatives (HTTP, DNS, FTP, SSH)

### 5.3 Moteur de règles
Suricata compare le trafic à un ensemble de règles définissant :
- des signatures connues,
- des comportements suspects.

---

## 6. Types de détection dans Suricata

### 6.1 Détection par signatures
- Basée sur des patterns connus
- Exemple :
  - scan Nmap
  - exploit Metasploit

### 6.2 Détection comportementale
- Analyse du comportement réseau
- Exemple :
  - trop de connexions en peu de temps
  - ports inhabituels

---

## 7. Actions possibles dans Suricata

Chaque règle peut déclencher une action :

| Action | Description |
|----|----|
| alert | Génère une alerte |
| drop | Bloque le paquet (mode IPS) |
| pass | Ignore le trafic |
| reject | Rejette avec réponse |

👉 Dans ce projet : **action = alert**

---

## 8. Structure d’une règle Suricata

Exemple :
```text
alert tcp any any -> any 22 (msg:"SSH Brute Force Attempt"; sid:1000001; rev:1;)
