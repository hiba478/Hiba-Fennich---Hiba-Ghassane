# Guide : créer un compte GitHub, tester et publier le TP Google Colab

Ce guide explique pas à pas :

1. comment créer un compte GitHub ;
2. comment tester le TP `TP_apprentissage_automatique_manchots.ipynb` dans Google Colab ;
3. comment publier ce TP sur GitHub, puis l'ouvrir directement depuis Colab.

**Durée estimée :** 30 à 45 minutes la première fois.

---

## Partie 1 : Créer un compte GitHub

GitHub est une plateforme d'hébergement de code et de projets. Un compte est gratuit.

### Étape 1.1 : s'inscrire

1. Ouvrir le site **https://github.com**.
2. Cliquer sur **Sign up** (S'inscrire).
3. Renseigner :
   - une **adresse courriel** valide ;
   - un **mot de passe** long (au moins 15 caractères recommandé, ou une phrase de passe) ;
   - un **nom d'utilisateur** (*username*). Il apparaîtra dans l'adresse de vos projets, par exemple `github.com/nom-utilisateur/nom-projet`. Choisissez-le avec soin, sobre et professionnel.
4. Résoudre le test de vérification (puzzle), puis valider le code reçu par courriel.
5. Choisir le plan **Free** (gratuit), qui suffit largement pour ce TP.

### Étape 1.2 : activer l'authentification à deux facteurs

Fortement recommandé, pour protéger votre compte :

1. Aller dans **Settings** (Paramètres), puis **Password and authentication**.
2. Choisir une méthode (application d'authentification, clés de sécurité ou SMS) et suivre les instructions.
3. Conserver précieusement les **codes de récupération** affichés.

### Étape 1.3 : vérifier l'adresse courriel

Ouvrir le courriel de GitHub et cliquer sur le lien de vérification. Sans cela, certaines actions sont bloquées.

### Étape 1.4 : créer un jeton d'accès (utile plus tard)

Depuis 2021, GitHub n'accepte plus le mot de passe pour pousser du code depuis un terminal. Il faut un **jeton d'accès personnel** (*Personal Access Token*, PAT) :

1. Aller dans **Settings**, puis **Developer settings**, puis **Personal access tokens**, puis **Tokens (classic)**.
2. Cliquer sur **Generate new token (classic)**, donner un nom, choisir une durée d'expiration et cocher la permission **repo**.
3. Copier le jeton immédiatement : il ne sera plus affiché.

> **Sécurité.** Ne partagez jamais votre mot de passe ni votre jeton. Ne les écrivez jamais dans un notebook, un fichier ou un message.

---

## Partie 2 : Tester le TP dans Google Colab

Google Colab (*Colaboratory*) est un environnement de notebooks Python qui s'exécute dans le navigateur. Il est gratuit et ne demande aucune installation. Un compte Google suffit.

### Méthode A : importer le fichier depuis votre ordinateur

1. Ouvrir **https://colab.research.google.com** et se connecter avec un compte Google.
2. Dans la fenêtre d'accueil, cliquer sur **Importer un notebook** (onglet *Importer*), ou dans le menu : **Fichier**, puis **Importer un notebook**.
3. Glisser-déposer le fichier `TP_apprentissage_automatique_manchots.ipynb`, ou le sélectionner.
4. Le notebook s'ouvre. Il est enregistré sur votre Google Drive, dans le dossier *Colab Notebooks*.

### Méthode B : ouvrir le notebook depuis GitHub

Cette méthode suppose que le TP est déjà publié (voir la partie 3).

1. Ouvrir **https://colab.research.google.com**.
2. Aller dans **Fichier**, puis **Ouvrir un notebook**, puis l'onglet **GitHub**.
3. Coller l'adresse de votre dépôt (par exemple `nom-utilisateur/nom-depot`) et appuyer sur Entrée.
4. Sélectionner le fichier `.ipynb` dans la liste.

### Exécuter le TP

1. Pour lancer une cellule : cliquer dessus, puis appuyer sur **Maj + Entrée** (ou sur le bouton ▶ à gauche).
2. Exécuter les cellules **dans l'ordre**, de haut en bas. Une cellule dépend souvent des variables créées précédemment.
3. Pour tout exécuter d'un coup : **Exécution**, puis **Tout exécuter** (*Run all*).
4. Les graphiques apparaissent sous chaque cellule. Le chargement des données se fait depuis Internet : une connexion est nécessaire.

### Vérifier que le TP fonctionne

Les repères suivants doivent apparaître :

| Cellule | Résultat attendu |
|---|---|
| Chargement des données | Dimensions `(344, 7)` |
| Nettoyage | `Observations après nettoyage : 333` |
| Découpage | Entraînement 249, test 84 |
| Référence | Exactitude moyenne d'environ 0,44 |
| Évaluation finale | Exactitude sur le test d'environ 0,99 |

De légers écarts peuvent apparaître selon la version des bibliothèques installées sur Colab. Ils ne doivent pas être importants.

### Résoudre les problèmes courants

- **« Module not found »** : exécuter la cellule d'importation. Colab fournit déjà les bibliothèques du TP. Si l'une manque, ajouter une cellule `!pip install nom-du-paquet` puis relancer.
- **Erreur de connexion à l'URL** : vérifier la connexion Internet, puis relancer la cellule de chargement.
- **Résultats différents** : relancer **Exécution**, puis **Redémarrer et tout exécuter**, pour repartir d'un environnement propre.
- **Avertissements (*warnings*) en orange** : ils sont sans gravité pour ce TP.

### Enregistrer une copie personnelle

Avant de modifier le TP, faire **Fichier**, puis **Enregistrer une copie dans Drive**. Vos modifications restent alors séparées de l'original.

---

## Partie 3 : Déployer le TP sur GitHub

### Étape 3.1 : créer un dépôt (*repository*)

Un dépôt est un dossier de projet suivi par GitHub.

1. Se connecter sur **https://github.com**.
2. Cliquer sur le bouton **+** en haut à droite, puis **New repository**.
3. Renseigner :
   - **Repository name** : par exemple `tp-apprentissage-automatique` (sans espaces, minuscules recommandées) ;
   - **Description** : *TP d'introduction à l'apprentissage automatique avec Google Colab* ;
   - **Visibility** : **Public** pour que Colab puisse ouvrir le notebook sans authentification ;
   - cocher **Add a README file**.
4. Cliquer sur **Create repository**.

> **Remarque.** Un dépôt privé fonctionne aussi, mais Colab demande alors une autorisation GitHub supplémentaire. Pour un TP destiné aux étudiants, le choix public est plus simple.

### Étape 3.2 : ajouter le notebook

**Méthode 1 : depuis le navigateur (la plus simple)**

1. Dans votre dépôt, cliquer sur **Add file**, puis **Upload files**.
2. Glisser le fichier `TP_apprentissage_automatique_manchots.ipynb`.
3. Dans **Commit changes**, écrire un message court, par exemple : *Ajout du TP sur les manchots*.
4. Cliquer sur **Commit changes**.

**Méthode 2 : avec Git, dans un terminal**

1. Installer Git (**https://git-scm.com**), puis vérifier :

   ```bash
   git --version
   ```

2. Configurer votre identité (une seule fois) :

   ```bash
   git config --global user.name "Votre Nom"
   git config --global user.email "votre-courriel@exemple.com"
   ```

3. Cloner le dépôt, copier le notebook dedans, puis publier :

   ```bash
   git clone https://github.com/nom-utilisateur/tp-apprentissage-automatique.git
   cd tp-apprentissage-automatique
   cp /chemin/vers/TP_apprentissage_automatique_manchots.ipynb .
   git add TP_apprentissage_automatique_manchots.ipynb
   git commit -m "Ajout du TP sur les manchots"
   git push
   ```

4. Lors du `git push`, GitHub demande un identifiant et un mot de passe : indiquer votre **nom d'utilisateur** et, comme mot de passe, le **jeton d'accès personnel** créé à l'étape 1.4.

### Étape 3.3 : ajouter un README

Le fichier `README.md` est la page d'accueil du dépôt. Il explique le projet et donne le lien d'ouverture dans Colab. Remplacer son contenu par le texte suivant, en adaptant les champs entre crochets :

```markdown
# TP : introduction à l'apprentissage automatique

Ce TP initie à l'apprentissage supervisé (classification multiclasse) à partir du jeu de données Palmer Penguins.

## Ouvrir le TP dans Google Colab

[![Ouvrir dans Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/[nom-utilisateur]/[nom-depot]/blob/main/TP_apprentissage_automatique_manchots.ipynb)

## Contenu

- Exploration et visualisation des données
- Préparation dans un pipeline scikit-learn
- Comparaison de modèles par validation croisée
- Évaluation : matrice de confusion, courbe d'apprentissage
- Interprétation et exercices

## Données

Palmer Penguins, mis à disposition par le projet seaborn-data : https://github.com/mwaskom/seaborn-data

## Licence

Indiquer ici la licence choisie (par exemple MIT ou CC BY 4.0).
```

Pour modifier le README dans le navigateur : ouvrir le fichier, cliquer sur l'icône crayon, modifier, puis **Commit changes**.

### Étape 3.4 : vérifier le lien Colab

1. Remplacer `[nom-utilisateur]` et `[nom-depot]` dans le badge par vos valeurs. Exemple : `https://colab.research.google.com/github/dupont/tp-apprentissage-automatique/blob/main/TP_apprentissage_automatique_manchots.ipynb`.
2. Ouvrir ce lien dans un navigateur. Colab doit charger le notebook.
3. Si Colab affiche une erreur, vérifier que le dépôt est **public**, que le nom de la branche est bien `main`, et que le nom du fichier est exact (respect des majuscules).

### Étape 3.5 : mettre à jour le TP

Chaque modification suit le même cycle : modifier, enregistrer, valider.

- Dans le navigateur : ouvrir le fichier, cliquer sur le crayon, modifier, puis **Commit changes**.
- Avec Git : `git add`, `git commit -m "Description"`, `git push`.

Colab lit toujours la dernière version validée sur la branche `main`.

---

## Bonnes pratiques

- **Ne publiez pas de données personnelles** ni de secrets (mots de passe, jetons, clés API, numéros de téléphone). Ce qui est publié sur un dépôt public peut être copié par n'importe qui.
- **Vérifiez le notebook avant publication** : supprimer les sorties contenant des informations privées (Édition, puis Effacer toutes les sorties), puis relancer l'exécution pour vérifier.
- **Choisissez une licence** pour indiquer les conditions de réutilisation du TP.
- **Nommez clairement vos fichiers** : sans espaces ni accents, par exemple `TP_apprentissage_automatique_manchots.ipynb`.

## Aide-mémoire

| Action | Où |
|---|---|
| Créer un compte | https://github.com, bouton *Sign up* |
| Créer un dépôt | Bouton **+**, puis *New repository* |
| Ajouter un fichier | *Add file*, puis *Upload files* |
| Ouvrir un notebook | https://colab.research.google.com, *Importer* ou *GitHub* |
| Lancer tout le notebook | *Exécution*, puis *Tout exécuter* |
| Enregistrer une copie | *Fichier*, puis *Enregistrer une copie dans Drive* |
| Badge Colab | Lien `colab.research.google.com/github/...` dans le README |
