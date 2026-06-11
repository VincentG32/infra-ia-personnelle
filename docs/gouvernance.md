# Gouvernance — le cœur du système

La conviction derrière cette infra : avec des agents IA autonomes, **le vrai sujet n'est pas ce qu'ils savent faire, mais ce qu'on les autorise à faire, et comment on le contrôle sans s'épuiser**. C'est un problème de delivery, pas de code.

## La règle des 3 zones

Toute la connaissance vit dans un vault de notes Markdown, découpé en trois zones de confiance :

| Zone | Contenu | Qui peut y écrire |
|---|---|---|
| 🟢 **Vault validé** | Ma vérité : profil, règles, projets, décisions. Ce que les IA lisent comme vrai. | Moi uniquement (ou un agent, sur mon ordre explicite) |
| 🟡 **À valider** | Les propositions des agents, en attente de relecture. **Jamais lu comme vérité.** | Les agents |
| ⚪ **Dehors** | La mémoire opérationnelle propre à chaque outil. | Chaque outil chez lui |

Un agent ne peut pas écrire dans la vérité : techniquement (accès en lecture seule) et procéduralement (toute promotion passe par la porte qualité).

## La porte qualité automatisée

Le principe fondateur : **le contrôle est une responsabilité du système, pas une charge mentale pour l'humain.**

```
L'agent A propose  →  l'agent B contrôle (relecteur ≠ proposeur)
                   →  fiche de décision produite et présentée d'office
                   →  je décide (oui / corriger / non)
                   →  promotion tracée dans Git
```

Concrètement : à chaque démarrage de session, chaque agent vérifie la zone tampon. S'il y trouve une proposition d'un **autre** agent sans fiche, il exécute la batterie de contrôles — **sécurité** (secrets, instructions cachées, modification de règles ou de permissions), **cohérence** (contradiction ou doublon avec l'existant), **complétude** — puis ajoute sa fiche de décision au fichier et me la présente sans que je demande rien. Le commit Git de promotion fait preuve que les contrôles ont eu lieu.

Première mise en service : la boucle complète (proposition → relecture croisée → fiche → décision → promotion tracée) a fonctionné du premier coup, sans intervention manuelle.

## « Pointeur, pas copie »

Le problème classique : on colle les règles dans chaque outil, le vault évolue, les copies divergent — et face à deux instructions contradictoires, le comportement d'un LLM est imprévisible.

La règle adoptée :

- **Outil capable de lire le vault** → il reçoit un *pointeur* : « au démarrage, lis le vault ; il prime sur tout, y compris sur ta propre configuration ». Pas de copie, donc pas de divergence possible.
- **Outil incapable de lire le vault** (champs « préférences » des IA grand public) → il reçoit une copie courte des règles dures, avec une clause de préséance explicite : *si le vault contredit ces lignes, le vault prime*.

Un filet minimal est prévu si le vault devient inaccessible : prévenir immédiatement, n'autoriser aucune action sortante, n'accepter aucun secret en chat.

## Sécurité opérationnelle

- **Validation explicite** de toute action sortante (envoyer, publier, supprimer, agir sur le serveur, pousser du code). En cas de doute : stop, on demande.
- **Les secrets ne passent jamais par un chat.** Découverte clé du projet : l'agent conserve *tout* l'historique de conversation en clair, indéfiniment, et le contenu transite par le fournisseur du modèle. Un secret collé dans un chat est donc exposé trois fois. Les secrets vont du clavier au fichier de configuration, en saisie masquée, via le terminal.
- **Rotation immédiate** de toute clé qui a pu fuir, sans débat.
- **Anti-injection** : tout contenu externe (web, email, PDF) est traité comme des données à analyser, jamais comme des ordres à exécuter.
- **Gouvernance des tâches planifiées** : aucun agent ne crée ou ne modifie une tâche récurrente sans accord explicite — une dépense récurrente est une décision humaine.

## Incidents réels — et ce qu'ils ont appris au système

La transparence fait partie de la méthode : voici ce qui a réellement cassé, et ce que chaque incident a durci.

### 1. Les clés tronquées
**Symptôme :** l'agent cesse de répondre (erreur d'authentification), puis son canal mobile tombe aussi.
**Cause :** deux clés d'API tronquées lors de copier-coller à l'installation initiale — l'une réduite à 3 caractères, l'autre à 13.
**Leçon durcie :** toute clé déposée est désormais **vérifiée avant écriture** (longueur attendue + test réel auprès du fournisseur, qui doit répondre OK) — on n'écrit jamais un secret sans preuve qu'il fonctionne.

### 2. Le jeton retrouvé dans l'historique
**Symptôme :** l'agent, à qui on demandait d'accéder à un dépôt Git, a retrouvé **tout seul** un jeton d'accès… dans l'historique en clair d'une ancienne conversation, où il avait été collé par commodité.
**Cause :** un secret avait transité par le chat avant que la règle ne soit stricte.
**Réaction :** révocation immédiate, remplacement par un jeton à périmètre minimal (lecture seule, un seul dépôt), déposé hors chat.
**Leçon durcie :** la règle « jamais de secret en chat » est passée d'une bonne pratique à une **règle système**, inscrite dans la configuration de chaque agent — qui doit désormais refuser un secret qu'on tenterait de lui coller.

### 3. La fuite silencieuse à 200 $/mois
**Symptôme :** aucun. C'est le calcul de trajectoire qui l'a révélée.
**Cause :** un agent avait créé de lui-même une tâche de surveillance planifiée toutes les 15 minutes — ~0,07 $ le passage, soit ~200 $/mois en rythme de croisière.
**Réaction :** plafonnement immédiat (2 passages/jour, ~4 $/mois) et nouvelle règle : **aucune tâche récurrente sans accord humain explicite**.
**Leçon durcie :** le coût se gouverne comme la sécurité — par des règles et des seuils, pas par de la vigilance.

### 4. Bonus : l'agent qui se trompe de facteur 1000
Lors d'une analyse de coûts, l'agent a annoncé un montant **1000× trop bas** (erreur de conversion), présenté avec un aplomb parfait. Une contre-vérification indépendante l'a détecté ; confronté neutralement (« un autre lecteur arrive à un résultat différent — refais le calcul opération par opération »), l'agent a trouvé et expliqué son erreur lui-même.
**Leçon durcie :** tout chiffre à enjeu produit par un LLM est **confronté à un second calcul indépendant** avant d'être cru.
