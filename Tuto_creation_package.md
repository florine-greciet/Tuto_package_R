# Création d'un package R

## Pourquoi créer un package R 

- Pour soit même : Pour conserver le bénéfice des fonctions que l'on crée pour soit.
- Pour les autres : Pour partager un package et la documentation associée est beaucoup plus simple que de partager des scripts.

Liens utiles pour réer un package : 
- http://kbroman.org/pkg_primer/
-   https://hilaryparker.com/2014/04/29/writing-an-r-package-from-scratch/

- http://perso.ens-lyon.fr/lise.vaudor/creation-dun-package-r/

- https://thinkr.fr/creer-package-r-quelques-minutes/

## Création d'un dépôt local pour le package

### Dépôt et squelette :

- créer un dossier **monpackage** dans l'emplacement voulu (github,document)

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
- un dossier**R** (qui contiendra les scripts contenant eux même les fonctions)
- Les fichiers **.gitignore**, **.Rbuildignire**, et un projet **Rstudio**

## Modification du fichier **DESCRIPTION**
Il faut modifier : 
- `Le titre` : mettre un titre qui décrit l'usage du package  
- `l'auteur` : 
```R
Authors@R: person("Florine", "Greciet", email = "florine.greciet@gmail.com", role = c("aut", "cre"))
```
- `Description`: description de ce que fait le package. Ne pas oublier de conserver le `.`à la fin du paragraphe.

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
il faut ajouter des `import`ou des `importFrom` :

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

Pour que les imports de librairie soit pris en compte il faut également les ajouter dans le fichier **DESCRIPTION** du pakage. Pour faire cela il faut créer un fichier `devtools_history.R`qui sera enregistrer dans le package `monpackage`.

Ce fichier contient : 
```R
usethis::use_build_ignore("devtools_history.R")
# Cette fonction permet d'importer le package stats dans
# le doc DESCRIPTION
usethis::use_package("stats") #Les packages à ajouter
usethis::use_package("magrittr")
```

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
On peut réexecuter cette fonction (puis reinstaller le package) pour mettre à jour la documentation si l'on modifie les entêtes par la suite.

#### Création d'une vignette
Une `vignette`correspond à la documentation informelle d'un package.

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
devtools::install("monpackage")
```

sinon on peut aussi faire : **CTRL + SHIFT + B**
où encore : dans la console : cliquer sur `Install and Restart`
Une fois le package installé on peut utiliser ses fonctions.

## Installation du package sous **CRAN**

Le dépôt du package sur **CRAN** a pour intéret de permettre l'installation des packaes par cette commande, et de garantir que le package est testé sur divers systèmes d'exploitation.
