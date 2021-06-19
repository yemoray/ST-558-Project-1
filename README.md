-   [Packages required to load and analyse the
    data](#packages-required-to-load-and-analyse-the-data)
-   [Introduction](#introduction)
-   [Reading data from the National Hockey League’s (NHL) records
    API](#reading-data-from-the-national-hockey-leagues-nhl-records-api)
    -   [Franchise data](#franchise-data)
    -   [Franchise team totals](#franchise-team-totals)
    -   [Franchise season records](#franchise-season-records)
    -   [Franchise goalie records](#franchise-goalie-records)
    -   [Franchise skater records](#franchise-skater-records)
    -   [Franchise detail](#franchise-detail)
-   [Reading data from the National Hockey League’s (NHL) stat
    API](#reading-data-from-the-national-hockey-leagues-nhl-stat-api)
-   [Wrapper function to access API
    endpoints](#wrapper-function-to-access-api-endpoints)
-   [Exploratory Data Analysis](#exploratory-data-analysis)
    -   [Data from 2 endpoints](#data-from-2-endpoints)
        -   [Some data for active and inactive players of the Dallas
            Stars:](#some-data-for-active-and-inactive-players-of-the-dallas-stars)
        -   [Home and away records for all teams during regular
            season:](#home-and-away-records-for-all-teams-during-regular-season)
    -   [Contingency tables](#contingency-tables)
        -   [Summary of active and inactive players for the Dallas
            Stars:](#summary-of-active-and-inactive-players-for-the-dallas-stars)

# Packages required to load and analyse the data

The following packages are needed for reading and analyzing the data:

    packages <- c("tidyverse","httr","jsonlite","rmarkdown", "knitr","DT","kableExtra")
    lapply(packages, library, character.only = TRUE)

# Introduction

This vignette reads data from the [NHL records
API](https://gitlab.com/dword4/nhlapi/-/blob/master/records-api.md)
(using functions from the `httr` and `jsonlite` packages) and shows some
basic exploratory data analysis using tools available in the `tidyverse`
package.

# Reading data from the National Hockey League’s (NHL) records API

## Franchise data

The franchise data returns id, firstSeasonId and last SeasonId and name
of every team in the history of the NHL.

    get_franchise_data <- function(){
      prefix_url <- "https://records.nhl.com/site/api"
      franchise_url <- paste0(prefix_url,"/","franchise")
      franchise_list <- franchise_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      return(franchise_list$data)
    }

## Franchise team totals

The franchise team totals returns the total stats for every franchise:

    get_franchise_team_totals <- function(){
      prefix_url <- "https://records.nhl.com/site/api"
      franchise_totals_url <- paste0(prefix_url,"/","franchise-team-totals")
      franchise_totals_list <- franchise_totals_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      return(franchise_totals_list$data)
    }

## Franchise season records

The franchise season records drills down into season records for a
specified franchise when the **franchiseId**, represented as **ID**
below, is provided.As an option, both the *ID* and full team name can be
supplied, as long as they are matched in the database

    get_franchise_season_records <- function(ID, name){
      if (ID %in% c(1:nrow(get_franchise_data()))){
        if (missing(name)){
        prefix_url <- "https://records.nhl.com/site/api"
        franchise_season_records_url <- paste0(prefix_url,"/","franchise-season-records?cayenneExp=franchiseId=",ID)
        franchise_season_records_list <- franchise_season_records_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      
      return(franchise_season_records_list$data)
      
    } else {
      if (name %in% get_franchise_data()$fullName){
        {if (ID != which(get_franchise_data()$fullName == name)){
          stop ("The franchise id entered is not for the team selected")
        }
        ID <- which(get_franchise_data()$fullName == name)}
        prefix_url <- "https://records.nhl.com/site/api"
        franchise_season_records_url <- paste0(prefix_url,"/","franchise-season-records?cayenneExp=franchiseId=",ID)
        franchise_season_records_list <- franchise_season_records_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE) 
        
        return(franchise_season_records_list$data)
       }
      }   
      }
     }

For example, the team with **franchiseID** = 6 (Boston Bruins) has the
following season record:

    paged_table(get_franchise_season_records(6,"Boston Bruins"))

<script data-pagedtable-source type="application/json">
{"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["id"],"name":[1],"type":["int"],"align":["right"]},{"label":["fewestGoals"],"name":[2],"type":["int"],"align":["right"]},{"label":["fewestGoalsAgainst"],"name":[3],"type":["int"],"align":["right"]},{"label":["fewestGoalsAgainstSeasons"],"name":[4],"type":["chr"],"align":["left"]},{"label":["fewestGoalsSeasons"],"name":[5],"type":["chr"],"align":["left"]},{"label":["fewestLosses"],"name":[6],"type":["int"],"align":["right"]},{"label":["fewestLossesSeasons"],"name":[7],"type":["chr"],"align":["left"]},{"label":["fewestPoints"],"name":[8],"type":["int"],"align":["right"]},{"label":["fewestPointsSeasons"],"name":[9],"type":["chr"],"align":["left"]},{"label":["fewestTies"],"name":[10],"type":["int"],"align":["right"]},{"label":["fewestTiesSeasons"],"name":[11],"type":["chr"],"align":["left"]},{"label":["fewestWins"],"name":[12],"type":["int"],"align":["right"]},{"label":["fewestWinsSeasons"],"name":[13],"type":["chr"],"align":["left"]},{"label":["franchiseId"],"name":[14],"type":["int"],"align":["right"]},{"label":["franchiseName"],"name":[15],"type":["chr"],"align":["left"]},{"label":["homeLossStreak"],"name":[16],"type":["int"],"align":["right"]},{"label":["homeLossStreakDates"],"name":[17],"type":["chr"],"align":["left"]},{"label":["homePointStreak"],"name":[18],"type":["int"],"align":["right"]},{"label":["homePointStreakDates"],"name":[19],"type":["chr"],"align":["left"]},{"label":["homeWinStreak"],"name":[20],"type":["int"],"align":["right"]},{"label":["homeWinStreakDates"],"name":[21],"type":["chr"],"align":["left"]},{"label":["homeWinlessStreak"],"name":[22],"type":["int"],"align":["right"]},{"label":["homeWinlessStreakDates"],"name":[23],"type":["chr"],"align":["left"]},{"label":["lossStreak"],"name":[24],"type":["int"],"align":["right"]},{"label":["lossStreakDates"],"name":[25],"type":["chr"],"align":["left"]},{"label":["mostGameGoals"],"name":[26],"type":["int"],"align":["right"]},{"label":["mostGameGoalsDates"],"name":[27],"type":["chr"],"align":["left"]},{"label":["mostGoals"],"name":[28],"type":["int"],"align":["right"]},{"label":["mostGoalsAgainst"],"name":[29],"type":["int"],"align":["right"]},{"label":["mostGoalsAgainstSeasons"],"name":[30],"type":["chr"],"align":["left"]},{"label":["mostGoalsSeasons"],"name":[31],"type":["chr"],"align":["left"]},{"label":["mostLosses"],"name":[32],"type":["int"],"align":["right"]},{"label":["mostLossesSeasons"],"name":[33],"type":["chr"],"align":["left"]},{"label":["mostPenaltyMinutes"],"name":[34],"type":["int"],"align":["right"]},{"label":["mostPenaltyMinutesSeasons"],"name":[35],"type":["chr"],"align":["left"]},{"label":["mostPoints"],"name":[36],"type":["int"],"align":["right"]},{"label":["mostPointsSeasons"],"name":[37],"type":["chr"],"align":["left"]},{"label":["mostShutouts"],"name":[38],"type":["int"],"align":["right"]},{"label":["mostShutoutsSeasons"],"name":[39],"type":["chr"],"align":["left"]},{"label":["mostTies"],"name":[40],"type":["int"],"align":["right"]},{"label":["mostTiesSeasons"],"name":[41],"type":["chr"],"align":["left"]},{"label":["mostWins"],"name":[42],"type":["int"],"align":["right"]},{"label":["mostWinsSeasons"],"name":[43],"type":["chr"],"align":["left"]},{"label":["pointStreak"],"name":[44],"type":["int"],"align":["right"]},{"label":["pointStreakDates"],"name":[45],"type":["chr"],"align":["left"]},{"label":["roadLossStreak"],"name":[46],"type":["int"],"align":["right"]},{"label":["roadLossStreakDates"],"name":[47],"type":["chr"],"align":["left"]},{"label":["roadPointStreak"],"name":[48],"type":["int"],"align":["right"]},{"label":["roadPointStreakDates"],"name":[49],"type":["chr"],"align":["left"]},{"label":["roadWinStreak"],"name":[50],"type":["int"],"align":["right"]},{"label":["roadWinStreakDates"],"name":[51],"type":["chr"],"align":["left"]},{"label":["roadWinlessStreak"],"name":[52],"type":["int"],"align":["right"]},{"label":["roadWinlessStreakDates"],"name":[53],"type":["chr"],"align":["left"]},{"label":["winStreak"],"name":[54],"type":["int"],"align":["right"]},{"label":["winStreakDates"],"name":[55],"type":["chr"],"align":["left"]},{"label":["winlessStreak"],"name":[56],"type":["int"],"align":["right"]},{"label":["winlessStreakDates"],"name":[57],"type":["chr"],"align":["left"]}],"data":[{"1":"6","2":"147","3":"172","4":"1952-53 (70)","5":"1955-56 (70)","6":"13","7":"1971-72 (78)","8":"38","9":"1961-62 (70)","10":"5","11":"1972-73 (78)","12":"14","13":"1962-63 (70)","14":"6","15":"Boston Bruins","16":"11","17":"Dec 08 1924 - Feb 17 1925","18":"27","19":"Nov 22 1970 - Mar 20 1971","20":"20","21":"Dec 03 1929 - Mar 18 1930","22":"11","23":"Dec 08 1924 - Feb 17 1925","24":"11","25":"Dec 03 1924 - Jan 05 1925","26":"14","27":"Jan 21 1945 - NYR 3 @ BOS 14","28":"399","29":"306","30":"1961-62 (70)","31":"1970-71 (78)","32":"47","33":"1961-62 (70), 1996-97 (82)","34":"2443","35":"1987-88 (80)","36":"121","37":"1970-71 (78)","38":"15","39":"1927-28 (44)","40":"21","41":"1954-55 (70)","42":"57","43":"1970-71 (78)","44":"23","45":"Dec 22 1940 - Feb 23 1941","46":"14","47":"Dec 27 1964 - Feb 21 1965","48":"16","49":"Jan 11 2014 - Mar 30 2014","50":"9","51":"Mar 02 2014 - Mar 30 2014","52":"14","53":"Oct 12 1963 - Dec 14 1963, Dec 27 1964 - Feb 21 1965, Nov 09 1966 - Jan 07 1967","54":"14","55":"Dec 03 1929 - Jan 09 1930","56":"20","57":"Jan 28 1962 - Mar 11 1962","_rn_":"1"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>

## Franchise goalie records

The franchise season records returns goalie records for a specified
franchise when the **franchiseId** with or without the team name is
provided:

    get_franchise_goalie_records <- function(ID, name){
      if (ID %in% c(1:nrow(get_franchise_data()))){
        if (missing(name)){
        prefix_url <- "https://records.nhl.com/site/api"
        franchise_goalie_records_url <- paste0(prefix_url,"/","franchise-goalie-records?cayenneExp=franchiseId=",ID)
        franchise_goalie_records_list <- franchise_goalie_records_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      
      return(franchise_goalie_records_list$data)
      
    } else {
      if (name %in% get_franchise_data()$fullName){
        {if (ID != which(get_franchise_data()$fullName == name)){
                stop ("The franchise id entered is not for the team selected")
        }
        ID <- which(get_franchise_data()$fullName == name)}
        prefix_url <- "https://records.nhl.com/site/api"
        franchise_goalie_records_url <- paste0(prefix_url,"/","franchise-goalie-records?cayenneExp=franchiseId=",ID)
        franchise_goalie_records_list <- franchise_goalie_records_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      
      return(franchise_goalie_records_list$data)
       }
      }   
      }
     }

## Franchise skater records

The franchise team totals returns the skater records.when the
**franchiseId** with or without the team name is provided:

    get_franchise_skater_records <- function(ID, name){
      if (ID %in% c(1:nrow(get_franchise_data()))){
        if (missing(name)){
        prefix_url <- "https://records.nhl.com/site/api"
        franchise_skater_records_url <- paste0(prefix_url,"/","franchise-skater-records?cayenneExp=franchiseId=",ID)
        franchise_skater_records_list <- franchise_skater_records_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      
      return(franchise_skater_records_list$data)
      
    } else {
      if (name %in% get_franchise_data()$fullName){
        {if (ID != which(get_franchise_data()$fullName == name)){
          stop ("The franchise id entered is not for the team selected")
        }
        ID <- which(get_franchise_data()$fullName == name)}
        prefix_url <- "https://records.nhl.com/site/api"
        franchise_skater_records_url <- paste0(prefix_url,"/","franchise-skater-records?cayenneExp=franchiseId=",ID)
        franchise_skater_records_list <- franchise_skater_records_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      
      return(franchise_skater_records_list$data)
       }
      }   
      }
     }

## Franchise detail

The franchise detail returns admin history and retired numbers when the
**mostRecentTeamId**, represented as *TeamID* is provided. As an option,
both the *TeamID* and full team name can be supplied, as long as they
are matched in the database:

    get_franchise_detail <- function(TeamID, name){
      if (TeamID %in% c(1:max(get_franchise_data()$mostRecentTeamId))){
        if (missing(name)){
        prefix_url <- "https://records.nhl.com/site/api"
        get_franchise_detail_url <- paste0(prefix_url,"/","franchise-detail?cayenneExp=mostRecentTeamId=",TeamID)
        get_franchise_detail_list <- get_franchise_detail_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
      
      
      return(get_franchise_detail_list$data)
      
    } else {
      if (name %in% get_franchise_data()$fullName){
        {if (TeamID != get_franchise_data()$mostRecentTeamId[which(get_franchise_data()$fullName == name)]){
          stop ("The Team id entered is not for the team selected")
        }
        TeamID <- get_franchise_data()$mostRecentTeamId[which(get_franchise_data()$fullName == name)]}
        prefix_url <- "https://records.nhl.com/site/api"
        get_franchise_detail_url <- paste0(prefix_url,"/","franchise-detail?cayenneExp=mostRecentTeamId=",TeamID)
        get_franchise_detail_list <- get_franchise_detail_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE)
        
        return(get_franchise_detail_list$data)
       }
      }   
      }
     }

# Reading data from the National Hockey League’s (NHL) stat API

The following code gets the data about individual teams from the NHL
stats API when the **mostRecentTeamId** is provided:

    get_single_team_stat <- function(ID){
      get_single_team_stat_url <- paste("https://statsapi.web.nhl.com/api/v1/teams/",ID,"/","?expand=team.stat",sep = "")
      get_single_team_stat_url %>% GET() %>% content(.,"text") %>% fromJSON(.,flatten = TRUE) %>% as.data.frame() %>% paged_table()
    }

# Wrapper function to access API endpoints

The wrapper function is called \`get\_NHL\_info. The choice of endpoint
selections are any of:

-   Franchise data
-   Franchise team totals
-   Franchise season records (needs a franchise ID to return data for a
    single team)
-   Franchise goalie records (needs a franchise ID to return data for a
    single team)
-   Franchise skater records (needs a franchise ID to return data for a
    single team)
-   Franchise detail (needs a team ID to return data for a single team)
-   Single team stat (needs a team ID to return data for a single team)

The franchise ID must be between 1 and 39, and the team ID must be
between 1 and 55 or an error message will be generated.

Please refer to [NHL records
API](https://gitlab.com/dword4/nhlapi/-/blob/master/records-api.md) for
a clear explanation of the purpose of the API endpoints.

    get_NHL_info<- function(endpoint,ID){

      if(endpoint == "Franchise data"){
        get_franchise_data()
      } else if (endpoint == "Franchise team totals"){
         get_franchise_team_totals()
      } else if (endpoint == "Franchise season records"){
         get_franchise_season_records(ID) 
      } else if (endpoint == "Franchise goalie records"){
         get_franchise_goalie_records (ID)
      } else if (endpoint == "Franchise skater records"){
         get_franchise_skater_records (ID) 
      } else if (endpoint == "Franchise detail"){
         get_franchise_detail (ID)
       
    }

    }

# Exploratory Data Analysis

## Data from 2 endpoints

### Some data for active and inactive players of the Dallas Stars:

The table below shows some data for active and inactive players for the
Dallas Stars:

    skater_records <- get_NHL_info("Franchise skater records",15)
    skater_records_subset <- skater_records %>% select(activePlayer,firstName,lastName,gamesPlayed,positionCode,seasons)
    kable(skater_records_subset,caption = "Summary of active and inactive Dallas Stars Players by position ,games played and number of seasons")

<table>
<caption>
Summary of active and inactive Dallas Stars Players by position ,games
played and number of seasons
</caption>
<thead>
<tr>
<th style="text-align:left;">
activePlayer
</th>
<th style="text-align:left;">
firstName
</th>
<th style="text-align:left;">
lastName
</th>
<th style="text-align:right;">
gamesPlayed
</th>
<th style="text-align:left;">
positionCode
</th>
<th style="text-align:right;">
seasons
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chris
</td>
<td style="text-align:left;">
Ahrens
</td>
<td style="text-align:right;">
52
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Antonovich
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Norm
</td>
<td style="text-align:left;">
Beaudin
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Don
</td>
<td style="text-align:left;">
Blackburn
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chuck
</td>
<td style="text-align:left;">
Arnason
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Baby
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Barrett
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Terry
</td>
<td style="text-align:left;">
Caffery
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Chernoff
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rick
</td>
<td style="text-align:left;">
Chinnick
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Bergloff
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brad
</td>
<td style="text-align:left;">
Berry
</td>
<td style="text-align:right;">
78
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Don
</td>
<td style="text-align:left;">
Biggs
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Cook
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
Boo
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Neil
</td>
<td style="text-align:left;">
Brady
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rob
</td>
<td style="text-align:left;">
Brown
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Murray
</td>
<td style="text-align:left;">
Brumwell
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jerry
</td>
<td style="text-align:left;">
Byers
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gary
</td>
<td style="text-align:left;">
Dineen
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jay
</td>
<td style="text-align:left;">
Caufield
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Colin
</td>
<td style="text-align:left;">
Chisholm
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Joe
</td>
<td style="text-align:left;">
Contini
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Grant
</td>
<td style="text-align:left;">
Erickson
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gerald
</td>
<td style="text-align:left;">
Diduck
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
Dobson
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Peter
</td>
<td style="text-align:left;">
Douris
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ken
</td>
<td style="text-align:left;">
Duggan
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gary
</td>
<td style="text-align:left;">
Geldart
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kevin
</td>
<td style="text-align:left;">
Evans
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Wayne
</td>
<td style="text-align:left;">
Hillman
</td>
<td style="text-align:right;">
50
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jon
</td>
<td style="text-align:left;">
Fontas
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Link
</td>
<td style="text-align:left;">
Gaetz
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jamie
</td>
<td style="text-align:left;">
Gallimore
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Don
</td>
<td style="text-align:left;">
Johns
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Marshall
</td>
<td style="text-align:left;">
Johnston
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Len
</td>
<td style="text-align:left;">
Lunde
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kim
</td>
<td style="text-align:left;">
MacDougall
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Barry
</td>
<td style="text-align:left;">
MacKenzie
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Greg
</td>
<td style="text-align:left;">
Hawgood
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ted
</td>
<td style="text-align:left;">
McCaskill
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Peter
</td>
<td style="text-align:left;">
Hayek
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Raimo
</td>
<td style="text-align:left;">
Helminen
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Archie
</td>
<td style="text-align:left;">
Henderson
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
McElmury
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bruce
</td>
<td style="text-align:left;">
McIntosh
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Barrie
</td>
<td style="text-align:left;">
Meissner
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Miszuk
</td>
<td style="text-align:right;">
50
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Wayne
</td>
<td style="text-align:left;">
Muloin
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ed
</td>
<td style="text-align:left;">
Hospodar
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Plager
</td>
<td style="text-align:right;">
60
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
Polanic
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Don
</td>
<td style="text-align:left;">
Jackson
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Andre
</td>
<td style="text-align:left;">
Pronovost
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Janssens
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Wes
</td>
<td style="text-align:left;">
Jarvis
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Paul
</td>
<td style="text-align:left;">
Jerrard
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
David
</td>
<td style="text-align:left;">
Jensen
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kevin
</td>
<td style="text-align:left;">
Kaminski
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Udo
</td>
<td style="text-align:left;">
Kiessling
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Robbie
</td>
<td style="text-align:left;">
Laird
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Langevin
</td>
<td style="text-align:right;">
80
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Peter
</td>
<td style="text-align:left;">
Lappin
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Reed
</td>
<td style="text-align:left;">
Larson
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ken
</td>
<td style="text-align:left;">
Leiter
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jyrki
</td>
<td style="text-align:left;">
Lumme
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Doug
</td>
<td style="text-align:left;">
Lidster
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pat
</td>
<td style="text-align:left;">
MacLeod
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dean
</td>
<td style="text-align:left;">
Magee
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
Smith
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
George
</td>
<td style="text-align:left;">
Standing
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sergei
</td>
<td style="text-align:left;">
Makarov
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Markell
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Daniel
</td>
<td style="text-align:left;">
Marois
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jean-Guy
</td>
<td style="text-align:left;">
Talbot
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Martinson
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Whitlock
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Michael
</td>
<td style="text-align:left;">
McHugh
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Roger
</td>
<td style="text-align:left;">
Melin
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mitchell
</td>
<td style="text-align:left;">
Messier
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jayson
</td>
<td style="text-align:left;">
More
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Lyle
</td>
<td style="text-align:left;">
Odelein
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Scott
</td>
<td style="text-align:left;">
Robinson
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Paradise
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Allen
</td>
<td style="text-align:left;">
Pedersen
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pat
</td>
<td style="text-align:left;">
Price
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Quinn
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dick
</td>
<td style="text-align:left;">
Redmond
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Scott
</td>
<td style="text-align:left;">
Sandelin
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
George
</td>
<td style="text-align:left;">
Servinis
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
David
</td>
<td style="text-align:left;">
Shaw
</td>
<td style="text-align:right;">
37
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jarrod
</td>
<td style="text-align:left;">
Skalde
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Randy
</td>
<td style="text-align:left;">
Smith
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Harold
</td>
<td style="text-align:left;">
Snepsts
</td>
<td style="text-align:right;">
71
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Stewart
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Don
</td>
<td style="text-align:left;">
Sweeney
</td>
<td style="text-align:right;">
63
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Peter
</td>
<td style="text-align:left;">
Taglianetti
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
William
</td>
<td style="text-align:left;">
Terry
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sean
</td>
<td style="text-align:left;">
Toomey
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kirk
</td>
<td style="text-align:left;">
Tomlinson
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mario
</td>
<td style="text-align:left;">
Thyer
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tony
</td>
<td style="text-align:left;">
White
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Douglas
</td>
<td style="text-align:left;">
Barrault
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Enrico
</td>
<td style="text-align:left;">
Ciccone
</td>
<td style="text-align:right;">
42
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
Colley
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kevin
</td>
<td style="text-align:left;">
Dean
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Iain
</td>
<td style="text-align:left;">
Fraser
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Duane
</td>
<td style="text-align:left;">
Joyce
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Marc
</td>
<td style="text-align:left;">
Labelle
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Roy
</td>
<td style="text-align:left;">
Mitchell
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Michael
</td>
<td style="text-align:left;">
Needham
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Osiecki
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Travis
</td>
<td style="text-align:left;">
Richards
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Reid
</td>
<td style="text-align:left;">
Simpson
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chris
</td>
<td style="text-align:left;">
Therien
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jamie
</td>
<td style="text-align:left;">
Pushor
</td>
<td style="text-align:right;">
62
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Zac
</td>
<td style="text-align:left;">
Boyer
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Kesa
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rob
</td>
<td style="text-align:left;">
Valicevic
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Lawrence
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kelly
</td>
<td style="text-align:left;">
Fairchild
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Patrick
</td>
<td style="text-align:left;">
Traverse
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Joel
</td>
<td style="text-align:left;">
Bouchard
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Wotton
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
Montgomery
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jeffrey
</td>
<td style="text-align:left;">
Mitchell
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Warren
</td>
<td style="text-align:left;">
Luhning
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Alan
</td>
<td style="text-align:left;">
Letang
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Nolan
</td>
<td style="text-align:left;">
Baumgartner
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jason
</td>
<td style="text-align:left;">
Botterill
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Patrick
</td>
<td style="text-align:left;">
Cote
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Petr
</td>
<td style="text-align:left;">
Buzek
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Keith
</td>
<td style="text-align:left;">
Aldridge
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ryan
</td>
<td style="text-align:left;">
Christie
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Willie
</td>
<td style="text-align:left;">
Mitchell
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Gainey
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Todd
</td>
<td style="text-align:left;">
Fedoruk
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Greg
</td>
<td style="text-align:left;">
Leeb
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jeff
</td>
<td style="text-align:left;">
MacMillan
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Doug
</td>
<td style="text-align:left;">
Janik
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Jancevski
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gavin
</td>
<td style="text-align:left;">
Morgan
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Maxime
</td>
<td style="text-align:left;">
Fortunus
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Aaron
</td>
<td style="text-align:left;">
Rome
</td>
<td style="text-align:right;">
52
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Vojtech
</td>
<td style="text-align:left;">
Polak
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Francis
</td>
<td style="text-align:left;">
Wathier
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Garrett
</td>
<td style="text-align:left;">
Stafford
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Adam
</td>
<td style="text-align:left;">
Pardy
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Aaron
</td>
<td style="text-align:left;">
Gagnon
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Travis
</td>
<td style="text-align:left;">
Morin
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Kris
</td>
<td style="text-align:left;">
Russell
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Perttu
</td>
<td style="text-align:left;">
Lindgren
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ivan
</td>
<td style="text-align:left;">
Vishnevskiy
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
David
</td>
<td style="text-align:left;">
Schlemko
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chris
</td>
<td style="text-align:left;">
Mueller
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Cameron
</td>
<td style="text-align:left;">
Gaunce
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Scott
</td>
<td style="text-align:left;">
Glennie
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Patrik
</td>
<td style="text-align:left;">
Nemeth
</td>
<td style="text-align:right;">
108
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brendan
</td>
<td style="text-align:left;">
Ranford
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
McNeill
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dillon
</td>
<td style="text-align:left;">
Heatherington
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Ben
</td>
<td style="text-align:left;">
Gleason
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Joel
</td>
<td style="text-align:left;">
Hanley
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ben
</td>
<td style="text-align:left;">
Lovejoy
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Sami
</td>
<td style="text-align:left;">
Vatanen
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
Archibald
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Charlebois
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Butters
</td>
<td style="text-align:right;">
72
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Chicoine
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jerry
</td>
<td style="text-align:left;">
Engele
</td>
<td style="text-align:right;">
100
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gord
</td>
<td style="text-align:left;">
Dineen
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gord
</td>
<td style="text-align:left;">
Donnelly
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gary
</td>
<td style="text-align:left;">
Gambucci
</td>
<td style="text-align:right;">
51
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pete
</td>
<td style="text-align:left;">
Goegan
</td>
<td style="text-align:right;">
46
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Duke
</td>
<td style="text-align:left;">
Harris
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Heindl
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Larry
</td>
<td style="text-align:left;">
Hillman
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rob
</td>
<td style="text-align:left;">
Flockhart
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bronco
</td>
<td style="text-align:left;">
Horvath
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Joey
</td>
<td style="text-align:left;">
Johnston
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Gotaas
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jari
</td>
<td style="text-align:left;">
Gronstrand
</td>
<td style="text-align:right;">
47
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
David
</td>
<td style="text-align:left;">
Hanson
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
Hirsch
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ken
</td>
<td style="text-align:left;">
Hodge Jr. 
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Paul
</td>
<td style="text-align:left;">
Houck
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Orban
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Keczmer
</td>
<td style="text-align:right;">
61
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dean
</td>
<td style="text-align:left;">
Kolstad
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Lalor
</td>
<td style="text-align:right;">
142
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Seguin
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Darryl
</td>
<td style="text-align:left;">
Sly
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Manson
</td>
<td style="text-align:right;">
60
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Terry
</td>
<td style="text-align:left;">
Martin
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
Martin
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Alan
</td>
<td style="text-align:left;">
May
</td>
<td style="text-align:right;">
35
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Moose
</td>
<td style="text-align:left;">
Vasko
</td>
<td style="text-align:right;">
145
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kevin
</td>
<td style="text-align:left;">
Maxwell
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Randy
</td>
<td style="text-align:left;">
McKay
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
McKenny
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ron
</td>
<td style="text-align:left;">
Meighan
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kip
</td>
<td style="text-align:left;">
Miller
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Craig
</td>
<td style="text-align:left;">
Muni
</td>
<td style="text-align:right;">
40
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Christopher
</td>
<td style="text-align:left;">
Pryor
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Stephane
</td>
<td style="text-align:left;">
Roy
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brent
</td>
<td style="text-align:left;">
Severyn
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chris
</td>
<td style="text-align:left;">
Tancill
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tim
</td>
<td style="text-align:left;">
Trimper
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Allan
</td>
<td style="text-align:left;">
Tuer
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Emanuel
</td>
<td style="text-align:left;">
Viveiros
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Randy
</td>
<td style="text-align:left;">
Wood
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Warren
</td>
<td style="text-align:left;">
Young
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Richard
</td>
<td style="text-align:left;">
Zemlak
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rob
</td>
<td style="text-align:left;">
Zettler
</td>
<td style="text-align:right;">
80
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jaroslav
</td>
<td style="text-align:left;">
Modry
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Daniel
</td>
<td style="text-align:left;">
Poulin
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Doug
</td>
<td style="text-align:left;">
Zmolek
</td>
<td style="text-align:right;">
91
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
Storm
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Robert
</td>
<td style="text-align:left;">
Petrovicky
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jeremy
</td>
<td style="text-align:left;">
Stevenson
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Nikolai
</td>
<td style="text-align:left;">
Borschevsky
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Matthew
</td>
<td style="text-align:left;">
Barnaby
</td>
<td style="text-align:right;">
39
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sami
</td>
<td style="text-align:left;">
Helenius
</td>
<td style="text-align:right;">
101
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sergey
</td>
<td style="text-align:left;">
Gusev
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ric
</td>
<td style="text-align:left;">
Jackman
</td>
<td style="text-align:right;">
38
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Begin
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Aaron
</td>
<td style="text-align:left;">
Downey
</td>
<td style="text-align:right;">
80
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pavel
</td>
<td style="text-align:left;">
Patera
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brad
</td>
<td style="text-align:left;">
Winchester
</td>
<td style="text-align:right;">
41
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Lubomir
</td>
<td style="text-align:left;">
Sekeras
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Warren
</td>
<td style="text-align:left;">
Peters
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
B.J.
</td>
<td style="text-align:left;">
Crombeen
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Marc
</td>
<td style="text-align:left;">
Methot
</td>
<td style="text-align:right;">
45
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Junior
</td>
<td style="text-align:left;">
Lessard
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Raymond
</td>
<td style="text-align:left;">
Sawada
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Greg
</td>
<td style="text-align:left;">
Pateryn
</td>
<td style="text-align:right;">
85
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Kevin
</td>
<td style="text-align:left;">
Connauton
</td>
<td style="text-align:right;">
44
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Matt
</td>
<td style="text-align:left;">
Fraser
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Julius
</td>
<td style="text-align:left;">
Honka
</td>
<td style="text-align:right;">
87
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Connor
</td>
<td style="text-align:left;">
Carrick
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Roman
</td>
<td style="text-align:left;">
Polak
</td>
<td style="text-align:right;">
118
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Erik
</td>
<td style="text-align:left;">
Condra
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Mats
</td>
<td style="text-align:left;">
Zuccarello
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Nick
</td>
<td style="text-align:left;">
Caamano
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Rhett
</td>
<td style="text-align:left;">
Gardner
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Warren
</td>
<td style="text-align:left;">
Babe
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Barr
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
James
</td>
<td style="text-align:left;">
Black
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rick
</td>
<td style="text-align:left;">
Boh
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Paul
</td>
<td style="text-align:left;">
Boutilier
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tim
</td>
<td style="text-align:left;">
Coulis
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sylvain
</td>
<td style="text-align:left;">
Cote
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chris
</td>
<td style="text-align:left;">
Dahlquist
</td>
<td style="text-align:right;">
116
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pelle
</td>
<td style="text-align:left;">
Eklund
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Murray
</td>
<td style="text-align:left;">
Hall
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Errey
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Terry
</td>
<td style="text-align:left;">
Holbrook
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Hardy
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rod
</td>
<td style="text-align:left;">
Norrish
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Johnson
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Rogers
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Duane
</td>
<td style="text-align:left;">
Rupp
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Claude
</td>
<td style="text-align:left;">
Lemieux
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Craig
</td>
<td style="text-align:left;">
Ludwig
</td>
<td style="text-align:right;">
584
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
8
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Lars
</td>
<td style="text-align:left;">
Lindgren
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
David
</td>
<td style="text-align:left;">
Mackey
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Maltais
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bryan
</td>
<td style="text-align:left;">
Maxwell
</td>
<td style="text-align:right;">
42
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
McKenzie
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Patrick
</td>
<td style="text-align:left;">
Micheletti
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Richter
</td>
<td style="text-align:right;">
120
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gord
</td>
<td style="text-align:left;">
Sherven
</td>
<td style="text-align:right;">
45
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ville
</td>
<td style="text-align:left;">
Siren
</td>
<td style="text-align:right;">
91
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Derrick
</td>
<td style="text-align:left;">
Smith
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Neil
</td>
<td style="text-align:left;">
Wilkinson
</td>
<td style="text-align:right;">
86
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Derek
</td>
<td style="text-align:left;">
Plante
</td>
<td style="text-align:right;">
26
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sergei
</td>
<td style="text-align:left;">
Gonchar
</td>
<td style="text-align:right;">
79
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Valeri
</td>
<td style="text-align:left;">
Bure
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jarkko
</td>
<td style="text-align:left;">
Varvio
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mattias
</td>
<td style="text-align:left;">
Norstrom
</td>
<td style="text-align:right;">
80
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Landon
</td>
<td style="text-align:left;">
Wilson
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Janne
</td>
<td style="text-align:left;">
Niinimaa
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chris
</td>
<td style="text-align:left;">
Murray
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Nathan
</td>
<td style="text-align:left;">
Perrott
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Erskine
</td>
<td style="text-align:right;">
107
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tyler
</td>
<td style="text-align:left;">
Bouck
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Andrew
</td>
<td style="text-align:left;">
Hutchinson
</td>
<td style="text-align:right;">
38
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mathias
</td>
<td style="text-align:left;">
Tjarnqvist
</td>
<td style="text-align:right;">
69
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jason
</td>
<td style="text-align:left;">
Williams
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jeff
</td>
<td style="text-align:left;">
Woywitka
</td>
<td style="text-align:right;">
99
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Fistric
</td>
<td style="text-align:right;">
257
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Nicklas
</td>
<td style="text-align:left;">
Grossmann
</td>
<td style="text-align:right;">
333
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jake
</td>
<td style="text-align:left;">
Dowell
</td>
<td style="text-align:right;">
52
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dustin
</td>
<td style="text-align:left;">
Jeffrey
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Lane
</td>
<td style="text-align:left;">
MacDermid
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jyrki
</td>
<td style="text-align:left;">
Jokipakka
</td>
<td style="text-align:right;">
91
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Gavin
</td>
<td style="text-align:left;">
Bayreuther
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Joel
</td>
<td style="text-align:left;">
L’Esperance
</td>
<td style="text-align:right;">
33
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gary
</td>
<td style="text-align:left;">
Bergman
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Leo
</td>
<td style="text-align:left;">
Boivin
</td>
<td style="text-align:right;">
97
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Helmut
</td>
<td style="text-align:left;">
Balderis
</td>
<td style="text-align:right;">
26
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Berger
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sandy
</td>
<td style="text-align:left;">
Fitzpatrick
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Clark
</td>
<td style="text-align:left;">
Donatelli
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Alain
</td>
<td style="text-align:left;">
Langlais
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mats
</td>
<td style="text-align:left;">
Hallin
</td>
<td style="text-align:right;">
44
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Mandich
</td>
<td style="text-align:right;">
111
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Corey
</td>
<td style="text-align:left;">
Millen
</td>
<td style="text-align:right;">
41
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Teppo
</td>
<td style="text-align:left;">
Numminen
</td>
<td style="text-align:right;">
62
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Paul
</td>
<td style="text-align:left;">
Shmyr
</td>
<td style="text-align:right;">
124
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ted
</td>
<td style="text-align:left;">
Taylor
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Valeri
</td>
<td style="text-align:left;">
Kamensky
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Scott
</td>
<td style="text-align:left;">
Pellerin
</td>
<td style="text-align:right;">
53
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Juha
</td>
<td style="text-align:left;">
Lind
</td>
<td style="text-align:right;">
73
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Manny
</td>
<td style="text-align:left;">
Malhotra
</td>
<td style="text-align:right;">
84
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Karlis
</td>
<td style="text-align:left;">
Skrastins
</td>
<td style="text-align:right;">
153
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sean
</td>
<td style="text-align:left;">
Avery
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Joel
</td>
<td style="text-align:left;">
Lundqvist
</td>
<td style="text-align:right;">
134
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Travis
</td>
<td style="text-align:left;">
Moen
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dan
</td>
<td style="text-align:left;">
Hamhuis
</td>
<td style="text-align:right;">
159
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jiri
</td>
<td style="text-align:left;">
Hudler
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Chris
</td>
<td style="text-align:left;">
Conner
</td>
<td style="text-align:right;">
71
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Philip
</td>
<td style="text-align:left;">
Larsen
</td>
<td style="text-align:right;">
95
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Jordie
</td>
<td style="text-align:left;">
Benn
</td>
<td style="text-align:right;">
302
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Reilly
</td>
<td style="text-align:left;">
Smith
</td>
<td style="text-align:right;">
40
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Justin
</td>
<td style="text-align:left;">
Dowling
</td>
<td style="text-align:right;">
76
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Andrej
</td>
<td style="text-align:left;">
Sekera
</td>
<td style="text-align:right;">
103
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Pysyk
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Ty
</td>
<td style="text-align:left;">
Dellandrea
</td>
<td style="text-align:right;">
26
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Tanner
</td>
<td style="text-align:left;">
Kero
</td>
<td style="text-align:right;">
39
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Donald
</td>
<td style="text-align:left;">
Audette
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Benoit
</td>
<td style="text-align:left;">
Brunet
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Cressman
</td>
<td style="text-align:right;">
85
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Milan
</td>
<td style="text-align:left;">
Marcetta
</td>
<td style="text-align:right;">
54
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Masterton
</td>
<td style="text-align:right;">
38
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
McCord
</td>
<td style="text-align:right;">
139
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Paul
</td>
<td style="text-align:left;">
Holmgren
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rich
</td>
<td style="text-align:left;">
Nantais
</td>
<td style="text-align:right;">
63
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
Johnson
</td>
<td style="text-align:right;">
247
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bobby
</td>
<td style="text-align:left;">
Rousseau
</td>
<td style="text-align:right;">
63
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
MacLean
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Woytowich
</td>
<td style="text-align:right;">
66
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Nyrop
</td>
<td style="text-align:right;">
42
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dusan
</td>
<td style="text-align:left;">
Pasek
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Pavelich
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rob
</td>
<td style="text-align:left;">
Ramage
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Rouse
</td>
<td style="text-align:right;">
351
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
Skrudland
</td>
<td style="text-align:right;">
75
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Randy
</td>
<td style="text-align:left;">
Velischek
</td>
<td style="text-align:right;">
88
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jon
</td>
<td style="text-align:left;">
Klemm
</td>
<td style="text-align:right;">
172
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brad
</td>
<td style="text-align:left;">
Lukowich
</td>
<td style="text-align:right;">
229
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jamie
</td>
<td style="text-align:left;">
Wright
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Radek
</td>
<td style="text-align:left;">
Dvorak
</td>
<td style="text-align:right;">
73
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ladislav
</td>
<td style="text-align:left;">
Nagy
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Blake
</td>
<td style="text-align:left;">
Sloan
</td>
<td style="text-align:right;">
142
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Martin
</td>
<td style="text-align:left;">
Skoula
</td>
<td style="text-align:right;">
61
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Krys
</td>
<td style="text-align:left;">
Barch
</td>
<td style="text-align:right;">
263
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jaroslav
</td>
<td style="text-align:left;">
Svoboda
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Derek
</td>
<td style="text-align:left;">
Roy
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Johnny
</td>
<td style="text-align:left;">
Oduya
</td>
<td style="text-align:right;">
119
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tomas
</td>
<td style="text-align:left;">
Vincour
</td>
<td style="text-align:right;">
86
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Taylor
</td>
<td style="text-align:left;">
Fedun
</td>
<td style="text-align:right;">
81
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dwight
</td>
<td style="text-align:left;">
Bialowas
</td>
<td style="text-align:right;">
116
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Bassen
</td>
<td style="text-align:right;">
117
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Shayne
</td>
<td style="text-align:left;">
Corson
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Curt
</td>
<td style="text-align:left;">
Fraser
</td>
<td style="text-align:right;">
53
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ted
</td>
<td style="text-align:left;">
Hampson
</td>
<td style="text-align:right;">
96
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Walt
</td>
<td style="text-align:left;">
McKechnie
</td>
<td style="text-align:right;">
112
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jean
</td>
<td style="text-align:left;">
Potvin
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Terry
</td>
<td style="text-align:left;">
Ruskowski
</td>
<td style="text-align:right;">
50
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ilkka
</td>
<td style="text-align:left;">
Sinisalo
</td>
<td style="text-align:right;">
46
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Greg
</td>
<td style="text-align:left;">
Smith
</td>
<td style="text-align:right;">
209
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Eric
</td>
<td style="text-align:left;">
Lindros
</td>
<td style="text-align:right;">
49
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jon
</td>
<td style="text-align:left;">
Sim
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Patrik
</td>
<td style="text-align:left;">
Stefan
</td>
<td style="text-align:right;">
41
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
Sutherby
</td>
<td style="text-align:right;">
139
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brandon
</td>
<td style="text-align:left;">
Segal
</td>
<td style="text-align:right;">
65
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Martin
</td>
<td style="text-align:left;">
Hanzal
</td>
<td style="text-align:right;">
45
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Andrew
</td>
<td style="text-align:left;">
Cogliano
</td>
<td style="text-align:right;">
154
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Corey
</td>
<td style="text-align:left;">
Perry
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Shane
</td>
<td style="text-align:left;">
Churla
</td>
<td style="text-align:right;">
366
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
8
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bo
</td>
<td style="text-align:left;">
Berglund
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Marc
</td>
<td style="text-align:left;">
Bureau
</td>
<td style="text-align:right;">
55
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ron
</td>
<td style="text-align:left;">
Friest
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Curt
</td>
<td style="text-align:left;">
Giles
</td>
<td style="text-align:right;">
760
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
12
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Don
</td>
<td style="text-align:left;">
Martineau
</td>
<td style="text-align:right;">
76
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Doug
</td>
<td style="text-align:left;">
Hicks
</td>
<td style="text-align:right;">
300
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Doug
</td>
<td style="text-align:left;">
Mohns
</td>
<td style="text-align:right;">
162
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dennis
</td>
<td style="text-align:left;">
O’Brien
</td>
<td style="text-align:right;">
470
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
8
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
Reid
</td>
<td style="text-align:right;">
615
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
10
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Doug
</td>
<td style="text-align:left;">
Rombough
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Craig
</td>
<td style="text-align:left;">
Levie
</td>
<td style="text-align:right;">
51
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Wally
</td>
<td style="text-align:left;">
Schreiber
</td>
<td style="text-align:right;">
41
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Scott
</td>
<td style="text-align:left;">
Thornton
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Peter
</td>
<td style="text-align:left;">
Zezel
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Huard
</td>
<td style="text-align:right;">
91
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Martin
</td>
<td style="text-align:left;">
Rucinsky
</td>
<td style="text-align:right;">
42
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brendan
</td>
<td style="text-align:left;">
Morrison
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sheldon
</td>
<td style="text-align:left;">
Souray
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Roman
</td>
<td style="text-align:left;">
Lyashenko
</td>
<td style="text-align:right;">
122
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Curtis
</td>
<td style="text-align:left;">
McKenzie
</td>
<td style="text-align:right;">
99
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Brenden
</td>
<td style="text-align:left;">
Dillon
</td>
<td style="text-align:right;">
149
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Jamie
</td>
<td style="text-align:left;">
Oleksiak
</td>
<td style="text-align:right;">
286
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Gemel
</td>
<td style="text-align:left;">
Smith
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Remi
</td>
<td style="text-align:left;">
Elie
</td>
<td style="text-align:right;">
90
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Joel
</td>
<td style="text-align:left;">
Kiviranta
</td>
<td style="text-align:right;">
37
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brent
</td>
<td style="text-align:left;">
Ashton
</td>
<td style="text-align:right;">
97
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Nick
</td>
<td style="text-align:left;">
Beverley
</td>
<td style="text-align:right;">
109
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ted
</td>
<td style="text-align:left;">
Harris
</td>
<td style="text-align:right;">
246
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Doug
</td>
<td style="text-align:left;">
Smail
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Shaun
</td>
<td style="text-align:left;">
Van Allen
</td>
<td style="text-align:right;">
78
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tommy
</td>
<td style="text-align:left;">
Sjodin
</td>
<td style="text-align:right;">
84
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
David
</td>
<td style="text-align:left;">
Oliver
</td>
<td style="text-align:right;">
45
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Aaron
</td>
<td style="text-align:left;">
Gavey
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rich
</td>
<td style="text-align:left;">
Peverley
</td>
<td style="text-align:right;">
62
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Matt
</td>
<td style="text-align:left;">
Niskanen
</td>
<td style="text-align:right;">
277
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
Wandell
</td>
<td style="text-align:right;">
229
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Jason
</td>
<td style="text-align:left;">
Demers
</td>
<td style="text-align:right;">
123
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Fred
</td>
<td style="text-align:left;">
Barrett
</td>
<td style="text-align:right;">
730
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
12
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jack
</td>
<td style="text-align:left;">
Carlson
</td>
<td style="text-align:right;">
124
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Shawn
</td>
<td style="text-align:left;">
Chambers
</td>
<td style="text-align:right;">
320
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
George
</td>
<td style="text-align:left;">
Ferguson
</td>
<td style="text-align:right;">
128
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bryan
</td>
<td style="text-align:left;">
Hextall Jr. 
</td>
<td style="text-align:right;">
58
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Flesch
</td>
<td style="text-align:right;">
90
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gord
</td>
<td style="text-align:left;">
Labossiere
</td>
<td style="text-align:right;">
38
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
Glynn
</td>
<td style="text-align:right;">
103
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gordie
</td>
<td style="text-align:left;">
Roberts
</td>
<td style="text-align:right;">
555
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
8
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ken
</td>
<td style="text-align:left;">
Solheim
</td>
<td style="text-align:right;">
114
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ted
</td>
<td style="text-align:left;">
Donato
</td>
<td style="text-align:right;">
65
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Parrish
</td>
<td style="text-align:right;">
44
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Adam
</td>
<td style="text-align:left;">
Burish
</td>
<td style="text-align:right;">
128
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Lauri
</td>
<td style="text-align:left;">
Korpikoski
</td>
<td style="text-align:right;">
60
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Stephen
</td>
<td style="text-align:left;">
Johns
</td>
<td style="text-align:right;">
167
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Blake
</td>
<td style="text-align:left;">
Comeau
</td>
<td style="text-align:right;">
183
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Aaron
</td>
<td style="text-align:left;">
Broten
</td>
<td style="text-align:right;">
35
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Fairbairn
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tony
</td>
<td style="text-align:left;">
Featherstone
</td>
<td style="text-align:right;">
54
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Larry
</td>
<td style="text-align:left;">
Depalma
</td>
<td style="text-align:right;">
121
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Blake
</td>
<td style="text-align:left;">
Dunlop
</td>
<td style="text-align:right;">
100
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Frantisek
</td>
<td style="text-align:left;">
Musil
</td>
<td style="text-align:right;">
271
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Danny
</td>
<td style="text-align:left;">
Lawson
</td>
<td style="text-align:right;">
95
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Grant
</td>
<td style="text-align:left;">
Ledyard
</td>
<td style="text-align:right;">
270
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Glen
</td>
<td style="text-align:left;">
Sather
</td>
<td style="text-align:right;">
72
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Moe
</td>
<td style="text-align:left;">
Mantha
</td>
<td style="text-align:right;">
46
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Richard
</td>
<td style="text-align:left;">
Matvichuk
</td>
<td style="text-align:right;">
733
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
12
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Kennedy
</td>
<td style="text-align:right;">
131
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Toby
</td>
<td style="text-align:left;">
Petersen
</td>
<td style="text-align:right;">
243
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Alex
</td>
<td style="text-align:left;">
Goligoski
</td>
<td style="text-align:right;">
385
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Jason
</td>
<td style="text-align:left;">
Dickinson
</td>
<td style="text-align:right;">
221
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Craig
</td>
<td style="text-align:left;">
Cameron
</td>
<td style="text-align:right;">
182
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Brooke
</td>
<td style="text-align:right;">
237
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Guy
</td>
<td style="text-align:left;">
Carbonneau
</td>
<td style="text-align:right;">
364
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Rob
</td>
<td style="text-align:left;">
DiMaio
</td>
<td style="text-align:right;">
199
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brent
</td>
<td style="text-align:left;">
Fedyk
</td>
<td style="text-align:right;">
41
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Hogaboam
</td>
<td style="text-align:right;">
109
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Barry
</td>
<td style="text-align:left;">
Gibbs
</td>
<td style="text-align:right;">
375
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kirk
</td>
<td style="text-align:left;">
Muller
</td>
<td style="text-align:right;">
235
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Larry
</td>
<td style="text-align:left;">
Murphy
</td>
<td style="text-align:right;">
121
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Polich
</td>
<td style="text-align:right;">
225
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
Younghans
</td>
<td style="text-align:right;">
382
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Stephane
</td>
<td style="text-align:left;">
Robidas
</td>
<td style="text-align:right;">
704
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
11
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jeff
</td>
<td style="text-align:left;">
Halpern
</td>
<td style="text-align:right;">
140
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Adam
</td>
<td style="text-align:left;">
Cracknell
</td>
<td style="text-align:right;">
70
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Hogaboam
</td>
<td style="text-align:right;">
109
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Harvey
</td>
<td style="text-align:left;">
Bennett
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Charlie
</td>
<td style="text-align:left;">
Burns
</td>
<td style="text-align:right;">
268
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Perry
</td>
<td style="text-align:left;">
Berezan
</td>
<td style="text-align:right;">
132
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Paul
</td>
<td style="text-align:left;">
Cavallini
</td>
<td style="text-align:right;">
126
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Donnelly
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dean
</td>
<td style="text-align:left;">
Evason
</td>
<td style="text-align:right;">
127
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jim
</td>
<td style="text-align:left;">
Roberts
</td>
<td style="text-align:right;">
106
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ray
</td>
<td style="text-align:left;">
Whitney
</td>
<td style="text-align:right;">
101
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Todd
</td>
<td style="text-align:left;">
Harvey
</td>
<td style="text-align:right;">
239
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Shawn
</td>
<td style="text-align:left;">
Horcoff
</td>
<td style="text-align:right;">
153
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Colton
</td>
<td style="text-align:left;">
Sceviour
</td>
<td style="text-align:right;">
170
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Esa
</td>
<td style="text-align:left;">
Lindell
</td>
<td style="text-align:right;">
364
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Basil
</td>
<td style="text-align:left;">
McRae
</td>
<td style="text-align:right;">
323
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Paul
</td>
<td style="text-align:left;">
Broten
</td>
<td style="text-align:right;">
111
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Anders
</td>
<td style="text-align:left;">
Hakansson
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Stew
</td>
<td style="text-align:left;">
Gavin
</td>
<td style="text-align:right;">
289
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ron
</td>
<td style="text-align:left;">
Wilson
</td>
<td style="text-align:right;">
113
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Derian
</td>
<td style="text-align:left;">
Hatcher
</td>
<td style="text-align:right;">
827
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
12
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Miro
</td>
<td style="text-align:left;">
Heiskanen
</td>
<td style="text-align:right;">
205
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jordy
</td>
<td style="text-align:left;">
Douglas
</td>
<td style="text-align:right;">
82
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tony
</td>
<td style="text-align:left;">
Hrkac
</td>
<td style="text-align:right;">
82
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Keane
</td>
<td style="text-align:right;">
242
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Napier
</td>
<td style="text-align:right;">
97
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gary
</td>
<td style="text-align:left;">
Sargent
</td>
<td style="text-align:right;">
187
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Sergei
</td>
<td style="text-align:left;">
Zubov
</td>
<td style="text-align:right;">
839
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
12
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Grant
</td>
<td style="text-align:left;">
Marshall
</td>
<td style="text-align:right;">
402
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ales
</td>
<td style="text-align:left;">
Hemsky
</td>
<td style="text-align:right;">
166
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Vernon
</td>
<td style="text-align:left;">
Fiddler
</td>
<td style="text-align:right;">
366
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Alex
</td>
<td style="text-align:left;">
Chiasson
</td>
<td style="text-align:right;">
86
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
John
</td>
<td style="text-align:left;">
Klingberg
</td>
<td style="text-align:right;">
478
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Devin
</td>
<td style="text-align:left;">
Shore
</td>
<td style="text-align:right;">
209
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Archibald
</td>
<td style="text-align:right;">
162
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Todd
</td>
<td style="text-align:left;">
Elik
</td>
<td style="text-align:right;">
108
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Norm
</td>
<td style="text-align:left;">
Gratton
</td>
<td style="text-align:right;">
66
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
McMahon Jr. 
</td>
<td style="text-align:right;">
117
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jaromir
</td>
<td style="text-align:left;">
Jagr
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Trent
</td>
<td style="text-align:left;">
Klatt
</td>
<td style="text-align:right;">
178
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Darryl
</td>
<td style="text-align:left;">
Sydor
</td>
<td style="text-align:right;">
714
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
10
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Niko
</td>
<td style="text-align:left;">
Kapanen
</td>
<td style="text-align:right;">
239
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Antoine
</td>
<td style="text-align:left;">
Roussel
</td>
<td style="text-align:right;">
413
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Tyler
</td>
<td style="text-align:left;">
Pitlick
</td>
<td style="text-align:right;">
127
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Valeri
</td>
<td style="text-align:left;">
Nichushkin
</td>
<td style="text-align:right;">
223
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Balon
</td>
<td style="text-align:right;">
73
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Henry
</td>
<td style="text-align:left;">
Boucha
</td>
<td style="text-align:right;">
51
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Don
</td>
<td style="text-align:left;">
Barber
</td>
<td style="text-align:right;">
74
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Craig
</td>
<td style="text-align:right;">
248
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kevin
</td>
<td style="text-align:left;">
Hatcher
</td>
<td style="text-align:right;">
121
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Nevin
</td>
<td style="text-align:right;">
138
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Danny
</td>
<td style="text-align:left;">
O’Shea
</td>
<td style="text-align:right;">
208
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tony
</td>
<td style="text-align:left;">
McKegney
</td>
<td style="text-align:right;">
108
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tommy
</td>
<td style="text-align:left;">
Williams
</td>
<td style="text-align:right;">
116
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mark
</td>
<td style="text-align:left;">
Tinordi
</td>
<td style="text-align:right;">
375
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pierre
</td>
<td style="text-align:left;">
Turgeon
</td>
<td style="text-align:right;">
207
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ron
</td>
<td style="text-align:left;">
Zanussi
</td>
<td style="text-align:right;">
244
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Stu
</td>
<td style="text-align:left;">
Barnes
</td>
<td style="text-align:right;">
329
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Antti
</td>
<td style="text-align:left;">
Miettinen
</td>
<td style="text-align:right;">
238
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bob
</td>
<td style="text-align:left;">
Barlow
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Gaetan
</td>
<td style="text-align:left;">
Duchesne
</td>
<td style="text-align:right;">
297
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Fred
</td>
<td style="text-align:left;">
Stanfield
</td>
<td style="text-align:right;">
111
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kent
</td>
<td style="text-align:left;">
Nilsson
</td>
<td style="text-align:right;">
105
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Trevor
</td>
<td style="text-align:left;">
Daley
</td>
<td style="text-align:right;">
756
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
11
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Eric
</td>
<td style="text-align:left;">
Nystrom
</td>
<td style="text-align:right;">
122
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Brett
</td>
<td style="text-align:left;">
Ritchie
</td>
<td style="text-align:right;">
241
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Craig
</td>
<td style="text-align:left;">
Hartsburg
</td>
<td style="text-align:right;">
570
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
10
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kent-Erik
</td>
<td style="text-align:left;">
Andersson
</td>
<td style="text-align:right;">
322
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Kris
</td>
<td style="text-align:left;">
Manery
</td>
<td style="text-align:right;">
88
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jussi
</td>
<td style="text-align:left;">
Jokinen
</td>
<td style="text-align:right;">
215
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Fabian
</td>
<td style="text-align:left;">
Brunnstrom
</td>
<td style="text-align:right;">
99
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ryan
</td>
<td style="text-align:left;">
Garbutt
</td>
<td style="text-align:right;">
198
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Radek
</td>
<td style="text-align:left;">
Faksa
</td>
<td style="text-align:right;">
406
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Jason
</td>
<td style="text-align:left;">
Robertson
</td>
<td style="text-align:right;">
54
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Andre
</td>
<td style="text-align:left;">
Boudrias
</td>
<td style="text-align:right;">
127
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Eaves
</td>
<td style="text-align:right;">
207
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Erik
</td>
<td style="text-align:left;">
Cole
</td>
<td style="text-align:right;">
160
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Benoit
</td>
<td style="text-align:left;">
Hogue
</td>
<td style="text-align:right;">
238
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Parker
</td>
<td style="text-align:left;">
MacDonald
</td>
<td style="text-align:right;">
104
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brad
</td>
<td style="text-align:left;">
Maxwell
</td>
<td style="text-align:right;">
472
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Reid
</td>
<td style="text-align:right;">
220
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Philippe
</td>
<td style="text-align:left;">
Boucher
</td>
<td style="text-align:right;">
346
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Cody
</td>
<td style="text-align:left;">
Eakin
</td>
<td style="text-align:right;">
349
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Mattias
</td>
<td style="text-align:left;">
Janmark
</td>
<td style="text-align:right;">
297
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Roope
</td>
<td style="text-align:left;">
Hintz
</td>
<td style="text-align:right;">
159
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Per-Olov
</td>
<td style="text-align:left;">
Brasar
</td>
<td style="text-align:right;">
167
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brent
</td>
<td style="text-align:left;">
Gilchrist
</td>
<td style="text-align:right;">
286
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
McPhee
</td>
<td style="text-align:right;">
163
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Alex
</td>
<td style="text-align:left;">
Pirus
</td>
<td style="text-align:right;">
155
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Patrick
</td>
<td style="text-align:left;">
Sharp
</td>
<td style="text-align:right;">
124
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Denis
</td>
<td style="text-align:left;">
Gurianov
</td>
<td style="text-align:right;">
141
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Buster
</td>
<td style="text-align:left;">
Harvey
</td>
<td style="text-align:right;">
199
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pierre
</td>
<td style="text-align:left;">
Jarry
</td>
<td style="text-align:right;">
115
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Lou
</td>
<td style="text-align:left;">
Nanne
</td>
<td style="text-align:right;">
635
</td>
<td style="text-align:left;">
D
</td>
<td style="text-align:right;">
11
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
Lawton
</td>
<td style="text-align:right;">
303
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dennis
</td>
<td style="text-align:left;">
Maruk
</td>
<td style="text-align:right;">
309
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Patrick
</td>
<td style="text-align:left;">
Eaves
</td>
<td style="text-align:right;">
160
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Greg
</td>
<td style="text-align:left;">
Adams
</td>
<td style="text-align:right;">
177
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Jensen
</td>
<td style="text-align:right;">
171
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brad
</td>
<td style="text-align:left;">
Palmer
</td>
<td style="text-align:right;">
95
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dean
</td>
<td style="text-align:left;">
Talafous
</td>
<td style="text-align:right;">
277
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Ott
</td>
<td style="text-align:right;">
566
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Fidler
</td>
<td style="text-align:right;">
103
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Marc
</td>
<td style="text-align:left;">
Habscheid
</td>
<td style="text-align:right;">
113
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Scott
</td>
<td style="text-align:left;">
Young
</td>
<td style="text-align:right;">
132
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jamie
</td>
<td style="text-align:left;">
Langenbrunner
</td>
<td style="text-align:right;">
471
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Roland
</td>
<td style="text-align:left;">
Eriksson
</td>
<td style="text-align:right;">
158
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Claude
</td>
<td style="text-align:left;">
Larose
</td>
<td style="text-align:right;">
142
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dirk
</td>
<td style="text-align:left;">
Graham
</td>
<td style="text-align:right;">
226
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Willi
</td>
<td style="text-align:left;">
Plett
</td>
<td style="text-align:right;">
317
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Glen
</td>
<td style="text-align:left;">
Sharpley
</td>
<td style="text-align:right;">
318
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Joe
</td>
<td style="text-align:left;">
Pavelski
</td>
<td style="text-align:right;">
123
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Keith
</td>
<td style="text-align:left;">
Acton
</td>
<td style="text-align:right;">
343
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Christoff
</td>
<td style="text-align:right;">
145
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dean
</td>
<td style="text-align:left;">
Prentice
</td>
<td style="text-align:right;">
168
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
Propp
</td>
<td style="text-align:right;">
147
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jude
</td>
<td style="text-align:left;">
Drouin
</td>
<td style="text-align:right;">
319
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Murray
</td>
<td style="text-align:left;">
Oliver
</td>
<td style="text-align:right;">
371
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
J.P.
</td>
<td style="text-align:left;">
Parise
</td>
<td style="text-align:right;">
588
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Ribeiro
</td>
<td style="text-align:right;">
461
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Niklas
</td>
<td style="text-align:left;">
Hagman
</td>
<td style="text-align:right;">
218
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
James
</td>
<td style="text-align:left;">
Neal
</td>
<td style="text-align:right;">
214
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ray
</td>
<td style="text-align:left;">
Cullen
</td>
<td style="text-align:right;">
208
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brad
</td>
<td style="text-align:left;">
Richards
</td>
<td style="text-align:right;">
220
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Collins
</td>
<td style="text-align:right;">
220
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Alexander
</td>
<td style="text-align:left;">
Radulov
</td>
<td style="text-align:right;">
223
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dennis
</td>
<td style="text-align:left;">
Hextall
</td>
<td style="text-align:right;">
328
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ernie
</td>
<td style="text-align:left;">
Hicke
</td>
<td style="text-align:right;">
199
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Pat
</td>
<td style="text-align:left;">
Verbeek
</td>
<td style="text-align:right;">
305
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tim
</td>
<td style="text-align:left;">
Young
</td>
<td style="text-align:right;">
565
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
8
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
MacLellan
</td>
<td style="text-align:right;">
211
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jason
</td>
<td style="text-align:left;">
Arnott
</td>
<td style="text-align:right;">
236
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Jere
</td>
<td style="text-align:left;">
Lehtinen
</td>
<td style="text-align:right;">
875
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
14
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brenden
</td>
<td style="text-align:left;">
Morrow
</td>
<td style="text-align:right;">
835
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
13
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Jason
</td>
<td style="text-align:left;">
Spezza
</td>
<td style="text-align:right;">
379
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Gartner
</td>
<td style="text-align:right;">
80
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Danny
</td>
<td style="text-align:left;">
Grant
</td>
<td style="text-align:right;">
463
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Guerin
</td>
<td style="text-align:right;">
216
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Wayne
</td>
<td style="text-align:left;">
Connelly
</td>
<td style="text-align:right;">
129
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Michael
</td>
<td style="text-align:left;">
Ryder
</td>
<td style="text-align:right;">
101
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Russ
</td>
<td style="text-align:left;">
Courtnall
</td>
<td style="text-align:right;">
200
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Ulf
</td>
<td style="text-align:left;">
Dahlen
</td>
<td style="text-align:right;">
369
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Loui
</td>
<td style="text-align:left;">
Eriksson
</td>
<td style="text-align:right;">
501
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Neal
</td>
<td style="text-align:left;">
Broten
</td>
<td style="text-align:right;">
992
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
16
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brett
</td>
<td style="text-align:left;">
Hull
</td>
<td style="text-align:right;">
218
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Tom
</td>
<td style="text-align:left;">
McCarthy
</td>
<td style="text-align:right;">
385
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Joe
</td>
<td style="text-align:left;">
Nieuwendyk
</td>
<td style="text-align:right;">
442
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dave
</td>
<td style="text-align:left;">
Gagner
</td>
<td style="text-align:right;">
609
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Tyler
</td>
<td style="text-align:left;">
Seguin
</td>
<td style="text-align:right;">
541
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
8
</td>
</tr>
<tr>
<td style="text-align:left;">
TRUE
</td>
<td style="text-align:left;">
Jamie
</td>
<td style="text-align:left;">
Benn
</td>
<td style="text-align:right;">
866
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
12
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Al
</td>
<td style="text-align:left;">
MacAdam
</td>
<td style="text-align:right;">
459
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Steve
</td>
<td style="text-align:left;">
Payne
</td>
<td style="text-align:right;">
613
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
10
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bobby
</td>
<td style="text-align:left;">
Smith
</td>
<td style="text-align:right;">
572
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Scott
</td>
<td style="text-align:left;">
Bjugstad
</td>
<td style="text-align:right;">
229
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Bill
</td>
<td style="text-align:left;">
Goldsworthy
</td>
<td style="text-align:right;">
670
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
10
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Mike
</td>
<td style="text-align:left;">
Modano
</td>
<td style="text-align:right;">
1459
</td>
<td style="text-align:left;">
C
</td>
<td style="text-align:right;">
20
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Brian
</td>
<td style="text-align:left;">
Bellows
</td>
<td style="text-align:right;">
753
</td>
<td style="text-align:left;">
L
</td>
<td style="text-align:right;">
10
</td>
</tr>
<tr>
<td style="text-align:left;">
FALSE
</td>
<td style="text-align:left;">
Dino
</td>
<td style="text-align:left;">
Ciccarelli
</td>
<td style="text-align:right;">
602
</td>
<td style="text-align:left;">
R
</td>
<td style="text-align:right;">
9
</td>
</tr>
</tbody>
</table>

### Home and away records for all teams during regular season:

The table below summarize the home and away records for all teams during
regular season. Only the first few rows are shown:

    team_totals <- get_franchise_team_totals()
    kable(head(team_totals %>% filter(gameTypeId==2) %>% select(teamName,starts_with("home"),starts_with("road"))))

<table>
<thead>
<tr>
<th style="text-align:left;">
teamName
</th>
<th style="text-align:right;">
homeLosses
</th>
<th style="text-align:right;">
homeOvertimeLosses
</th>
<th style="text-align:right;">
homeTies
</th>
<th style="text-align:right;">
homeWins
</th>
<th style="text-align:right;">
roadLosses
</th>
<th style="text-align:right;">
roadOvertimeLosses
</th>
<th style="text-align:right;">
roadTies
</th>
<th style="text-align:right;">
roadWins
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
New Jersey Devils
</td>
<td style="text-align:right;">
525
</td>
<td style="text-align:right;">
85
</td>
<td style="text-align:right;">
96
</td>
<td style="text-align:right;">
790
</td>
<td style="text-align:right;">
686
</td>
<td style="text-align:right;">
84
</td>
<td style="text-align:right;">
123
</td>
<td style="text-align:right;">
604
</td>
</tr>
<tr>
<td style="text-align:left;">
New York Islanders
</td>
<td style="text-align:right;">
678
</td>
<td style="text-align:right;">
84
</td>
<td style="text-align:right;">
170
</td>
<td style="text-align:right;">
963
</td>
<td style="text-align:right;">
909
</td>
<td style="text-align:right;">
82
</td>
<td style="text-align:right;">
177
</td>
<td style="text-align:right;">
725
</td>
</tr>
<tr>
<td style="text-align:left;">
New York Rangers
</td>
<td style="text-align:right;">
1143
</td>
<td style="text-align:right;">
76
</td>
<td style="text-align:right;">
448
</td>
<td style="text-align:right;">
1614
</td>
<td style="text-align:right;">
1573
</td>
<td style="text-align:right;">
77
</td>
<td style="text-align:right;">
360
</td>
<td style="text-align:right;">
1269
</td>
</tr>
<tr>
<td style="text-align:left;">
Philadelphia Flyers
</td>
<td style="text-align:right;">
584
</td>
<td style="text-align:right;">
93
</td>
<td style="text-align:right;">
193
</td>
<td style="text-align:right;">
1216
</td>
<td style="text-align:right;">
868
</td>
<td style="text-align:right;">
90
</td>
<td style="text-align:right;">
264
</td>
<td style="text-align:right;">
863
</td>
</tr>
<tr>
<td style="text-align:left;">
Pittsburgh Penguins
</td>
<td style="text-align:right;">
683
</td>
<td style="text-align:right;">
60
</td>
<td style="text-align:right;">
205
</td>
<td style="text-align:right;">
1138
</td>
<td style="text-align:right;">
1051
</td>
<td style="text-align:right;">
91
</td>
<td style="text-align:right;">
178
</td>
<td style="text-align:right;">
765
</td>
</tr>
<tr>
<td style="text-align:left;">
Boston Bruins
</td>
<td style="text-align:right;">
960
</td>
<td style="text-align:right;">
92
</td>
<td style="text-align:right;">
376
</td>
<td style="text-align:right;">
1885
</td>
<td style="text-align:right;">
1443
</td>
<td style="text-align:right;">
99
</td>
<td style="text-align:right;">
415
</td>
<td style="text-align:right;">
1356
</td>
</tr>
</tbody>
</table>

The following table shows home win percentage and road win percentage
for all teams during regular season. Only the first few rows are shown:

    regular_season_records <- team_totals %>% filter(gameTypeId==2) %>% select(teamName,starts_with("home"),starts_with("road"))
    regular_season_win_percent <-regular_season_records %>%  mutate(HomeWinPercent=(homeWins/(homeTies+homeLosses)),roadWinPercent=(roadWins/(roadTies+roadLosses))) %>% select(teamName,HomeWinPercent,roadWinPercent)
    kable(head(regular_season_win_percent, caption = "Home and away win percentages during regular season"),digits = 2)

<table>
<thead>
<tr>
<th style="text-align:left;">
teamName
</th>
<th style="text-align:right;">
HomeWinPercent
</th>
<th style="text-align:right;">
roadWinPercent
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
New Jersey Devils
</td>
<td style="text-align:right;">
1.27
</td>
<td style="text-align:right;">
0.75
</td>
</tr>
<tr>
<td style="text-align:left;">
New York Islanders
</td>
<td style="text-align:right;">
1.14
</td>
<td style="text-align:right;">
0.67
</td>
</tr>
<tr>
<td style="text-align:left;">
New York Rangers
</td>
<td style="text-align:right;">
1.01
</td>
<td style="text-align:right;">
0.66
</td>
</tr>
<tr>
<td style="text-align:left;">
Philadelphia Flyers
</td>
<td style="text-align:right;">
1.56
</td>
<td style="text-align:right;">
0.76
</td>
</tr>
<tr>
<td style="text-align:left;">
Pittsburgh Penguins
</td>
<td style="text-align:right;">
1.28
</td>
<td style="text-align:right;">
0.62
</td>
</tr>
<tr>
<td style="text-align:left;">
Boston Bruins
</td>
<td style="text-align:right;">
1.41
</td>
<td style="text-align:right;">
0.73
</td>
</tr>
</tbody>
</table>

## Contingency tables

### Summary of active and inactive players for the Dallas Stars:

The table below shows currently active and inactive players for the
Dallas Stars:

    skater_records$activePlayer <-  as.factor(skater_records$activePlayer)
    levels(skater_records$activePlayer) = c("Inactive", "Active")
    kable(table(skater_records$activePlayer, skater_records$positionCode),caption = "Summary of Dallas Stars Players by position and activity")

<table>
<caption>
Summary of Dallas Stars Players by position and activity
</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:right;">
C
</th>
<th style="text-align:right;">
D
</th>
<th style="text-align:right;">
L
</th>
<th style="text-align:right;">
R
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Inactive
</td>
<td style="text-align:right;">
137
</td>
<td style="text-align:right;">
183
</td>
<td style="text-align:right;">
125
</td>
<td style="text-align:right;">
115
</td>
</tr>
<tr>
<td style="text-align:left;">
Active
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
11
</td>
</tr>
</tbody>
</table>
