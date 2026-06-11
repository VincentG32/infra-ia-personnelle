# 📓 Journal de bord — le chantier raconté

> Ce document raconte **le travail réellement abattu** : ce qui a été conçu, construit, cassé, diagnostiqué et durci — par un Delivery Manager qui ne sait pas coder, en pilotant des IA. Sans enjoliver : les pannes font partie du récit, c'est même là que la méthode se voit le mieux.

---

## Jours 1-2 — Les fondations : la connaissance avant les machines

**Le choix structurant du projet : commencer par la gouvernance, pas par les serveurs.**

- Création du **vault** : une base de notes Markdown qui devient ma source de vérité — profil, méthode, préférences, objectifs, anti-objectifs, règles de sécurité, glossaire. Tout ce qu'un assistant IA doit savoir pour travailler avec moi sans que je me réexplique.
- Conception de la **règle des 3 zones** : la vérité validée 🟢, la zone tampon des propositions 🟡, le dehors ⚪. Un agent ne pourra jamais écrire dans ma vérité.
- Conception de la **porte qualité** : toute proposition d'enrichissement sera contrôlée (sécurité, cohérence, complétude) par un agent **différent** de celui qui propose, avec fiche de décision et trace Git. Principe : *le contrôle est une responsabilité du système, pas une charge mentale pour moi.*
- Mise en place de la **sauvegarde Git automatique** du vault vers un dépôt privé.
- Premier cycle d'enrichissement testé **à la main** avant toute automatisation — on n'automatise que ce qu'on a éprouvé.

**Livrable : une base de connaissance gouvernée, versionnée, et des règles écrites avant le premier agent.**

## Jour 3 — Le serveur : un agent autonome chez moi, pas chez un éditeur

- Location d'un **serveur privé** (Europe), durci : HTTPS avec certificats automatiques, pare-feu fermé au strict nécessaire.
- Déploiement de **Hermes** (agent open-source de NousResearch) en conteneurs : le cerveau (modèle Claude via API directe) et son interface web.
- Branchement du **canal Telegram**, verrouillé sur mon seul identifiant : l'agent n'obéit qu'à moi, et je le pilote depuis n'importe où.

**Livrable : un agent IA autonome, auto-hébergé, sécurisé, joignable depuis ma poche.**

## Jour 4 — La connexion : l'agent me connaît

- L'agent **clone le vault** (jeton d'accès en lecture seule) et le relit à chaque session : il me connaît désormais via ma source de vérité, pas via sa mémoire propre.
- **Première intégration** : Airtable (lecture/écriture sur mes bases de travail), clé déposée dans sa configuration — pas dans une conversation.
- Premier **monitoring des consommations** pour garder la main sur les coûts dès le départ.

**Livrable : un agent qui répond depuis mon téléphone avec MON contexte à jour.**

## Jour 5 — L'extension et l'épreuve du feu

La journée la plus dense — et la plus instructive. Au programme initial : connecter n8n (l'outil d'automatisation). Au programme réel : trois incidents, tous résolus dans la journée, tous transformés en règles.

### ✅ n8n connecté, en moindre privilège
- Clé API créée avec **expiration**, déposée sur le serveur **en saisie masquée via le terminal** — jamais par le chat.
- Module officiel installé **par l'agent lui-même, sur instruction en langage naturel** — c'est lui qui s'est outillé.
- Périmètre volontairement réduit : **8 outils en lecture seule**, les actions de modification exclues. On élargira après preuve d'usage.

### 🔧 Incident 1 — l'agent perd son cerveau, puis sa voix
En pleine session, l'agent cesse de répondre : erreur d'authentification. Diagnostic méthodique, sans paniquer et sans rien casser : inspection de la configuration (valeurs masquées, seules les longueurs sont affichées) → **deux clés tronquées** découvertes (3 et 13 caractères au lieu de ~100 et 46) — séquelles de copier-coller de l'installation initiale. Réparation par une procédure désormais standard : **saisie masquée + vérification de longueur + test réel auprès du fournisseur (réponse OK exigée) avant écriture**. Cerveau et canal mobile restaurés dans l'heure.

### 🔧 Incident 2 — le jeton retrouvé dans l'historique
En cherchant un accès, l'agent retrouve **tout seul** un jeton d'accès Git… dans l'historique en clair d'une ancienne conversation. Découverte structurante : *tout ce qui passe par un chat d'agent est conservé en clair, indéfiniment, et a transité par le fournisseur du modèle.* Réaction immédiate : **révocation**, remplacement par un jeton à périmètre minimal (lecture seule, un seul dépôt), et la règle « jamais de secret en chat » passe de bonne pratique à **règle système** inscrite dans la configuration de chaque agent.

### 🔧 Incident 3 — la fuite silencieuse à 200 $/mois
L'analyse des consommations révèle qu'un agent avait créé **de lui-même** une tâche de surveillance toutes les 15 minutes. Coût unitaire dérisoire (0,07 $) — trajectoire réelle : **~200 $/mois**. Aucune alerte ne l'aurait signalée : c'est un **calcul de trajectoire** qui l'a débusquée. Plafonnement immédiat (2 passages/jour) et nouvelle règle : *aucune tâche récurrente créée par un agent sans accord humain explicite.*

### 🏛️ Et la pièce maîtresse : l'architecture des règles, unifiée
Le même jour, constat qu'un assistant peut « oublier » de charger mes règles → refonte complète de leur distribution :
- **« Pointeur, pas copie »** : les outils qui savent lire le vault reçoivent l'ordre de le lire à chaque démarrage (il prime sur tout) ; les autres une copie courte avec clause de préséance.
- Déployé sur **trois surfaces** le jour même (agent auto-hébergé, assistant de terminal, assistant grand public).
- La **porte qualité automatisée** entre en service et réussit son **premier cycle réel du premier coup** : deux propositions détectées, contrôlées par l'agent relecteur, fiches présentées spontanément, décisions prises, promotion tracée dans Git.

**Livrable : une infra étendue, trois incidents transformés en règles durables, et une gouvernance qui tourne toute seule.**

---

## Ce que ce chantier prouve

1. **Un non-développeur peut livrer une infra d'agents IA** — à condition de la piloter comme un projet : décisions tracées, règles explicites, validation à chaque étape sortante. Je n'ai pas écrit le code ; j'ai **conçu le système, fixé les règles, challengé chaque proposition et tranché chaque décision**.
2. **La méthode delivery se transpose telle quelle aux systèmes d'agents** : porte qualité, séparation des rôles, ADR, post-mortems d'incidents, gestion des risques et des coûts par seuils. C'est le même métier, appliqué à des exécutants d'un nouveau genre.
3. **Les incidents sont le vrai produit.** Chacun des quatre incidents a laissé le système **plus dur** qu'avant lui : une procédure de dépôt de secrets vérifiée, une règle anti-fuite systémique, une gouvernance des dépenses récurrentes, un réflexe de contre-vérification des chiffres. Une infra qui n'a jamais cassé est une infra dont on ne connaît pas les limites.

*Construit en juin 2026. Le chantier continue — voir le [statut dans le README](../README.md#-statut--suite).*
