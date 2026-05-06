# Rapport de Surface d'Attaque (Attack Surface)

## Résumé Global
Le rapport généré par Drozer fournit une vue d'ensemble du nombre de vecteurs d'attaque potentiels via les composants IPC.

## Commande Drozer utilisée
```bash
run app.package.attacksurface com.withsecure.example.sieve
```

## Résultats de l'Analyse
*   **Activities :** 2 activités exportées détectées.
*   **Services :** 2 services exportés détectés.
*   **Broadcast Receivers :** 1 récepteur exporté détecté.
*   **Content Providers :** 2 fournisseurs de contenu exportés détectés.

## Analyse Critique
La surface d'attaque est totale pour une application de gestion de mots de passe. Tous les types de composants Android sont exposés, ce qui multiplie les points d'entrée pour un attaquant local. L'absence de barrières au niveau IPC (Inter-Process Communication) rend l'application vulnérable à l'extraction de données et au détournement de fonctionnalités.

## Conclusion Technique
L'application Sieve présente une posture de sécurité "ouverte", ce qui est inacceptable pour un gestionnaire de secrets. La réduction de cette surface d'attaque doit être la priorité absolue de l'équipe de développement.
