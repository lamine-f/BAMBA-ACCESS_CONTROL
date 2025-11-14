# KPIs - Système de Contrôle d'Accès pour Étudiants

## Table des matières
1. [Introduction](#introduction)
2. [KPIs Opérationnels](#kpis-opérationnels)
3. [KPIs Académiques](#kpis-académiques)
4. [KPIs Financiers](#kpis-financiers)
5. [KPIs de Sécurité](#kpis-de-sécurité)
6. [KPIs de Gestion](#kpis-de-gestion)
7. [Dashboard Recommandé](#dashboard-recommandé)
8. [Alertes et Seuils Critiques](#alertes-et-seuils-critiques)

---

## Introduction

Ce document définit l'ensemble des **Indicateurs Clés de Performance (KPIs)** pour le système de contrôle d'accès des étudiants. Ces KPIs permettent de :

- **Mesurer** l'efficacité opérationnelle du système
- **Suivre** l'assiduité et la présence des étudiants
- **Monitorer** les aspects financiers (paiements, impayés)
- **Garantir** la sécurité et la traçabilité des accès
- **Optimiser** la gestion administrative

Les KPIs sont organisés en **5 catégories principales** avec un total de **20 indicateurs** couvrant tous les aspects critiques du système.

---

## KPIs Opérationnels

Ces indicateurs mesurent la performance technique et la fiabilité du système.

### KPI-OP-01 : Taux de Disponibilité du Système

**Objectif :** Garantir la disponibilité continue du système de contrôle d'accès

| Critère | Détail |
|---------|--------|
| **Formule** | `(Temps de fonctionnement / Temps total) × 100` |
| **Objectif** | ≥ 99.5% |
| **Seuil critique** | < 95% |
| **Fréquence de mesure** | Temps réel / Quotidien / Hebdomadaire |
| **Source de données** | Logs système, monitoring serveur |
| **Responsable** | Administrateur technique / IT |

**Calcul détaillé :**
```
Temps total = 24h × 7 jours = 168 heures/semaine
Temps d'arrêt acceptable = 168h × 0.5% = 0.84h ≈ 50 minutes/semaine

Taux disponibilité = ((168h - Temps d'arrêt) / 168h) × 100
```

**Importance :** CRITIQUE - Le système doit être opérationnel en permanence pour ne pas bloquer l'accès des étudiants.

**Actions si hors objectif :**
- < 99% : Investigation immédiate des causes
- < 95% : Alerte prioritaire, intervention urgente
- Plan de continuité à activer si nécessaire

---

### KPI-OP-02 : Temps de Réponse Moyen du Système

**Objectif :** Assurer une fluidité des contrôles d'accès sans ralentissement

| Critère | Détail |
|---------|--------|
| **Formule** | `Moyenne(Temps entre présentation badge et décision)` |
| **Objectif** | ≤ 2 secondes |
| **Seuil critique** | > 5 secondes |
| **Fréquence de mesure** | Temps réel |
| **Source de données** | Logs des transactions de contrôle |
| **Responsable** | Administrateur système |

**Décomposition du temps de réponse :**
- Lecture badge : 0.1-0.3s
- Requête BD : 0.2-0.5s
- Vérifications : 0.3-0.7s
- Affichage résultat : 0.1-0.2s
- **Total optimal :** 0.7-1.7s

**Importance :** HAUTE - Impact direct sur l'expérience utilisateur et la fluidité des entrées

**Actions si hors objectif :**
- 2-5s : Optimisation requêtes BD, vérification réseau
- > 5s : Investigation urgente, possible problème matériel/logiciel

---

### KPI-OP-03 : Nombre de Passages par Jour

**Objectif :** Suivre l'activité quotidienne et détecter les anomalies

| Critère | Détail |
|---------|--------|
| **Formule** | `COUNT(Passages autorisés WHERE date = aujourd'hui)` |
| **Objectif** | Suivi de tendance (pas d'objectif fixe) |
| **Seuil critique** | Variation > 30% vs moyenne |
| **Fréquence de mesure** | Quotidien |
| **Source de données** | Table Passage |
| **Responsable** | Administrateur académique |

**Analyse par plage horaire :**
- 07h00-08h00 : Pic matinal (40-50% des passages)
- 08h00-10h00 : Arrivées tardives (30-40%)
- 10h00-12h00 : Retardataires (10-20%)
- Après 12h00 : Cas exceptionnels (< 5%)

**Importance :** MOYENNE - Permet de détecter événements exceptionnels (grève, vacances non planifiées, problème système)

---

### KPI-OP-04 : Taux d'Accès Refusés

**Objectif :** Mesurer la proportion d'échecs de contrôle d'accès

| Critère | Détail |
|---------|--------|
| **Formule** | `(Passages refusés / Total tentatives d'accès) × 100` |
| **Objectif** | ≤ 5% (hors début de trimestre) |
| **Seuil critique** | > 15% |
| **Fréquence de mesure** | Quotidien / Hebdomadaire |
| **Source de données** | Table Passage (résultat = REFUSE_*) |
| **Responsable** | Responsable pédagogique + Admin système |

**Répartition typique des refus :**
- Paiement manquant : 70-80%
- Badge bloqué/inactif : 10-15%
- Badge invalide/inconnu : 5-10%
- Étudiant suspendu : 5%

**Importance :** HAUTE - Indicateur de problèmes de paiement ou techniques

**Actions si hors objectif :**
- Analyse des causes de refus
- Campagne de rappel paiements si nécessaire
- Vérification technique si badges invalides nombreux

---

## KPIs Académiques

Ces indicateurs mesurent l'assiduité et la présence des étudiants.

### KPI-AC-01 : Taux de Présence Global

**Objectif :** Mesurer l'assiduité générale de l'établissement

| Critère | Détail |
|---------|--------|
| **Formule** | `(Nombre d'étudiants présents / Total étudiants inscrits actifs) × 100` |
| **Objectif** | ≥ 85% |
| **Seuil critique** | < 70% |
| **Fréquence de mesure** | Quotidien / Hebdomadaire / Mensuel |
| **Source de données** | Table Passage + Table Etudiant |
| **Responsable** | Direction pédagogique |

**Calcul détaillé :**
```sql
-- Pour un jour donné
SELECT
  (COUNT(DISTINCT p.idEtudiant) /
   (SELECT COUNT(*) FROM Etudiant WHERE statut = 'ACTIF')) * 100
  AS taux_presence
FROM Passage p
WHERE DATE(p.dateHeurePassage) = '2024-XX-XX'
  AND p.resultat = 'AUTORISE'
```

**Variations saisonnières attendues :**
- Septembre-Octobre : 90-95% (début d'année)
- Novembre-Mars : 85-90% (normal)
- Avril-Juin : 75-85% (fin d'année, examens)

**Importance :** TRÈS HAUTE - Indicateur principal de la santé académique de l'établissement

---

### KPI-AC-02 : Taux de Présence par Classe

**Objectif :** Identifier les classes problématiques nécessitant une attention particulière

| Critère | Détail |
|---------|--------|
| **Formule** | `(Étudiants présents classe X / Total étudiants classe X) × 100` |
| **Objectif** | ≥ 85% par classe |
| **Seuil critique** | < 70% pour une classe |
| **Fréquence de mesure** | Hebdomadaire / Mensuel |
| **Source de données** | Tables Passage + Etudiant + Classe |
| **Responsable** | Chef d'établissement + Professeurs principaux |

**Analyse comparative :**
```
Classe A : 92% ✓
Classe B : 87% ✓
Classe C : 68% ⚠ → Action requise
Classe D : 90% ✓
```

**Importance :** HAUTE - Permet une intervention ciblée sur les classes en difficulté

**Actions si hors objectif :**
- Réunion avec professeur principal
- Analyse des causes (problème de paiement, démotivation, etc.)
- Plan d'action correctif

---

### KPI-AC-03 : Nombre d'Absences Consécutives par Étudiant

**Objectif :** Détecter précocement le décrochage scolaire

| Critère | Détail |
|---------|--------|
| **Formule** | `Jours ouvrables consécutifs sans passage pour un étudiant` |
| **Objectif** | Détection et alerte |
| **Seuil d'alerte** | ≥ 3 jours consécutifs |
| **Seuil critique** | ≥ 7 jours consécutifs |
| **Fréquence de mesure** | Temps réel / Quotidien |
| **Source de données** | Table Passage (absence de ligne) |
| **Responsable** | Responsable vie scolaire |

**Système d'alerte automatique :**
- **3 jours :** Notification au professeur principal
- **5 jours :** Appel téléphonique aux parents
- **7 jours :** Convocation obligatoire + alerte direction
- **10 jours+ :** Procédure décrochage scolaire

**Importance :** TRÈS HAUTE - Détection précoce des cas de décrochage

---

### KPI-AC-04 : Heure Moyenne d'Arrivée par Classe

**Objectif :** Mesurer la ponctualité des étudiants

| Critère | Détail |
|---------|--------|
| **Formule** | `MOYENNE(Heure du premier passage quotidien)` |
| **Objectif** | Avant 08h00 (selon règlement) |
| **Seuil critique** | Moyenne > 08h30 |
| **Fréquence de mesure** | Hebdomadaire |
| **Source de données** | Table Passage (premier passage du jour) |
| **Responsable** | Vie scolaire |

**Analyse des retards :**
- À l'heure (< 08h00) : 70-80%
- Retard léger (08h00-08h15) : 15-20%
- Retard important (> 08h15) : 5-10%

**Importance :** MOYENNE - Indicateur de discipline et organisation

---

## KPIs Financiers

Ces indicateurs suivent les aspects financiers et les paiements.

### KPI-FI-01 : Taux de Paiement par Trimestre

**Objectif :** Mesurer le taux de recouvrement des frais de scolarité

| Critère | Détail |
|---------|--------|
| **Formule** | `(Étudiants ayant payé trimestre X / Total étudiants actifs) × 100` |
| **Objectif** | 100% (idéal) / 95% (réaliste) |
| **Seuil critique** | < 80% |
| **Fréquence de mesure** | Par trimestre (suivi hebdomadaire) |
| **Source de données** | Table PaiementTrimestre |
| **Responsable** | Responsable financier |

**Évolution typique d'un trimestre :**
```
Semaine 1 : 30-40%
Semaine 2 : 60-70%
Semaine 3 : 80-85%
Semaine 4 : 90-95%
Fin trimestre : 95-98%
```

**Calcul détaillé :**
```sql
SELECT
  (COUNT(DISTINCT idEtudiant) /
   (SELECT COUNT(*) FROM Etudiant WHERE statut = 'ACTIF')) * 100
FROM PaiementTrimestre
WHERE idTrimestre = @trimestreEnCours
  AND statut IN ('PAYE', 'PARTIEL')
```

**Importance :** TRÈS HAUTE - Impact direct sur le budget de l'établissement

---

### KPI-FI-02 : Montant Total des Impayés

**Objectif :** Suivre le montant des créances en cours

| Critère | Détail |
|---------|--------|
| **Formule** | `SOMME(Montant trimestre - Montant payé) pour tous impayés` |
| **Objectif** | Minimiser |
| **Seuil critique** | > 20% du budget trimestriel |
| **Fréquence de mesure** | Hebdomadaire / Mensuel |
| **Source de données** | Tables PaiementTrimestre + Trimestre |
| **Responsable** | Direction financière |

**Calcul :**
```
Impayés = SOMME(
  (Montant trimestre × Nombre d'étudiants impayés) +
  (Montant trimestre - Montant payé pour paiements partiels)
)
```

**Catégorisation des impayés :**
- Impayés récents (< 1 mois) : Relance douce
- Impayés moyens (1-2 mois) : Relance ferme
- Impayés anciens (> 2 mois) : Procédure contentieuse

**Importance :** TRÈS HAUTE - Gestion de trésorerie et viabilité financière

---

### KPI-FI-03 : Délai Moyen de Paiement

**Objectif :** Mesurer la rapidité de recouvrement

| Critère | Détail |
|---------|--------|
| **Formule** | `MOYENNE(Date paiement - Date début trimestre) en jours` |
| **Objectif** | ≤ 15 jours |
| **Seuil critique** | > 30 jours |
| **Fréquence de mesure** | Par trimestre |
| **Source de données** | Tables PaiementTrimestre + Trimestre |
| **Responsable** | Service comptabilité |

**Benchmark :**
- Excellent : < 10 jours
- Bon : 10-15 jours
- Acceptable : 15-25 jours
- Problématique : > 25 jours

**Importance :** HAUTE - Indicateur de solvabilité et de discipline financière

---

### KPI-FI-04 : Taux de Paiements Partiels

**Objectif :** Identifier les difficultés financières des familles

| Critère | Détail |
|---------|--------|
| **Formule** | `(Nombre paiements partiels / Total paiements) × 100` |
| **Objectif** | Suivi de tendance (< 10% idéal) |
| **Seuil d'alerte** | > 20% |
| **Fréquence de mesure** | Par trimestre |
| **Source de données** | Table PaiementTrimestre (statut = PARTIEL) |
| **Responsable** | Service social / Direction |

**Importance :** MOYENNE - Indicateur social, peut nécessiter mise en place d'aides

---

## KPIs de Sécurité

Ces indicateurs garantissent la sécurité et la traçabilité des accès.

### KPI-SE-01 : Nombre de Tentatives avec Badge Invalide

**Objectif :** Détecter les tentatives frauduleuses ou problèmes techniques

| Critère | Détail |
|---------|--------|
| **Formule** | `COUNT(Passages avec resultat = REFUSE_BADGE_INVALIDE)` |
| **Objectif** | Minimal (< 5 tentatives/jour) |
| **Seuil d'alerte** | > 10 tentatives/jour |
| **Seuil critique** | > 20 tentatives/jour ou multiples sur même badge |
| **Fréquence de mesure** | Temps réel / Quotidien |
| **Source de données** | Table Passage |
| **Responsable** | Responsable sécurité |

**Analyse des causes :**
- Badge non enregistré (erreur admin) : 40%
- Badge endommagé : 30%
- Tentative de fraude : 20%
- Erreur de lecture : 10%

**Importance :** HAUTE - Sécurité et détection de fraudes

**Actions :**
- Tentative isolée : Investigation standard
- Tentatives répétées : Alerte sécurité immédiate
- Pattern suspect : Renforcement surveillance

---

### KPI-SE-02 : Nombre de Badges Bloqués/Perdus/Volés

**Objectif :** Suivre le parc de badges et les incidents

| Critère | Détail |
|---------|--------|
| **Formule** | `COUNT(Badges WHERE statut IN ('BLOQUE', 'PERDU', 'VOLE'))` |
| **Objectif** | ≤ 2% du parc total de badges |
| **Seuil critique** | > 5% |
| **Fréquence de mesure** | Mensuel |
| **Source de données** | Table Badge |
| **Responsable** | Gestionnaire badges |

**Répartition typique :**
- Badges perdus : 60-70%
- Badges bloqués : 20-30%
- Badges volés : 5-10%

**Coût associé :**
- Coût remplacement badge : X€
- Impact total = Nombre badges × Coût unitaire

**Importance :** MOYENNE - Gestion du parc et budget

---

### KPI-SE-03 : Taux d'Utilisation des Badges Visiteurs

**Objectif :** Suivre et tracer les visites externes

| Critère | Détail |
|---------|--------|
| **Formule** | `COUNT(Badges visiteurs actifs par jour)` |
| **Objectif** | Suivi de tendance |
| **Seuil d'alerte** | Variation anormale > 200% vs moyenne |
| **Fréquence de mesure** | Quotidien |
| **Source de données** | Tables Badge + Visiteur + Passage |
| **Responsable** | Service accueil |

**Statistiques attendues :**
- Moyenne quotidienne : 5-15 visiteurs
- Jours événements : 30-50 visiteurs
- Durée moyenne visite : 2-4 heures

**Importance :** MOYENNE - Traçabilité et sécurité des visiteurs

---

## KPIs de Gestion

Ces indicateurs optimisent la gestion administrative du système.

### KPI-GE-01 : Nombre d'Étudiants Actifs

**Objectif :** Suivre l'effectif réel de l'établissement

| Critère | Détail |
|---------|--------|
| **Formule** | `COUNT(Etudiants WHERE statut = 'ACTIF')` |
| **Objectif** | Suivi d'évolution (capacité établissement) |
| **Fréquence de mesure** | Mensuel |
| **Source de données** | Table Etudiant |
| **Responsable** | Secrétariat pédagogique |

**Évolution annuelle typique :**
```
Sept : +15% (nouvelles inscriptions)
Oct-Mai : Stable (±2%)
Juin : -10% (fins de cycle)
```

**Importance :** HAUTE - Dimensionnement du système et ressources

---

### KPI-GE-02 : Taux d'Utilisation du Système

**Objectif :** Vérifier que tous les étudiants actifs utilisent leur badge

| Critère | Détail |
|---------|--------|
| **Formule** | `(Étudiants ayant badgé sur période / Total étudiants actifs) × 100` |
| **Objectif** | ≥ 90% |
| **Seuil d'alerte** | < 80% |
| **Fréquence de mesure** | Hebdomadaire |
| **Source de données** | Tables Passage + Etudiant |
| **Responsable** | Administrateur système |

**Analyse des non-utilisateurs :**
- Badges non distribués : 40%
- Étudiants en congé non déclaré : 30%
- Badges défectueux : 20%
- Autres causes : 10%

**Importance :** HAUTE - Détection de badges non distribués ou problèmes techniques

---

### KPI-GE-03 : Nombre d'Actions Administrateur par Jour

**Objectif :** Mesurer la charge de travail administrative

| Critère | Détail |
|---------|--------|
| **Formule** | `COUNT(Actions dans HistoriqueModification par jour)` |
| **Objectif** | Suivi de tendance |
| **Seuil d'alerte** | Variation > 200% vs moyenne |
| **Fréquence de mesure** | Quotidien |
| **Source de données** | Table HistoriqueModification |
| **Responsable** | Superviseur administratif |

**Types d'actions :**
- Création étudiants : 20%
- Enregistrement paiements : 40%
- Gestion badges : 15%
- Génération rapports : 10%
- Modifications diverses : 15%

**Importance :** MOYENNE - Optimisation des processus et charge de travail

---

### KPI-GE-04 : Temps Moyen de Traitement d'une Inscription

**Objectif :** Optimiser l'efficacité administrative

| Critère | Détail |
|---------|--------|
| **Formule** | `MOYENNE(Durée inscription complète)` |
| **Objectif** | ≤ 10 minutes |
| **Seuil critique** | > 20 minutes |
| **Fréquence de mesure** | Lors des périodes d'inscription |
| **Source de données** | Logs système + Chronométrage |
| **Responsable** | Chef de service administratif |

**Décomposition du temps :**
- Saisie informations : 3-5 min
- Upload photo : 1 min
- Attribution badge : 2 min
- Validation et tests : 2 min
- **Total :** 8-10 min

**Importance :** MOYENNE - Efficacité administrative

---

### KPI-GE-05 : Taux de Complétude des Données

**Objectif :** Garantir la qualité des données dans le système

| Critère | Détail |
|---------|--------|
| **Formule** | `(Étudiants avec tous champs remplis / Total étudiants) × 100` |
| **Objectif** | ≥ 95% |
| **Seuil critique** | < 85% |
| **Fréquence de mesure** | Mensuel |
| **Source de données** | Table Etudiant |
| **Responsable** | Responsable qualité des données |

**Champs critiques à vérifier :**
- Nom, Prénom : 100% (obligatoire)
- Photo : 90%+
- Email : 85%+
- Téléphone : 90%+
- Classe : 100% (obligatoire)

**Importance :** HAUTE - Qualité des données et fiabilité du système

---

## Dashboard Recommandé

### Vue Temps Réel (Affichage permanent)

```
┌─────────────────────────────────────────────────────────────┐
│            SYSTÈME DE CONTRÔLE D'ACCÈS - AUJOURD'HUI        │
├─────────────────────────────────────────────────────────────┤
│  Étudiants présents : 1,247 / 1,350  (92.4%) ✓             │
│  Passages autorisés : 1,250                                 │
│  Passages refusés   : 18 (1.4%)      ✓                     │
│  Statut système     : OPÉRATIONNEL   ✓                     │
│  Temps réponse moy. : 1.2s           ✓                     │
├─────────────────────────────────────────────────────────────┤
│  🔴 ALERTES ACTIVES : 2                                     │
│    • Classe 3B : Taux présence 68% ⚠                       │
│    • 3 étudiants : 5+ jours absence consécutive ⚠          │
└─────────────────────────────────────────────────────────────┘
```

### Vue Hebdomadaire

**Indicateurs à afficher :**
- Taux de présence par classe (graphique en barres)
- Évolution présence quotidienne (courbe)
- Top 10 absences consécutives
- Nombre de refus d'accès par motif (camembert)

### Vue Trimestrielle

**Indicateurs financiers :**
- Taux de paiement : 94% (Objectif : 95%)
- Montant impayés : 12,500€
- Délai moyen paiement : 18 jours
- Paiements partiels : 8%

**Indicateurs académiques :**
- Taux présence moyen : 87%
- Nombre d'abandons : 5
- Heure moyenne arrivée : 07h52

---

## Alertes et Seuils Critiques

### Système d'Alerte à 3 Niveaux

#### 🟢 VERT : Normal
- Tous les KPIs dans les objectifs
- Aucune action requise
- Monitoring standard

#### 🟠 ORANGE : Attention
- 1 ou plusieurs KPIs hors objectif mais > seuil critique
- Investigation recommandée
- Surveillance renforcée

**Exemples :**
- Taux présence : 80-85% (objectif : ≥85%)
- Temps réponse : 2-5s (objectif : ≤2s)
- Taux paiement : 85-95% (objectif : ≥95%)

#### 🔴 ROUGE : Critique
- 1 ou plusieurs KPIs en dessous seuil critique
- Action immédiate requise
- Escalade hiérarchique

**Exemples :**
- Disponibilité système : < 95%
- Taux présence : < 70%
- Taux paiement : < 80%
- Temps réponse : > 5s

### Configuration des Notifications

**Notifications temps réel :**
- Badge invalide répété (>3 tentatives même badge)
- Disponibilité système < 98%
- Temps réponse > 5s

**Notifications quotidiennes :**
- Rapport présence du jour
- Liste nouvelles absences consécutives (≥3 jours)
- Tentatives d'accès frauduleuses

**Notifications hebdomadaires :**
- Synthèse KPIs académiques
- État des impayés
- Performance système

**Notifications mensuelles :**
- Rapport complet tous KPIs
- Analyse des tendances
- Recommandations d'amélioration

---

## Conclusion

Ces 20 KPIs couvrent l'ensemble des aspects critiques du système de contrôle d'accès :

- **4 KPIs Opérationnels** : Performance technique
- **4 KPIs Académiques** : Assiduité et présence
- **4 KPIs Financiers** : Paiements et recouvrement
- **3 KPIs de Sécurité** : Traçabilité et incidents
- **5 KPIs de Gestion** : Efficacité administrative

**Recommandations d'implémentation :**

1. **Phase 1 (MVP)** : Implémenter les KPIs critiques (marqués TRÈS HAUTE importance)
2. **Phase 2** : Ajouter les KPIs de haute importance
3. **Phase 3** : Compléter avec tous les KPIs

**Outils nécessaires :**
- Dashboard temps réel (affichage écran dédié)
- Système d'alertes (email, SMS, notifications)
- Rapports automatisés (quotidiens, hebdomadaires, mensuels)
- Exports pour analyses (Excel, PDF)

**Révision et ajustement :**
- Révision trimestrielle des objectifs et seuils
- Ajustement selon contexte et retour d'expérience
- Ajout de nouveaux KPIs si besoins identifiés
