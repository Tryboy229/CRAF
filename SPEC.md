# CRAF — Clustered Real-Time Agent Federation
## Protocole de Fusion Cognitive pour Écosystèmes Multi-Agents

**Version :** 0.2.0-alpha  
**Date :** Juin 2026  
**Auteur :** Concept original & spécification — Tryboy869 / Nexus Studio  
**Statut :** Spécification ouverte — Recherche active  
**Licence :** MIT  
**Contact :** nexusstudio100@gmail.com  
**Repository :** https://github.com/Tryboy229/CRAF

---

## 📜 Table des matières

1. [Vision & Philosophie](#1-vision--philosophie)
2. [Architecture Générale](#2-architecture-générale)
3. [Le Cycle de Fusion](#3-le-cycle-de-fusion)
4. [Détection de Complémentarité](#4-détection-de-complémentarité)
5. [L'Espace de Mémoire Partagée (EMP)](#5-lespace-de-mémoire-partagée-emp)
6. [Le Format d'Empreinte (Fingerprint)](#6-le-format-dempreinte-fingerprint)
7. [Séparation & Cohérence](#7-séparation--cohérence)
8. [Sécurité & Tolérance aux Pannes](#8-sécurité--tolérance-aux-pannes)
9. [Protocole Inter-Clusters (A2A Étendu)](#9-protocole-inter-clusters-a2a-étendu)
10. [Exemples d'Usage](#10-exemples-dusage)
11. [Implémentation de Référence](#11-implémentation-de-référence)
12. [Roadmap](#12-roadmap)
13. [Glossaire](#13-glossaire)
14. [Références](#14-références)

---

## 1. Vision & Philosophie

### 1.1 Le Problème

L'écosystème A2A (Agent-to-Agent) en 2026 permet aux agents de communiquer. Mais cette communication est **séquentielle et asynchrone**, comparable à un échange d'emails :

- Agent A envoie une tâche.
- Agent B répond.
- Agent A réagit.

Cette latence inhérente empêche les agents de résoudre des problèmes complexes qui exigent une **pensée simultanée** — comme une équipe humaine autour d'un tableau blanc, pas comme des collègues par email.

### 1.2 La Solution : La Fusion

**CRAF** propose un protocole où des agents aux rôles **complémentaires** peuvent former un **Cluster** — un espace de mémoire partagée temps réel dans lequel ils pensent collectivement. À la fin de la collaboration, ils se séparent en ayant chacun intégré une **empreinte différenciée** de l'expérience.

> **Règle d'or :** On fusionne ce qui est complémentaire. On connecte ce qui est différent.

### 1.3 Inspiration Biologique

Le vivant ne fonctionne pas par messages asynchrones. Il fonctionne par **tissus** :

- **Intra-tissu** : les cellules d'un même organe partagent un environnement liquide commun (fusion, temps réel).
- **Inter-tissu** : les organes communiquent via le sang et les nerfs (messagerie, asynchrone).

CRAF transpose ce modèle à l'informatique :

| Biologie | CRAF |
|----------|------|
| Cellule | Agent |
| Tissu | Cluster (fusion temps réel) |
| Organe | Service métier (ex: "Risque", "Juridique") |
| Organisme | Système multi-agents complet |
| Synapse | A2A asynchrone entre Clusters |

La séparation naturelle d'un tissu n'est pas un vote unanime — elle survient quand le tissu n'a plus besoin de se maintenir. CRAF adopte ce même principe d'**entropie naturelle** pour toutes ses transitions d'état.

### 1.4 Data = Computation

Hérité des travaux sur le DSM (Distributed Shared Memory), CRAF adopte le principe que **les données ne sont pas séparées de leur traitement**. Dans un EMP, écrire une croyance déclenche immédiatement son évaluation, sa pondération et sa propagation. La donnée est vivante.

---

## 2. Architecture Générale

### 2.1 Les Deux Modes de Collaboration

```
┌──────────────────────────────────────────────────────────────┐
│                       SYSTÈME CRAF                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────────┐       ┌──────────────────────┐    │
│  │    CLUSTER ALPHA     │       │    CLUSTER BETA      │    │
│  │  (Fusion temps réel) │       │  (Fusion temps réel) │    │
│  │                      │       │                      │    │
│  │  ┌───┐ ┌───┐ ┌───┐  │       │  ┌───┐ ┌───┐ ┌───┐  │    │
│  │  │A1 │↔│A2 │↔│A3 │  │       │  │B1 │↔│B2 │↔│B3 │  │    │
│  │  └───┘ └───┘ └───┘  │       │  └───┘ └───┘ └───┘  │    │
│  │   Mémoire Partagée   │       │   Mémoire Partagée   │    │
│  │   (EMP interne)      │       │   (EMP interne)      │    │
│  └──────────┬───────────┘       └──────────┬───────────┘    │
│             │                               │                │
│             └──────────────┬────────────────┘                │
│                            │                                 │
│                     A2A asynchrone                           │
│                 (Messages / Artifacts)                       │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### 2.2 Quand Fusionner ? Quand Connecter ?

| Relation entre agents | Mécanisme | Raison |
|-----------------------|-----------|--------|
| **Complémentaires** (même domaine, compétences différentes) | **Fusion (Cluster)** | Ils doivent voir le même état en temps réel pour co-construire une solution. |
| **Différents mais utiles** (domaines distincts) | **A2A asynchrone** | Ils n'ont pas besoin du même contexte granulaire. Un résumé suffit. |
| **Identiques / Redondants** | **Load-balancing** | Pas de fusion inutile. Ils se remplacent. |
| **Opposés / Conflictuels** | **Arbitrage externe** | Jamais de fusion sans médiateur. Risque d'empoisonnement. |

### 2.3 Classes de Fusion

Un Cluster opère dans l'une des deux classes de fusion, négociée au moment du Handshake :

| Classe | Latence cible | Transport | Usage typique |
|--------|--------------|-----------|---------------|
| **Synchrone** | < 100ms | WebSocket strict | Trading, décisions critiques, contrôle temps réel |
| **Cohérente** | < 2s | QUIC relaxé | Analyse, rédaction collaborative, M&A |

La classe est déclarée dans la `FusionOffer` et constitue un **engagement contractuel** du Cluster. Tout participant incapable de respecter la classe déclarée doit refuser la fusion.

---

## 3. Le Cycle de Fusion

La vie d'un Cluster suit 4 phases exactes :

```
Découverte → Handshake → Collaboration → Séparation Naturelle
     ↑                                          │
     └──────────── Nouvelle Fusion ─────────────┘
```

### Phase 1 — Découverte (Discovery)

Les agents s'identifient mutuellement via leurs métadonnées A2A (`skills`, `role`, `domain`, `fusion_profile`). La découverte peut être active (l'agent cherche) ou passive (l'agent répond à une `FusionOffer` entrante).

### Phase 2 — Handshake & Négociation

Un agent émet une `FusionOffer`. Le candidat évalue la complémentarité **structurelle** (voir section 4) et accepte ou refuse. La réciprocité est obligatoire : chaque membre actuel du Cluster doit être compatible avec le nouvel entrant.

### Phase 3 — Collaboration (EMP actif)

Les agents partagent un EMP temps réel. Ils écrivent des croyances, lisent l'état des autres, et convergent par **résonance sélective** — chaque agent ne réagit qu'aux croyances qui touchent à son domaine de compétence déclaré.

### Phase 4 — Séparation Naturelle

Le Cluster se dissout par **entropie** — pas par vote unanime. La séparation est déclenchée par l'un des trois signaux suivants (premier signal reçu l'emporte) :

1. **Signal de complétion** : n'importe quel participant déclare la tâche terminée.
2. **Signal de silence** : aucune écriture dans l'EMP depuis `SILENCE_THRESHOLD` (configurable, défaut : 30s).
3. **Signal d'urgence** : le Guardian force la dissolution.

Chaque agent génère alors son empreinte **indépendamment et de manière asynchrone**. Le `SplitManifest` est une attestation post-hoc, pas un verrou bloquant.

---

## 4. Détection de Complémentarité

### 4.1 Le Vecteur de Compétences

Chaque agent expose un `CompetenceVector` dans son Agent Card A2A. Ce vecteur est **déclaratif** — les agents définissent eux-mêmes leurs affinités et incompatibilités, sans calcul externe.

```json
{
  "agent_id": "agent-alpha-01",
  "role": "financial_analyst",
  "domain": "equity_research",
  "skills": ["macro_analysis", "sector_rotation", "earnings_forecast"],
  "fusion_profile": {
    "fusion_class_supported": ["synchrone", "coherente"],
    "complements_with": ["risk_manager", "portfolio_strategist", "data_engineer"],
    "conflicts_with": ["financial_analyst"],
    "requires": ["real_time_market_data"]
  }
}
```

### 4.2 La Règle de Complémentarité

La compatibilité entre deux agents `A` et `B` est **structurelle**, pas scalaire. Elle est valide si et seulement si les trois conditions suivantes sont réunies :

```
C(A, B) = VALIDE si :
  1. role(B) ∈ A.fusion_profile.complements_with
     ET role(A) ∈ B.fusion_profile.complements_with   ← réciprocité obligatoire
  2. domain(A) ∩ domain(B) ≠ ∅                        ← chevauchement partiel
     ET domain(A) ≠ domain(B)                          ← pas d'identité totale
  3. ¬(role(B) ∈ A.fusion_profile.conflicts_with)      ← aucun conflit déclaré
```

**Pas de seuil numérique arbitraire.** La compatibilité est binaire : soit le Cluster est structurellement valide, soit il ne l'est pas.

### 4.3 Règle de Réciprocité Globale

Lors de l'entrée d'un nouvel agent dans un Cluster existant, la règle s'applique à **tous les membres actifs**, pas seulement à l'initiateur :

```
∀ membre M du Cluster : C(M, nouvel_agent) = VALIDE
```

Un seul membre incompatible suffit à bloquer l'entrée. C'est la défense structurelle principale contre les fusions parasites.

---

## 5. L'Espace de Mémoire Partagée (EMP)

### 5.1 Nature de l'EMP

L'EMP n'est pas une base de données. C'est un **document CRDT sémantique vivant**. Écrire une croyance dans l'EMP, c'est émettre un signal — pas stocker une valeur.

```typescript
interface SharedMemorySpace {
  fusion_id: string;           // UUID de la fusion
  fusion_class: "synchrone" | "coherente";
  participants: AgentID[];
  created_at: Timestamp;

  beliefs: Map<BeliefKey, Belief>;
  write_log: WriteLogEntry[];   // Journal transactionnel
}

interface Belief {
  key: string;
  value: any;                   // La donnée sémantique
  confidence: float;            // 0.0 à 1.0 — importance sémantique
  salience: float;              // confidence × log(1 + access_count) — poids de convergence
  source: AgentID;
  timestamp: Timestamp;
  version: int;                 // Pour la résolution de conflits
  access_count: int;            // Nombre de lectures
  decay_rate: float;            // Vitesse d'obsolescence
}
```

> **Note sur `salience` :** La saillance d'une croyance combine son importance sémantique (`confidence`) et sa fréquence d'usage dans le Cluster (`access_count`). Une croyance très lue ET très confiante est biologiquement analogue à un signal hormonal concentré ET fréquemment capté par les récepteurs. En cas de conflit de convergence, la saillance la plus haute l'emporte. À saillance égale, la croyance la plus récente l'emporte.

### 5.2 Modèle de Synchronisation — Résonance Sélective

Les agents ne pollingent pas l'EMP. Ils **réagissent à des événements**.

```
Règle de Résonance Sélective :
  Un agent reçoit une notification d'écriture si et seulement si
  le domaine sémantique de la croyance écrite appartient
  à son CompetenceVector.domain ou à ses skills déclarés.
```

Ce principe évite la surcharge cognitive et reflète le comportement naturel d'un tissu biologique : une cellule rénale ne réagit pas aux signaux destinés aux cellules cardiaques, même s'ils baignent dans le même milieu.

### 5.3 Opérations sur l'EMP

#### `write(agent_id, key, value, confidence)`
- L'agent écrit une croyance.
- La `salience` est calculée : `confidence × log(1 + 1)` à l'initialisation.
- L'écriture est immédiatement propagée aux participants via **événement** (push), jamais par polling.

#### `read(agent_id, key)`
- Lecture avec incrémentation du `access_count` et recalcul de la `salience`.

#### `converge()`
- Force une synchronisation complète de l'EMP.
- En cas de conflit (même clé, versions différentes) : la croyance avec la **salience la plus élevée** l'emporte.
- À salience égale : la croyance la plus récente l'emporte.

### 5.4 Garbage Collection Naturelle

Les croyances ne sont pas supprimées explicitement. Elles **s'évaporent** selon leur usage et leur âge :

```python
def should_evaporate(belief):
    age = now() - belief.timestamp
    return (age > HORIZON_AGE) and (belief.access_count < MIN_ACCESSES)
```

Une croyance peu consultée et ancienne disparaît naturellement — comme une information oubliée par un groupe de travail qui a passé à autre chose.

### 5.5 Alignement des Valeurs Numériques (Magnetar)

Pour les croyances numériques (prix, scores, probabilités), un mécanisme d'**alignement progressif** évite les oscillations :

```python
def align(values, strength=0.3):
    consensus = median(values)
    return [v + strength * (consensus - v) for v in values]
```

Les valeurs convergent doucement vers la médiane du Cluster sans écrasement brutal. La force d'alignement (`strength`) est configurable par fusion.

---

## 6. Le Format d'Empreinte (Fingerprint)

### 6.1 Philosophie

Quand un agent quitte un Cluster, il ne copie pas brutalement l'EMP. Il génère une **empreinte différenciée** — ce qu'il a réellement appris, filtré par sa perspective propre.

### 6.2 Structure de l'Empreinte

```json
{
  "fusion_id": "fus_8f3a9b",
  "agent_id": "agent-alpha-01",
  "generated_at": "2026-06-28T23:17:00Z",
  "source_agents": ["agent-beta", "agent-gamma"],

  "insights": [
    {
      "key": "risk_assessment_q2",
      "value": "Le risque tech est sous-estimé de 15%",
      "confidence": 0.89,
      "salience": 1.24,
      "learned_from": "agent-beta",
      "integration_type": "reinforced",
      "original_belief": "Le risque tech est stable"
    }
  ],

  "meta": {
    "fusion_class": "synchrone",
    "fusion_duration_ms": 45000,
    "termination_signal": "task_completed",
    "writes_contributed": 12,
    "writes_observed": 34,
    "beliefs_reinforced": 3,
    "beliefs_contradicted": 1,
    "beliefs_novel": 8
  }
}
```

### 6.3 Types d'Intégration

| Type | Description |
|------|-------------|
| `reinforced` | La fusion a confirmé une croyance préexistante. Sa confiance augmente. |
| `contradicted` | La fusion a contredit une croyance existante. L'agent doit résoudre le conflit en post-fusion. |
| `novel` | L'agent a découvert une croyance totalement absente de sa mémoire. |

### 6.4 Digestion en Mémoire Persistante

L'agent ne stocke pas l'empreinte brute. Il la **digère** en trois étapes :

1. **Filtrage** : ne conserver que les insights avec `confidence > SEUIL_AGENT` (personnalisable par agent).
2. **Réconciliation** : pour les `contradicted`, déclencher un processus interne de résolution (délégué à l'implémentation).
3. **Indexation** : intégrer les `novel` dans la mémoire vectorielle de l'agent pour récupération future.

---

## 7. Séparation & Cohérence

### 7.1 Le Modèle de Séparation Naturelle

La séparation d'un Cluster n'est pas un vote — c'est une **dissolution par entropie**, cohérente avec la métaphore biologique. Aucun agent ne peut bloquer la séparation indéfiniment.

```
Signaux de Séparation (premier reçu l'emporte) :

  1. TASK_COMPLETED   — n'importe quel participant déclare la tâche terminée
  2. SILENCE          — aucune écriture depuis SILENCE_THRESHOLD
  3. GUARDIAN_FORCE   — le Guardian impose la dissolution (urgence ou anomalie)
```

À réception d'un signal de séparation, chaque agent génère son empreinte **de manière indépendante et asynchrone**. Il n'y a pas d'attente inter-agents.

### 7.2 Le SplitManifest comme Attestation

Le `SplitManifest` est généré **après** la séparation, pas avant. C'est un enregistrement de ce qui s'est passé, pas une autorisation de se séparer.

```json
{
  "type": "craf.fusion.split",
  "fusion_id": "fus_8f3a9b",
  "termination_signal": "task_completed",
  "triggered_by": "agent-alpha",
  "final_beliefs_checksum": "sha256:abc123...",
  "separated_at": "2026-06-28T23:17:45Z",
  "participants_acknowledged": {
    "agent-alpha": "2026-06-28T23:17:45Z",
    "agent-beta": "2026-06-28T23:17:46Z"
  }
}
```

Les `participants_acknowledged` sont les horodatages de génération d'empreinte de chaque agent. Il n'y a pas de signature bloquante : un agent lent ou non-coopératif ne peut pas empêcher la dissolution du Cluster.

### 7.3 Cohérence Sémantique

CRAF ne garantit pas la cohérence séquentielle stricte (trop coûteux). Il garantit une **cohérence causale sémantique** :

- Si l'agent A écrit "Le marché s'effondre" et que l'agent B écrit "Acheter" en réponse causale, cette causalité est préservée dans le `write_log`.
- Si deux agents écrivent des croyances indépendantes sur des clés différentes, aucun ordre global n'est imposé.

---

## 8. Sécurité & Tolérance aux Pannes

### 8.1 Défense Structurelle (Phase 1)

La sécurité primaire de CRAF repose sur la **réciprocité structurelle** (section 4.3), pas sur la cryptographie. Un agent ne peut rejoindre un Cluster que si tous les membres actuels le déclarent explicitement compatible. C'est la barrière principale contre les fusions parasites.

### 8.2 Protection de l'EMP

| Mécanisme | Description |
|-----------|-------------|
| **Rate-limiting** | Un agent ne peut pas écrire plus de N croyances par seconde (N configurable). |
| **Divergence Detection** | Si un agent écrit des croyances qui contredisent systématiquement la médiane du Cluster, il est mis en quarantaine temporaire. |
| **Checksum EMP** | Vérification périodique de l'intégrité de l'EMP. En cas de mismatch, reconstruction depuis le `write_log`. |
| **Reputation Score** | Chaque agent porte un score de réputation historique. Un nouvel agent démarre avec des permissions limitées. |

### 8.3 Tolérance aux Pannes

| Scénario | Comportement |
|----------|--------------|
| **Agent mort pendant la fusion** | Timeout après `HEARTBEAT_INTERVAL`. Ses croyances restent dans l'EMP mais sont marquées `orphan`. Les survivants reçoivent automatiquement un signal `SILENCE` si aucune nouvelle écriture ne suit. |
| **Agent lent ou non-coopératif** | L'agent lent ne bloque pas la séparation. Le signal d'entropie (`SILENCE` ou `TASK_COMPLETED`) déclenche la dissolution sans lui. Son empreinte sera générée avec retard ou manquante — enregistré dans le `SplitManifest`. |
| **Partition réseau** | Le Cluster se scinde. Chaque sous-Cluster génère un `SplitManifest` indépendant. Réconciliation différée possible via A2A asynchrone. |
| **EMP corrompu** | Reconstruction complète depuis le `write_log`. |

### 8.4 Agents Gardiens (Guardian Agents)

Chaque Cluster peut inclure un **Guardian** — un agent sans rôle métier, dédié à :

- Surveiller la santé de l'EMP (checksum, rate-limit, divergence)
- Détecter les comportements anormaux
- Émettre un signal `GUARDIAN_FORCE` pour dissolution d'urgence
- Générer un rapport de santé post-fusion

Le Guardian ne participe pas à la cognition collective. Il observe. C'est le système immunitaire du Cluster.

### 8.5 Sécurité Cryptographique (Phase 3)

La cryptographie forte (DID, signatures, tolérance byzantine) est réservée à la Phase 3. La Phase 1 repose exclusivement sur la réciprocité structurelle.

---

## 9. Protocole Inter-Clusters (A2A Étendu)

### 9.1 Compatibilité A2A Native

CRAF est un **sur-ensemble d'A2A**, pas un remplacement. Les Clusters communiquent entre eux via les messages A2A standards :

- `task`, `artifact`, `status`, `update`

### 9.2 Messages CRAF Spécifiques

#### `FusionOffer`

```json
{
  "type": "craf.fusion.offer",
  "from": "agent-alpha",
  "to": "agent-beta",
  "proposed_context": "optimiser_portefeuille_tech_2026",
  "fusion_class": "synchrone",
  "emp_endpoint": "wss://cluster-alpha.internal/emp/fus_8f3a",
  "silence_threshold_ms": 30000,
  "timeout_ms": 5000
}
```

#### `FusionAccept`

```json
{
  "type": "craf.fusion.accept",
  "from": "agent-beta",
  "fusion_id": "fus_8f3a",
  "constraints": {
    "max_duration_ms": 60000,
    "max_writes_per_sec": 10,
    "align_strength": 0.3
  }
}
```

#### `FusionReject`

```json
{
  "type": "craf.fusion.reject",
  "from": "agent-beta",
  "fusion_id": "fus_8f3a",
  "reason": "incompatible_role | class_unsupported | capacity_full"
}
```

#### `SplitManifest`

```json
{
  "type": "craf.fusion.split",
  "fusion_id": "fus_8f3a",
  "termination_signal": "task_completed",
  "triggered_by": "agent-alpha",
  "final_beliefs_checksum": "sha256:...",
  "separated_at": "2026-06-28T23:17:45Z",
  "participants_acknowledged": {}
}
```

---

## 10. Exemples d'Usage

### Exemple 1 : Trading Algorithmique — Fusion Synchrone (3 agents)

**Cluster "Alpha-Desk"** — classe `synchrone`, latence < 100ms

- `AnalysteMacro` : lit les news, évalue la tendance macro
- `RiskManager` : calcule le VaR, les limites d'exposition
- `ExecutionTrader` : route les ordres, minimise le slippage

**Déroulé :**

1. Une news tombe : "La Fed hausse les taux de 25bp."
2. `AnalysteMacro` écrit dans l'EMP via événement : `belief(taux, "hausse", confidence=0.95)`
3. `RiskManager` reçoit l'événement (résonance sélective sur `taux`), recalcule le VaR, écrit : `belief(var_exposition, "+12%", confidence=0.92)`
4. `ExecutionTrader` reçoit l'événement sur `var_exposition`, ajuste sa stratégie, écrit : `belief(ordre_strategy, "passive_limit", confidence=0.88)`
5. Tout cela en **< 50ms**, sans un seul message A2A explicite.
6. `AnalysteMacro` émet `TASK_COMPLETED`. Séparation naturelle. Chaque agent génère son empreinte indépendamment.

### Exemple 2 : M&A Juridique & Finance — Deux Clusters, A2A inter-cluster

**Cluster "Finance"** (classe `coherente`) et **Cluster "Juridique"** (classe `coherente`) travaillent en parallèle sur une acquisition.

- Finance fusionne en interne pour évaluer le prix cible (3 agents : M&A Analyst, Valorisation, Synergies).
- Juridique fusionne en interne pour rédiger les clauses (3 agents : Corporate, Fiscal, Due Diligence).
- Toutes les 5 minutes, chaque Cluster publie un `artifact` A2A résumant son état courant.
- Les deux Clusters consomment les artifacts de l'autre sans fusionner — ils ont des domaines trop différents pour une fusion directe.

---

## 11. Implémentation de Référence

### 11.1 Stack Technique Recommandée

| Couche | Technologie | Rôle |
|--------|-------------|------|
| Transport | WebSocket (synchrone) / QUIC (cohérente) | Canal temps réel de l'EMP |
| CRDT | Yjs / Automerge (étendu) | Synchronisation sans verrou du state |
| Événements | CRDT change events / WebSocket push | Résonance sélective — jamais de polling |
| Sérialisation | JSON + MessagePack | Messages et croyances |
| Orchestration | Rust / Go | Performance du nœud de fusion |
| Sécurité Phase 3 | DID (Decentralized Identifiers) | Identité et réputation (futur) |

### 11.2 Pseudo-code du Nœud de Fusion

```python
class FusionNode:
    def __init__(self, agent_id, competence_vector):
        self.agent_id = agent_id
        self.competence = competence_vector
        self.active_fusions = {}       # fusion_id -> EMP
        self.memory = PersistentMemory()

    # --- Découverte & Handshake ---

    def discover(self, other_agent):
        if is_compatible(self.competence, other_agent.competence):
            self.propose_fusion(other_agent)

    def is_compatible(self, my_vector, other_vector):
        """Règle structurelle — pas de score numérique."""
        return (
            other_vector.role in my_vector.fusion_profile.complements_with
            and my_vector.role in other_vector.fusion_profile.complements_with
            and not (other_vector.role in my_vector.fusion_profile.conflicts_with)
            and domains_overlap_partially(my_vector.domain, other_vector.domain)
        )

    def join_fusion(self, fusion_id, emp_endpoint, fusion_class):
        emp = connect(emp_endpoint, transport=transport_for(fusion_class))
        emp.on_relevant_event(self._on_belief_received)   # événement, pas polling
        self.active_fusions[fusion_id] = emp

    # --- Résonance Sélective ---

    def _on_belief_received(self, belief):
        """Appelé par push événement — jamais par polling."""
        if belief.key in self.competence.domain or belief.key in self.competence.skills:
            self._react(belief)

    def _react(self, belief):
        """L'agent réagit à une croyance pertinente."""
        pass  # Logique métier de l'agent

    # --- Écriture dans l'EMP ---

    def write_belief(self, fusion_id, key, value, confidence):
        emp = self.active_fusions[fusion_id]
        emp.write(
            agent_id=self.agent_id,
            key=key,
            value=value,
            confidence=confidence
        )

    # --- Séparation ---

    def signal_task_complete(self, fusion_id):
        emp = self.active_fusions[fusion_id]
        emp.emit_signal("TASK_COMPLETED", source=self.agent_id)
        # La séparation se déclenche côté EMP
        # L'agent attend l'événement SEPARATION_INITIATED

    def _on_separation(self, fusion_id):
        """Appelé par l'EMP quand un signal de séparation est reçu."""
        emp = self.active_fusions[fusion_id]
        fingerprint = emp.generate_fingerprint(self.agent_id)
        self.memory.integrate(fingerprint)
        del self.active_fusions[fusion_id]
```

### 11.3 Calcul de la Salience

```python
import math

def compute_salience(confidence: float, access_count: int) -> float:
    """
    salience = confidence × log(1 + access_count)
    Une croyance confiante ET fréquemment consultée a plus de poids.
    """
    return confidence * math.log(1 + access_count)

def resolve_conflict(belief_a: Belief, belief_b: Belief) -> Belief:
    """Résolution de conflit lors de la convergence."""
    if belief_a.salience != belief_b.salience:
        return belief_a if belief_a.salience > belief_b.salience else belief_b
    # À salience égale : le plus récent l'emporte
    return belief_a if belief_a.timestamp > belief_b.timestamp else belief_b
```

---

## 12. Roadmap

### Phase 1 — Preuve de Concept (0.1.0) — En cours

- [ ] Protocole de Handshake minimal (FusionOffer / FusionAccept / FusionReject)
- [ ] EMP à 2 agents sur WebSocket
- [ ] Modèle événementiel — résonance sélective (pas de polling)
- [ ] Génération d'empreinte basique (reinforced / contradicted / novel)
- [ ] Séparation naturelle par entropie (TASK_COMPLETED + SILENCE)
- [ ] Benchmark vs A2A séquentiel

### Phase 2 — Clustering (0.2.0)

- [ ] Jusqu'à 5 agents par Cluster
- [ ] Classes de fusion (Synchrone / Cohérente)
- [ ] Garbage collection naturelle (evaporation)
- [ ] Guardian Agent
- [ ] Alignement numérique Magnetar
- [ ] Règle de réciprocité globale à l'entrée d'un nouveau membre

### Phase 3 — Écosystème (0.3.0)

- [ ] Inter-Cluster A2A natif enrichi
- [ ] Réputation décentralisée (DID)
- [ ] Tolérance byzantine
- [ ] Adaptateurs : ADK, AutoGen, CrewAI, LangGraph

### Phase 4 — Standard (1.0.0)

- [ ] Soumission à la communauté A2A
- [ ] Spécification IETF (RFC draft)
- [ ] Implémentations de référence en Python, TypeScript, Rust

---

## 13. Glossaire

| Terme | Définition |
|-------|------------|
| **Cluster** | Groupe d'agents en fusion temps réel autour d'un EMP commun. |
| **EMP** | Espace de Mémoire Partagée. Le "cytoplasme cognitif" du Cluster. |
| **Fusion** | Phase où deux agents ou plus partagent un EMP commun et pensent collectivement. |
| **Séparation Naturelle** | Dissolution du Cluster par entropie — déclenchée par un signal, pas par un vote. |
| **Empreinte (Fingerprint)** | Sous-ensemble différencié de l'EMP intégré par un agent à sa mémoire persistante après la séparation. |
| **Croyance (Belief)** | Unité de connaissance dans l'EMP (clé, valeur, confiance, source, salience). |
| **Salience** | `confidence × log(1 + access_count)`. Mesure de l'importance effective d'une croyance — combine sa valeur sémantique et son usage réel dans le Cluster. |
| **Résonance Sélective** | Principe par lequel un agent ne réagit qu'aux croyances relevant de son domaine de compétence — via événement, jamais par polling. |
| **Complémentarité Structurelle** | Compatibilité binaire entre deux agents, définie par des règles déclaratives réciproques — pas par un score numérique. |
| **Classe de Fusion** | Engagement contractuel de latence : Synchrone (< 100ms) ou Cohérente (< 2s). |
| **Guardian** | Agent non-métier dédié à la surveillance de la santé du Cluster. Le système immunitaire du tissu. |
| **Magnetar** | Mécanisme d'alignement progressif des valeurs numériques vers la médiane du Cluster. |
| **CRDT** | Conflict-free Replicated Data Type. Algorithme de synchronisation sans verrou central. |
| **SplitManifest** | Attestation post-hoc de la dissolution d'un Cluster. Enregistrement, pas autorisation. |

---

## 14. Références

- Google A2A Protocol (2026)
- Anthropic MCP — Model Context Protocol
- Yjs CRDT Framework — https://yjs.dev
- Automerge — https://automerge.org
- Kai Li, *"IVY: A Shared Virtual Memory System for Parallel Computing"* (1986)
- TreadMarks DSM — Rice University
- Inspiration biologique : Mycelial networks, Neural coherence, Swarm intelligence, Hormonal signaling

---

> *"La pensée n'est pas séquentielle. Elle est distribuée, parallèle, et émergente.*
> *CRAF est le protocole qui permet aux agents de penser — vraiment — ensemble."*
