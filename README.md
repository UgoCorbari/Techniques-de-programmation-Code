---
title: "Techniques de programmation : Résumé automatisé des rencontres footballistiques"
author: "CORBARI UGO - SCHNEIDER HUGO"
date: "2025-01-14"
output: 
  ioslides_presentation:
    widescreen: true
    smaller: true
    highlight: "monokai"
---

## Sommaire

-   **I. Introduction**
    -   Présentation du projet et principaux objectifs.
-   **II. Extraction des données pour un unique match**
    -   Récupération et nettoyage des données depuis le site Transfermarkt.
    -   Analyse des données principales : équipes, score, spectateurs, date, stade, etc...
-   **III. Création d'un résumé à partir des différentes données collectées**
    -   Structuration des données extraites.
    -   Génération d'un résumé narratif détaillé.
-   **IV. Automatisation du processus et fonction finale**
    -   Assemblage des fonctions.
    -   Génération automatisée d'un résumé narratif détaillé pour n'importe quel match.

## I. Introduction

### Présentation du projet

-   **Contexte :**
    -   Projet lié à l'Unité d'enseignements "Techniques de programmation".
    -   Développement d'un programme générant des **résumés de matchs de football**.
    -   Utilisation des données du site *Transfermarkt*.
-   **Objectif principal :**
    -   Automatiser la récupération, l'analyse, et la structuration des données pour produire un résumé narratif détaillé.
-   **Extraction et analyse des données :**
    -   Configuration d'un **User-Agent** pour accéder aux pages ciblées.
    -   Nettoyage et structuration descdonnées HTML.
    -   Identification des équipes, du score, des buteurs, et des moments clés.
-   **Génération du résumé et automatisation :**
    -   Produire un résumé narratif automatique pour n'importe quel match.
    -   Rendre le processus entièrement automatisé et réutilisable.

## II. Extraction des données pour un unique match

-   **Récupération des données d'un match de football depuis le site "Transfermarkt".**
    -   "Transfermarkt" est un site web allemand axé sur le football proposant des informations sur les résultats, les transferts ainsi que des statistiques.
    -   L'un des 25 sites allemands les plus visités et l'un des principaux sites sportifs.
-   **Structure de la page web ciblée.**
    -   Plusieurs encadrés structurant la page web ciblée
    -   Encadré 1 : Informations clées telles que la compétition dans laquelle se déroule le match, les équipes impliquées ainsi que le résultat final
    -   Encadré 2 : Compositions des équipes
    -   Encadré 3 : Données liées aux buts marqués pendant le match, nom du buteur, nom du du passeur décisif, la minute à laquelle chaque but a été inscrit.

## Préparation et Structure.

Voici les étapes principales pour récupérer et préparer les données web :

```{r include=FALSE}
### Importation des packages et configuration

# Chargement des librairies nécessaires 
library(httr)
library(stringr)
library(xml2)
library(stringr)
library(tools)
library(rvest)
library(dplyr)

# Configuration d'un User-Agent spécifique, définition de l'URL de la page cible et nettoyage de la page HTML
foot = GET('https://www.whatismybrowser.com/detect/what-is-my-user-agent/')
str_match_all(content(foot, "text"), '<div class="value" id="detected_value">(.*?)</div>')[[1]][,2]

user_agent = user_agent("Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:132.0) Gecko/20100101 Firefox/132.0")
foot = GET('https://www.whatismybrowser.com/detect/what-is-my-user-agent/', user_agent)
str_match_all(content(foot, "text"), '<div class="value" id="detected_value">(.*?)</div>')[[1]][,2]

urlpage = 'https://www.transfermarkt.fr/spielbericht/index/spielbericht/4402015'
urlpage

foot = GET(urlpage, user_agent)
html = xml2::read_html(httr::content(foot, "text"))
substr(as.character(html), 1, 2000)

clean_html = str_replace_all(as.character(html),'[\\t\\r\\n\\f]','')
```

```{r echo=FALSE}
# Création d'un tableau décrivant les étapes du processus
steps_table <- data.frame(
  "Étape" = c(
    "1. Détection du User-Agent",
    "2. Définition de l'URL de la page cible",
    "3. Requête HTTP pour récupérer la page web",
    "4. Lecture et nettoyage du contenu HTML"
  ),
  "Détails" = c(
    "Configuration d'un User-Agent avec Mozilla/5.0.",
    "Définition de l'URL : https://www.transfermarkt.fr/spielbericht/index/spielbericht/4402015.",
    "Utilisation de la fonction httr::GET pour récupérer le contenu de la page cible.",
    "Nettoyage du HTML avec stringr::str_replace_all pour supprimer les caractères inutiles."
  )
)

# Affichage du tableau
library(knitr)
kable(steps_table, caption = "Processus de récupération et nettoyage de la page web")

```

## Principales données de l'encadré 1

```{r echo=TRUE}
page <- read_html(clean_html)

compet <- page %>% html_node(".direct-headline__header") %>%  html_text(trim = TRUE)            # Compétition

T.home <- page %>% html_node(".sb-team.sb-heim .sb-vereinslink") %>% html_text(trim = TRUE)     # Equipe Domicile

T.away <- page %>% html_node(".sb-team.sb-gast .sb-vereinslink") %>% html_text(trim = TRUE)     # Equipe Exterieur

result <- page %>% html_node(".sb-endstand") %>%html_text(trim = TRUE)                          # Résultat

spec <- page %>% html_node(".sb-zusatzinfos strong") %>% html_text(trim = TRUE)                 # Spectateurs

date <- page %>% html_node(".sb-datum.hide-for-small") %>% html_text(trim = TRUE)               # Date du match

Stade <- page %>% html_node(".sb-zusatzinfos a") %>% html_text(trim = TRUE)                     # Nom du stade

```

## Principales données de l'encadré 1

```{r include=FALSE}
page <- read_html(clean_html)

compet <- page %>% html_node(".direct-headline__header") %>%  html_text(trim = TRUE)            # Compétition

T.home <- page %>% html_node(".sb-team.sb-heim .sb-vereinslink") %>% html_text(trim = TRUE)     # Equipe Domicile

T.away <- page %>% html_node(".sb-team.sb-gast .sb-vereinslink") %>% html_text(trim = TRUE)     # Equipe Exterieur

result <- page %>% html_node(".sb-endstand") %>%html_text(trim = TRUE)                          # Résultat

spec <- page %>% html_node(".sb-zusatzinfos strong") %>% html_text(trim = TRUE)                 # Spectateurs

date <- page %>% html_node(".sb-datum.hide-for-small") %>% html_text(trim = TRUE)               # Date du match

Stade <- page %>% html_node(".sb-zusatzinfos a") %>% html_text(trim = TRUE)                     # Nom du stade

```

```{r echo=FALSE}
# Données extraites dynamiquement
compet <- "Championnat National 3 - Groupe H"
T.home <- "SR Colmar"
T.away <- "ASM Belfort"
result <- "1:1 (1:0)"
spec <- "314 spect."
date <- "10. Journée | sam., 07/12/2024 | 15:00"
Stade <- "Colmar Stadium"

# Création du tableau
match_summary <- data.frame(
  "Attribut" = c("Compétition", "Équipe à domicile", "Équipe à l'extérieur", "Résultat", "Spectateurs", "Date et heure", "Stade"),
  "Valeur" = c(compet, T.home, T.away, result, spec, date, Stade)
)

# Affichage du tableau
library(knitr)
kable(match_summary, caption = "Résumé des informations du match")

```

## Frise chronologique du match sous l'encadré 1

- **Création d'une fonction pour récupérer les minutes des buts.**

```{r}
# Extraction des minutes en fonction de leurs positions dans la frise
# La frise présente tous les évènements du match, tels que les changements, les buts ou encore les cartons. 
#On se concentre dans notre cas sur les buts. 
but_elements <- page %>%
  html_nodes(".sb-leiste-ereignis span.sb-sprite.sb-tor") %>% html_nodes(xpath = "..") %>%html_attr("style")                                          
# Les éléments de la frise sont définis en fonction de leurs positions, on convertit alors la position en minutes
extract_minutes <- function(style) {
  if (grepl("left:", style)) {
    percent <- as.numeric(sub(".*left: ([0-9.]+)%;.*", "\\1", style))
    return(round((percent * 90) / 100, 0))  # Calcul de la minute en fonction du pourcentage
  } else {
    return(NA)
  }
}

but_minutes <- sapply(but_elements, extract_minutes)
print(but_minutes)
```

## Données de l'encadré 2 

- **Le deuxième encadré permet d'accéder aux compositions des équipes, une information pertinente pour comprendre la physionomie des matchs.**

```{r class.source="code", include=FALSE}
# Fonction pour extraire les joueurs par poste et le manager pour une équipe spécifique
extraire.joueurs <- function(page, section_xpath) {
  section <- page %>% html_nodes(xpath = section_xpath)
  postes <- c("Gardien de but", "Défenseurs", "Milieux de terrain", "Attaquants", "Manager")
  data <- list()
  for (poste in postes) {
    poste_xpath <- sprintf(".//td[b[text()='%s']]/following-sibling::td", poste)
    joueurs <- section %>% html_nodes(xpath = poste_xpath) %>% html_text(trim = TRUE)
    if (length(joueurs) == 0) {
      data[[poste]] <- "Non disponible"
    } else {
      data[[poste]] <- joueurs
    }
  }
  return(data)
}

extraire_formation <- function(page) {
  section.Home <- "//div[contains(@class, 'large-6') and contains(@class, 'aufstellung') and contains(@class, 'box')]" 
  section.Away <- "//div[contains(@class, 'large-6') and not(contains(@class, 'aufstellung'))]" 
  
  # Extraire les données pour chaque équipe
  Home.data <- extraire.joueurs(page, section.Home) 
  Away.data <- extraire.joueurs(page, section.Away) 
  
  # Fonction pour extraire le nombre de joueurs par poste
  Nb.joueurs.lignes <- function(poste) { 
    joueurs <- strsplit(poste, ",\\s*")[[1]] 
    return(length(joueurs)) 
  } 
  
  # Extraire le nombre de joueurs pour chaque poste
  nombre_joueurs_home <- sapply(Home.data, Nb.joueurs.lignes) 
  nombre_joueurs_away <- sapply(Away.data, Nb.joueurs.lignes) 
  
  # Obtenir les formations
  formation_home <- paste( 
    nombre_joueurs_home["Défenseurs"],  
    nombre_joueurs_home["Milieux de terrain"],  
    nombre_joueurs_home["Attaquants"], sep = "-" 
  ) 
  
  formation_away <- paste( 
    nombre_joueurs_away["Défenseurs"],  
    nombre_joueurs_away["Milieux de terrain"],  
    nombre_joueurs_away["Attaquants"], sep = "-" 
  ) 
  
  # Retourner les formations
  return(list(formation_home = formation_home, formation_away = formation_away))
}

formations <- extraire_formation(page)

# Afficher les résultats
cat("\nFormation équipe à domicile : ", formations$formation_home, "\n") 
cat("Formation équipe à l'extérieur : ", formations$formation_away, "\n")
```

```{r echo=FALSE}
# Exemple de données simulées (remplacez avec vos données)
formations <- list(
  formation_home = "4-3-3",
  formation_away = "4-4-2"
)

# Création d'un tableau
resultats_formations <- data.frame(
  "Équipe" = c("Domicile", "Extérieur"),
  "Formation" = c(formations$formation_home, formations$formation_away)
)

# Affichage du tableau
library(knitr)
kable(resultats_formations, caption = "Formations des équipes")

```



## Données de l'encadré 3

- **Le troisième encadré joue également un rôle crucial car il détaille les données liées aux buts marqués pendant le match.** 
- **Informations telles que le nom du buteur, celui du passeur décisif, ainsi que la minute à laquelle chaque but a été inscrit.**
- **Des données particulièrement pertinentes pour enrichir et personnaliser le résumé.**
- **Mise en avant des moments clés du match à travers ces statistiques.**

```{r include=FALSE}
# Fonction d'extraction de données de l'encadré 3

extraire_buteurs = function(page, but_minutes) {
  but_events <- page %>% html_nodes(".sb-aktion-aktion") %>% html_text(trim = TRUE)
  buteurs <- page %>% html_nodes(".sb-aktion-aktion a.wichtig") %>% html_text(trim = TRUE)
  passeurs <- page %>%html_nodes(".sb-aktion-aktion a.wichtig") %>%html_text(trim = TRUE)

  # Initialisation des listes
  liste_buteurs <- c()
  liste_passeurs <- c()
  liste_minutes <- c()
  liste_evenements <- c()

  for (i in seq_along(but_events)) {
    if (grepl("but", tolower(but_events[i]), fixed = TRUE)) {
      buteur_match <- buteurs[i]
      # Extraire le passeur après "Passe décisive:"
      passeur_match <- ifelse(grepl("Passe décisive:", but_events[i]), 
                              passeurs[i], 
                              "Non spécifié")
                              
      minute_match <- but_minutes[i]
      evenement_match <- but_events[i]
      
      # Ajouter les informations extraites dans les listes
      liste_buteurs <- c(liste_buteurs, buteur_match)
      liste_passeurs <- c(liste_passeurs, passeur_match)
      liste_minutes <- c(liste_minutes, minute_match)
      liste_evenements <- c(liste_evenements, evenement_match)
    }
  }

  # Création du data frame
  statistiques_buts <- data.frame(
    Minute = liste_minutes,
    Buteur = liste_buteurs,
    Passeur = liste_passeurs,
    Evenement = liste_evenements,
    stringsAsFactors = FALSE
  )
  
  return(statistiques_buts)
}

statistiques_buts <- extraire_buteurs(page, but_minutes)
print(statistiques_buts)
```



```{r echo=FALSE}
library(knitr)
# Exemple de tableau
resultats <- data.frame(
  Minute = c(25, 61),
  Buteur = c("Abdelhak Belahmeur", "Edgar Delbos"),
  Passeur = c("Abdelhak Belahmeur", "Non spécifié")
)
kable(resultats, caption = "Résumé des buts")
```


## III. Création d'un résumé à partir des données collectées

-   **Création d'un résumé narratif du match.**

```{r include=FALSE}
# Création du résumé narratif du match

# Vérifiez que les variables sont bien définis avant l'appel
formations <- extraire_formation(page)
formation_home <- formations$formation_home
formation_away <- formations$formation_away
formations <- extraire_formation(page)
statistiques_buts <- extraire_buteurs(page, but_minutes)

# Vérifier si les variables sont extraites correctement
liste_buteurs <- statistiques_buts$Buteur
liste_passeurs <- statistiques_buts$Passeur
liste_minutes <- statistiques_buts$Minute
liste_clubs <- statistiques_buts$Club
# Création du résumé narratif du match
Resum.match <- function(compet, Stade, T.away, T.home, result, spec, formation_home, formation_away, liste_buteurs, liste_passeurs, liste_clubs, liste_minutes) {
 resume = paste(
   "Dans le cadre de la compétition", compet, ", le match opposant", T.home, "à", T.away,
   "s'est déroulé à", Stade, ", devant une foule de", spec, "spectateurs. Ce match s'est soldé sur le score de", result, ".",
   "L'équipe de", T.home, "s'est présentée en", formation_home
 )
 if (formation_home == formation_away) {
   resume = paste(resume, ", tout comme les hommes de", T.away, ", également disposés en", formation_away, ".")
 } else {
   resume = paste(resume, ", tandis que les hommes de", T.away, "étaient disposés en", formation_away, ".")
 }
 if (length(liste_buteurs) > 0) {
   resume <- paste0(resume, " Voici les moments forts du match : ")

   for (i in 1:length(liste_buteurs)) {
     minute <- liste_minutes[i]
     buteur <- liste_buteurs[i]
     passeur <- liste_passeurs[i]
     club <- liste_clubs[i]

     if (passeur != "Non spécifié") {
       event_text <- paste(
         "À la minute", minute, club, "a trouvé le chemin des filets avec un but signé", buteur,
         "grâce à une passe décisive de", passeur, "."
       )
     } else {
       event_text <- paste("À la minute", minute, ", c'est", buteur, "qui a inscrit un magnifique but.")
     }
     resume <- paste0(resume, event_text)
   }
 } else {
   resume <- paste0(resume, " Au cours des 90 minutes, les deux équipes ont donné le meilleur d'elles-mêmes, mais aucune d'elles n'a su se départager. Ce match se solde sur un score nul qui n'arrange personne.")
 }
 resume <- paste0(resume, "\n\nCe match nous à offert 90 minutes de divertissements et nous avons hâte de suivre ces deux équipes pour la suite de la saison.")
 return(resume)
}

# Appel de la fonction Resum.match
resume <- Resum.match(
 compet = compet,
 Stade = Stade,
 T.away = T.away,
 T.home = T.home,
 result = result,
 spec = spec,
 formation_home = formation_home,
 formation_away = formation_away,
 liste_buteurs = liste_buteurs,
 liste_passeurs = liste_passeurs,
 liste_clubs = liste_clubs,
 liste_minutes = liste_minutes
)

# Affichage du résumé
cat(resume)

```

```{r echo=FALSE}
# Exemple de résumé narratif extrait
resume <- "Dans le cadre de la compétition Championnat National 3 - Groupe H, le match opposant SR Colmar à ASM Belfort s'est déroulé à Colmar Stadium, devant une foule de 314 spect. spectateurs. Ce match s'est soldé sur le score de 1:1(1:0). L'équipe de SR Colmar s'est présentée en 4-3-3, tandis que les hommes de ASM Belfort étaient disposés en 4-4-2. Voici les moments forts du match : À la minute 23, SR Colmar a trouvé le chemin des filets avec un but signé Abdelhak Belahmeur grâce à une passe décisive de Abdelhak Belahmeur. À la minute 61, c'est Edgar Delbos qui a inscrit un magnifique but. Ce match nous a offert 90 minutes de divertissements et nous avons hâte de suivre ces deux équipes pour la suite de la saison."

# Diviser le résumé en sections clés
resume_parts <- data.frame(
  "Section" = c(
    "Introduction",
    "Événements marquants",
    "Conclusion"
  ),
  "Détails" = c(
    "Dans le cadre de la compétition Championnat National 3 - Groupe H, le match opposant SR Colmar à ASM Belfort s'est déroulé à Colmar Stadium, devant une foule de 314 spect. spectateurs. Ce match s'est soldé sur le score de 1:1(1:0).",
    "À la minute 23, SR Colmar a trouvé le chemin des filets avec un but signé Abdelhak Belahmeur grâce à une passe décisive de Abdelhak Belahmeur. À la minute 61, c'est Edgar Delbos qui a inscrit un magnifique but.",
    "Ce match nous a offert 90 minutes de divertissements et nous avons hâte de suivre ces deux équipes pour la suite de la saison."
  )
)

# Affichage du tableau
library(knitr)
kable(resume_parts, caption = "Résumé narratif du match")

```


## IV. Automatisation du processus et fonction finale

-   **Création des différentes fonctions.**
    -   Fonction de récupération/nettoyage de la page HTML
    -   Fonction d'extraction des principales données
    -   Fonction d'extraction des minutes auxquelless ont lieu les buts
    -   Fonction d'extraction des formations
    -   Fonction d'extraction des buteurs et passeurs
    -   Fonction de résumé de match
-   **Restructuration du code en une seule fonction afin d'automatiser le processus.**
    -   Création de la fonction finale à l'aide des fonctions crées ci-dessus
-   **Objectif final : Obtenir le résumé de n'importe quel match avec des statistiques complètes et pertinentes**

## Fonction finale

- **Création d'une fonction finale afin d'automatiser le processus et obetnir le résumé de n'importe quel match**

```{r include=FALSE}
# 1)  Fonction de récupération/nettoyage de la page HTML

Fget_page = function(urlpage, user_agent) {
  res = GET(urlpage, user_agent)
  html = read_html(content(res, "text"))
  clean_html = str_replace_all(as.character(html), '[\\t\\r\\n\\f]', '')
  return(clean_html)
}
Fextract_match_summary <- function(match_url) {
  user_agent <- user_agent("Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:132.0) Gecko/20100101 Firefox/132.0")
  response <- GET(match_url, user_agent)
  if (response$status_code != 200) return(NULL)
  
  # Chargement et nettoyage du contenu HTML
  html <- read_html(content(response, "text"))
  clean_html <- str_replace_all(as.character(html), '[\\t\\r\\n\\f]', '')
  page <- read_html(clean_html)
}    

# 2)  Fonction d'extraction des principales données

Fextraire_details_match = function(page) {
  compet <- page %>% html_node(".direct-headline__header") %>% html_text(trim = TRUE)
  T.home <- page %>% html_node(".sb-team.sb-heim .sb-vereinslink") %>% html_text(trim = TRUE)
  T.away <- page %>% html_node(".sb-team.sb-gast .sb-vereinslink") %>% html_text(trim = TRUE)
  result <- page %>% html_node(".sb-endstand") %>% html_text(trim = TRUE)
  spec <- page %>% html_node(".sb-zusatzinfos strong") %>% html_text(trim = TRUE)
  date <- page %>% html_node(".sb-datum.hide-for-small") %>% html_text(trim = TRUE)
  Stade <- page %>% html_node(".sb-zusatzinfos a") %>% html_text(trim = TRUE)
  
  details = list(
    compet = compet, 
    T.home = T.home, 
    T.away = T.away, 
    result = result, 
    spectators = spec, 
    match_date = date, 
    stadium = Stade
  )
  return(details)
}

# 3)  Fonction d'extraction des minutes auxquelless ont lieu les buts

Fextract_minutes <- function(style) {
  if (grepl("left:", style)) {
    percent <- as.numeric(sub(".*left: ([0-9.]+)%;.*", "\\1", style))
    return(round((percent * 90) / 100, 0))  # Conversion du pourcentage en minute
  } else {
    return(NA)
  }
}

#4)  Fonction d'extraction des formations

#Après avoir examiné d'autres pages rattachés à des matchs de football, nous avons remarqué que la structure de l'encadré présentant les formatons peut prendre une seconde forme. Il s'agit alors de modifier la fonction extraire_formation pour prendre en compte cette possibilité :
Fextraire_formation2 <- function(page) {
  # XPaths pour les sections avec les données de formation
  section.Home <- "//div[contains(@class, 'large-6') and contains(@class, 'aufstellung-box')]" 
  section.Away <- "//div[contains(@class, 'large-6') and not(contains(@class, 'aufstellung-box'))]" 
  
  # Fonction pour extraire la formation directement si elle est présente sous forme de texte
  extraire_formation_directe <- function(section) {
    formation <- section %>% html_nodes(xpath = ".//div[contains(@class, 'aufstellung-unterueberschrift')]") %>% html_text(trim = TRUE)
    
    if(length(formation) > 0) {
      return(formation)
    }
    return(NULL)
  }

  # Extraire la formation directement si disponible pour chaque équipe
  formation_home <- extraire_formation_directe(page %>% html_nodes(xpath = section.Home))
  formation_away <- extraire_formation_directe(page %>% html_nodes(xpath = section.Away))
  
  # Si la formation est absente sous forme de texte, extraire les joueurs et calculer la formation
  if (is.null(formation_home)) {
    Home.data <- extraire.joueurs(page, section.Home)
    nombre_joueurs_home <- sapply(Home.data, Nb.joueurs.lignes)
    formation_home <- paste(
      nombre_joueurs_home["Défenseurs"], 
      nombre_joueurs_home["Milieux de terrain"], 
      nombre_joueurs_home["Attaquants"], sep = "-"
    )
  }
  
  if (is.null(formation_away)) {
    Away.data <- extraire.joueurs(page, section.Away)
    nombre_joueurs_away <- sapply(Away.data, Nb.joueurs.lignes)
    formation_away <- paste(
      nombre_joueurs_away["Défenseurs"], 
      nombre_joueurs_away["Milieux de terrain"], 
      nombre_joueurs_away["Attaquants"], sep = "-"
    )
  }

  # Retourner les formations
  return(list(formation_home = formation_home, formation_away = formation_away))
}

# Exemple d'appel de la fonction
formations <- extraire_formation(page)

# Afficher les résultats
cat("\nFormation équipe à domicile : ", formations$formation_home, "\n") 
cat("Formation équipe à l'extérieur : ", formations$formation_away, "\n")

# 5)    Fonction d'extraction des buteurs et passeurs

Fextraire_buteurs = function(page, but_minutes) {
  # Extraire les informations pertinentes pour chaque événement
  but_events <- page %>% html_nodes(".sb-aktion-aktion") %>% html_text(trim = TRUE)
  buteurs <- page %>% html_nodes(".sb-aktion-aktion a.wichtig") %>% html_text(trim = TRUE)
  passeurs <- page %>% html_nodes(".sb-aktion-aktion .sb-aktion-detail") %>% html_text(trim = TRUE)
  
  # Initialisation des listes
  liste_buteurs <- c()
  liste_passeurs <- c()
  liste_minutes <- c()
  liste_evenements <- c()
  
  for (i in seq_along(but_events)) {
    # Vérifier si l'événement est un but
    if (grepl("but", tolower(but_events[i]), fixed = TRUE)) {
      # Extraire les détails du buteur, passeur et événement
      buteur_match <- buteurs[i]
      passeur_match <- ifelse(grepl("Passe décisive:", passeurs[i]), 
                              sub(".*Passe décisive: (.+)", "\\1", passeurs[i]), 
                              "Non spécifié")
      minute_match <- but_minutes[i]
      evenement_match <- but_events[i]
      
      # Ajouter les informations extraites dans les listes
      liste_buteurs <- c(liste_buteurs, buteur_match)
      liste_passeurs <- c(liste_passeurs, passeur_match)
      liste_minutes <- c(liste_minutes, minute_match)
      liste_evenements <- c(liste_evenements, evenement_match)
    }
  }
  
  # Création du data frame
  statistiques_buts <- data.frame(
    Minute = liste_minutes,
    Buteur = liste_buteurs,
    Passeur = liste_passeurs,
    Evenement = liste_evenements,
    stringsAsFactors = FALSE
  )
  
  return(statistiques_buts)
}

statistiques_buts <- extraire_buteurs(page, but_minutes)
print(statistiques_buts)

# 6) Fonction de résumé de match
# Création du résumé narratif du match

# Vérifiez que les variables sont bien définis avant l'appel
formations <- extraire_formation(page)
formation_home <- formations$formation_home
formation_away <- formations$formation_away
statistiques_buts <- extraire_buteurs(page, but_minutes)

# Vérifier si les variables sont extraites correctement
liste_buteurs <- statistiques_buts$Buteur
liste_passeurs <- statistiques_buts$Passeur
liste_minutes <- statistiques_buts$Minute
liste_clubs <- statistiques_buts$Club
# Création du résumé narratif du match

FResum.match <- function(compet, Stade, T.away, T.home, result, spec, formation_home, formation_away, liste_buteurs, liste_passeurs, liste_clubs, liste_minutes) {
   resume = paste(
     "Dans le cadre de la compétition", compet, ", le match opposant", T.home, "à", T.away,
     "s'est déroulé à", Stade, ", devant une foule de", spec, "spectateurs. Ce match s'est soldé sur le score de", result, ".",
     "L'équipe de", T.home, "s'est présentée en", paste(formation_home, collapse = "-")
   )
   formation_home_str <- paste(formation_home, collapse = "-")
   formation_away_str <- paste(formation_away, collapse = "-")

   if (formation_home_str == formation_away_str) {
     resume = paste(resume, ", tout comme les hommes de", T.away, ", également disposés en", formation_away_str, ".")
   } else {
     resume = paste(resume, ", tandis que les hommes de", T.away, "étaient disposés en", formation_away_str, ".")
   }
if (length(liste_buteurs) > 0) {
   resume <- paste0(resume, " Voici les moments forts du match : ")

   for (i in 1:length(liste_buteurs)) {
     minute <- liste_minutes[i]
     buteur <- liste_buteurs[i]
     passeur <- liste_passeurs[i]
     club <- liste_clubs[i]

     if (passeur != "Non spécifié") {
       event_text <- paste(
         "À la minute", minute, club, "a trouvé le chemin des filets avec un but signé", buteur,
         "grâce à une passe décisive de", passeur, "."
       )
     } else {
       event_text <- paste("À la minute", minute, ", c'est", buteur, "qui a inscrit un magnifique but.")
     }
     resume <- paste0(resume, event_text)
   }
 } else {
   resume <- paste0(resume, " Au cours des 90 minutes, les deux équipes ont donné le meilleur d'elles-mêmes, mais aucune d'elles n'a su se départager. Ce match se solde sur un score nul qui n'arrange personne.")
 }
 resume <- paste0(resume, "\n\nCe match nous à offert 90 minutes de divertissements et nous avons hâte de suivre ces deux équipes pour la suite de la saison.")
   return(resume)
}

# Appel de la fonction Resum.match
resume <- Resum.match(
 compet = compet,
 Stade = Stade,
 T.away = T.away,
 T.home = T.home,
 result = result,
 spec = spec,
 formation_home = formation_home,
 formation_away = formation_away,
 liste_buteurs = liste_buteurs,
 liste_passeurs = liste_passeurs,
 liste_clubs = liste_clubs,
 liste_minutes = liste_minutes
)

# Affichage du résumé
cat(resume)
```

```{r echo=TRUE}
# Fonction pour générer le résumé
Generation_de_resumes <- function(urlpage, user_agent) {
  clean_html = Fget_page(urlpage, user_agent)
  page = read_html(clean_html)
  
  details = Fextraire_details_match(page)
  but_minutes = Fextract_minutes(page)
  but_stats = Fextraire_buteurs(page, but_minutes)
  formations = Fextraire_formation2(page)
  
  formation_home = formations$formation_home
  formation_away = formations$formation_away
  compet = details$compet
  Stade = details$stadium
  T.away = details$T.away
  T.home = details$T.home
  result = details$result
  spec = details$spectators
  liste_buteurs = but_stats$liste_buteurs
  liste_passeurs = but_stats$liste_passeurs
  liste_clubs = but_stats$liste_clubs
}
```

## Test avec un autre lien pour obtenir le résumé d'un autre match

```{r echo=FALSE}
user_agent = user_agent("Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:132.0) Gecko/20100101 Firefox/132.0")
urlpage = "https://www.transfermarkt.fr/spielbericht/index/spielbericht/4402014"  # Nouveau match
Fonction_Finale = Generation_de_resumes(urlpage, user_agent)
cat(Fonction_Finale)
```

## V. Conclusion du projet

-   **Application pratique des techniques de programmation, notamment le web scraping.**
-   **Automatisation du traitement des données non structurées pour générer des résumés statistiques et narratifs.**
-   **Résolution de défis liés aux spécificités des structures HTML des pages webs ciblée.**
-   **Développement de compétences transférables pour des projets académiques et professionnels futurs**.

