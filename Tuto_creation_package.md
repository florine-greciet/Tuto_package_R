# Création d'un package R

## Pourquoi créer un package R 

- Pour soit même : Pour conserver le bénéfice des fonctions que l'on crée pour soit.
- Pour les autres : Pour partager un package et la documentation associée est beaucoup plus simple que de partager des scripts.

Liens utiles pour créer un package : 
- http://kbroman.org/pkg_primer/
-   https://hilaryparker.com/2014/04/29/writing-an-r-package-from-scratch/

- http://perso.ens-lyon.fr/lise.vaudor/creation-dun-package-r/

- https://thinkr.fr/creer-package-r-quelques-minutes/

- https://www.math.u-psud.fr/~goude/Materials/ProjetMLF/editer_package_R.html 

## Création d'un dépôt local pour le package

### Dépôt et squelette :

- créer un dossier **monpackage** dans l'emplacement voulu (mes documents par exemple)

Pour créer l'architecture d'un package, nous aurons besoin des deux packages suivants : 
- le package **devtools** : pour la création et l'installation du package.
- le package **roxygen2** : pour la création de la documentation

```R
if(!require(devtools)){install.packages("devtools")}
if(!require(roxygen2)){devtools::install_github("klutometis/roxygen")}
```

Pour créer mon package il suffit d'utiliser la commande suivante : 
```R
devtools::create("monpackage")
```
Selon la version de **R** utilisée, cette version serait obsolète et il faudrait  plutôt utiliser la commande suivante :
```R
usethis::create_package("monpackage")
```

Pour avoir la console de **R** en mode package il suffit d'ouvrir le projet en faisant : `project` (en haut à droite) 
puis `open project`.

Sinon il est également possible de créer son package **R** directement à partir de la console en faisant : `project`-> `new project` -> `new directory` -> `create package using devtools`

Lorsque l'on crée son package, les fichiers suivants sont créés dans le dossier choisi : 
- un fichier **DESCRIPTION**
- un fichier **NAMESPACE**
- un dossier **R** (qui contiendra les scripts contenant eux même les fonctions)
- Les fichiers **.gitignore**, **.Rbuildignire**, et un projet **Rstudio**

Le **.gitignore** permet d’indiquer les fichiers/dossiers que l’on ne souhaite pas versionner sur git, le **.Rbuildignore** permet de lister les fichiers/dossiers qui ne doivent pas être pris en compte par le package, c’est le cas du fichier en .Rproj, par exemple, qui indique qu’il s’agit d’un projet Rstudio

## Modification du fichier **DESCRIPTION**
Il faut modifier : 
- `Le titre` : mettre un titre qui décrit l'usage du package  
- `l'auteur` : 
```R
Authors@R: c(person("Florine", "Greciet", email = "florine.greciet@gmail.com", role = c("aut", "cre")),person("Romain", "Azaïs", email = "romain.azais@", role = c("aut")))
```
`aut` est utilisé pour les auteurs du package. `cre`est utilisé pour la personne qui maintiendra le package. 

- `Description`: description de ce que fait le package. Ne pas oublier de conserver le `.`à la fin du paragraphe.
Dans la partie description, nous pouvons citer un travail qui a été réalisé et qui fait appel au package R. 
Par exemple : Travail basé sur l'article : Florine Greciet Nom de l'article. Annee. <https://nomarticle>.

- `Depends`: A partir de quelle version on peut utiliser notre package. 

- `Licence`: Licence utilisée (GPL , MIT)
par exemple : 
```R 
Package: HSPOR
Title: Modeling and ruptures detections
Version: 0.0.0.9000
Authors@R: person("Florine", "Greciet", email = "florine.greciet@gmail.com", role = c("aut", "cre"))
Description: Faur functions to model and detect ruptures on data.
Depends: R (>= 3.2.0)
License: GPL-3
Encoding: UTF-8
LazyData: true
```


## Organisation des scripts

Il faut déposer l'ensemble des scripts contenant les fonctions du packages sous le dossier **R**.

On peut organiser les fonctions soit en les regroupant dans un script soit en faisant une fonction = un script.

## Documentation 
### Fichiers d'aide associés aux fonctions

#### Entête de fonction
Pour générer l'entête d'un fichier il suffit d'être à l'intérieur de la fonction puis de cliquer sur **CTRL + ALT + SHIFT + R**

```R
#' Title
#'
#' @param X 
#' @param Y 
#'
#' @return
#' @export
#'
#' @examples
somme <- function(X,Y)
  return(X+Y)
}
```

Il faut compléter chaque entête : 

```R
#' somme
#'
#' @param X a number
#' @param Y a number
#'
#' @return a number
#' @export
#'
#' @examples
#' somme(1,1)
somme <- function(X,Y)
  return(X+Y)
}
```

#### Import de librairie utilisée dans une fonction

Si la fonction contient des packages qu'il faut importer 
il faut ajouter des `import` (pour importer toute la librairie) ou des `importFrom` (pour importer une fonction de la librairie) :

```R
#' moyenne
#' @param x a vector
#'
#' @return a number
#' @export
#' @importFrom stats na.omit
#' @import magrittr
#' @examples
#' moyenne(c(1,6,7,NA))
moyenne <- function(x)
  x = x %>% na.omit()
  sum(x)/length(x) 
}
```
Attention, si on utilise une fonction uniquement dans la partie exemple, 
```R
#' @examples
#' library(base)
#' echantillon = sample(seq(1,100,by=1),15)
#' moyenne(echantillon)
```
alors il n'est pas nécessaire d'importer la fonction par un `@import` il faut directement écrire dans l'exemple `library(nomLibrary)`. 

Pour que les imports de librairie soit pris en compte il faut également les ajouter dans le fichier **DESCRIPTION** du pakage. Afin de ne pas modifier directement ce fichier et de s'assurer de la reproductibilité du travail effectué, il est recommandé de créer un fichier intitulé `devtools_history.R`. Celui-ci contiendra tous les appels à **devtools** qui doivent être effectués. Ce fichier n'est donc là que pour les archives du package. Il peut être positionné à la racine du package. 

Ce fichier contient : 
```R
usethis::use_build_ignore("devtools_history.R")
# Cette fonction permet d'importer le package stats dans
# le doc DESCRIPTION
usethis::use_package("stats") #Les packages à ajouter
usethis::use_package("magrittr")
```

L'instruction `usethis::use_build_ignore("devtools_history.R")` indique à `Rstudio` qu'il doit ignorer ce fichier. Cela évite de recevoir un warning lié à la présence de fichier dans le package.

Il est nécessaire de compiler ce fichier manuellement pour que les lignes de codes s'executent car elles ne seront pas automatiquement lancées.

Si l'on regarde le fichier **DESCRIPTION** après avoir compiler le code ci-dessus : 

```R 
Package: HSPOR
Title: Modeling and ruptures detections
Version: 0.0.0.9000
Authors@R: person("Florine", "Greciet", email = "florine.greciet@gmail.com", role = c("aut", "cre"))
Description: Faur function to model and detect ruptures on data.
Depends: R (>= 3.2.0)
License: GPL-3
Encoding: UTF-8
LazyData: true
RoxygenNote: 6.1.1
Imports: 
    stats,
      magrittr
```

#### Ecriture des fichiers d'aides à partir des entêtes
La commande suivante permet de générer les fichiers d'aides à partir des entêtes :
```R 
devtools::document("monpackage")
```
Cette commande est directement exécutée si l'on fait un `check` du package.
Il suffit ensuite de cliquer sur `install and restart` pour réinstaller le pacage pour mettre à jour la documentation. 

Pour que l'aide soir clairement écrite il est important de bien compléter son entête. 

Par exemple 
```R
#' @title Inference methods for two regimes

#' @description  H2SPOR is an inference method that estimate, under regularity constraint, the parameters of a piecewise
#' polynomial regression model with 2 regimes.
#'
#' @param X a numerical vector corresponding to the explicative variable.
#' @param Y a numerical vector corresponding to the variable to explain. Y contains two regimes that
#' could be model by polynomials.
#' @param deg Degree of the polynomials
#' @param constraint Number which determines the applied assumption. By default assumption is 1. If this
#' term is 1, The estimation is obtained under assumption of continuity. If it is 0 or 2, it is designed for
#' no assumption or assumption of differentiability.
#' @param EM A logical value. If TRUE (default) the estimations will be obtained accross an EM algorithm.
#' If FALSE the estimations will be obtained accross a fixed point algorithm.
#' @param TimeTrans_Prop A numerical vector. This vector is empty by default. If you want to test one specific
#' time of transition you can put it on TimeTrans_Prop.
#' @param plotG A logical value. If TRUE (default) the H2SPOR is plottedy.
#'
#' @return A data.frame which contains the estimated parameters (Times of transitions, polynomials coefficients and variances)
#'  of the piecewise polynomial regression model with two regimes.
#'  If plotG = TRUE, the data and the estimated model will be plotted.
#'
#' @import graphics
#' @export
#'
#' @examples
#' xgrid1 = seq(0,10,by=0.2)
#' xgrid2 = seq(10.2,20,by=0.2)
#' ygrid1 = xgrid1^2-xgrid1+1+ rnorm(length(xgrid1),0,3)
#' ygrid2 = rep(91,length(xgrid2))+ rnorm(length(xgrid2),0,3)
#' xgrid = c(xgrid1,xgrid2)
#' ygrid = c(ygrid1,ygrid2)
#'
#' H2SPOR(xgrid,ygrid,2,1,TRUE,c())
```

Après avoir réinstallé le package, si je tape `?H2SPOR` (H2SPOR est le nom de ma fonction), alors l'aide de la fonction s'ouvre sur Rstudio et contient toutes les informations indiquées dans l'entête de la fonction.

#### Création d'une vignette
Une `vignette` correspond à la documentation informelle d'un package.

Pour générer une vignette : 
```R 
devtools::use_vignette("monpackage_vignette",pkg="monpackage")
```
On peut alors aller modifier le fichier **vignettes/monpackage_vignette.rmd** et y inclure les explications, la bibliographie et les exeples d'application du package.

## Vérification du package : 
Il suffit de cliquer sur `Build`-> `check`
Il permet de vérifier qu'il n'y a pas d'erreur dans la construction du package ou dans la construction d'une fonction.

 Après avoir effectuer un check il est essentil d'avoir en sortie :
 ```R
 0 error x | 0 warnings v | 0 notes v
```

Si tel n'est pas le cas l'erreur est indiquée dans le texte : 
- Par exemple : 
```R 
 > ### ** Examples
  > 
  > .Observations_weights_2_SPOR(seq(1,100,by=0.5),20)
  Error in .Observations_weights_2_SPOR(seq(1, 100, by = 0.5), 20) : 
    could not find function ".Observations_weights_2_SPOR"
  Execution halted

1 error x | 0 warnings v | 0 notes v
Erreur : R CMD check found ERRORs
Execution arretee

Exited with status 1.
```
Il est donc important de régler les erreurs.

Pour les limiter et s'assurer d'une construction efficace du package, il est préférable d'impporter les fonctions une a une et d'effectuer un check après l'introduction de toute nouvelle fonction.


## Installation et utilisation du package en local

Installation depuis le dépôt local vers une librairie : 
```R
usethis::install("monpackage")
```
sinon on peut aussi faire : **CTRL + SHIFT + B**
où encore : dans la console : cliquer sur `Install and Restart`
Une fois le package installé on peut utiliser ses fonctions.

Pour que d'autres personnes puissent utiliser notre package avant qu'il soit déposer sur le CRAN il suffit de faire 
`build`-> `More`-> `build source package`. 
Un fichier tar.gz (HSPOR_1.0.0.tar.gz dans mon cas) sera créé dans le fichier qui contient le package.
Il suffit ensuite d'aller dans R studio puis installer le package en choisisant de l'installer à partir d'un fichier tar.gz. 

## Installation du package sous **CRAN**

Le dépôt du package sur **CRAN** a pour intéret de permettre l'installation des packaes par cette commande, et de garantir que le package est testé sur divers systèmes d'exploitation.
