# 🛡️ ANAOS — Automated Network & Analysis Operations System

> **Automated SOC Deployment & Detection Coverage**  
> **Chapitre de Recherche & Implémentation Pratique** | **Année Académique :** 2025–2026  
> **Établissement :** ENSA Khouribga · Cybersecurity Research  
> **Encadré par :** Dr. Yassine MALEH  

---

## 👥 Auteurs du Projet

* **Ismail BAJJOU**
* **Ousmane ISSA ADAM**
* **Othmane NECHCHADI**
* **Yassine SARIH**
* **Akram ZERBANE**

---

## 📌 Résumé Exécutif

**ANAOS** est un centre d'opérations de sécurité (**SOC**) open-source, entièrement automatisé et basé sur une architecture réseau segmentée en 4 zones (WAN, DMZ, LAN1, LAN2). 

Le système intègre **Wazuh** comme moteur SIEM/XDR centralisé, **pfSense avec Suricata** pour la détection d'intrusions réseau, **Sysmon** et **Auditd** pour la télémesure hôte, ainsi qu'**Ansible** pour le déploiement automatisé (*Zero-Touch Provisioning*). Une interface sur mesure en Python (`anaos_gui.py`) assure le tri en temps réel, le calcul du **MTTD** (*Mean Time to Detect*) et le suivi du taux de faux positifs (**FPR**).

📄 **Le chapitre de recherche complet est disponible au format PDF :**  
👉 [`ANAOS-Automated-SOC.pdf`](./ANAOS-Automated-SOC.pdf)

---

## 📊 Résultats Expérimentaux & Métriques

L'évaluation du SOC a été réalisée face à 4 scénarios d'attaque réels issus de la matrice **MITRE ATT&CK Enterprise** :

| Métrique | Valeur Obtenue | Objectif Cible | Statut |
| :--- | :--- | :--- | :--- |
| **Rappel (Detection Rate)** | **100.0%** (4/4) | $\ge 80\%$ |  |
| **Taux de Faux Positifs (FPR)** | **0.0%** | $\le 5\%$ |  |
| **Temps Moyen de Détection (MTTD)** | **$\approx 0$ s** (Réseau) | $\le 60$ s |  |
| **Couverture MITRE ATT&CK** | **100%** (4/4) | $\ge 75\%$ |  |
| **Temps de Déploiement IaC** | **$\approx 12$ min** | $\le 15$ min |  |

---

## 🛡️ Scénarios d'Attaque Évalués (MITRE ATT&CK)

1. **Reconnaissance Active (`T1595.002`) :** Balayage réseau via Nmap (Détecté par Suricata + Règle Wazuh `100104`).
2. **Exploitation Web - SQLi (`T1190`) :** Injections SQL sur l'application OWASP Juice Shop (Détecté via Règle Wazuh `100116`).
3. **Attaque par Force Brute (`T1110`) :** Attaque par dictionnaire HTTP POST via Hydra (Détecté via Règle Wazuh `100115`).
4. **Exécution de Charge Utile - Squiblydoo (`T1218.010`) :** Exécution de binaire signé `regsvr32.exe` sous Windows (Détecté via Sysmon EID 1 + Règle Wazuh `100051`).

---

## 🏗️ Architecture Système

```text
 +------------------+        Trafic Réseau        +------------------------+
 | WAN (Kali Linux) | --------------------------> | pfSense + Suricata IDS |
 +------------------+                             +-----------+------------+
                                                              |
    +---------------------------------------------------------+---------------------------------------------------------+
    |                                                         |                                                         |
    v                                                         v                                                         v
 +------------------------+                +------------------------------------+                +----------------------+
 | DMZ: OWASP Juice Shop  |                | LAN1: Windows 10 & Ubuntu Endpoint |                | LAN2: Wazuh Server   |
 | (Wazuh Agent + Auditd) |                | (Sysmon v14 + Wazuh Agent)          |                | & ANAOS Dashboard    |
 +------------------------+                +------------------------------------+                +----------------------+
