# Projet Data Science

**Université Hassan 1er — École Nationale de Commerce et de Gestion de Settat (ENCG)**

## Les variations passées du taux de change permettent-elles de prévoir l'amplitude des variations à venir ?

**Réalisé par :** Hiba Fennich, Hiba Ghassane
**Encadré par :** Mr Abderrahim LARHLIMI

---

# Prévoir l'amplitude des variations du taux de change EUR/USD

## 1. Introduction et problématique

Les variations passées du taux de change permettent-elles de prévoir l'amplitude des variations à venir ? La question compte pour mesurer le risque de change et dimensionner une couverture. Le sens d'un mouvement (hausse ou baisse) est quasi imprévisible ; en revanche, l'ampleur des fluctuations pourrait garder de la mémoire, car les gros mouvements arrivent « en grappes ».

Le problème est donc de prévoir, un jour à l'avance, l'amplitude de la variation de l'EUR/USD, et non sa direction. On la mesure par la valeur absolue du rendement |r|, proxy de la volatilité. Hypothèse nulle (H0) : le passé n'aide pas, et aucun modèle ne fait mieux que la volatilité historique moyenne. On compare quatre approches : cette référence, une régression linéaire avec retards, un gradient boosting et un GARCH(1,1), avec deux métriques, le RMSE et la QLIKE (adaptée à la volatilité). Précaution centrale : |r| est une mesure très bruitée de la volatilité, donc une erreur faible sur |r| n'est pas une bonne prévision de direction.

## 2. Données et prétraitement

### 2.1 Source et nettoyage

Les données viennent de la Réserve fédérale (FRED, série DEXUSEU) : nombre de dollars pour un euro, un relevé par jour ouvré, du 4 janvier 1999 au 30 octobre 2026, soit 7 260 lignes. Les 281 jours sans cotation (environ 4 %, jours fériés) sont retirés ; il reste 6 978 rendements logarithmiques r<sub>t</sub> = 100 × ln(S<sub>t</sub> / S<sub>t−1</sub>). Les taux restent entre 0,83 et 1,60 $ : aucune valeur aberrante. Le |r| moyen est de 0,42 % par jour (écart-type 0,57 %) ; 64 rendements sont exactement nuls.

> *[Figure 1 – Taux EUR/USD, rendement logarithmique quotidien et |r| (proxy de volatilité).]*

Le niveau du taux change beaucoup (1,17 $ en 1999, 0,83 $ vers 2000-2001, 1,60 $ à la mi-2008, entre 1,05 et 1,25 $ depuis 2015) : on travaille donc sur les rendements. Ceux-ci oscillent autour de zéro, mais leur amplitude varie (étroite en 2005-2007, 2013-2014 et 2017-2019, très large en 2008-2009). Les plus gros mouvements suivent la crise financière (+4,6 % le 19 mars 2009) ou des décisions de banques centrales (+3,1 % le 3 décembre 2015 après la BCE, −2,7 % le 15 décembre 2016 après la Fed). Les jours sans cotation (7 à 12 par an) sont répartis de façon stable : ils ne biaisent pas une période plus qu'une autre.

### 2.2 Construction des variables et découpage

- **Cible :** |r<sub>t+1</sub>|, l'amplitude du jour ouvré suivant.
- **Variables explicatives :** les rendements absolus des cinq derniers jours (a0 = |r<sub>t</sub>| à a4) et leurs moyennes sur 5, 22 et 66 jours (semaine, mois, trimestre).
- **Référence :** la volatilité historique moyenne, c'est-à-dire la moyenne de tous les |r| observés depuis 1999 jusqu'à t.
- **Point essentiel : ne pas tricher.** Toutes les variables sont mesurées à la clôture de t (fenêtres arrière seulement). Le découpage est chronologique, jamais aléatoire : la volatilité d'un jour ressemble à celle de ses voisins, et un mélange au hasard serait une fuite d'information.

**Tableau 1 – Découpage chronologique (selon la date du jour prévu)**

| Échantillon | Période | Jours | Rôle |
|---|---|---:|---|
| Entraînement | avril 1999 – décembre 2014 | 3 955 | Réglage du boosting, facteur k, autocorrélations |
| Test | janvier 2015 – octobre 2026 | 2 957 | Évaluation walk-forward, un jour prévu à la fois |

## 3. Méthodologie

### 3.1 Modèles comparés

- **Volatilité historique moyenne (référence) :** prévoir toujours la moyenne passée. C'est le niveau à battre.
- **Régression linéaire :** |r<sub>t+1</sub>| expliqué par les huit variables (a0 à a4, ma5, ma22, ma66).
- **Gradient boosting :** mêmes variables ; profondeur et nombre d'arbres choisis une seule fois par validation croisée temporelle sur l'entraînement (résultat : profondeur 1, 150 arbres).
- **GARCH(1,1) à loi de Student**, estimé sur les rendements seuls : σ<sub>t+1</sub>² = ω + α r<sub>t</sub>² + β σ<sub>t</sub>², avec une prévision de |r| égale à k·σ<sub>t+1</sub>.

### 3.2 Évaluation walk-forward

Les modèles sont réestimés tous les 21 jours ouvrés (environ un mois) sur les seules lignes dont la cible est déjà connue, puis prévoient les 21 jours suivants : c'est ce qu'un praticien ferait en temps réel. Les hyperparamètres du boosting et le facteur k sont fixés sur l'entraînement initial, jamais sur le test.

### 3.3 Métriques

Le RMSE mesure l'erreur sur |r<sub>t+1</sub>| en points de pourcentage. La QLIKE s'applique à la variance : pour une variance prévue h et un proxy r², c'est la moyenne de ln h + r²/h. Elle pénalise davantage une volatilité sous-estimée et reste un bon critère de comparaison quand le proxy est bruité (Patton, 2011). La variance prévue est h = (|r| prévu / k)², avec k = E|r| / √E[r²] = 0,74 (0,80 pour une loi normale ; les queues épaisses le font baisser) et un plancher de 0,05 % sur |r| prévu. Pour les deux métriques, plus c'est bas, mieux c'est.

## 4. Analyse du code

Le notebook (exécutable dans Google Colab) suit 12 étapes. Tout le prétraitement est écrit en SQL (SQLite) ; Python sert aux modèles, aux tests et aux graphiques. Le tableau 2 résume ce que fait chaque bloc de code et pourquoi.

**Tableau 2 – Structure du notebook et justification des choix**

| Étape | Ce que fait le code | Pourquoi |
|---|---|---|
| 1. Imports et paramètres | Charge sqlite3, pandas, scikit-learn, arch et scipy ; ouvre la base SQLite en mémoire ; fixe la graine aléatoire (42). | Obtenir les mêmes résultats à chaque exécution. |
| 2. Chargement | Télécharge DEXUSEU depuis FRED, garde le texte brut (table fred_brut), convertit en nombres en SQL (fred_jour) et affiche un bilan de qualité. | Les jours sans cotation (vide ou « . ») deviennent NULL, sans erreur de conversion. |
| 3. Rendements | Retire les 281 jours sans cotation, calcule r = 100·ln(S<sub>t</sub>/S<sub>t−1</sub>) avec LAG, puis \|r\| et r². | Produire la cible ; LAG sur les seuls jours cotés évite les rendements vides. |
| 4. Exploration | Autocorrélations de r et de \|r\| (retards 1 à 60, avant 2015) ; table des 10 régimes de marché. | Vérifier que la volatilité est persistante, donc prévisible. |
| 5. Variables et découpage | Table variables : a0 à a4, ma5, ma22, ma66, volatilité historique moyenne, cible par LEAD, jeu train/test selon la date du jour prévu. | Aucune information du futur ; découpage chronologique. |
| 6. Bruit du proxy | Estime k sur l'entraînement ; construit l'oracle (volatilité des 10 jours avant et après le jour cible). | Mesurer le plafond de qualité imposé par le bruit de \|r\|. |
| 7. Modèles | Règle le boosting (TimeSeriesSplit), puis walk_forward : réestimation des 4 modèles tous les 21 jours. | Imiter l'usage réel : prévoir chaque jour avec le seul passé connu. |
| 8. Résultats | Calcule RMSE et QLIKE, les compare à la référence ; recalcule les mêmes chiffres en SQL. | Mesurer le gain avec une métrique adaptée à la volatilité, et contrôler le calcul. |
| 9. Prévu contre réalisé | Trace le graphique central, deux zooms (Covid, 2022), la calibration et le RMSE glissant. | Voir où les modèles réussissent et où ils échouent. |
| 10. Régimes et robustesse | Métriques par régime ; sans Covid, sans les 1 % de plus grosses erreurs, sur trois sous-périodes. | Vérifier que la conclusion ne tient pas à un seul épisode. |
| 11. Tests statistiques | Diebold-Mariano (Newey-West), bootstrap par blocs de 22 jours, contrôle de direction. | L'écart est-il réel ou dû au hasard ? La prévision d'amplitude dit-elle le sens ? |
| 12. Synthèse et export | Tableau de synthèse, conclusion automatique, export de trois fichiers CSV (prévisions, métriques, variables). | Réutiliser les résultats sans réexécuter. |

## 5. Découverte : persistance de la volatilité et bruit du proxy

### 5.1 La volatilité a de la mémoire

Sur la période d'entraînement (avant 2015), l'autocorrélation du rendement est presque nulle (0,008 au retard 1) : le passé ne dit rien du signe. Celle de |r| est positive et décroît très lentement : 0,03 au retard 1 (au niveau du seuil à 5 %), environ 0,10 aux retards 2, 5 et 10, 0,13 à 22 jours et encore 0,09 à 60 jours.

> *[Figure 2 – Autocorrélation de r (gauche, quasi nulle) et de |r| (droite, positive et persistante) ; tirets : seuil à 5 %.]*

À gauche, presque toutes les barres restent dans la bande de confiance. À droite, dès le retard 2, toutes les barres de |r| dépassent la bande : les gros mouvements arrivent en grappes. Les régimes de marché (définis après coup, pour le diagnostic seulement) le confirment : la volatilité annualisée atteint 16,8 % pendant la crise financière de 2008-2009 et environ 10 % pendant la crise de la dette et la divergence BCE / Fed, contre 6 à 7 % en 2017-2020 et depuis 2023. Une moyenne sur plusieurs semaines devrait donc être plus informative que la seule valeur de la veille.

### 5.2 Le bruit du proxy |r|

Si r = σz avec z normal, l'erreur quadratique minimale sur |r| vaut environ 0,6 σ, même si l'on connaissait parfaitement σ. On le mesure avec un oracle : la volatilité réalisée sur les 10 jours avant et après le jour cible (jour exclu). Il utilise le futur : ce n'est pas un modèle, seulement un plafond. Sur le test, il n'atteint qu'un RMSE de 0,3165 % sur |r| (88 % du |r| moyen de 0,3598 %), du même ordre que les trois modèles (0,315 à 0,317 %) et seulement 7 % sous la référence (0,340 %). Le plafond de qualité est donc proche de la référence, ce qui limite tout gain en RMSE. Une erreur de 0,32 % n'est pas « mauvaise » en soi.

> *[Figure 3 – Même avec la bonne volatilité (oracle, trait noir), |r| reste très dispersé (mars-décembre 2022).]*

## 6. Résultats : tous les modèles battent la référence, le GARCH(1,1) est légèrement devant

**Tableau 3 – Performances sur le jeu de test 2015-2026 (plus bas = mieux)**

| Modèle | RMSE (%) | QLIKE | RMSE / référence | Gain RMSE (%) | QLIKE − référence |
|---|---:|---:|---:|---:|---:|
| Volatilité historique moyenne (réf.) | 0,3400 | −0,3669 | 1,000 | 0,00 | 0,0000 |
| Régression linéaire | 0,3173 | −0,5429 | 0,934 | 6,65 | −0,1761 |
| Gradient boosting | 0,3173 | −0,5392 | 0,934 | 6,65 | −0,1723 |
| GARCH(1,1) | 0,3154 | −0,5592 | 0,928 | 7,21 | −0,1923 |

Les trois modèles qui utilisent le passé battent la référence ; le GARCH(1,1) arrive en tête dans les deux métriques (gain de 7,21 % sur le RMSE). La régression linéaire et le boosting font pratiquement jeu égal : la relation entre le passé et la volatilité à venir est lisse et presque additive, d'où un meilleur boosting très simple (profondeur 1). Le gain en RMSE paraît modeste à cause du bruit de |r| (section 5.2), pas parce que les modèles prévoient mal la volatilité.

> *[Figure 4 – Volatilité prévue la veille contre volatilité réalisée, 2015-2026, avec zooms Covid-19 (2020) et 2022. Points gris : |r| quotidiens ; courbe noire : moyenne centrée sur 22 jours (affichage uniquement).]*

- **Vue d'ensemble.** Les trois modèles suivent bien la volatilité réalisée (haute en 2015-2016, basse de 2018 à début 2020, remontée en 2022-2023, calme en 2024-2026). La référence reste figée entre 0,47 % et 0,42 % et surestime la volatilité en période calme. La calibration le confirme : les prévisions des modèles s'alignent sur le |r| réalisé de 0,23 % à 0,58 %, celles de la référence ne discriminent rien.
- **Covid et 2022.** En mars 2020, la volatilité réalisée monte à environ 0,77 % alors que les prévisions restent autour de 0,25 % jusqu'au choc, puis plafonnent vers 0,5 %. En 2022, elles sous-estiment les poussées de février-mars et de septembre-novembre. Les modèles réagissent après le choc, pas avant : ils ne peuvent pas anticiper une rupture soudaine.

## 7. Tests statistiques

Contre la référence, tous les gains sont très significatifs ; entre modèles, seul l'avantage du GARCH ressort, et il reste très petit.

1. **Test de Diebold-Mariano :** compare les pertes jour par jour (erreur quadratique, puis QLIKE), avec une correction de Newey-West (10 retards) car les pertes sont très autocorrélées.
2. **Bootstrap par blocs (blocs de 22 jours) :** intervalle de confiance à 95 % du rapport de RMSE et de la différence de QLIKE avec la référence.
3. **Contrôle de direction :** la prévision d'amplitude ne doit contenir aucune information sur le signe du rendement.

> *[Figure 9 – Intervalles de confiance à 95 % par bootstrap par blocs, contre la référence.]*

| Test | Résultat |
|---|---|
| Diebold-Mariano contre la référence | Statistiques de −8 à −10, p-values proches de zéro |
| Bootstrap : rapport de RMSE / différence de QLIKE | 0,91 à 0,95 (exclut 1) / −0,23 à −0,13 (exclut 0) |
| Boosting contre régression linéaire | Non distinguables (p = 0,98 en erreur quadratique, 0,39 en QLIKE) |
| GARCH contre les deux autres | p entre 0,004 et 0,007 (seuil de Bonferroni d'environ 0,004 : avantage limite) |
| Corrélation prévision / signe suivant | De 0,00 à 0,03, non significative ; signe d'aujourd'hui = signe de demain 49,7 % du temps (p = 0,74) |

## 8. Limites et pistes d'amélioration

- **Données :** relevé quotidien unique de la Fed, pas une clôture à heure fixe (bruit en plus) ; un rendement après un jour férié porte sur deux jours calendaires ; horizon d'un jour seulement.
- **Référence simple :** la moyenne depuis 1999 est volontairement simple. Une moyenne mobile sur 22 jours serait un adversaire plus dur : le gain de 7 % est mesuré contre une référence faible.
- **Régimes :** définis après coup, utilisables pour le diagnostic uniquement, jamais comme variable explicative.
- **Tests multiples :** l'avantage du GARCH sur les deux autres modèles ne résiste pas clairement à une correction de Bonferroni.
- **Pistes :** moyenne mobile sur 22 jours comme référence ; GARCH asymétrique (GJR, EGARCH) ou régression HAR ; variables externes (écart de taux BCE / Fed, VIX) ; prévisions à 5 ou 22 jours ; volatilité réalisée intrajournalière, bien moins bruitée que |r|.

## 9. Conclusion

**Oui, mais avec des limites.** Le passé aide à prévoir l'amplitude des variations de l'EUR/USD, et c'est statistiquement très solide ; il n'aide pas à prévoir leur sens.

- **Source de la prévisibilité :** la persistance de la volatilité (figure 2). L'information utile semble concentrée dans les moyennes récentes (22 et 66 jours), d'où l'équivalence entre régression linéaire et boosting.
- **Gain réel mais modeste :** environ 7 % de RMSE et 0,17 à 0,19 point de QLIKE (tableau 3), car le plafond de qualité de l'oracle est proche de la référence (figure 3).
- **Choix du modèle :** un modèle simple suffit. Le GARCH(1,1) est le meilleur sur les deux métriques, mais son avantage est marginal et fragile aux tests multiples.
- **Chocs imprévisibles :** les modèles sont les moins utiles quand la prévision compterait le plus (Covid, décisions de banques centrales), car ils réagissent après le choc (figure 4).
- **Usage :** utile pour dimensionner un risque ou une couverture, surtout en période calme ; inutile pour parier sur la direction du change.
