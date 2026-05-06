# Analyse des Risques - Content Providers

## Providers Détectés
*   `DBContentProvider` (Exporté: Oui)
*   `FileBackupProvider` (Exporté: Oui)

## Commande Drozer utilisée
```bash
run app.provider.info -a com.withsecure.example.sieve
run scanner.provider.injection -a com.withsecure.example.sieve
run scanner.provider.traversal -a com.withsecure.example.sieve
```

## Risques Associés

### 1. DBContentProvider (Injection SQL & Fuite de Données)
Ce provider gère les bases de données de l'application (mots de passe et clés). Sa configuration permissive permet à des applications tierces d'interroger les données via `content://`.
*   **Risque :** Lecture non autorisée de la base de données.
*   **Vérification :** `run app.provider.query content://com.withsecure.example.sieve.provider.DBContentProvider/Passwords/`

### 2. FileBackupProvider (Directory Traversal)
Ce provider semble conçu pour gérer les sauvegardes de fichiers, mais il est vulnérable à des attaques de type traversée de répertoire.
*   **Risque :** Lecture de fichiers privés situés en dehors du répertoire de sauvegarde prévu.
*   **Scénario :** Un attaquant pourrait utiliser une URI comme `content://com.withsecure.example.sieve.provider.FileBackupProvider/../../../../etc/hosts` (ou des chemins internes à l'app) pour extraire des fichiers sensibles.

## Impacts Sécurité
*   **Confidentialité Totale Compromise :** Fuite massive de mots de passe, clés de chiffrement et fichiers privés.
*   **Exposition de Données Utilisateur :** Les informations les plus sensibles de l'utilisateur sont accessibles sans aucune authentification.

## Recommandations de Sécurité
*   **Désactiver l'exportation :** `android:exported="false"` si le provider est interne.
*   **Requêtes Paramétrées :** Prévenir les injections SQL en utilisant des `SelectionArgs`.
*   **Validation de Chemin :** Utiliser `File.getCanonicalPath()` et vérifier que le chemin final se trouve bien dans le répertoire autorisé pour contrer le Directory Traversal.
*   **Permissions fines :** Utiliser `readPermission` et `writePermission` granulaires.
