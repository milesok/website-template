---
layout: post
published: true
title: 'College Baseball Run Environments, Pt. 1'
---
### The Idea

In comparing players from different years of Major League Baseball, or across different minor leagues (for instance the Pacific Coast vs International League), it is important to consider how offensive production differs. This same principle carries over to college baseball, where teams are grouped into conferences with  different levels of competition, regional geographic and weather factors, and styles of baseball. This is the first part of a series investigating the differences in run-scoring environments across college baseball where I compared the average run scoring per game in each conference.


### The Process
First, I loaded the tidyverse package and loaded in [all of the data](https://github.com/milesok/milesok.github.io/tree/master/files/run_env_files) I'd need.
```r
require(tidyverse)
headers <- read.csv('data/scores/headers.csv')
scores2019 <- read_csv('data/scores/scores2019.csv', col_names =FALSE)
names(scores2019) = headers[, "Header"]
scores2018 <- read_csv('data/scores/scores2018.csv', col_names =FALSE)
names(scores2018) <- c("away", "away_abb", "away_seed", "away_score", "home", "home_abb", "home_seed", "home_score", "location", "innings", "date")
scores2017 <- read_csv('data/scores/scores2017.csv', col_names =FALSE)
names(scores2017) = headers[, "Header"]
scores2016 <- read_csv('data/scores/scores2016.csv', col_names =FALSE)
names(scores2016) = headers[, "Header"]
scores2015 <- read_csv('data/scores/scores2015.csv', col_names =FALSE)
names(scores2015) = headers[, "Header"]
scores2014 <- read_csv('data/scores/scores2014.csv', col_names =FALSE)
names(scores2014) = headers[, "Header"]
scores2013 <- read_csv('data/scores/scores2013.csv', col_names =FALSE)
names(scores2013) = headers[, "Header"]
scores2012 <- read_csv('data/scores/scores2012.csv', col_names =FALSE)
names(scores2012) = headers[, "Header"]
scores <- bind_rows(scores2012, scores2013, scores2014, scores2015, scores2016, scores2017, scores2018, scores2019)
teams <- read_csv('data/ncaa_teams.csv')
```
As a start, I calculated the average runs scored per team, per game across all of Division 1 for 2019 to compare that to the MLB average.
```r
mean((scores2019$home_score + scores2019$away_score)/2)
```
##### Run Environment  

| MLB (Thru 7/24) | NCAA D1 2019 |
|-----------------|--------------|
| 4.83            | 5.84         |  

NCAA games saw an average of one more run per team, per game than MLB games so far this year. I made histograms to compare how those runs were distributed:
![ncaahist](https://github.com/milesok/milesok.github.io/blob/master/files/run_env_img/ncaa_run_dist.PNG?raw=true)


I added columns to the combined scores dataframe for each team's conference.
```r
teams %>%
  filter(division == 1) -> d1teams
scores %>%
  mutate(year = strtoi(substr(date,0,4))) -> scores
conf <- cbind(d1teams["school"], d1teams["conference"], d1teams["year"])
names(conf) <- c("home", "home_conf", "year")
scores %>%
  left_join(conf, by = c("year", "home")) -> scores
names(conf) <- c("away", "away_conf", "year")
scores %>%
  left_join(conf, by = c("year", "away")) -> scores
```

Next I filtered the games by only conference games played in non-neutral locations. This provided a dataset of all the regular season conference matchups.  
```r
scores %>%
  filter(home_conf == away_conf) %>%
  filter(is.na(location)) -> scores_conf

scores_conf %>%
  group_by(home_conf) %>%
  summarise(rpg = round(mean((home_score + away_score)/2),3)) -> runenvconf
```
This resulted in a nice table showing the runs per team per game for each conference for the 2019 season. For brevity, I've included a table here of the Power 5 conferences' run environments:

| Conference | Runs Per Game |
|------------|---------------|
| ACC        | 5.96          |
| Big 12     | 5.57          |
| Big Ten    | 5.04          |
| Pac-12     | 6.04          |
| SEC        | 5.38          |

Next, I looked at the data across multiple seasons. I grouped by "era," with the 2012-2014 group representing before the balls were altered and 2015-2019 the time after the seams were lowered on the ball.
```r
scores %>%
  mutate(era = ifelse(year < 2015, "2012-2014", "2015-2019")) -> scores

scores %>%
  filter(home_conf == away_conf) %>%
  filter(is.na(location)) -> scores_conf

scores_conf %>%
  filter(year > 2014) %>%
  filter(home_conf != "Independent") %>%
  group_by(home_conf) %>%
  summarise(rpg = round(mean((home_score + away_score)/2),3)) -> runenvconf

scores_conf %>%
  group_by(home_conf, era) %>%
  summarise(rpg = round(mean((home_score + away_score)/2),3)) %>%
  filter(home_conf %in% c("Big 12", "Pac-12", "Big Ten", "SEC", "ACC")) -> p5runenv
```  
| home_conf | era       | rpg   |
|-----------|-----------|-------|
| ACC       | 2012-2014 | 4.809 |
| ACC       | 2015-2019 | 5.500 |
| Big 12    | 2012-2014 | 4.554 |
| Big 12    | 2015-2019 | 5.310 |
| Big Ten   | 2012-2014 | 4.841 |
| Big Ten   | 2015-2019 | 5.152 |
| Pac-12    | 2012-2014 | 4.643 |
| Pac-12    | 2015-2019 | 5.258 |
| SEC       | 2012-2014 | 4.518 |
| SEC       | 2015-2019 | 5.172 |

As expected and as intended by the NCAA, run scoring increased by about half a run per game following the changes to the baseball after the 2014 season. I also noticed that the average run scoring for the past 5 seasons was generally lower than this past season, so I grouped the 2015-2019 data by season to see if there was any noticeable trend in offense. Here is a graph showing the change in run environment from 2015 to 2019 for each conference:
![runenvdiff](https://github.com/milesok/milesok.github.io/blob/master/files/run_env_img/run_env_chg_15-19.PNG?raw=true)  
This graph suggests that teams are generally scoring more runs now than 5 seasons ago. This uptick could be attributed to a number of different reasons, from weather factors to advances in hitting (i.e. the "launch angle revolution"), to any number of other things I haven't considered.


### The Next Steps

* I plan to examine the underlying factors that contribute to the run environment to look at what trends may be currently affecting run environments from year to year.
* It will also be interesting to see if I can find enough data to construct individualized run environments based on pitchers and hitters for further modeling


Data for this project can be found [here](https://github.com/milesok/milesok.github.io/tree/master/files/run_env_files)
