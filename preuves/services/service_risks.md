# Analyse des Risques - Services

## Services Exportés Détectées
*   `com.withsecure.example.sieve.service.AuthService`
*   `com.withsecure.example.sieve.service.CryptoService`

## Commande Drozer utilisée
```bash
run app.service.info -a com.withsecure.example.sieve
```

## Risques Associés
Les services `AuthService` et `CryptoService` ne possèdent aucune permission de protection (`Permission: null`). Ils sont exposés et accessibles par toute application malveillante via des Intents.

### Scénarios d'Abus
1.  **Exécution de Fonctions Privilégiées :** Un attaquant peut appeler des méthodes du `AuthService` pour manipuler l'état d'authentification.
2.  **Abus de Cryptographie :** Le `CryptoService` peut être utilisé pour effectuer des opérations cryptographiques non autorisées ou pour tenter d'extraire des clés si des faiblesses existent dans l'implémentation IPC.

## Impacts Sécurité
*   **Manipulation de Données :** Possibilité de modifier des secrets ou de contourner les mécanismes de chiffrement de l'application.
*   **Risque IPC Android :** Exposition des mécanismes internes de l'application à des acteurs non autorisés.

## Recommandations de Sécurité
*   Ajouter des permissions restrictives dans le manifeste (`android:permission`).
*   Utiliser des permissions de type `signature` pour s'assurer que seuls les composants de confiance peuvent lier ces services.
