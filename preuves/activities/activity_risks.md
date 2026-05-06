# Analyse des Risques - Activities

## Activités Exportées Détectées
*   `com.withsecure.example.sieve.MainLoginActivity`
*   `com.withsecure.example.sieve.PWList`

## Commande Drozer utilisée
```bash
run app.activity.info -a com.withsecure.example.sieve
```

## Risques Associés
Les activités sont marquées comme `exported="true"` dans le manifeste sans aucune permission de protection (`android:permission`). Cela signifie qu'elles peuvent être lancées par n'importe quelle application tierce présente sur l'appareil.

### Scénarios d'Abus
1.  **Contournement d'Authentification :** Un attaquant peut lancer directement `PWList` pour accéder à la liste des mots de passe sans passer par l'écran de connexion (`MainLoginActivity`).
2.  **Hameçonnage (Phishing) Local :** Une application malveillante pourrait lancer `MainLoginActivity` de manière inattendue pour tromper l'utilisateur et voler ses identifiants.

## Impacts Sécurité
*   **Confidentialité :** Accès direct aux données sensibles stockées dans l'application.
*   **Intégrité :** Possibilité d'interagir avec des fonctionnalités internes de gestion des comptes.

## Recommandations de Sécurité
*   Définir `android:exported="false"` pour les activités qui ne sont utilisées qu'en interne.
*   Si l'exportation est nécessaire, protéger l'accès avec une permission personnalisée de niveau `signature`.
