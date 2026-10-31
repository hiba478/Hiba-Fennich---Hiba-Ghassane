# 10 projets de science des données orientés finance

**Niveau :** maîtrise
**Durée :** environ 2 semaines
**Langage :** Python (pandas, scikit-learn, matplotlib)

Les sources ont été vérifiées au moment de la rédaction. Les jeux de données UCI 350 (défaut de carte), UCI 222 (dépôts bancaires), UCI 572 (faillites) et le jeu Kaggle de fraude figurent déjà dans la liste générale de 40 projets et ne sont pas repris ici.

---

## Tableau récapitulatif

| N° | Titre | Type d'apprentissage | Difficulté | Source |
|---|---|---|---|---|
| F1 | Risque de crédit en Allemagne | Supervisé, classification à coût asymétrique | Débutant | UCI 144 |
| F2 | Prévision du taux à 10 ans | Supervisé, régression sur séries temporelles | Intermédiaire | FRED DGS10 |
| F3 | Prévision de l'inflation | Supervisé, régression sur séries temporelles | Intermédiaire | FRED CPIAUCSL, UNRATE, FEDFUNDS |
| F4 | Sentiment des nouvelles financières | Supervisé, classification de texte | Intermédiaire | Hugging Face, financial_phrasebank |
| F5 | Facteurs de risque d'un portefeuille | Supervisé, régression linéaire | Intermédiaire | Ken French, facteurs Fama-French |
| F6 | Prédire les récessions avec la courbe des taux | Supervisé, classification déséquilibrée | Intermédiaire | FRED DGS10, DGS2, USREC |
| F7 | Anomalies sur le S&P 500 | Non supervisé, détection d'anomalies | Débutant | FRED SP500 |
| F8 | Peut-on battre la marche aléatoire ? | Supervisé, classification avec référence | Intermédiaire | FRED SP500 |
| F9 | Régimes de marché par partitionnement | Non supervisé, partitionnement | Intermédiaire | FRED SP500 |
| F10 | Gestion de position par renforcement | Renforcement, Q-learning ou DQN | Avancé | FRED SP500 |

---

## Détail des projets

### F1. Risque de crédit en Allemagne

- **Question :** quels attributs du client et du prêt permettent d'identifier les mauvais risques ?
- **Tâche :** classification binaire, avec coûts d'erreur asymétriques.
- **Données :** UCI 144, https://archive.ics.uci.edu/dataset/144. 1 000 dossiers, 20 variables, 300 mauvais risques.
- **Prétraitement :** encodage des variables catégorielles, standardisation des montants et des durées.
- **Modèles :** régression logistique (référence), arbre de décision, forêt aléatoire.
- **Métriques :** AUC et coût total. Fixer la matrice de coûts (par exemple : accepter un mauvais risque coûte cinq fois plus que refuser un bon) et la justifier.
- **Livrable :** notebook et courbe de coût selon le seuil de décision.
- **Risque :** le seuil optimal dépend des hypothèses de coût. Présenter une analyse de sensibilité.

### F2. Prévision du taux à 10 ans

- **Question :** les taux passés permettent-ils de prévoir le taux à 10 ans un mois plus tard ?
- **Tâche :** régression sur séries temporelles.
- **Données :** FRED DGS10, https://fred.stlouisfed.org/graph/fredgraph.csv?id=DGS10. Environ 16 900 observations quotidiennes depuis 1962.
- **Prétraitement :** traiter les valeurs manquantes (codées « . »), rééchantillonnage mensuel, création de retards.
- **Modèles :** marche aléatoire (référence), régression linéaire avec retards, gradient boosting.
- **Métriques :** RMSE et MAE, comparées à la référence.
- **Livrable :** notebook et graphique prévision contre réel.
- **Risque :** une fuite temporelle apparaît si le découpage est aléatoire. Utiliser une validation chronologique.

### F3. Prévision de l'inflation

- **Question :** le chômage et le taux directeur aident-ils à prévoir l'inflation d'un an sur l'autre ?
- **Tâche :** régression sur séries mensuelles.
- **Données :** FRED CPIAUCSL, UNRATE et FEDFUNDS, par exemple https://fred.stlouisfed.org/graph/fredgraph.csv?id=CPIAUCSL. Environ 950 observations mensuelles.
- **Prétraitement :** calculer la variation annuelle de l'indice des prix, aligner les dates, construire des retards.
- **Modèles :** dernière valeur (référence), régression Ridge, gradient boosting.
- **Métriques :** RMSE sur un horizon de 12 mois.
- **Livrable :** notebook et analyse de l'importance des variables.
- **Risque :** les séries économiques sont révisées après publication. Les données actuelles ne sont pas celles disponibles à l'époque : c'est un biais d'anticipation (*look-ahead bias*). Le signaler dans le rapport.

### F4. Sentiment des nouvelles financières

- **Question :** un modèle de texte détecte-t-il si une phrase financière est positive, négative ou neutre ?
- **Tâche :** classification de texte à trois classes.
- **Données :** Financial PhraseBank, https://huggingface.co/datasets/takala/financial_phrasebank. Environ 4 800 phrases annotées, avec plusieurs niveaux d'accord entre annotateurs.
- **Prétraitement :** nettoyage, TF-IDF avec unigrammes et bigrammes.
- **Modèles :** régression logistique (référence), SVM linéaire, comparaison avec un modèle pré-entraîné si le temps le permet.
- **Métriques :** F1 macro, matrice de confusion.
- **Livrable :** notebook et analyse commentée des erreurs.
- **Risque :** les annotations sont subjectives. Évaluer séparément les phrases à accord élevé et celles à accord faible.

### F5. Facteurs de risque d'un portefeuille

- **Question :** la rentabilité d'un portefeuille s'explique-t-elle par le marché, la taille et la valeur ?
- **Tâche :** régression linéaire multiple, avec estimation de l'alpha et du bêta.
- **Données :** facteurs Fama-French, https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/ftp/F-F_Research_Data_Factors_CSV.zip (fichier zip à décompresser). Les rendements du portefeuille doivent être fournis par l'étudiant, par exemple depuis Yahoo Finance.
- **Prétraitement :** aligner les dates mensuelles, convertir en rendements en excès.
- **Modèles :** régression MCO avec tests de significativité ; comparaison à un modèle à un seul facteur.
- **Métriques :** R² ajusté, alpha et son intervalle de confiance.
- **Livrable :** notebook et tableau de résultats commenté.
- **Risque :** un alpha positif peut venir du hasard. Constituer le portefeuille avec les entreprises actuelles crée un biais de survie.

### F6. Prédire les récessions avec la courbe des taux

- **Question :** l'écart entre les taux à 10 ans et à 2 ans annonce-t-il une récession dans les 12 mois ?
- **Tâche :** classification binaire déséquilibrée.
- **Données :** FRED DGS10 et DGS2 (https://fred.stlouisfed.org/graph/fredgraph.csv?id=DGS2), ainsi que l'indicateur de récession USREC (https://fred.stlouisfed.org/graph/fredgraph.csv?id=USREC).
- **Prétraitement :** calculer l'écart 10 ans moins 2 ans, agréger en mensuel, définir la cible « récession dans les 12 mois ».
- **Modèles :** régression logistique sur l'écart seul (référence), puis avec retards et chômage.
- **Métriques :** AUC, aire sous la courbe précision-rappel, rappel sur les périodes de récession.
- **Livrable :** notebook et discussion du nombre limité d'épisodes de récession.
- **Risque :** une dizaine de récessions seulement : résultats très fragiles. Ne pas surinterpréter.

### F7. Anomalies sur le S&P 500

- **Question :** quelles journées de marché sont statistiquement atypiques, et correspondent-elles à des événements connus ?
- **Tâche :** détection d'anomalies sans étiquettes.
- **Données :** FRED SP500, https://fred.stlouisfed.org/graph/fredgraph.csv?id=SP500. Environ 2 500 observations quotidiennes depuis 2016.
- **Prétraitement :** calculer les rendements logarithmiques et la volatilité glissante sur 20 jours.
- **Modèles :** Isolation Forest, LOF.
- **Métriques :** proportion d'anomalies détectées, comparaison qualitative avec des événements documentés.
- **Livrable :** notebook et frise chronologique des anomalies.
- **Risque :** sans étiquettes, l'évaluation reste subjective. Le signaler clairement.

### F8. Peut-on battre la marche aléatoire ?

- **Question :** un modèle entraîné sur les rendements passés prédit-il le signe du rendement du lendemain mieux que le hasard ?
- **Tâche :** classification binaire (hausse ou baisse), avec référence de base.
- **Données :** FRED SP500, https://fred.stlouisfed.org/graph/fredgraph.csv?id=SP500.
- **Prétraitement :** rendements retardés, cible binaire, découpage chronologique.
- **Modèles :** prédiction de la classe majoritaire (référence), régression logistique, forêt aléatoire.
- **Métriques :** exactitude comparée à la référence, ratio de Sharpe d'une stratégie simulée après coûts.
- **Livrable :** notebook et conclusion sur l'efficience du marché.
- **Risque :** tester beaucoup de modèles et ne garder que le meilleur (*data snooping*) donne des résultats trompeurs.

### F9. Régimes de marché par partitionnement

- **Question :** le marché alterne-t-il entre des régimes identifiables de faible ou forte volatilité ?
- **Tâche :** partitionnement sur des indicateurs de rendement et de volatilité.
- **Données :** FRED SP500, https://fred.stlouisfed.org/graph/fredgraph.csv?id=SP500 ; comparaison avec l'indicateur USREC.
- **Prétraitement :** rendements et volatilité calculés sur fenêtres glissantes, standardisation.
- **Modèles :** k-means (référence), GMM.
- **Métriques :** silhouette, puis comparaison des régimes aux périodes de récession.
- **Livrable :** notebook et graphique des régimes superposé au cours.
- **Risque :** standardiser avec la moyenne et l'écart-type de toute la série introduit une information future. Standardiser uniquement avec une fenêtre passée.

### F10. Gestion de position par renforcement

- **Question :** un agent peut-il apprendre à passer de long à neutre à court selon l'historique des rendements ?
- **Tâche :** apprentissage par renforcement avec environnement personnalisé.
- **Données :** FRED SP500, https://fred.stlouisfed.org/graph/fredgraph.csv?id=SP500.
- **Prétraitement :** état défini par des rendements et une volatilité passés ; récompense égale au rendement de la position moins un coût à chaque changement de position.
- **Modèles :** Q-learning tabulaire (référence : position fixe), puis DQN simple.
- **Métriques :** rendement cumulé, ratio de Sharpe, drawdown maximal, sur un jeu de test postérieur chronologiquement.
- **Livrable :** notebook, environnement Gymnasium documenté, courbes de capital.
- **Risque :** surapprentissage au passé très fort. Décrire les hypothèses simplificatrices (absence de frais, exécution au cours de clôture).

---

## Points à noter

1. Le jeu FRED SP500 ne couvre que les dix dernières années. Il suffit pour F7 à F10 mais limite les conclusions.
2. Les liens FRED, Ken French et Hugging Face ont été testés et répondent. Pour F5, les rendements des portefeuilles doivent venir d'une source externe comme Yahoo Finance : cette source n'est pas une API officielle et peut changer sans préavis.
3. Les projets de finance exigent une attention particulière aux biais de temps (fuite temporelle, biais d'anticipation) et aux biais de sélection. Ces points sont signalés dans chaque fiche.

---

## Projets complémentaires (F11 à F20)

### Tableau récapitulatif

| N° | Titre | Type d'apprentissage | Difficulté | Source |
|---|---|---|---|---|
| F11 | Volatilité du change euro-dollar | Supervisé, régression sur séries temporelles | Intermédiaire | FRED DEXUSEU |
| F12 | Prévision du prix du pétrole WTI | Supervisé, régression sur séries temporelles | Intermédiaire | FRED DCOILWTICO |
| F13 | Pics de volatilité avec le VIX | Supervisé, classification déséquilibrée | Intermédiaire | FRED VIXCLS |
| F14 | Taux hypothécaire et taux à 10 ans | Supervisé, régression | Débutant | FRED MORTGAGE30US, DGS10 |
| F15 | Le moral des ménages annonce-t-il la croissance ? | Supervisé, régression sur séries temporelles | Intermédiaire | FRED UMCSENT, GDPC1 |
| F16 | Loi d'Okun : chômage et croissance | Supervisé, régression linéaire | Débutant | FRED UNRATE, GDPC1 |
| F17 | Prévision des mises en chantier de logements | Supervisé, régression sur séries temporelles | Débutant | FRED HOUST |
| F18 | Rendements hebdomadaires du Dow Jones | Supervisé, régression ou classification | Intermédiaire | UCI 329 |
| F19 | Groupes de titres du Dow Jones | Non supervisé, partitionnement | Débutant | UCI 329 |
| F20 | Structure de la courbe des taux par ACP | Non supervisé, réduction de dimension | Avancé | FRED DGS3MO, DGS1, DGS5, DGS10, DGS30 |

### Détail des projets

### F11. Volatilité du change euro-dollar

- **Question :** les variations passées du taux de change permettent-elles de prévoir l'amplitude des variations à venir ?
- **Tâche :** régression sur la valeur absolue du rendement (proxy de volatilité).
- **Données :** FRED DEXUSEU, https://fred.stlouisfed.org/graph/fredgraph.csv?id=DEXUSEU. Environ 7 000 observations quotidiennes depuis 1999.
- **Prétraitement :** rendements logarithmiques, valeurs manquantes (jours fériés) à retirer, retards des rendements absolus.
- **Modèles :** volatilité historique moyenne (référence), régression linéaire avec retards, gradient boosting. Un modèle GARCH peut servir de comparaison avancée.
- **Métriques :** RMSE et QLIKE, mesure adaptée à la volatilité.
- **Livrable :** notebook et graphique de la volatilité prévue contre la volatilité réalisée.
- **Risque :** la valeur absolue d'un rendement est une mesure bruitée de la volatilité. Ne pas interpréter une erreur faible comme une bonne prévision de direction.

### F12. Prévision du prix du pétrole WTI

- **Question :** le prix du pétrole à un mois peut-il être prévu à partir de ses valeurs passées et du contexte économique ?
- **Tâche :** régression sur séries temporelles.
- **Données :** FRED DCOILWTICO, https://fred.stlouisfed.org/graph/fredgraph.csv?id=DCOILWTICO. Environ 9 500 observations quotidiennes depuis 1986.
- **Prétraitement :** rééchantillonnage mensuel, retards, variables macroéconomiques issues de FRED (par exemple DGS10 ou taux directeur) ajoutées avec précaution.
- **Modèles :** marche aléatoire (référence), régression Ridge avec retards, gradient boosting.
- **Métriques :** RMSE et MAE sur un horizon d'un mois, comparés à la référence.
- **Livrable :** notebook, graphique prévision contre réel, discussion des régimes de prix.
- **Risque :** le prix a été négatif le 20 avril 2020 à la clôture du contrat. Traiter ou signaler ces valeurs extrêmes. La marche aléatoire est souvent difficile à battre : un résultat « égal à la référence » est un résultat honnête.

### F13. Pics de volatilité avec le VIX

- **Question :** peut-on anticiper, à partir des 20 derniers jours, qu'un pic de l'indice VIX au-dessus de 30 surviendra dans les cinq jours suivants ?
- **Tâche :** classification binaire déséquilibrée, car les pics sont rares.
- **Données :** FRED VIXCLS, https://fred.stlouisfed.org/graph/fredgraph.csv?id=VIXCLS. Environ 9 300 observations quotidiennes depuis 1990.
- **Prétraitement :** définir la cible, construire des variables sur fenêtres passées (moyenne, pente, écart à la moyenne mobile), découpage chronologique.
- **Modèles :** régression logistique (référence), forêt aléatoire, gradient boosting.
- **Métriques :** aire sous la courbe précision-rappel, rappel sur les pics, nombre de fausses alertes par an.
- **Livrable :** notebook et courbe de détection superposée au VIX.
- **Risque :** chevauchement des fenêtres d'observations voisines : les exemples ne sont pas indépendants. Ne pas utiliser de validation croisée aléatoire.

### F14. Taux hypothécaire et taux à 10 ans

- **Question :** quelle part des variations du taux hypothécaire à 30 ans s'explique par le taux à 10 ans ?
- **Tâche :** régression linéaire simple, puis avec retards.
- **Données :** FRED MORTGAGE30US, https://fred.stlouisfed.org/graph/fredgraph.csv?id=MORTGAGE30US (hebdomadaire, depuis 1971), et FRED DGS10 déjà utilisé en F2.
- **Prétraitement :** aligner les dates hebdomadaires, moyenne hebdomadaire du taux à 10 ans, écarts entre taux.
- **Modèles :** régression simple (référence), régression avec retards, comparaison de l'erreur sur deux périodes.
- **Métriques :** R², RMSE, stabilité du coefficient selon la période.
- **Livrable :** notebook et graphique de l'écart entre les deux taux dans le temps.
- **Risque :** une forte corrélation n'établit pas un lien causal. Le taux hypothécaire dépend aussi du spread de crédit des banques.

### F15. Le moral des ménages annonce-t-il la croissance ?

- **Question :** l'indice de confiance des consommateurs de l'université du Michigan prévoit-il la croissance du PIB réel au trimestre suivant ?
- **Tâche :** régression sur séries trimestrielles, avec agrégation mensuel vers trimestriel.
- **Données :** FRED UMCSENT, https://fred.stlouisfed.org/graph/fredgraph.csv?id=UMCSENT (mensuel, depuis 1952), et FRED GDPC1, https://fred.stlouisfed.org/graph/fredgraph.csv?id=GDPC1 (trimestriel, depuis 1947).
- **Prétraitement :** moyenne trimestrielle de l'indice, croissance trimestrielle du PIB, alignement des dates.
- **Modèles :** croissance passée seule (référence), régression avec l'indice retardé, gradient boosting.
- **Métriques :** RMSE sur le trimestre suivant, comparaison à la référence.
- **Livrable :** notebook et tableau comparatif des modèles.
- **Risque :** peu d'observations trimestrielles récentes et révisions du PIB. Utiliser la version de la série disponible à la date de prévision lorsque c'est possible.

### F16. Loi d'Okun : chômage et croissance

- **Question :** combien de points de croissance du PIB sont associés à un point de chômage en plus, dans les données américaines ?
- **Tâche :** régression linéaire simple, estimation d'un coefficient.
- **Données :** FRED UNRATE, https://fred.stlouisfed.org/graph/fredgraph.csv?id=UNRATE (mensuel), et FRED GDPC1 (trimestriel). Environ 300 observations trimestrielles.
- **Prétraitement :** moyenne trimestrielle du chômage, variation trimestrielle du PIB, alignement.
- **Modèles :** régression linéaire, puis découpage en deux périodes pour vérifier la stabilité.
- **Métriques :** coefficient estimé, intervalle de confiance, R².
- **Livrable :** notebook et comparaison du coefficient avant et après une période donnée.
- **Risque :** le coefficient d'Okun varie selon les époques. Ne pas le présenter comme une constante économique.

### F17. Prévision des mises en chantier de logements

- **Question :** les mises en chantier de logements suivent-elles une saisonnalité prévisible ?
- **Tâche :** prévision de série temporelle avec saisonnalité.
- **Données :** FRED HOUST, https://fred.stlouisfed.org/graph/fredgraph.csv?id=HOUST. 813 observations mensuelles depuis 1959.
- **Prétraitement :** décomposition saisonnière, variables de mois, retards, découpage chronologique.
- **Modèles :** saisonnalité moyenne par mois (référence), lissage exponentiel de Holt-Winters, régression avec variables de mois.
- **Métriques :** MAPE et RMSE sur les 24 derniers mois.
- **Livrable :** notebook et graphique de décomposition.
- **Risque :** les mises en chantier sont révisées après publication. Le signaler, comme pour F3.

### F18. Rendements hebdomadaires du Dow Jones

- **Question :** les rendements hebdomadaires passés et les prix d'ouverture permettent-ils de prévoir le rendement de la semaine suivante ?
- **Tâche :** régression, ou classification (hausse ou baisse), sur 30 titres.
- **Données :** UCI 329, https://archive.ics.uci.edu/dataset/329. 1 151 observations, 19 variables, données de 2011.
- **Prétraitement :** convertir les prix de chaque titre en rendements hebdomadaires, construire la cible « rendement de la semaine suivante » en évitant toute information future.
- **Modèles :** rendement nul ou moyenne historique (référence), régression linéaire, forêt aléatoire.
- **Métriques :** exactitude de la direction, RMSE, comparaison à la référence.
- **Livrable :** notebook et discussion de la précision des prévisions.
- **Risque :** le jeu couvre une seule année et une seule période : les résultats ne se généralisent pas. Une fuite est possible si les variables de la semaine cible sont utilisées.

### F19. Groupes de titres du Dow Jones

- **Question :** les titres du Dow Jones se regroupent-ils selon leur comportement de prix (rendement moyen, volatilité) ?
- **Tâche :** partitionnement de titres à partir de caractéristiques dérivées des prix.
- **Données :** UCI 329, https://archive.ics.uci.edu/dataset/329. Le même jeu que F18.
- **Prétraitement :** calculer par titre le rendement moyen, la volatilité, la corrélation avec le marché, puis standardiser.
- **Modèles :** k-means, CAH, GMM.
- **Métriques :** silhouette, interprétation des groupes par secteur d'activité.
- **Livrable :** notebook, dendrogramme et tableau des groupes.
- **Risque :** les groupes peuvent refléter la période choisie plutôt qu'une structure durable. Tester la stabilité en découpant la période.

### F20. Structure de la courbe des taux par ACP

- **Question :** trois facteurs suffisent-ils à décrire l'essentiel des mouvements de la courbe des taux américaine ?
- **Tâche :** réduction de dimension sur plusieurs maturités.
- **Données :** FRED DGS3MO, DGS1, DGS5, DGS10 et DGS30, par exemple https://fred.stlouisfed.org/graph/fredgraph.csv?id=DGS30. Données quotidiennes, à aligner sur la période commune (depuis 1977 pour DGS30).
- **Prétraitement :** aligner les dates, supprimer les jours sans valeur complète, travailler sur les variations journalières.
- **Modèles :** ACP (référence : une seule composante), comparaison avec une analyse factorielle.
- **Métriques :** part de variance expliquée par composante, interprétation des composantes comme niveau, pente et courbure.
- **Livrable :** notebook et graphique des trois premiers facteurs.
- **Risque :** la structure dépend de la période et de la politique monétaire en vigueur. Comparer plusieurs sous-périodes.

---

## Projets complémentaires (F21 à F30)

### Tableau récapitulatif

| N° | Titre | Type d'apprentissage | Difficulté | Source |
|---|---|---|---|---|
| F21 | Force du dollar et conditions d'échange | Supervisé, régression sur séries temporelles | Débutant | FRED DTWEXBGS |
| F22 | Anticipations d'inflation des marchés | Supervisé, régression sur séries temporelles | Intermédiaire | FRED T10YIE, CPIAUCSL |
| F23 | Spread de crédit Baa et récessions | Supervisé, classification déséquilibrée | Intermédiaire | FRED BAA10Y, USREC |
| F24 | Corrélation glissante entre Nasdaq et S&P 500 | Supervisé, régression sur séries temporelles | Intermédiaire | FRED NASDAQCOM, SP500 |
| F25 | Le taux directeur explique-t-il la pente de la courbe ? | Supervisé, régression | Débutant | FRED DFF, T10Y2Y |
| F26 | Scoring de crédit australien | Supervisé, classification | Intermédiaire | UCI 143 |
| F27 | Scoring de crédit explicable | Supervisé, classification et interprétation | Avancé | UCI 27 |
| F28 | Rendements des portefeuilles taille et valeur | Supervisé, régression multiple | Avancé | Ken French, 25 portefeuilles 5x5 |
| F29 | Le momentum est-il prévisible ? | Supervisé, classification | Intermédiaire | Ken French, facteur momentum |
| F30 | Value-at-Risk historique et paramétrique | Hors apprentissage supervisé, estimation statistique et backtesting | Intermédiaire | FRED NASDAQCOM |

### Détail des projets

### F21. Force du dollar et conditions d'échange

- **Question :** les variations passées de l'indice pondéré du dollar permettent-elles de prévoir sa variation le mois suivant ?
- **Tâche :** régression sur séries temporelles.
- **Données :** FRED DTWEXBGS, https://fred.stlouisfed.org/graph/fredgraph.csv?id=DTWEXBGS. Environ 5 400 observations quotidiennes depuis 2006.
- **Prétraitement :** rééchantillonnage mensuel, rendements, retards, traitement des jours sans valeur.
- **Modèles :** marche aléatoire (référence), régression linéaire avec retards, gradient boosting.
- **Métriques :** RMSE et MAE comparées à la référence.
- **Livrable :** notebook et graphique prévision contre réel.
- **Risque :** période courte, qui contient peu de cycles monétaires. Conclure avec prudence.

### F22. Anticipations d'inflation des marchés

- **Question :** le taux d'inflation implicite déduit des obligations indexées prédit-il l'inflation réalisée douze mois plus tard ?
- **Tâche :** régression sur séries mensuelles.
- **Données :** FRED T10YIE, https://fred.stlouisfed.org/graph/fredgraph.csv?id=T10YIE (depuis 2003), et FRED CPIAUCSL déjà utilisé en F3.
- **Prétraitement :** moyenne mensuelle du taux d'inflation implicite à dix ans, variation annuelle de l'indice des prix, décalage de douze mois.
- **Modèles :** dernière inflation observée (référence), régression linéaire sur le taux implicite, Ridge avec retards.
- **Métriques :** RMSE sur horizon de douze mois, biais moyen de la prévision.
- **Livrable :** notebook et graphique comparant anticipations et inflation réalisée.
- **Risque :** le taux implicite contient une prime de risque d'inflation. Il ne mesure pas une anticipation pure.

### F23. Spread de crédit Baa et récessions

- **Question :** l'écart de rendement entre obligations de qualité Baa et Treasury à dix ans annonce-t-il une récession dans les douze mois ?
- **Tâche :** classification binaire déséquilibrée.
- **Données :** FRED BAA10Y, https://fred.stlouisfed.org/graph/fredgraph.csv?id=BAA10Y (quotidien, depuis 1986), et USREC pour la cible.
- **Prétraitement :** moyenne mensuelle, variations du spread sur trois et six mois, cible « récession dans les douze mois ».
- **Modèles :** régression logistique sur le niveau du spread (référence), puis avec variations.
- **Métriques :** AUC, aire précision-rappel, nombre de fausses alertes.
- **Livrable :** notebook et courbe du spread avec périodes de récession.
- **Risque :** peu d'épisodes de récession. Ce projet se recoupe avec F6 : le comparer aux résultats du projet de courbe des taux.

### F24. Corrélation glissante entre Nasdaq et S&P 500

- **Question :** la corrélation entre les deux indices boursiers varie-t-elle dans le temps, et peut-on la prévoir ?
- **Tâche :** régression sur une mesure dérivée de séries temporelles.
- **Données :** FRED NASDAQCOM, https://fred.stlouisfed.org/graph/fredgraph.csv?id=NASDAQCOM (depuis 1971), et FRED SP500 déjà utilisé en F7 à F10. Les deux séries se recouvrent surtout depuis 2016.
- **Prétraitement :** rendements journaliers alignés, corrélation glissante sur 60 jours, retards de la corrélation.
- **Modèles :** dernière corrélation observée (référence), régression Ridge, gradient boosting.
- **Métriques :** RMSE de la corrélation prévue à 20 jours.
- **Livrable :** notebook et graphique de la corrélation glissante.
- **Risque :** les fenêtres glissantes se chevauchent, ce qui crée une dépendance entre observations. Utiliser un découpage chronologique strict.

### F25. Le taux directeur explique-t-il la pente de la courbe ?

- **Question :** le taux de la Réserve fédérale permet-il d'expliquer l'écart entre les taux à dix et à deux ans ?
- **Tâche :** régression linéaire simple, puis multiple.
- **Données :** FRED DFF, https://fred.stlouisfed.org/graph/fredgraph.csv?id=DFF (quotidien, depuis 1954), et FRED T10Y2Y, https://fred.stlouisfed.org/graph/fredgraph.csv?id=T10Y2Y (depuis 1976).
- **Prétraitement :** moyennes mensuelles, alignement des dates, écart entre taux.
- **Modèles :** régression simple, régression avec retards du taux directeur.
- **Métriques :** R², coefficient et son intervalle de confiance, stabilité sur deux sous-périodes.
- **Livrable :** notebook et analyse du signe du coefficient.
- **Risque :** les deux variables dépendent des anticipations du marché. Une corrélation ne prouve pas l'effet de la politique monétaire.

### F26. Scoring de crédit australien

- **Question :** quelles caractéristiques d'un demandeur sont associées à une demande de crédit acceptée ?
- **Tâche :** classification binaire, variables anonymisées et mixtes.
- **Données :** UCI 143, https://archive.ics.uci.edu/dataset/143. Environ 690 dossiers, 14 variables, noms et valeurs anonymisés.
- **Prétraitement :** traiter les valeurs manquantes codées, encodage des catégorielles, standardisation des numériques.
- **Modèles :** régression logistique (référence), arbre de décision, forêt aléatoire, gradient boosting.
- **Métriques :** AUC, exactitude, matrice de confusion.
- **Livrable :** notebook et comparatif des modèles.
- **Risque :** les attributs sont anonymisés, donc l'interprétation métier est limitée. Ne pas inventer de signification.

### F27. Scoring de crédit explicable

- **Question :** un modèle de scoring performant reste-t-il interprétable pour justifier une décision de crédit ?
- **Tâche :** classification binaire avec analyse d'explicabilité.
- **Données :** UCI 27, https://archive.ics.uci.edu/dataset/27. Environ 690 dossiers, 15 variables, données anonymisées.
- **Prétraitement :** encodage, imputation des valeurs manquantes par médiane ou catégorie dominante, découpage stratifié.
- **Modèles :** régression logistique avec coefficients interprétés, gradient boosting expliqué par valeurs SHAP.
- **Métriques :** AUC, calibration, cohérence des explications entre modèles.
- **Livrable :** notebook et fiche d'explication pour un refus de crédit.
- **Risque :** les explications locales ne sont pas une preuve de non-discrimination. Le signaler dans le rapport.

### F28. Rendements des portefeuilles taille et valeur

- **Question :** les facteurs taille et valeur expliquent-ils les rendements de portefeuilles classés par taille et ratio valeur comptable sur valeur de marché ?
- **Tâche :** régression multiple sur 25 portefeuilles.
- **Données :** Ken French, https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/ftp/25_Portfolios_5x5_CSV.zip (fichier zip à décompresser), associé aux facteurs Fama-French déjà utilisés en F5.
- **Prétraitement :** extraire les rendements mensuels par portefeuille, aligner avec les facteurs, rendements en excès.
- **Modèles :** modèle à un facteur (référence), modèle à trois facteurs, comparaison de leurs erreurs.
- **Métriques :** alphas par portefeuille, R² ajusté, test conjoint des alphas nuls.
- **Livrable :** notebook et tableau de résultats par portefeuille.
- **Risque :** les portefeuilles sont construits ex post à partir de données qui mélangent sélection et rendement. Ce biais doit être discuté.

### F29. Le momentum est-il prévisible ?

- **Question :** la performance passée d'un actif (momentum) prédit-elle sa performance du mois suivant ?
- **Tâche :** classification binaire (rendement positif ou négatif le mois suivant), à partir du facteur momentum.
- **Données :** Ken French, https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/ftp/F-F_Momentum_Factor_CSV.zip (fichier zip à décompresser).
- **Prétraitement :** construire les rendements passés du facteur sur plusieurs horizons, cible binaire, découpage chronologique.
- **Modèles :** signe du dernier mois (référence), régression logistique, forêt aléatoire.
- **Métriques :** exactitude par rapport à la référence, rendement d'une stratégie simulée après coûts.
- **Livrable :** notebook et conclusion sur la persistance du momentum.
- **Risque :** les crashes du momentum sont rares et massifs. Une bonne exactitude moyenne peut masquer des pertes très importantes.

### F30. Value-at-Risk historique et paramétrique

- **Question :** quelle perte journalière maximale l'indice Nasdaq Composite peut-il causer avec 99 % de confiance, et les méthodes d'estimation donnent-elles des résultats cohérents ?
- **Tâche :** estimation statistique et backtesting. Ce projet relève de la science des données appliquée à la finance, plus que de l'apprentissage supervisé.
- **Données :** FRED NASDAQCOM, https://fred.stlouisfed.org/graph/fredgraph.csv?id=NASDAQCOM. Environ 14 000 observations quotidiennes depuis 1971.
- **Prétraitement :** rendements logarithmiques, découpage en fenêtres d'estimation glissantes.
- **Modèles :** VaR historique, VaR paramétrique gaussienne, VaR avec volatilité estimée par modèle GARCH.
- **Métriques :** taux de dépassements, test de Kupiec de couverture, comparaison des pertes moyennes au-delà de la VaR.
- **Livrable :** notebook et graphique des rendements avec seuils de VaR.
- **Risque :** les rendements ne sont pas gaussiens et leurs variances varient dans le temps. La méthode paramétrique sous-estime souvent les pertes extrêmes.
