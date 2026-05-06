# LAB-9 : Analyse de surface d'attaque Android avec Drozer - Audit défensif

<img width="728" height="191" alt="image" src="https://github.com/user-attachments/assets/71413f28-8968-4fb0-a4f3-0e905b79fa60" />

<img width="1049" height="118" alt="image" src="https://github.com/user-attachments/assets/228f0eb3-8f99-4c69-a517-85a79fd5f880" />

<img width="1045" height="148" alt="image" src="https://github.com/user-attachments/assets/363df57e-0070-4d4a-839f-5bd33702fa81" />

<img width="353" height="756" alt="image" src="https://github.com/user-attachments/assets/96d99470-e6d6-4055-9b71-a26fddc74154" />

<img width="1082" height="434" alt="image" src="https://github.com/user-attachments/assets/73ac990c-cc08-4778-8782-959041c6988a" />

<img width="942" height="476" alt="image" src="https://github.com/user-attachments/assets/73e3974d-0bf7-4f8c-9076-ef28117664ad" />

<img width="1021" height="640" alt="image" src="https://github.com/user-attachments/assets/bdd1fa6e-a521-4eeb-be6b-6fa2b503f421" />

<img width="755" height="504" alt="image" src="https://github.com/user-attachments/assets/aa5959f4-314d-42c5-bc47-dcae8ad6286d" />

<img width="712" height="330" alt="image" src="https://github.com/user-attachments/assets/2a99704b-a4e2-4297-a16e-491f303b9af5" />

<img width="1144" height="82" alt="image" src="https://github.com/user-attachments/assets/4aa12992-5fcf-4e2b-a9aa-704cea9581a3" />

<img width="578" height="265" alt="image" src="https://github.com/user-attachments/assets/6cc5f88b-fbb5-4b81-9b3a-304e399d98c9" />

## Introduction
Ce dépôt contient les résultats d'un audit de sécurité dynamique réalisé sur l'application Android **Sieve** (`com.withsecure.example.sieve`). L'objectif principal est d'identifier et d'analyser la surface d'attaque IPC (Inter-Process Communication) en utilisant le framework **Drozer**. Ce travail s'inscrit dans une démarche d'audit défensif visant à sécuriser les composants Android contre les accès non autorisés et les fuites de données.

## Objectifs
*   Énumérer les composants exposés (Activities, Services, Receivers, Providers).
*   Évaluer la robustesse des permissions configurées dans le `AndroidManifest.xml`.
*   Identifier les vulnérabilités de type SQL Injection et Directory Traversal dans les Content Providers.
*   Documenter les risques et proposer des mesures de remédiation conformes aux standards de sécurité.

## Environnement utilisé
*   **Système Hôte :** Windows (Local)
*   **Émulateur :** Android Emulator (Environnement contrôlé et autorisé)
*   **Cible :** Application Sieve v1.0

## Outils utilisés
*   **Drozer :** Framework principal pour l'analyse dynamique et l'exploitation des composants IPC.
*   **ADB (Android Debug Bridge) :** Pour la communication avec le terminal et la gestion des ports.
*   **Sieve :** Application de test vulnérable (Gestionnaire de mots de passe).

## Architecture du Lab
```text
/preuves/
├── /activities/       # Analyse des écrans exposés
├── /services/         # Audit des services d'arrière-plan
├── /receivers/        # Vérification des Broadcast Receivers
├── /providers/        # Tests sur les Content Providers (SQLi/Traversal)
├── /manifest/         # Analyse du fichier AndroidManifest.xml
├── /attacksurface/    # Rapport global de la surface d'attaque
└── /screenshots/      # Captures d'écran de la console et de l'émulateur
```

## Commandes Drozer principales
*   `run app.package.attacksurface com.withsecure.example.sieve`
*   `run app.activity.info -a com.withsecure.example.sieve`
*   `run app.service.info -a com.withsecure.example.sieve`
*   `run app.provider.info -a com.withsecure.example.sieve`
*   `run app.provider.query content://...`
*   `run scanner.provider.injection`
*   `run scanner.provider.traversal`

## Cartographie des composants exposés
| Type     | Nom                    | Exporté | Protection              |
| -------- | ---------------------- | ------- | ----------------------- |
| Activity | MainLoginActivity      | Oui     | Aucune                  |
| Activity | PWList                 | Oui     | Aucune                  |
| Service  | AuthService            | Oui     | Aucune                  |
| Service  | CryptoService          | Oui     | Aucune                  |
| Receiver | ProfileInstallReceiver | Oui     | android.permission.DUMP |
| Provider | DBContentProvider      | Oui     | Faible                  |
| Provider | FileBackupProvider     | Oui     | Vulnérable              |

## Analyse des Risques

### 1. Composants Exportés sans Protection
Les activités `MainLoginActivity` et `PWList`, ainsi que les services `AuthService` et `CryptoService`, sont exportés sans aucune permission (`Permission: null`). Cela permet à n'importe quelle application malveillante installée sur le même appareil de lancer ces composants directement, contournant potentiellement les écrans d'authentification.

### 2. Content Providers Vulnérables
Le `DBContentProvider` expose des données sensibles (mots de passe, clés) via des URIs accessibles. Les tests ont révélé des faiblesses permettant la lecture de données sans autorisation adéquate. Le `FileBackupProvider` présente un risque élevé de Directory Traversal, permettant potentiellement d'accéder à des fichiers privés de l'application.

### 3. Broadcast Receivers
Le `ProfileInstallReceiver` est exporté mais protégé par la permission `android.permission.DUMP`. Bien que protégé, une application disposant de cette permission pourrait interagir avec ce récepteur.

## Captures d'écran
*Les preuves visuelles de l'audit sont disponibles dans le dossier `/preuves/screenshots/`.*

## Recommandations de sécurité
1.  **Principe du moindre privilège :** Définir `android:exported="false"` pour tous les composants qui ne nécessitent pas d'interaction externe.
2.  **Permissions de Signature :** Utiliser `protectionLevel="signature"` pour restreindre l'accès aux composants IPC aux seules applications du même développeur.
3.  **Sécurisation des Providers :** Implémenter des requêtes paramétrées pour prévenir les injections SQL et valider strictement les chemins de fichiers pour empêcher les traversées de répertoire.
4.  **Audit du Manifest :** Revoir systématiquement les balises `<activity>`, `<service>` et `<provider>` pour éviter toute exposition accidentelle.

## Conclusion
L'audit réalisé avec Drozer a mis en évidence une surface d'attaque significative sur l'application Sieve. L'absence de permissions sur des services critiques et la mauvaise configuration des Content Providers posent un risque majeur pour la confidentialité des données utilisateur. Ce lab démontre l'importance d'une configuration rigoureuse des composants IPC dès la phase de développement.

> [!IMPORTANT]
> ## Avertissement légal/éthique
> Ce projet est réalisé dans un cadre académique et professionnel. L'utilisation de ces outils et techniques sur des systèmes sans autorisation préalable est illégale. L'objectif est purement éducatif et défensif.
