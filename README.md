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
        -   [Summary of active and inactive players for the Dallas
            Stars:](#summary-of-active-and-inactive-players-for-the-dallas-stars)

# Packages required to load and analyse the data

The following packages are needed for reading and analyzing the data:

    packages <- c("tidyverse","httr","jsonlite","rmarkdown", "knitr")
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

### Summary of active and inactive players for the Dallas Stars:

The table below shows currently active and inactive players for the
Dallas Stars:

    skater_records <- get_NHL_info("Franchise skater records",15)
    skater_records$activePlayer = as.factor(skater_records$activePlayer)
    levels(skater_records$activePlayer) = c("Inactive", "Active")
    kable(table(skater_records$activePlayer, skater_records$positionCode),caption = "Summary of Dallas Stars Players by position and activity")

<table>
<caption>Summary of Dallas Stars Players by position and activity</caption>
<thead>
<tr class="header">
<th style="text-align: left;"></th>
<th style="text-align: right;">C</th>
<th style="text-align: right;">D</th>
<th style="text-align: right;">L</th>
<th style="text-align: right;">R</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Inactive</td>
<td style="text-align: right;">137</td>
<td style="text-align: right;">183</td>
<td style="text-align: right;">125</td>
<td style="text-align: right;">115</td>
</tr>
<tr class="even">
<td style="text-align: left;">Active</td>
<td style="text-align: right;">16</td>
<td style="text-align: right;">23</td>
<td style="text-align: right;">9</td>
<td style="text-align: right;">11</td>
</tr>
</tbody>
</table>

Summary of Dallas Stars Players by position and activity

The table below summarize the home and away records during regular
season:

    team_totals <- get_franchise_team_totals()
    kable(team_totals %>% filter(gameTypeId==2) %>% select(teamName,starts_with("home"),starts_with("road")))

<table style="width:100%;">
<colgroup>
<col style="width: 20%" />
<col style="width: 9%" />
<col style="width: 15%" />
<col style="width: 7%" />
<col style="width: 7%" />
<col style="width: 9%" />
<col style="width: 15%" />
<col style="width: 7%" />
<col style="width: 7%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">teamName</th>
<th style="text-align: right;">homeLosses</th>
<th style="text-align: right;">homeOvertimeLosses</th>
<th style="text-align: right;">homeTies</th>
<th style="text-align: right;">homeWins</th>
<th style="text-align: right;">roadLosses</th>
<th style="text-align: right;">roadOvertimeLosses</th>
<th style="text-align: right;">roadTies</th>
<th style="text-align: right;">roadWins</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">New Jersey Devils</td>
<td style="text-align: right;">525</td>
<td style="text-align: right;">85</td>
<td style="text-align: right;">96</td>
<td style="text-align: right;">790</td>
<td style="text-align: right;">686</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">123</td>
<td style="text-align: right;">604</td>
</tr>
<tr class="even">
<td style="text-align: left;">New York Islanders</td>
<td style="text-align: right;">678</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">170</td>
<td style="text-align: right;">963</td>
<td style="text-align: right;">909</td>
<td style="text-align: right;">82</td>
<td style="text-align: right;">177</td>
<td style="text-align: right;">725</td>
</tr>
<tr class="odd">
<td style="text-align: left;">New York Rangers</td>
<td style="text-align: right;">1143</td>
<td style="text-align: right;">76</td>
<td style="text-align: right;">448</td>
<td style="text-align: right;">1614</td>
<td style="text-align: right;">1573</td>
<td style="text-align: right;">77</td>
<td style="text-align: right;">360</td>
<td style="text-align: right;">1269</td>
</tr>
<tr class="even">
<td style="text-align: left;">Philadelphia Flyers</td>
<td style="text-align: right;">584</td>
<td style="text-align: right;">93</td>
<td style="text-align: right;">193</td>
<td style="text-align: right;">1216</td>
<td style="text-align: right;">868</td>
<td style="text-align: right;">90</td>
<td style="text-align: right;">264</td>
<td style="text-align: right;">863</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Pittsburgh Penguins</td>
<td style="text-align: right;">683</td>
<td style="text-align: right;">60</td>
<td style="text-align: right;">205</td>
<td style="text-align: right;">1138</td>
<td style="text-align: right;">1051</td>
<td style="text-align: right;">91</td>
<td style="text-align: right;">178</td>
<td style="text-align: right;">765</td>
</tr>
<tr class="even">
<td style="text-align: left;">Boston Bruins</td>
<td style="text-align: right;">960</td>
<td style="text-align: right;">92</td>
<td style="text-align: right;">376</td>
<td style="text-align: right;">1885</td>
<td style="text-align: right;">1443</td>
<td style="text-align: right;">99</td>
<td style="text-align: right;">415</td>
<td style="text-align: right;">1356</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Buffalo Sabres</td>
<td style="text-align: right;">639</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">197</td>
<td style="text-align: right;">1053</td>
<td style="text-align: right;">925</td>
<td style="text-align: right;">83</td>
<td style="text-align: right;">212</td>
<td style="text-align: right;">752</td>
</tr>
<tr class="even">
<td style="text-align: left;">Montréal Canadiens</td>
<td style="text-align: right;">881</td>
<td style="text-align: right;">95</td>
<td style="text-align: right;">381</td>
<td style="text-align: right;">2038</td>
<td style="text-align: right;">1421</td>
<td style="text-align: right;">80</td>
<td style="text-align: right;">456</td>
<td style="text-align: right;">1435</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Ottawa Senators</td>
<td style="text-align: right;">413</td>
<td style="text-align: right;">93</td>
<td style="text-align: right;">60</td>
<td style="text-align: right;">533</td>
<td style="text-align: right;">527</td>
<td style="text-align: right;">76</td>
<td style="text-align: right;">55</td>
<td style="text-align: right;">438</td>
</tr>
<tr class="even">
<td style="text-align: left;">Toronto Maple Leafs</td>
<td style="text-align: right;">1082</td>
<td style="text-align: right;">85</td>
<td style="text-align: right;">388</td>
<td style="text-align: right;">1702</td>
<td style="text-align: right;">1614</td>
<td style="text-align: right;">89</td>
<td style="text-align: right;">385</td>
<td style="text-align: right;">1171</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Atlanta Thrashers</td>
<td style="text-align: right;">204</td>
<td style="text-align: right;">38</td>
<td style="text-align: right;">26</td>
<td style="text-align: right;">183</td>
<td style="text-align: right;">233</td>
<td style="text-align: right;">40</td>
<td style="text-align: right;">19</td>
<td style="text-align: right;">159</td>
</tr>
<tr class="even">
<td style="text-align: left;">Carolina Hurricanes</td>
<td style="text-align: right;">323</td>
<td style="text-align: right;">77</td>
<td style="text-align: right;">52</td>
<td style="text-align: right;">453</td>
<td style="text-align: right;">402</td>
<td style="text-align: right;">97</td>
<td style="text-align: right;">34</td>
<td style="text-align: right;">374</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Florida Panthers</td>
<td style="text-align: right;">390</td>
<td style="text-align: right;">115</td>
<td style="text-align: right;">65</td>
<td style="text-align: right;">485</td>
<td style="text-align: right;">480</td>
<td style="text-align: right;">93</td>
<td style="text-align: right;">77</td>
<td style="text-align: right;">404</td>
</tr>
<tr class="even">
<td style="text-align: left;">Tampa Bay Lightning</td>
<td style="text-align: right;">414</td>
<td style="text-align: right;">67</td>
<td style="text-align: right;">56</td>
<td style="text-align: right;">559</td>
<td style="text-align: right;">533</td>
<td style="text-align: right;">83</td>
<td style="text-align: right;">56</td>
<td style="text-align: right;">426</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Washington Capitals</td>
<td style="text-align: right;">620</td>
<td style="text-align: right;">83</td>
<td style="text-align: right;">153</td>
<td style="text-align: right;">959</td>
<td style="text-align: right;">847</td>
<td style="text-align: right;">80</td>
<td style="text-align: right;">150</td>
<td style="text-align: right;">741</td>
</tr>
<tr class="even">
<td style="text-align: left;">Chicago Blackhawks</td>
<td style="text-align: right;">1128</td>
<td style="text-align: right;">86</td>
<td style="text-align: right;">410</td>
<td style="text-align: right;">1655</td>
<td style="text-align: right;">1633</td>
<td style="text-align: right;">87</td>
<td style="text-align: right;">404</td>
<td style="text-align: right;">1157</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Detroit Red Wings</td>
<td style="text-align: right;">940</td>
<td style="text-align: right;">99</td>
<td style="text-align: right;">368</td>
<td style="text-align: right;">1741</td>
<td style="text-align: right;">1506</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">405</td>
<td style="text-align: right;">1150</td>
</tr>
<tr class="even">
<td style="text-align: left;">Nashville Predators</td>
<td style="text-align: right;">282</td>
<td style="text-align: right;">73</td>
<td style="text-align: right;">34</td>
<td style="text-align: right;">477</td>
<td style="text-align: right;">374</td>
<td style="text-align: right;">90</td>
<td style="text-align: right;">26</td>
<td style="text-align: right;">375</td>
</tr>
<tr class="odd">
<td style="text-align: left;">St. Louis Blues</td>
<td style="text-align: right;">674</td>
<td style="text-align: right;">72</td>
<td style="text-align: right;">218</td>
<td style="text-align: right;">1122</td>
<td style="text-align: right;">971</td>
<td style="text-align: right;">95</td>
<td style="text-align: right;">214</td>
<td style="text-align: right;">807</td>
</tr>
<tr class="even">
<td style="text-align: left;">Calgary Flames</td>
<td style="text-align: right;">508</td>
<td style="text-align: right;">69</td>
<td style="text-align: right;">135</td>
<td style="text-align: right;">863</td>
<td style="text-align: right;">728</td>
<td style="text-align: right;">81</td>
<td style="text-align: right;">136</td>
<td style="text-align: right;">634</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Colorado Avalanche</td>
<td style="text-align: right;">327</td>
<td style="text-align: right;">62</td>
<td style="text-align: right;">55</td>
<td style="text-align: right;">543</td>
<td style="text-align: right;">401</td>
<td style="text-align: right;">80</td>
<td style="text-align: right;">46</td>
<td style="text-align: right;">464</td>
</tr>
<tr class="even">
<td style="text-align: left;">Edmonton Oilers</td>
<td style="text-align: right;">587</td>
<td style="text-align: right;">74</td>
<td style="text-align: right;">125</td>
<td style="text-align: right;">830</td>
<td style="text-align: right;">750</td>
<td style="text-align: right;">93</td>
<td style="text-align: right;">137</td>
<td style="text-align: right;">639</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Vancouver Canucks</td>
<td style="text-align: right;">736</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">210</td>
<td style="text-align: right;">943</td>
<td style="text-align: right;">1010</td>
<td style="text-align: right;">75</td>
<td style="text-align: right;">181</td>
<td style="text-align: right;">706</td>
</tr>
<tr class="even">
<td style="text-align: left;">Anaheim Ducks</td>
<td style="text-align: right;">359</td>
<td style="text-align: right;">82</td>
<td style="text-align: right;">58</td>
<td style="text-align: right;">557</td>
<td style="text-align: right;">475</td>
<td style="text-align: right;">98</td>
<td style="text-align: right;">49</td>
<td style="text-align: right;">433</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Dallas Stars</td>
<td style="text-align: right;">314</td>
<td style="text-align: right;">81</td>
<td style="text-align: right;">65</td>
<td style="text-align: right;">594</td>
<td style="text-align: right;">424</td>
<td style="text-align: right;">81</td>
<td style="text-align: right;">60</td>
<td style="text-align: right;">490</td>
</tr>
<tr class="even">
<td style="text-align: left;">Los Angeles Kings</td>
<td style="text-align: right;">776</td>
<td style="text-align: right;">71</td>
<td style="text-align: right;">211</td>
<td style="text-align: right;">1027</td>
<td style="text-align: right;">1053</td>
<td style="text-align: right;">94</td>
<td style="text-align: right;">213</td>
<td style="text-align: right;">727</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Phoenix Coyotes</td>
<td style="text-align: right;">249</td>
<td style="text-align: right;">48</td>
<td style="text-align: right;">43</td>
<td style="text-align: right;">340</td>
<td style="text-align: right;">297</td>
<td style="text-align: right;">57</td>
<td style="text-align: right;">51</td>
<td style="text-align: right;">275</td>
</tr>
<tr class="even">
<td style="text-align: left;">San Jose Sharks</td>
<td style="text-align: right;">407</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">58</td>
<td style="text-align: right;">589</td>
<td style="text-align: right;">513</td>
<td style="text-align: right;">79</td>
<td style="text-align: right;">63</td>
<td style="text-align: right;">481</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Columbus Blue Jackets</td>
<td style="text-align: right;">300</td>
<td style="text-align: right;">77</td>
<td style="text-align: right;">18</td>
<td style="text-align: right;">390</td>
<td style="text-align: right;">398</td>
<td style="text-align: right;">82</td>
<td style="text-align: right;">15</td>
<td style="text-align: right;">288</td>
</tr>
<tr class="even">
<td style="text-align: left;">Minnesota Wild</td>
<td style="text-align: right;">243</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">28</td>
<td style="text-align: right;">429</td>
<td style="text-align: right;">356</td>
<td style="text-align: right;">70</td>
<td style="text-align: right;">27</td>
<td style="text-align: right;">330</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Minnesota North Stars</td>
<td style="text-align: right;">391</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">163</td>
<td style="text-align: right;">477</td>
<td style="text-align: right;">579</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">171</td>
<td style="text-align: right;">281</td>
</tr>
<tr class="even">
<td style="text-align: left;">Quebec Nordiques</td>
<td style="text-align: right;">245</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">83</td>
<td style="text-align: right;">300</td>
<td style="text-align: right;">354</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">77</td>
<td style="text-align: right;">197</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Winnipeg Jets (1979)</td>
<td style="text-align: right;">274</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">88</td>
<td style="text-align: right;">307</td>
<td style="text-align: right;">386</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">199</td>
</tr>
<tr class="even">
<td style="text-align: left;">Hartford Whalers</td>
<td style="text-align: right;">297</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">95</td>
<td style="text-align: right;">318</td>
<td style="text-align: right;">412</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">82</td>
<td style="text-align: right;">216</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Colorado Rockies</td>
<td style="text-align: right;">115</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">47</td>
<td style="text-align: right;">78</td>
<td style="text-align: right;">166</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">39</td>
<td style="text-align: right;">35</td>
</tr>
<tr class="even">
<td style="text-align: left;">Ottawa Senators (1917)</td>
<td style="text-align: right;">81</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">30</td>
<td style="text-align: right;">160</td>
<td style="text-align: right;">140</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">33</td>
<td style="text-align: right;">98</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Hamilton Tigers</td>
<td style="text-align: right;">30</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">33</td>
<td style="text-align: right;">48</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">14</td>
</tr>
<tr class="even">
<td style="text-align: left;">Pittsburgh Pirates</td>
<td style="text-align: right;">55</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">10</td>
<td style="text-align: right;">41</td>
<td style="text-align: right;">67</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">13</td>
<td style="text-align: right;">26</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Philadelphia Quakers</td>
<td style="text-align: right;">17</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">2</td>
<td style="text-align: right;">3</td>
<td style="text-align: right;">19</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">2</td>
<td style="text-align: right;">1</td>
</tr>
<tr class="even">
<td style="text-align: left;">Detroit Cougars</td>
<td style="text-align: right;">42</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">11</td>
<td style="text-align: right;">35</td>
<td style="text-align: right;">45</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">14</td>
<td style="text-align: right;">29</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Montreal Wanderers</td>
<td style="text-align: right;">2</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">3</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">0</td>
</tr>
<tr class="even">
<td style="text-align: left;">Quebec Bulldogs</td>
<td style="text-align: right;">8</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">4</td>
<td style="text-align: right;">12</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">0</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Montreal Maroons</td>
<td style="text-align: right;">107</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">48</td>
<td style="text-align: right;">156</td>
<td style="text-align: right;">153</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">43</td>
<td style="text-align: right;">115</td>
</tr>
<tr class="even">
<td style="text-align: left;">New York Americans</td>
<td style="text-align: right;">154</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">67</td>
<td style="text-align: right;">147</td>
<td style="text-align: right;">219</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">57</td>
<td style="text-align: right;">92</td>
</tr>
<tr class="odd">
<td style="text-align: left;">St. Louis Eagles</td>
<td style="text-align: right;">14</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">3</td>
<td style="text-align: right;">7</td>
<td style="text-align: right;">17</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">3</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">Oakland Seals</td>
<td style="text-align: right;">46</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">23</td>
<td style="text-align: right;">44</td>
<td style="text-align: right;">72</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">19</td>
<td style="text-align: right;">22</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Atlanta Flames</td>
<td style="text-align: right;">104</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">53</td>
<td style="text-align: right;">161</td>
<td style="text-align: right;">156</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">55</td>
<td style="text-align: right;">107</td>
</tr>
<tr class="even">
<td style="text-align: left;">Kansas City Scouts</td>
<td style="text-align: right;">44</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">16</td>
<td style="text-align: right;">20</td>
<td style="text-align: right;">66</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">7</td>
<td style="text-align: right;">7</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Cleveland Barons</td>
<td style="text-align: right;">34</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">18</td>
<td style="text-align: right;">28</td>
<td style="text-align: right;">53</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">8</td>
<td style="text-align: right;">19</td>
</tr>
<tr class="even">
<td style="text-align: left;">Detroit Falcons</td>
<td style="text-align: right;">10</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">11</td>
<td style="text-align: right;">25</td>
<td style="text-align: right;">31</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">6</td>
<td style="text-align: right;">9</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Brooklyn Americans</td>
<td style="text-align: right;">12</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">2</td>
<td style="text-align: right;">10</td>
<td style="text-align: right;">17</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">6</td>
</tr>
<tr class="even">
<td style="text-align: left;">Winnipeg Jets</td>
<td style="text-align: right;">136</td>
<td style="text-align: right;">33</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">207</td>
<td style="text-align: right;">156</td>
<td style="text-align: right;">42</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">175</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Arizona Coyotes</td>
<td style="text-align: right;">120</td>
<td style="text-align: right;">30</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">116</td>
<td style="text-align: right;">142</td>
<td style="text-align: right;">30</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">98</td>
</tr>
<tr class="even">
<td style="text-align: left;">Vegas Golden Knights</td>
<td style="text-align: right;">38</td>
<td style="text-align: right;">13</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">96</td>
<td style="text-align: right;">56</td>
<td style="text-align: right;">11</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">77</td>
</tr>
<tr class="odd">
<td style="text-align: left;">California Golden Seals</td>
<td style="text-align: right;">100</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">52</td>
<td style="text-align: right;">84</td>
<td style="text-align: right;">183</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">21</td>
<td style="text-align: right;">32</td>
</tr>
<tr class="even">
<td style="text-align: left;">Toronto Arenas</td>
<td style="text-align: right;">5</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">15</td>
<td style="text-align: right;">17</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">3</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Toronto St. Patricks</td>
<td style="text-align: right;">37</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">5</td>
<td style="text-align: right;">73</td>
<td style="text-align: right;">74</td>
<td style="text-align: right;">NA</td>
<td style="text-align: right;">5</td>
<td style="text-align: right;">36</td>
</tr>
</tbody>
</table>
