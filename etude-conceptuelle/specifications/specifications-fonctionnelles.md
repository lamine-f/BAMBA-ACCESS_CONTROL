# Spécifications Fonctionnelles - Système de Contrôle d'Accès pour Étudiants

## Table des matières
1. [Vue d'ensemble du projet](#vue-densemble-du-projet)
2. [Acteurs du système](#acteurs-du-système)
3. [Fonctionnalités détaillées](#fonctionnalités-détaillées)
4. [Processus métier](#processus-métier)
5. [Règles de gestion](#règles-de-gestion)
6. [Contraintes et exigences](#contraintes-et-exigences)
7. [Interfaces utilisateur](#interfaces-utilisateur)

---

## Vue d'ensemble du projet

### Contexte

Le système de contrôle d'accès pour étudiants est une solution automatisée destinée à gérer l'entrée des étudiants dans un établissement d'enseignement. Il repose sur l'utilisation de badges électroniques et d'une application connectée à une base de données centralisée.

### Objectifs principaux

1. **Automatiser** le contrôle d'accès des étudiants
2. **Sécuriser** les entrées de l'établissement
3. **Tracer** les présences et absences
4. **Conditionner** l'accès au paiement des trimestres
5. **Faciliter** la gestion administrative
6. **Générer** des rapports de présence et statistiques

### Périmètre fonctionnel

**Inclus dans le projet :**
- Gestion des étudiants (CRUD complet)
- Gestion des badges (attribution, activation, blocage)
- Contrôle d'accès automatisé basé sur badge
- Gestion des paiements trimestriels
- Gestion des trimestres académiques
- Gestion des visiteurs temporaires
- Génération de rapports et statistiques
- Historique complet des passages
- Interface administrateur

**Hors périmètre :**
- Gestion de la paie du personnel
- Gestion des cours et emplois du temps
- Gestion des notes et évaluations
- Système de messagerie interne
- Gestion comptabilité complète (uniquement paiements trimestres)

---

## Acteurs du système

### 1. Étudiant

**Rôle :** Utilisateur principal du système pour l'accès quotidien

**Interactions avec le système :**
- Présente son badge au point de contrôle
- Reçoit une confirmation visuelle/sonore d'accès
- Visualise ses informations (nom, classe, heure) sur l'écran

**Caractéristiques :**
- Possède un badge RFID/NFC personnel
- Enregistré dans la base de données
- Soumis à la condition de paiement trimestriel

### 2. Visiteur

**Rôle :** Personne externe accédant temporairement à l'établissement

**Interactions avec le système :**
- Utilise un badge visiteur temporaire
- Accès limité dans le temps (date début/fin)
- Passages enregistrés pour traçabilité

**Caractéristiques :**
- Badge temporaire sans condition de paiement
- Validité limitée (durée de visite)
- Enregistrement du motif de visite

### 3. Administrateur

**Rôle :** Gestionnaire du système et des données

**Interactions avec le système :**
- Gère les étudiants (création, modification, suppression)
- Gère les badges (attribution, activation, désactivation)
- Enregistre les paiements
- Gère les trimestres
- Consulte les historiques
- Génère les rapports
- Configure le système

**Niveaux d'accès :**
- **Super Admin :** Accès complet + configuration système
- **Admin :** Gestion courante (étudiants, paiements, badges)
- **Opérateur :** Consultation et génération de rapports uniquement

### 4. Système de Badge (Acteur technique)

**Rôle :** Lecteur automatique de badges

**Fonctions :**
- Lit le numéro de badge
- Communique avec le système central
- Affiche les résultats visuellement
- Contrôle le dispositif d'accès (porte/barrière)

### 5. Base de Données (Acteur technique)

**Rôle :** Stockage centralisé des données

**Fonctions :**
- Stocke toutes les informations (étudiants, badges, passages, etc.)
- Répond aux requêtes du système
- Garantit la persistance des données

---

## Fonctionnalités détaillées

### Module 1 : Gestion des Étudiants

#### F1.1 - Créer un étudiant

**Description :** Permet d'enregistrer un nouvel étudiant dans le système

**Acteur :** Administrateur

**Données nécessaires :**
- **Obligatoires :** Nom, Prénom, Date de naissance, Classe
- **Optionnelles :** Email, Téléphone, Photo, Adresse

**Processus :**
1. Admin accède au formulaire de création
2. Saisit les informations de l'étudiant
3. Upload la photo (optionnel)
4. Sélectionne la classe
5. Valide le formulaire
6. Système génère un ID unique
7. Système enregistre dans la BD
8. Confirmation affichée

**Règles de validation :**
- Nom et prénom : Alphabétique, 2-50 caractères
- Date de naissance : Format valide, âge cohérent (6-25 ans)
- Email : Format email valide
- Photo : JPG/PNG, max 2 MB, résolution recommandée 300×400px
- Classe : Doit exister dans le système

**Résultat :** Étudiant créé avec statut "Inscrit"

---

#### F1.2 - Modifier un étudiant

**Description :** Permet de mettre à jour les informations d'un étudiant existant

**Acteur :** Administrateur

**Champs modifiables :**
- Nom, Prénom
- Date de naissance
- Classe
- Email, Téléphone
- Photo
- Statut (Actif, Inactif, Suspendu, En congé)

**Champs non modifiables :**
- ID étudiant (clé primaire)
- Date d'inscription (historique)

**Processus :**
1. Admin recherche l'étudiant
2. Affiche la fiche complète
3. Modifie les champs nécessaires
4. Valide les modifications
5. Système enregistre l'historique de modification
6. Confirmation affichée

**Traçabilité :** Toute modification est enregistrée dans l'historique avec :
- Utilisateur ayant effectué la modification
- Date et heure
- Champs modifiés (avant/après)

---

#### F1.3 - Supprimer un étudiant

**Description :** Permet de supprimer un étudiant du système

**Acteur :** Administrateur (niveau Admin minimum)

**Type de suppression :** Suppression logique (soft delete)

**Processus :**
1. Admin sélectionne l'étudiant
2. Clique sur "Supprimer"
3. Système affiche confirmation avec avertissement
4. Admin confirme
5. Système change le statut à "Inactif"
6. Système désactive le badge associé
7. Historique conservé pour traçabilité

**Vérifications préalables :**
- Vérifier existence d'impayés (alerte si oui)
- Informer de la récupération du badge

**Note :** La suppression physique n'est jamais effectuée pour préserver l'historique des passages et paiements.

---

#### F1.4 - Consulter un étudiant

**Description :** Affiche toutes les informations d'un étudiant

**Acteur :** Administrateur

**Informations affichées :**

**Bloc Informations personnelles :**
- Photo
- Nom, Prénom
- Date de naissance, Âge
- Classe, Niveau
- Email, Téléphone
- Statut du compte

**Bloc Badge :**
- Numéro de badge
- Date d'attribution
- Statut du badge (Actif, Inactif, Bloqué)

**Bloc Paiements :**
- Trimestre 1 : Statut, Date, Montant
- Trimestre 2 : Statut, Date, Montant
- Trimestre 3 : Statut, Date, Montant
- Total impayés

**Bloc Présence :**
- Taux de présence (mois en cours)
- Dernier passage (date et heure)
- Nombre d'absences consécutives
- Historique des 10 derniers passages

**Actions disponibles :**
- Modifier l'étudiant
- Attribuer/Changer badge
- Enregistrer un paiement
- Voir historique complet
- Générer rapport individuel

---

### Module 2 : Gestion des Badges

#### F2.1 - Attribuer un badge

**Description :** Associe un badge à un étudiant ou visiteur

**Acteur :** Administrateur

**Processus pour étudiant :**
1. Sélectionner l'étudiant
2. Choisir un badge disponible OU saisir nouveau numéro
3. Système vérifie unicité du numéro
4. Système crée/met à jour le badge
5. Associe badge à l'étudiant
6. Définit le statut selon paiement :
   - Si trimestre payé : Badge ACTIF
   - Si trimestre impayé : Badge INACTIF
7. Enregistre date d'attribution
8. Confirmation

**Processus pour visiteur :**
1. Créer le dossier visiteur (nom, organisation, motif, dates)
2. Attribuer badge visiteur disponible
3. Définir période de validité
4. Activer le badge
5. Badge opérationnel immédiatement

**Règles :**
- Un badge ne peut être attribué qu'à une seule personne à la fois
- Le numéro de badge doit être unique dans le système
- Un étudiant ne peut avoir qu'un seul badge actif

---

#### F2.2 - Activer/Désactiver un badge

**Description :** Change le statut opérationnel d'un badge

**Acteur :** Administrateur OU Système (automatique)

**Activation manuelle :**
- Admin sélectionne le badge
- Clique "Activer"
- Badge passe à statut ACTIF
- Accès autorisé dès activation

**Activation automatique :**
- Déclenchée lors d'un paiement de trimestre
- Tous les badges des étudiants ayant payé sont activés

**Désactivation manuelle :**
- Admin sélectionne le badge
- Clique "Désactiver"
- Badge passe à statut INACTIF
- Accès refusé immédiatement

**Désactivation automatique :**
- Nouveau trimestre sans paiement
- Expiration badge visiteur
- Suspension/Radiation de l'étudiant

---

#### F2.3 - Bloquer un badge

**Description :** Bloque temporairement ou définitivement un badge

**Acteur :** Administrateur

**Motifs de blocage :**
- Badge perdu
- Badge volé
- Mesure disciplinaire
- Problème technique
- Badge endommagé

**Processus :**
1. Admin sélectionne le badge
2. Clique "Bloquer"
3. Sélectionne le motif
4. Saisit commentaire (optionnel)
5. Confirme
6. Badge passe à statut BLOQUÉ
7. Toute tentative d'accès est refusée et alertée

**Déblocage :**
- Possible uniquement pour motifs réversibles
- Validation hiérarchique recommandée
- Traçabilité complète

---

#### F2.4 - Gérer badge perdu/volé

**Description :** Processus spécifique pour remplacement de badge

**Acteur :** Administrateur

**Processus badge perdu :**
1. Étudiant signale la perte
2. Admin recherche l'étudiant
3. Bloque l'ancien badge (motif : PERDU)
4. Crée/Attribue nouveau badge
5. Transfère les droits d'accès
6. Active le nouveau badge (si trimestre payé)
7. Enregistre l'incident
8. Notification confirmation

**Processus badge volé :**
1. Étudiant signale le vol
2. Admin bloque immédiatement (motif : VOLÉ)
3. Génère alerte sécurité
4. Surveillance renforcée des tentatives d'accès avec ce badge
5. Crée nouveau badge
6. Active selon statut paiement
7. Rapport d'incident conservé

**Frais de remplacement :** Possibilité de configurer frais de remplacement (à intégrer au système de paiement)

---

### Module 3 : Contrôle d'Accès

#### F3.1 - Vérifier badge et autoriser/refuser accès

**Description :** Processus automatique de contrôle lors de présentation du badge

**Acteur :** Système (automatique)

**Déclencheur :** Présentation du badge au lecteur

**Processus détaillé :**

```
1. Lecture du numéro de badge
2. Recherche dans la base de données
3. SI badge trouvé :
   a. Récupérer type de badge
   b. SI type = ÉTUDIANT :
      - Récupérer infos étudiant
      - Vérifier statut étudiant (ACTIF ?)
      - Vérifier statut badge (ACTIF ?)
      - Récupérer trimestre en cours
      - Vérifier paiement trimestre
      - SI tout OK : AUTORISER
      - SINON : REFUSER avec motif
   c. SI type = VISITEUR :
      - Récupérer infos visiteur
      - Vérifier période de validité
      - Vérifier statut badge (ACTIF ?)
      - SI dans période ET actif : AUTORISER
      - SINON : REFUSER avec motif
4. SINON (badge non trouvé) :
   - REFUSER (badge invalide)
   - Alerte sécurité
5. Enregistrer le passage dans tous les cas
6. Afficher résultat
```

**Affichage en cas d'autorisation :**
- Signal sonore et visuel POSITIF (vert, bip)
- Affichage : "Bienvenue [Nom Prénom]"
- Classe
- Heure d'arrivée
- Photo (optionnel)
- Ouverture porte/barrière (3-5 secondes)

**Affichage en cas de refus :**
- Signal sonore et visuel NÉGATIF (rouge, bip différent)
- Affichage du motif :
  - "Trimestre impayé - Contactez l'administration"
  - "Badge bloqué - Contactez l'administration"
  - "Badge invalide - Contactez la sécurité"
  - "Badge expiré"
- Montant dû si impayé
- Porte reste fermée

**Temps de réponse cible :** ≤ 2 secondes

---

#### F3.2 - Enregistrer passage

**Description :** Enregistre chaque tentative d'accès (réussie ou échouée)

**Acteur :** Système (automatique)

**Données enregistrées :**
- ID du badge
- Date et heure exacte (timestamp)
- Résultat (AUTORISE / REFUSE_*)
- Type de passage (ENTREE par défaut, SORTIE si configuré)
- Motif de refus (si applicable)
- Localisation (porte/portail d'accès)

**Types de résultats :**
- `AUTORISE` : Accès accordé
- `REFUSE_PAIEMENT_MANQUANT` : Trimestre impayé
- `REFUSE_BADGE_INVALIDE` : Badge inconnu
- `REFUSE_BADGE_BLOQUE` : Badge bloqué
- `REFUSE_BADGE_EXPIRE` : Badge visiteur expiré
- `REFUSE_ETUDIANT_INACTIF` : Compte étudiant inactif/suspendu

**Stockage :** Table `Passage` dans la base de données

**Conservation :** Minimum 3 ans pour traçabilité et historique

---

### Module 4 : Gestion des Paiements

#### F4.1 - Enregistrer un paiement trimestriel

**Description :** Enregistre le paiement d'un trimestre pour un étudiant

**Acteur :** Administrateur

**Processus :**
1. Admin sélectionne l'étudiant
2. Système affiche statut paiements (T1, T2, T3)
3. Admin sélectionne le trimestre à payer
4. Système vérifie qu'il n'est pas déjà payé
5. Admin saisit :
   - Montant payé
   - Mode de paiement (Espèces, Chèque, Virement, Mobile Money, Carte)
   - Référence/Numéro de transaction
   - Date de paiement
6. Admin valide
7. Système vérifie les règles métier
8. Système enregistre le paiement
9. Système détermine le statut :
   - Si montant = montant trimestre : PAYE
   - Si montant < montant trimestre : PARTIEL
10. SI statut = PAYE :
    - Activer le badge de l'étudiant
11. Génère le reçu (PDF)
12. Enregistre dans l'historique
13. Affiche confirmation + aperçu reçu

**Modes de paiement :**
- **Espèces :** Validation immédiate
- **Chèque :** Statut "En Attente" jusqu'à encaissement
- **Virement :** Statut "En Attente" jusqu'à réception
- **Mobile Money :** Validation immédiate (si intégration API)
- **Carte bancaire :** Validation immédiate (si TPE intégré)

**Reçu généré automatiquement contenant :**
- Logo et infos établissement
- Infos étudiant (nom, prénom, classe)
- Trimestre concerné
- Montant payé
- Mode de paiement
- Date et heure
- Numéro de reçu unique
- QR code (optionnel pour vérification)
- Signature numérique

---

#### F4.2 - Modifier statut de paiement

**Description :** Permet de corriger ou modifier un paiement existant

**Acteur :** Administrateur (Super Admin uniquement)

**Cas d'usage :**
- Correction d'erreur de saisie
- Annulation de paiement (chèque impayé)
- Remboursement

**Processus :**
1. Admin recherche le paiement
2. Affiche les détails
3. Modifie les champs nécessaires
4. Justifie la modification (commentaire obligatoire)
5. Valide
6. Système met à jour
7. Système ajuste le statut du badge si nécessaire
8. Enregistre modification dans historique avec motif

**Contrainte :** Traçabilité totale - l'ancienne valeur est conservée dans l'historique

---

#### F4.3 - Consulter impayés

**Description :** Affiche la liste des étudiants n'ayant pas payé le trimestre

**Acteur :** Administrateur

**Filtres disponibles :**
- Par trimestre
- Par classe
- Par montant dû
- Par délai (> 15 jours, > 30 jours, etc.)

**Informations affichées :**
- Nom, Prénom
- Classe
- Trimestre(s) impayé(s)
- Montant dû
- Nombre de jours de retard
- Dernier contact/relance

**Actions possibles :**
- Enregistrer un paiement
- Envoyer rappel (email/SMS)
- Générer liste d'impayés (export Excel/PDF)
- Programmer relance automatique

**Export :** Liste exportable en Excel/PDF pour transmission à la direction

---

### Module 5 : Gestion des Trimestres

#### F5.1 - Créer un trimestre

**Description :** Configure un nouveau trimestre académique

**Acteur :** Administrateur (Super Admin)

**Données à saisir :**
- Numéro du trimestre (1, 2, ou 3)
- Année académique (ex: 2024-2025)
- Date de début
- Date de fin
- Montant du trimestre
- Statut initial : EN_PREPARATION

**Validations :**
- Dates cohérentes (début < fin)
- Pas de chevauchement avec autre trimestre
- Montant > 0
- Numéro trimestre entre 1 et 3

**Processus :**
1. Admin accède à "Gestion trimestres"
2. Clique "Nouveau trimestre"
3. Remplit le formulaire
4. Valide
5. Système vérifie les règles
6. Système crée le trimestre
7. Confirmation

**Note :** Le trimestre est créé mais pas encore actif

---

#### F5.2 - Activer un trimestre

**Description :** Active un trimestre et met à jour les droits d'accès

**Acteur :** Administrateur (Super Admin)

**Déclencheur :** Début effectif du trimestre

**Processus :**
1. Admin sélectionne le trimestre
2. Clique "Activer"
3. Système affiche confirmation avec impacts
4. Admin confirme
5. Système :
   - Modifie statut trimestre → EN_COURS
   - Désactive tout autre trimestre actif
   - Pour chaque étudiant actif :
     * Vérifie si paiement existe pour ce trimestre
     * SI payé : Badge reste ACTIF
     * SI impayé : Badge → INACTIF
   - Génère statistiques initiales
   - Enregistre dans historique
6. Optionnel : Envoi notifications automatiques impayés

**Impact :** Désactivation massive des badges impayés

**Recommandation :** Effectuer en dehors des heures d'accès (soirée/weekend)

---

#### F5.3 - Clôturer un trimestre

**Description :** Ferme un trimestre terminé

**Acteur :** Administrateur (Super Admin)

**Processus :**
1. Admin sélectionne le trimestre actif
2. Clique "Clôturer"
3. Système génère rapport final :
   - Taux de paiement final
   - Statistiques de présence
   - Impayés restants
   - Moyennes diverses
4. Admin consulte le rapport
5. Admin confirme clôture
6. Système :
   - Modifie statut → CLOTURE
   - Archive les données
   - Génère rapport PDF final
   - Enregistre date de clôture
7. Trimestre figé (plus de modifications possibles)

**Note :** La clôture ne supprime rien, elle fige les données du trimestre

---

### Module 6 : Gestion des Visiteurs

#### F6.1 - Créer un visiteur

**Description :** Enregistre un visiteur temporaire et lui attribue un badge

**Acteur :** Administrateur / Agent d'accueil

**Données à saisir :**
- Nom, Prénom
- Organisation/Entreprise
- Motif de visite
- Personne à contacter dans l'établissement
- Téléphone
- Date et heure de début
- Date et heure de fin prévue

**Processus :**
1. Accueil reçoit le visiteur
2. Remplit le formulaire visiteur
3. Système attribue automatiquement un badge visiteur disponible
4. Active le badge pour la période définie
5. Remet le badge au visiteur
6. Enregistre le passage d'entrée
7. Visiteur peut accéder

**Badge visiteur :**
- Type : VISITEUR
- Validité limitée dans le temps
- Pas de condition de paiement
- Désactivation automatique à la date de fin

---

#### F6.2 - Prolonger visite

**Description :** Étend la période de validité d'un badge visiteur

**Acteur :** Administrateur

**Processus :**
1. Rechercher le visiteur
2. Afficher ses informations
3. Modifier la date/heure de fin
4. Saisir nouveau motif (optionnel)
5. Valider
6. Badge reste actif jusqu'à nouvelle date

---

#### F6.3 - Clôturer visite

**Description :** Termine une visite et récupère le badge

**Acteur :** Administrateur / Agent d'accueil

**Processus :**
1. Visiteur se présente à la sortie
2. Remet le badge
3. Agent clôture la visite dans le système
4. Système désactive le badge
5. Badge redevient disponible pour autre visiteur

---

### Module 7 : Rapports et Statistiques

#### F7.1 - Générer rapport de présence

**Description :** Crée un rapport détaillé de présence/absence

**Acteur :** Administrateur

**Paramètres :**
- **Période :** Date début - Date fin
- **Portée :** Toutes classes / Classe spécifique / Étudiant spécifique
- **Type :** Synthèse / Détaillé
- **Format :** PDF / Excel / CSV

**Contenu rapport synthèse :**
- Taux de présence global
- Taux de présence par classe
- Nombre total de présences/absences
- Graphique d'évolution
- Top 10 étudiants assidus
- Top 10 étudiants absents

**Contenu rapport détaillé :**
- Liste de tous les étudiants avec :
  * Nom, Prénom, Classe
  * Nombre de jours de présence
  * Nombre de jours d'absence
  * Liste des dates de présence
  * Liste des dates d'absence
  * Taux de présence individuel
  * Nombre de retards

**Génération :**
1. Admin définit les paramètres
2. Clique "Générer"
3. Système collecte les données
4. Effectue les calculs
5. Génère le document
6. Affiche aperçu
7. Possibilité de télécharger / imprimer / envoyer par email

---

#### F7.2 - Générer rapport de paiements

**Description :** État des paiements pour un trimestre

**Acteur :** Administrateur

**Paramètres :**
- Trimestre concerné
- Classe (optionnel)
- Format de sortie

**Contenu :**
- Taux de paiement global
- Taux de paiement par classe
- Montant total collecté
- Montant total des impayés
- Liste des étudiants :
  * Payés (nom, date paiement, mode)
  * Partiels (nom, montant payé, reste dû)
  * Impayés (nom, montant dû, délai)
- Statistiques par mode de paiement

---

#### F7.3 - Générer rapport visiteurs

**Description :** Historique des visites

**Acteur :** Administrateur

**Paramètres :**
- Période
- Format

**Contenu :**
- Nombre total de visiteurs
- Durée moyenne des visites
- Liste détaillée :
  * Nom, Organisation
  * Motif de visite
  * Personne contactée
  * Date/heure entrée
  * Date/heure sortie
  * Durée effective

**Usage :** Sécurité et traçabilité

---

#### F7.4 - Consulter statistiques dashboard

**Description :** Tableau de bord temps réel

**Acteur :** Administrateur

**Informations affichées :**

**Section Aujourd'hui :**
- Nombre d'étudiants présents
- Taux de présence du jour
- Nombre de passages autorisés
- Nombre de passages refusés
- Statut du système (opérationnel/dégradé)

**Section Cette semaine :**
- Taux de présence moyen
- Évolution quotidienne (graphique)
- Taux de présence par classe

**Section Trimestre en cours :**
- Taux de paiement
- Montant collecté
- Montant impayés
- Nombre d'étudiants actifs

**Alertes :**
- Étudiants avec absences consécutives ≥ 3 jours
- Classes avec taux présence < 70%
- Tentatives d'accès frauduleuses
- Problèmes techniques

**Rafraîchissement :** Automatique toutes les 5 minutes

---

## Processus métier

### Processus 1 : Inscription complète d'un nouvel étudiant

**Objectif :** Inscrire un étudiant et le rendre opérationnel dans le système

**Acteurs :** Administrateur, Étudiant

**Étapes :**
1. Réception du dossier d'inscription (papier/numérique)
2. Création du compte étudiant dans le système
3. Saisie des informations personnelles
4. Upload de la photo
5. Attribution à une classe
6. Sélection/Création d'un badge
7. Association badge-étudiant
8. Vérification du paiement :
   - Si payé : Activation badge
   - Si non payé : Badge inactif
9. Test du badge sur le lecteur
10. Remise du badge à l'étudiant
11. Explication du fonctionnement

**Durée cible :** 10 minutes

**Résultat :** Étudiant opérationnel, peut accéder (si payé)

---

### Processus 2 : Début de trimestre

**Objectif :** Activer un nouveau trimestre et mettre à jour tous les accès

**Acteurs :** Administrateur (Super Admin)

**Étapes :**
1. Création du nouveau trimestre (si pas déjà fait)
2. Configuration dates et montant
3. Vérification de la cohérence
4. Planification de l'activation (date/heure)
5. Communication aux familles (nouveau trimestre, montant, date limite)
6. À la date prévue :
   - Clôture trimestre précédent
   - Activation nouveau trimestre
   - Mise à jour massive des badges :
     * Badges payés → ACTIF
     * Badges impayés → INACTIF
7. Génération rapport initial (taux paiement initial)
8. Envoi notifications/rappels impayés
9. Suivi quotidien évolution paiements

**Durée :** Activation : 15-30 minutes / Suivi : Tout le trimestre

**Critère de succès :** Transition fluide, pas de blocage d'étudiants payés

---

### Processus 3 : Gestion badge perdu

**Objectif :** Remplacer rapidement un badge perdu

**Acteurs :** Étudiant, Administrateur

**Étapes :**
1. Étudiant signale perte (directement ou via parent)
2. Admin recherche l'étudiant dans le système
3. Consultation de son dossier :
   - Badge actuel
   - Statut paiement
   - Historique
4. Blocage immédiat ancien badge (motif : PERDU)
5. Sélection nouveau badge disponible
6. Attribution à l'étudiant
7. Vérification statut paiement :
   - Si trimestre payé → Badge ACTIF
   - Si impayé → Badge INACTIF
8. Enregistrement incident dans historique
9. Facturation frais remplacement (si applicable)
10. Remise nouveau badge à l'étudiant

**Durée :** 5 minutes

**SLA :** Traitement le jour même si signalé avant 15h

---

### Processus 4 : Génération rapport mensuel de présence

**Objectif :** Produire un rapport complet de présence pour le mois écoulé

**Acteurs :** Administrateur

**Étapes :**
1. Début de nouveau mois (automatique ou manuel)
2. Accès au module de rapports
3. Sélection "Rapport mensuel de présence"
4. Configuration :
   - Période : 1er au dernier jour mois précédent
   - Portée : Toutes classes
   - Format : PDF
   - Niveau : Synthèse + Détaillé
5. Lancement génération
6. Système :
   - Collecte tous les passages du mois
   - Calcule jours ouvrables
   - Calcule présences/absences par étudiant
   - Calcule taux par classe
   - Identifie étudiants problématiques
   - Génère graphiques
   - Compile le document
7. Validation aperçu
8. Téléchargement PDF
9. Diffusion :
   - Email à la direction
   - Archivage dans dossier mensuel
   - Présentation conseil pédagogique

**Fréquence :** Mensuel (automatisable)

**Résultat :** Rapport PDF complet + graphiques

---

## Règles de gestion

### RG-01 : Unicité du numéro de badge

**Règle :** Un numéro de badge doit être unique dans tout le système

**Justification :** Éviter les conflits et garantir l'identification correcte

**Implémentation :** Contrainte UNIQUE sur la colonne `numeroBadge`

---

### RG-02 : Un badge par personne active

**Règle :** Un étudiant ou visiteur ne peut avoir qu'un seul badge actif à la fois

**Exception :** Badge de remplacement (l'ancien doit être bloqué d'abord)

---

### RG-03 : Accès conditionné au paiement (étudiants)

**Règle :** Un étudiant ne peut accéder que si le trimestre en cours est payé

**Statuts de paiement autorisant l'accès :**
- PAYE : Accès total
- PARTIEL : Configurable (par défaut : accès refusé)

**Statuts refusant l'accès :**
- IMPAYE : Accès refusé
- EN_ATTENTE : Accès refusé (paiement non validé)

---

### RG-04 : Accès visiteur non conditionné au paiement

**Règle :** Les visiteurs ne sont pas soumis à condition de paiement

**Condition d'accès :** Badge actif + dans période de validité

---

### RG-05 : Paiement unique par trimestre

**Règle :** Un étudiant ne peut avoir qu'un seul paiement avec statut PAYE par trimestre

**Exception :** Paiements partiels multiples cumulés jusqu'au total

---

### RG-06 : Trimestre unique actif

**Règle :** Un seul trimestre peut avoir le statut EN_COURS à un moment donné

**Justification :** Éviter les ambiguïtés sur le trimestre de référence pour les paiements et accès

---

### RG-07 : Conservation historique des passages

**Règle :** Les données de passage ne sont jamais supprimées physiquement

**Durée de conservation :** Minimum 3 ans, recommandé 5 ans

**Justification :** Traçabilité, légal, statistiques historiques

---

### RG-08 : Validation des données étudiant

**Règle :** Les champs obligatoires doivent être renseignés avant activation du compte

**Champs obligatoires :**
- Nom, Prénom
- Date de naissance
- Classe

**Champs recommandés :**
- Photo (pour identification visuelle)
- Email (pour notifications)
- Téléphone (pour contacts d'urgence)

---

### RG-09 : Calcul de présence

**Règle :** Un étudiant est considéré présent s'il a au moins un passage autorisé dans la journée

**Précision :** Le premier passage de la journée est utilisé pour l'heure d'arrivée

**Retard :** Configurable, par défaut : arrivée après 8h00

---

### RG-10 : Désactivation automatique badge visiteur

**Règle :** Un badge visiteur est automatiquement désactivé à sa date de fin

**Mécanisme :** Tâche planifiée (CRON) vérifiant quotidiennement les badges visiteurs expirés

---

### RG-11 : Traçabilité des modifications

**Règle :** Toute modification de données critiques doit être tracée dans l'historique

**Données critiques :**
- Informations étudiant
- Statuts de paiement
- Attribution/modification de badges
- Création/modification trimestres

**Information à tracer :**
- Utilisateur
- Date et heure
- Action effectuée
- Anciennes et nouvelles valeurs

---

### RG-12 : Niveaux d'habilitation

**Règle :** Certaines actions nécessitent des droits élevés

**Super Admin uniquement :**
- Création/modification/suppression trimestres
- Modification statuts de paiement
- Suppression définitive de données
- Configuration système

**Admin :**
- Gestion étudiants
- Gestion badges
- Enregistrement paiements
- Génération rapports

**Opérateur :**
- Consultation données
- Génération rapports
- Pas de modification

---

## Contraintes et exigences

### Contraintes techniques

**CT-01 : Performance**
- Temps de réponse contrôle d'accès : ≤ 2 secondes
- Temps de chargement interface admin : ≤ 3 secondes
- Capacité : 2000 étudiants minimum
- Passages simultanés : 50 badges/minute minimum

**CT-02 : Disponibilité**
- Taux de disponibilité : 99.5% minimum
- Temps d'arrêt planifié : Hors heures d'accès uniquement
- Plan de reprise d'activité (PRA) : 4 heures maximum

**CT-03 : Sécurité**
- Chiffrement des mots de passe (bcrypt, Argon2)
- HTTPS obligatoire pour interface web
- Sauvegarde quotidienne automatique
- Logs d'accès et d'audit
- Protection injection SQL
- Validation côté serveur

**CT-04 : Compatibilité**
- Navigateurs : Chrome, Firefox, Edge, Safari (2 dernières versions)
- Systèmes d'exploitation : Windows, Linux, macOS
- Lecteurs badges : RFID 125 kHz et/ou NFC 13.56 MHz

**CT-05 : Évolutivité**
- Architecture modulaire
- API pour intégrations futures
- Base de données scalable

---

### Contraintes fonctionnelles

**CF-01 : Multilingue** (Optionnel)
- Interface en français (obligatoire)
- Possibilité ajout d'autres langues

**CF-02 : Accessibilité**
- Conformité WCAG 2.1 niveau AA (recommandé)
- Interface lisible et intuitive

**CF-03 : Conformité légale**
- RGPD : Consentement, droit à l'oubli, portabilité
- Protection données personnelles
- Conservation limitée dans le temps

---

### Exigences non fonctionnelles

**ENF-01 : Utilisabilité**
- Interface simple et intuitive
- Formation utilisateur : ≤ 2 heures
- Manuel utilisateur fourni

**ENF-02 : Maintenabilité**
- Code documenté
- Architecture claire
- Tests unitaires et d'intégration

**ENF-03 : Fiabilité**
- Gestion des erreurs robuste
- Messages d'erreur explicites
- Logs détaillés pour débogage

**ENF-04 : Portabilité**
- Base de données : PostgreSQL, MySQL, ou SQL Server
- Serveur web : Apache, Nginx, IIS
- Technologie cross-platform privilégiée

---

## Interfaces utilisateur

### Interface Administrateur

**IU-01 : Page d'accueil / Dashboard**

Éléments affichés :
- En-tête avec logo, nom utilisateur, déconnexion
- Menu principal (vertical ou horizontal)
- Widgets :
  * Présence du jour (nombre, taux, graphique)
  * Alertes actives (liste)
  * Trimestre en cours (statut paiement)
  * Statistiques rapides
- Accès rapides (boutons)

**IU-02 : Liste des étudiants**

Fonctionnalités :
- Tableau paginé (50 étudiants/page)
- Colonnes : Photo, Nom, Prénom, Classe, Badge, Statut, Actions
- Recherche instantanée (nom, prénom, classe, badge)
- Filtres : Classe, Statut, Paiement
- Tri par colonnes
- Actions : Voir, Modifier, Supprimer
- Bouton "+ Nouvel étudiant"

**IU-03 : Fiche étudiant**

Organisation en onglets :
- **Informations** : Données personnelles, photo, classe
- **Badge** : Numéro, statut, historique badges
- **Paiements** : Statut T1, T2, T3, bouton "Enregistrer paiement"
- **Présence** : Statistiques, graphiques, historique passages
- **Historique** : Toutes modifications du dossier

Actions en haut : Modifier, Supprimer, Imprimer fiche

**IU-04 : Formulaire enregistrement paiement**

Disposition :
- Sélection étudiant (autocomplete)
- Affichage infos étudiant (photo, nom, classe)
- Affichage statut paiements trimestres
- Sélection trimestre à payer
- Montant (pré-rempli, modifiable)
- Mode de paiement (liste déroulante)
- Référence/numéro
- Date (pré-remplie aujourd'hui)
- Boutons : Enregistrer, Annuler

**IU-05 : Module de rapports**

Interface en 3 étapes :
1. **Choix du type de rapport**
   - Présence
   - Paiements
   - Visiteurs
   - Personnalisé

2. **Configuration paramètres**
   - Période (date picker)
   - Portée (toutes classes, classe, étudiant)
   - Format (PDF, Excel, CSV)
   - Niveau détail (synthèse, détaillé)

3. **Génération et visualisation**
   - Barre de progression
   - Aperçu du rapport
   - Boutons : Télécharger, Imprimer, Envoyer par email, Nouvelle génération

**IU-06 : Écran de contrôle d'accès** (Point d'entrée)

Affichage sur écran dédié au lecteur :
- **État de veille :**
  - Message : "Présentez votre badge"
  - Logo établissement
  - Date et heure

- **Accès autorisé :**
  - Fond vert
  - Photo de l'étudiant
  - "Bienvenue [Nom Prénom]"
  - Classe
  - Heure : 07:45
  - Pictogramme porte ouverte
  - Son : Bip court

- **Accès refusé :**
  - Fond rouge
  - Pictogramme interdit
  - Motif : "TRIMESTRE IMPAYÉ"
  - "Contactez l'administration"
  - Montant dû (si applicable)
  - Son : Bip long

---

**FIN DES SPÉCIFICATIONS FONCTIONNELLES**

---

*Document créé le : [Date]*
*Version : 1.0*
*Auteur : Système de documentation automatique*
*Statut : Validé pour implémentation*
