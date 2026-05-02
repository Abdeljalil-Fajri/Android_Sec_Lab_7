# Lab 7 : Analyse Dynamique Mobile avec MobSF et DIVA

## Introduction
Ce projet détaille la mise en œuvre d'une plateforme d'analyse de sécurité pour applications Android en utilisant Mobile Security Framework (MobSF) et l'application vulnérable DIVA (Damn Insecure and Vulnerable App). L'objectif principal est de démontrer les capacités de l'analyse dynamique (runtime) pour identifier des vulnérabilités en temps réel, telles que les fuites de logs, le stockage non sécurisé et les faiblesses de communication réseau.

## Objectifs pédagogiques
La réalisation de ce laboratoire permet d'atteindre les objectifs suivants :
*   Comprendre les principes fondamentaux de l'analyse dynamique des applications Android.
*   Configurer un émulateur Android (AVD) optimisé pour l'analyse, sans les services Google Play.
*   Déployer MobSF via Docker pour assurer un environnement d'analyse isolé et reproductible.
*   Pratiquer l'instrumentation de code via Frida pour intercepter et modifier le comportement de l'application au repos et en exécution.

---

## Configuration de l'environnement de test

### 1. Création de l'émulateur (AVD)
La première étape consiste à créer un appareil virtuel Android via Android Studio. Il est impératif de sélectionner une image système "Google APIs" sans "Google Play" (recommandé : API 28 à 30) pour permettre l'accès root nécessaire à l'analyse dynamique. L'émulateur doit être nommé de manière explicite, par exemple `MobSF_DIVA_API_30`.

### 2. Initialisation de MobSF et Scripts AVD
Le framework MobSF est cloné depuis son dépôt officiel pour accéder aux scripts de configuration automatique. Le script `start_avd` est utilisé pour lancer l'émulateur avec les privilèges root activés et prêt pour l'instrumentation. La vérification de la connexion se fait via la commande `adb devices` pour identifier l'ID de l'émulateur (généralement `emulator-5554`).

### 3. Déploiement via Docker
MobSF est lancé à l'aide d'un conteneur Docker en spécifiant l'identifiant de l'analyseur pour lier le framework à l'émulateur actif. La commande utilisée expose le port 8000 et définit la variable d'environnement `MOBSF_ANALYZER_IDENTIFIER`.

---

## Méthodologie d'Analyse Dynamique

### Analyse de l'application DIVA
L'application DIVA est téléchargée sous forme de fichier APK et soumise à MobSF via l'interface "Upload & Analyze". Une fois l'analyse statique terminée, le module "Dynamic Analyzer" est activé pour lancer l'application dans l'émulateur instrumenté.

### Interface et Outils d'Analyse
L'interface de l'analyseur dynamique offre une suite d'outils critiques pour le chercheur en sécurité :
*   **Mirroring en temps réel** : Permet d'interagir avec l'application DIVA directement depuis le navigateur.
*   **Logcat Stream** : Affiche les flux de logs Android en direct pour détecter les informations sensibles (identifiants, secrets) fuyant via `Log.d()` ou `Log.e()`.
*   **Frida Code Editor** : Permet l'injection de scripts Java.perform pour manipuler les méthodes de l'application ou contourner des mécanismes de sécurité comme le SSL Pinning ou la détection du root.
*   **Shell Access** : Fournit un terminal root direct sur l'appareil pour inspecter le système de fichiers, notamment les bases de données SQLite et les fichiers XML de préférences partagées.

### Tests effectués sur DIVA
Le laboratoire se concentre sur l'exploration de 13 challenges vulnérables présents dans DIVA :
1.  **Insecure Logging** : Capture des données saisies par l'utilisateur via le flux Logcat.
2.  **Hardcoding Issues** : Identification de secrets codés en dur dans le code source ou les ressources.
3.  **Insecure Data Storage** : Analyse des fichiers créés par l'application dans son répertoire privé (/data/data/jakhar.aseem.diva).
4.  **Access Control Issues** : Tentative de lancement d'activités exportées non sécurisées via l'outil "Activity Tester".

---

## Synthèse des Outils du Menu de l'Analyseur Dynamique

| Outil | Fonctionnalité |
| :--- | :--- |
| **Stop Screen** | Interrompt la diffusion de l'écran de l'émulateur. |
| **Remove Root CA** | Nettoie les certificats de confiance installés pour l'interception HTTPS. |
| **Unset HTTP(S) Proxy** | Désactive la redirection du trafic réseau vers le proxy de MobSF. |
| **TLS/SSL Security Tester** | Analyse la robustesse de la validation des certificats par l'application. |
| **Logcat Stream** | Outil principal pour la détection de fuites d'informations en temps réel. |
| **Generate Report** | Consolide toutes les preuves (logs, captures d'écran, trafic) dans un rapport final. |

## Conclusion
Ce projet démontre l'efficacité de l'intégration entre MobSF et un environnement Android rooté pour l'audit de sécurité. Grâce à l'instrumentation Frida et au monitoring réseau, il est possible de cartographier précisément la surface d'attaque d'une application mobile et de documenter ses faiblesses structurelles.
