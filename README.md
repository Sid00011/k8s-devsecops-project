# k8s-devsecops-project
# Multi-Node Secured Cloud Infrastructure with Kubernetes & DevSecOps Pipeline

**Auteur :** Sidali  
**Sujet :** Automatisation, Sécurisation et Résilience d'une infrastructure Micro-services.

---

## I. RÉSUMÉ EXÉCUTIF
Ce projet expose la conception et le déploiement d'une infrastructure Cloud-Native hybride, intégrant les principes de l'**Infrastructure-as-Code (IaC)** et du **Zero-Trust**. L'objectif principal était de construire un pipeline CI/CD automatisé capable de scanner, bâtir et déployer une application sécurisée sur un cluster Kubernetes, tout en garantissant une gestion dynamique des secrets via **Hashicorp Vault**.

---

## II. ÉCOSYSTÈME TECHNIQUE & AUTOMATISATION

### 1. Provisioning via Ansible
L'intégralité de la configuration des nœuds (Master et Workers) a été orchestrée via Ansible. Cette approche garantit l'idempotence de l'infrastructure.

* **Validation :** Le succès du déploiement est confirmé par le `PLAY RECAP` ci-dessous, affichant une synchronisation parfaite des états sur les trois instances.

<img width="1918" height="1011" alt="proofX1" src="https://github.com/user-attachments/assets/85ad43eb-71dd-44fb-8204-b3ad3249fa7d" />

*Figure 1 : Extrait du succès de l'exécution du Playbook Ansible (Play Recap).*

* **Topologie :** Un cluster multi-nœuds (1 Master, 2 Workers) sous Ubuntu 24.04 LTS. L'état `Ready` des nœuds confirme la stabilité de la couche OS et du runtime container.



<img width="1875" height="831" alt="proofX3" src="https://github.com/user-attachments/assets/6198c246-3cee-4f5d-9a5d-9a15733f0da4" />
*Figure 2 : État opérationnel des nœuds du cluster Kubernetes via kubectl.*

---

## III. PIPELINE CI/CD ET POSTURE DE SÉCURITÉ

### 1. Orchestration GitHub Actions
Le workflow implémenté suit une logique de promotion d'image rigoureuse. Chaque étape est verrouillée par le succès de la précédente.

<img width="1918" height="861" alt="proofX5" src="https://github.com/user-attachments/assets/22277c23-64b2-4ac6-94bc-30774951ee3d" />
*Figure 3 : Dashboard GitHub Actions montrant le succès complet du pipeline.*

* **Security Scanning (Trivy) :** Avant le build, l'image de base est scannée. Le rapport Trivy confirme un score de vulnérabilité nul (`0: Clean`), autorisant le passage en production.

<img width="1418" height="866" alt="proofX7" src="https://github.com/user-attachments/assets/d4da2fa8-2345-4bcb-955c-70717b434c0f" />
*Figure 4 : Rapport de scan Trivy intégré au pipeline (Validation de l'image alpine).*

### 2. Déploiement Déclaratif
Le déploiement automatisé utilise des manifestes YAML pour définir l'état désiré des ressources.

<img width="480" height="156" alt="proofX6" src="https://github.com/user-attachments/assets/2ef76fe4-53da-412a-9ffc-02f9b51b260c" />
*Figure 5 : Logs de déploiement confirmant la création du Deployment et du Service.*

---

## IV. OBSERVABILITÉ ET RÉSILIENCE (SELF-HEALING)

### 1. Analyse de l'Instabilité Réseau (VXLAN)
Lors de la phase de monitoring, un phénomène de `CrashLoopBackOff` a été identifié. L'analyse a révélé un overhead CPU significatif causé par l'encapsulation VXLAN du plugin CNI Flannel.

* **Preuve de Résilience :** Le rapport de statut montre que Kubernetes a activé ses mécanismes de **Self-Healing**. Malgré les redémarrages, le cluster maintient la cohérence du service.

<img width="1473" height="742" alt="proof4" src="https://github.com/user-attachments/assets/90e04fa7-c3fa-4597-a6fe-d595d3c223f0" />
*Figure 6 : Analyse des Restarts confirmant l'auto-réparation par les Liveness Probes.*

---

## V. SÉCURISATION DES DONNÉES : HASHICORP VAULT

### 1. Injection Dynamique de Secrets
Pour respecter le principe de moindre privilège, aucun secret n'est stocké en clair. L'authentification utilise les `ServiceAccounts` Kubernetes pour interroger Vault.

* **Validation API :** Une requête directe depuis le pod applicatif démontre la récupération réussie des identifiants (`api_key`, `db_password`).


<img width="1805" height="450" alt="proofX8" src="https://github.com/user-attachments/assets/24ca929d-c5c5-4d9f-a35b-63299c45e40c" />
*Figure 7 : Extraction du secret Vault en temps réel depuis le container.*

---

## CONCLUSION
Ce projet démontre une maîtrise complète de la chaîne DevSecOps. La capacité à diagnostiquer des problématiques d'infrastructure tout en maintenant un pipeline sécurisé atteste d'une maturité technique avancée. L'infrastructure est non seulement opérationnelle, mais elle est surtout résiliente et auditable.
