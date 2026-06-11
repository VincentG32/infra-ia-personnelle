<div align="center">

# 🧠 Infra IA personnelle

### *« Je pilote, l'IA code. »*

**Comment un Delivery Manager non-développeur a conçu, livré et gouverné<br/>une infrastructure d'agents IA auto-hébergés — seul, en 5 jours.**

![Statut](https://img.shields.io/badge/statut-🟢_opérationnelle-22c55e?style=for-the-badge)
![Code écrit à la main](https://img.shields.io/badge/code_écrit_à_la_main-0_ligne-8b5cf6?style=for-the-badge)
![Sécurité](https://img.shields.io/badge/sécurité-first-ef4444?style=for-the-badge)

![Auto-hébergé](https://img.shields.io/badge/agent-auto--hébergé-3b82f6?style=flat-square)
![Source de vérité](https://img.shields.io/badge/source_de_vérité-unique-f59e0b?style=flat-square)
![Gouvernance](https://img.shields.io/badge/porte_qualité-automatisée-10b981?style=flat-square)
![Coût](https://img.shields.io/badge/coût_de_croisière-≈15--30$%2Fmois-64748b?style=flat-square)
![Lock-in](https://img.shields.io/badge/lock--in-zéro_(Markdown_+_Git)-475569?style=flat-square)

</div>

---

## 💡 L'idée en 30 secondes

J'ai construit une infrastructure IA personnelle dont le cœur n'est pas la technique, mais la **gouvernance** :

- 📚 Une **source de vérité unique** (un vault de notes Markdown) que tous mes assistants IA lisent — je n'explique jamais deux fois qui je suis, mes règles ou mes projets.
- 🤖 Un **agent autonome auto-hébergé** (Hermes, de NousResearch) sur un serveur privé, joignable depuis mon téléphone, qui me connaît via ce vault et agit sous des règles strictes.
- 🛡️ Un système où **les agents proposent, les contrôles sont automatiques, et l'humain décide** — jamais l'inverse.

Le tout **sans écrire une ligne de code moi-même** : j'ai conçu, décidé, challengé et validé — les IA ont exécuté.

## 📊 Le chantier en chiffres

<div align="center">

| | | | |
|:---:|:---:|:---:|:---:|
| **5 jours**<br/>de l'idée à l'infra gouvernée | **0 ligne**<br/>de code écrite à la main | **2 agents IA**<br/>gouvernés en relecture croisée | **4 services**<br/>intégrés (vault, Telegram, Airtable, n8n) |
| **9 secrets**<br/>gérés sans jamais transiter par un chat | **4 incidents réels**<br/>résolus et durcis en règles | **9 décisions ADR**<br/>tracées avec leur pourquoi | **~200 $/mois**<br/>de dérive évitée par calcul de trajectoire |

</div>

## 🗓️ La frise du chantier

```mermaid
timeline
    title De zéro à une infra IA gouvernée — 5 jours
    section Fondations
        Jours 1-2 : Vault source de vérité (Markdown + Git)
                  : Règle des 3 zones & porte qualité conçues
                  : Sauvegarde Git automatique
    section Déploiement
        Jour 3 : Serveur privé (HTTPS + pare-feu)
               : Agent Hermes auto-hébergé déployé
               : Canal Telegram verrouillé sur mon identité
    section Connexion
        Jour 4 : L'agent lit le vault (jeton lecture seule)
               : Première intégration (Airtable)
               : Monitoring des coûts
    section Extension & épreuve du feu
        Jour 5 : n8n connecté (lecture seule d'abord)
               : 2 pannes diagnostiquées et réparées
               : 1 fuite de jeton détectée → rotation immédiate
               : Porte qualité automatisée et testée en réel
```

👉 **Le récit complet du chantier, pannes comprises : [le journal de bord](docs/journal-de-bord.md)** — c'est là que se voit le travail.

## 🏗️ Architecture

```mermaid
flowchart TB
    classDef humain fill:#4f46e5,color:#ffffff,stroke:#312e81,stroke-width:2px
    classDef serveur fill:#0f766e,color:#ffffff,stroke:#134e4a,stroke-width:2px
    classDef stockage fill:#b45309,color:#ffffff,stroke:#78350f,stroke-width:2px
    classDef agent fill:#be185d,color:#ffffff,stroke:#831843,stroke-width:2px

    subgraph Moi["👤 MOI — je décide"]
        TG["📱 Telegram<br/>(mobile, partout)"]
        OBS["📚 Obsidian<br/>le vault — SOURCE DE VÉRITÉ"]
        CC["💻 Claude Code<br/>(sur Mac)"]
    end

    subgraph VPS["☁️ SERVEUR PRIVÉ — HTTPS + pare-feu"]
        H["🤖 Hermes<br/>agent autonome"]
        N8N["⚙️ n8n<br/>automatisation"]
    end

    GH[("🔐 Dépôt Git privé<br/>sauvegarde + trace")]

    OBS == "sauvegarde auto" ==> GH
    GH -- "🔒 lecture seule" --> H
    TG <--> H
    H -- "🔒 API lecture seule" --> N8N
    CC <--> OBS
    H -. "propositions<br/>(zone tampon)" .-> OBS

    class TG,OBS,CC humain
    class H,N8N agent
    class GH stockage
```

Les agents accèdent au vault en **lecture seule** (jeton scopé au strict nécessaire). Leurs propositions passent par une **zone tampon** et une **porte qualité automatisée** avant d'entrer dans la vérité :

```mermaid
flowchart LR
    classDef propose fill:#f59e0b,color:#111827,stroke:#b45309,stroke-width:2px
    classDef controle fill:#3b82f6,color:#ffffff,stroke:#1d4ed8,stroke-width:2px
    classDef decide fill:#22c55e,color:#111827,stroke:#15803d,stroke-width:2px
    classDef trace fill:#64748b,color:#ffffff,stroke:#334155,stroke-width:2px

    A["🤖 Agent A<br/>PROPOSE"]:::propose --> B["🤖 Agent B<br/>CONTRÔLE<br/>(relecteur ≠ proposeur)"]:::controle
    B --> C["📋 Fiche de décision<br/>présentée d'office"]:::controle
    C --> D["👤 MOI<br/>JE DÉCIDE"]:::decide
    D --> E["✅ Promotion<br/>tracée dans Git"]:::trace
```

## 🧭 Les principes

1. 🛡️ **Sécurité first, non négociable.** Toute action sortante exige ma validation explicite. Un secret ne transite **jamais** par un chat — dépôt direct dans la configuration, en saisie masquée.
2. 📚 **Une seule source de vérité.** Le vault prime sur tout. Les outils qui savent le lire reçoivent un *pointeur* (« lis le vault, il prime ») ; les autres une copie subordonnée. Jamais deux vérités qui divergent.
3. 🔒 **Moindre privilège.** Chaque agent reçoit un secret scopé et révocable par service — jamais plus que le strict nécessaire (ex. : n8n connecté en lecture seule d'abord, élargi seulement après preuve d'usage).
4. ⚖️ **Le système contrôle, l'humain décide.** Relecture croisée automatique entre agents, fiche de décision prête à trancher. Je n'ai jamais à y penser — mais c'est toujours moi qui tranche.
5. 💸 **Efficience des tokens.** Chaque dépense d'IA doit être justifiée : déclenchements événementiels plutôt que minuteurs, routage des tâches vers le bon modèle économique, seuils chiffrés.
6. 🔓 **Réversibilité.** Markdown + Git partout : aucun enfermement propriétaire, tout est portable et l'historique fait preuve.

## ⚡ Ce que ça change au quotidien

- 📱 Depuis mon téléphone : « Rappelle-moi mon focus actuel » → l'agent relit le vault et répond avec **mon** contexte, à jour.
- 🧠 Mes assistants connaissent mes règles **avant** que je tape le premier mot — chargées automatiquement à chaque session.
- 🔄 Un agent repère une amélioration → un second la **contrôle** → je reçois une **fiche de décision** prête à valider.
- ⚙️ Mes automatisations n8n sont consultables par l'agent — en lecture seule tant que l'usage n'a pas justifié plus.

## 📖 La documentation

| Document | Ce qu'il raconte |
|---|---|
| 📓 **[Journal de bord](docs/journal-de-bord.md)** | **Le chantier, jour par jour** — ce qui a été construit, ce qui a cassé, comment ça a été réparé. La preuve par le récit. |
| 🛡️ [Gouvernance](docs/gouvernance.md) | La règle des 3 zones, la porte qualité automatisée, et les **4 incidents réels** avec les règles qui en sont sorties |
| 📋 [Journal de décisions](docs/decisions.md) | 9 décisions d'architecture au format ADR — le *pourquoi* derrière chaque choix |
| 💸 [Coûts](docs/couts.md) | L'économie réelle d'une infra IA personnelle : calibrage mesuré, pièges, règles d'efficience |

## 🚀 Statut & suite

🟢 **Opérationnelle** (montée en juin 2026) et en évolution continue. Prochaines étapes : veille automatisée (digest quotidien sur mobile), délégation de développement à un agent codeur, étude d'un rapatriement sur machine locale.

## 👤 À propos

Je suis **Vincent Granouillit** — Delivery Manager / Chef de projet (France & Suisse), en route vers le *product building* augmenté à l'IA. Cette infra est à la fois mon outil quotidien et ma preuve de concept : **la gouvernance du delivery appliquée aux systèmes d'agents.**

🔗 **[vincent-granouillit.vercel.app](https://vincent-granouillit.vercel.app)**
