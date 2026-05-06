# Analyse du Manifeste (AndroidManifest.xml)

## Analyse de l'Exposition des Composants
L'analyse du manifeste de `com.withsecure.example.sieve` via Drozer révèle une configuration par défaut extrêmement permissive.

## Commande Drozer utilisée
```bash
run app.package.manifest com.withsecure.example.sieve
```

## Observations Clés
*   **android:exported="true" :** De nombreux composants (Activities, Services, Providers) ont cette balise activée de manière explicite ou implicite (via des Intent Filters) sans permissions de protection.
*   **Permissions de Protection Manquantes :** La plupart des composants exportés ne spécifient pas de `android:permission`, laissant l'accès libre à n'importe quel package sur le système.
*   **Protection Faible sur le Receiver :** Bien que `ProfileInstallReceiver` utilise la permission `DUMP`, cette permission est souvent accessible à des applications système ou à des outils de débogage, ce qui n'est pas suffisant pour une application de gestion de secrets.

## Impact sur la Sécurité
Le manifeste est la première ligne de défense d'une application Android. Dans le cas de Sieve, il sert plutôt de "plan" pour l'attaquant, indiquant précisément quels composants peuvent être attaqués sans résistance.

## Recommandations
*   **Audit Systématique :** Chaque composant doit être marqué `exported="false"` par défaut.
*   **Utilisation de Niveaux de Protection :** Privilégier `signature` pour les permissions internes.
