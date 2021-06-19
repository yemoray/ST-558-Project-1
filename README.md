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

### Summary of active and inactive players for the Dallas Stars:

The table below shows currently active and inactive players for the
Dallas Stars:

    skater_records <- get_NHL_info("Franchise skater records",15)
    skater_records$activePlayer = as.factor(skater_records$activePlayer)
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
    kable(head(regular_season_win_percent, caption = "Home and away win percentages during regular season",digits = 2))

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
1.272142
</td>
<td style="text-align:right;">
0.7466007
</td>
</tr>
<tr>
<td style="text-align:left;">
New York Islanders
</td>
<td style="text-align:right;">
1.135613
</td>
<td style="text-align:right;">
0.6675875
</td>
</tr>
<tr>
<td style="text-align:left;">
New York Rangers
</td>
<td style="text-align:right;">
1.014456
</td>
<td style="text-align:right;">
0.6564925
</td>
</tr>
<tr>
<td style="text-align:left;">
Philadelphia Flyers
</td>
<td style="text-align:right;">
1.564994
</td>
<td style="text-align:right;">
0.7623675
</td>
</tr>
<tr>
<td style="text-align:left;">
Pittsburgh Penguins
</td>
<td style="text-align:right;">
1.281532
</td>
<td style="text-align:right;">
0.6224573
</td>
</tr>
<tr>
<td style="text-align:left;">
Boston Bruins
</td>
<td style="text-align:right;">
1.410928
</td>
<td style="text-align:right;">
0.7298170
</td>
</tr>
</tbody>
</table>
