# Analyse des Risques - Broadcast Receivers

## Receivers Exportés Détectées
*   `androidx.profileinstaller.ProfileInstallReceiver`

## Protection
*   `android.permission.DUMP`

## Commande Drozer utilisée
```bash
run app.broadcast.info -a com.withsecure.example.sieve
```

## Risques Associés
Bien que le récepteur soit protégé par la permission `DUMP`, il reste une cible potentielle pour toute application (souvent système ou privilégiée) disposant de ce privilège. L'exposition de récepteurs peut mener à des déclenchements d'actions non désirées via des Intents.

### Scénario d'Abus
Un attaquant disposant de la permission `DUMP` sur l'appareil (via une autre vulnérabilité ou une application malveillante privilégiée) peut envoyer des Intents à ce récepteur pour provoquer des comportements inattendus liés à l'installation de profils ou à la performance de l'application.

## Impacts Sécurité
*   **Perturbation du fonctionnement :** Risque de provoquer des actions non autorisées en arrière-plan.
*   **Stabilité :** Déclenchement abusif pouvant affecter les performances de l'application.

## Recommandations de Sécurité
*   Limiter l'exposition des récepteurs si leur usage externe n'est pas strictement requis.
*   Utiliser des permissions de niveau `signature` pour restreindre l'accès au cadre de confiance de l'appareil.
