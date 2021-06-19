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

    packages <- c("tidyverse","httr","jsonlite","rmarkdown", "knitr","DT")
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

The table below summarize the home and away records for all teams during
regular season:

    team_totals <- get_franchise_team_totals()
    datatable(team_totals %>% filter(gameTypeId==2) %>% select(teamName,starts_with("home"),starts_with("road")))

<div id="htmlwidget-89e737f3859102a73850" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-89e737f3859102a73850">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57"],["New Jersey Devils","New York Islanders","New York Rangers","Philadelphia Flyers","Pittsburgh Penguins","Boston Bruins","Buffalo Sabres","Montréal Canadiens","Ottawa Senators","Toronto Maple Leafs","Atlanta Thrashers","Carolina Hurricanes","Florida Panthers","Tampa Bay Lightning","Washington Capitals","Chicago Blackhawks","Detroit Red Wings","Nashville Predators","St. Louis Blues","Calgary Flames","Colorado Avalanche","Edmonton Oilers","Vancouver Canucks","Anaheim Ducks","Dallas Stars","Los Angeles Kings","Phoenix Coyotes","San Jose Sharks","Columbus Blue Jackets","Minnesota Wild","Minnesota North Stars","Quebec Nordiques","Winnipeg Jets (1979)","Hartford Whalers","Colorado Rockies","Ottawa Senators (1917)","Hamilton Tigers","Pittsburgh Pirates","Philadelphia Quakers","Detroit Cougars","Montreal Wanderers","Quebec Bulldogs","Montreal Maroons","New York Americans","St. Louis Eagles","Oakland Seals","Atlanta Flames","Kansas City Scouts","Cleveland Barons","Detroit Falcons","Brooklyn Americans","Winnipeg Jets","Arizona Coyotes","Vegas Golden Knights","California Golden Seals","Toronto Arenas","Toronto St. Patricks"],[525,678,1143,584,683,960,639,881,413,1082,204,323,390,414,620,1128,940,282,674,508,327,587,736,359,314,776,249,407,300,243,391,245,274,297,115,81,30,55,17,42,2,8,107,154,14,46,104,44,34,10,12,136,120,38,100,5,37],[85,84,76,93,60,92,84,95,93,85,38,77,115,67,83,86,99,73,72,69,62,74,84,82,81,71,48,84,77,84,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,33,30,13,null,null,null],[96,170,448,193,205,376,197,381,60,388,26,52,65,56,153,410,368,34,218,135,55,125,210,58,65,211,43,58,18,28,163,83,88,95,47,30,0,10,2,11,0,0,48,67,3,23,53,16,18,11,2,null,null,null,52,0,5],[790,963,1614,1216,1138,1885,1053,2038,533,1702,183,453,485,559,959,1655,1741,477,1122,863,543,830,943,557,594,1027,340,589,390,429,477,300,307,318,78,160,33,41,3,35,1,4,156,147,7,44,161,20,28,25,10,207,116,96,84,15,73],[686,909,1573,868,1051,1443,925,1421,527,1614,233,402,480,533,847,1633,1506,374,971,728,401,750,1010,475,424,1053,297,513,398,356,579,354,386,412,166,140,48,67,19,45,3,12,153,219,17,72,156,66,53,31,17,156,142,56,183,17,74],[84,82,77,90,91,99,83,80,76,89,40,97,93,83,80,87,84,90,95,81,80,93,75,98,81,94,57,79,82,70,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,42,30,11,null,null,null],[123,177,360,264,178,415,212,456,55,385,19,34,77,56,150,404,405,26,214,136,46,137,181,49,60,213,51,63,15,27,171,77,84,82,39,33,1,13,2,14,0,0,43,57,3,19,55,7,8,6,1,null,null,null,21,0,5],[604,725,1269,863,765,1356,752,1435,438,1171,159,374,404,426,741,1157,1150,375,807,634,464,639,706,433,490,727,275,481,288,330,281,197,199,216,35,98,14,26,1,29,0,0,115,92,4,22,107,7,19,9,6,175,98,77,32,3,36]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>teamName<\/th>\n      <th>homeLosses<\/th>\n      <th>homeOvertimeLosses<\/th>\n      <th>homeTies<\/th>\n      <th>homeWins<\/th>\n      <th>roadLosses<\/th>\n      <th>roadOvertimeLosses<\/th>\n      <th>roadTies<\/th>\n      <th>roadWins<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[2,3,4,5,6,7,8,9]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

The following table shows home win percentage and road win percentage
for all teams during regular season:

    regular_season_records <- team_totals %>% filter(gameTypeId==2) %>% select(teamName,starts_with("home"),starts_with("road"))
    regular_season_win_percent <-regular_season_records %>%  mutate(HomeWinPercent=(homeWins/(homeTies+homeLosses)),roadWinPercent=(roadWins/(roadTies+roadLosses))) %>% select(teamName,HomeWinPercent,roadWinPercent)
    datatable(regular_season_win_percent, caption = "Home and away win percentages during regular season") %>% formatRound(c("HomeWinPercent","roadWinPercent"),digits=2)

<div id="htmlwidget-fd5b10818cd81c67c436" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-fd5b10818cd81c67c436">{"x":{"filter":"none","caption":"<caption>Home and away win percentages during regular season<\/caption>","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57"],["New Jersey Devils","New York Islanders","New York Rangers","Philadelphia Flyers","Pittsburgh Penguins","Boston Bruins","Buffalo Sabres","Montréal Canadiens","Ottawa Senators","Toronto Maple Leafs","Atlanta Thrashers","Carolina Hurricanes","Florida Panthers","Tampa Bay Lightning","Washington Capitals","Chicago Blackhawks","Detroit Red Wings","Nashville Predators","St. Louis Blues","Calgary Flames","Colorado Avalanche","Edmonton Oilers","Vancouver Canucks","Anaheim Ducks","Dallas Stars","Los Angeles Kings","Phoenix Coyotes","San Jose Sharks","Columbus Blue Jackets","Minnesota Wild","Minnesota North Stars","Quebec Nordiques","Winnipeg Jets (1979)","Hartford Whalers","Colorado Rockies","Ottawa Senators (1917)","Hamilton Tigers","Pittsburgh Pirates","Philadelphia Quakers","Detroit Cougars","Montreal Wanderers","Quebec Bulldogs","Montreal Maroons","New York Americans","St. Louis Eagles","Oakland Seals","Atlanta Flames","Kansas City Scouts","Cleveland Barons","Detroit Falcons","Brooklyn Americans","Winnipeg Jets","Arizona Coyotes","Vegas Golden Knights","California Golden Seals","Toronto Arenas","Toronto St. Patricks"],[1.27214170692432,1.13561320754717,1.0144563167819,1.56499356499356,1.28153153153153,1.41092814371257,1.25956937799043,1.6148969889065,1.12684989429175,1.1578231292517,0.795652173913043,1.208,1.06593406593407,1.18936170212766,1.24062095730919,1.07607282184655,1.33103975535168,1.50949367088608,1.25784753363229,1.34214618973561,1.42146596858639,1.16573033707865,0.996828752642706,1.33573141486811,1.56728232189974,1.04052684903749,1.16438356164384,1.26666666666667,1.22641509433962,1.5830258302583,0.86101083032491,0.914634146341463,0.848066298342541,0.811224489795918,0.481481481481481,1.44144144144144,1.1,0.630769230769231,0.157894736842105,0.660377358490566,0.5,0.5,1.00645161290323,0.665158371040724,0.411764705882353,0.63768115942029,1.02547770700637,0.333333333333333,0.538461538461538,1.19047619047619,0.714285714285714,null,null,null,0.552631578947368,3,1.73809523809524],[0.746600741656366,0.667587476979742,0.656492498706674,0.762367491166078,0.622457282343369,0.729817007534984,0.661389621811785,0.764517847629196,0.752577319587629,0.585792896448224,0.630952380952381,0.857798165137615,0.725314183123878,0.723259762308998,0.743229689067202,0.567992145311733,0.601779173207745,0.9375,0.681012658227848,0.733796296296296,1.03803131991051,0.720405862457723,0.592779177162049,0.826335877862595,1.01239669421488,0.574249605055292,0.790229885057471,0.835069444444444,0.697336561743341,0.861618798955614,0.374666666666667,0.45707656612529,0.423404255319149,0.437246963562753,0.170731707317073,0.566473988439306,0.285714285714286,0.325,0.0476190476190476,0.491525423728814,0,0,0.586734693877551,0.333333333333333,0.2,0.241758241758242,0.507109004739337,0.0958904109589041,0.311475409836066,0.243243243243243,0.333333333333333,null,null,null,0.156862745098039,0.176470588235294,0.455696202531646]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>teamName<\/th>\n      <th>HomeWinPercent<\/th>\n      <th>roadWinPercent<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"targets":2,"render":"function(data, type, row, meta) {\n    return type !== 'display' ? data : DTWidget.formatRound(data, 2, 3, \",\", \".\");\n  }"},{"targets":3,"render":"function(data, type, row, meta) {\n    return type !== 'display' ? data : DTWidget.formatRound(data, 2, 3, \",\", \".\");\n  }"},{"className":"dt-right","targets":[2,3]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":["options.columnDefs.0.render","options.columnDefs.1.render"],"jsHooks":[]}</script>
