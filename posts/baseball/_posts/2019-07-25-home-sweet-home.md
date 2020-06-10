---
layout: post
published: true
title: Home Sweet Home
subtitle: Examining Home Field Advantage in NCAA D1 Baseball
date: '2019-07-25'
tags:
  - baseball
  - ncaa
---
### The Idea

One of the most important factors used to select the at-large bids for the NCAA D1 Baseball Tournament is the Rating Percentage Index (RPI). In [their description of the RPI formula](https://web.archive.org/web/20180629074159/https://www.ncaa.com/news/baseball/2011-08-03/rpi-formula-altering-2013-season) the NCAA explains how RPI values road victories almost twice as much as home wins.

> The revised RPI formula will value each road victory as 1.3 instead of 1.0. Each home win will be valued at 0.7 instead of 1.0. Conversely, each home loss will count 1.3 against a team’s RPI and each road loss will count 0.7 against a team’s RPI. Neutral-site games will retain the same value of 1.0, but the committee is studying how to determine if a game should be considered a neutral-site contest. The weighting is based on data showing that home teams win about 62 percent of the time in Division I baseball.

### The Code
First, I looked at the data from the 2019 season. Using R, I imported the tidyverse package and read in data files.
```r
require(tidyverse)
headers <- read.csv('../data/scores/headers.csv')
scores2019 <- read_csv('../data/scores/scores2019.csv', col_names =FALSE)
names(scores2019) = headers[, "Header"]
```

I created a column representing whether the home team won the game, with a 1 representing a home win, a 0 representing a home loss, and .5 representing a tie. This made calculating winning percentage as simple as taking the mean of this column.
```r
scores2019 %>%
  mutate(win = ifelse(home_score > away_score, 1, ifelse(home_score == away_score, .5, 0))) -> scores19
mean(scores19$win)
```

The home winning percentage across all of Division 1 baseball for the 2019 season was **0.594**. However, this number includes postseason games where higher seeded teams were the home team as well as neutral site games, where we would expect the home field advantage to be less significant.

```r
scores19 %>%
  filter(is.na(location)) -> scores2019home
mean(scores2019home$win)
```
The home team winning percentage for 2019 for all games at home stadiums was **0.602**.

We can compare this to all neutral site games:

```r
scores19 %>%
  filter(!is.na(location)) -> scores2019neutral
mean(scores2019neutral$win)
```
The home team won neutral site games at a **0.521** clip for 2019. This number still might show the effects of some neutral sites being much closer to some teams than others, as well as postseason seeding's effect. However, the drop off between home team winning percentage from true home games to neutral site games suggests that there is a noticeable advantage to a team playing in its home ballpark.  
  
To try to control better for quality of competition, I filtered the games to only include conference competition. These are the games that generally carry the most weight on a team's schedule, and therefore represent the team's "best foot forward."
```r
teams <- read_csv('../data/master_ncaa_teams.csv')
teams %>%
  filter(year == 2019, division == 1) -> teams2019

conf19 <- cbind(teams2019["school"], teams2019["conference"])
names(conf19) <- c("home", "home_conf")
scores19 %>%
  left_join(conf19) -> scores19
names(conf19) <- c("away", "away_conf")
scores19 %>%
  left_join(conf19) -> scores19

scores19 %>%
  filter(home_conf == away_conf) %>%
  filter(is.na(location)) -> conf2019

conf2019 %>%
  group_by(home_conf) %>%
  summarise(win.pct = round(mean(win),3)) -> hfa2019
hfa2019
```
This outputted a table of the home team winning percentage for each conference. For brevity, I've included the Power 5 conferences here:  

| Conference | Home Win % |
|------------|-------|
| ACC        | .524  |
| Big 12     | .590  |
| Big Ten    | .568  |
| Pac 12     | .552  |
| SEC        | .579  |  

Next, I expanded my dataset to include the last 4 seasons. This was to avoid any changes caused by the 2014 change in baseball specs and any bias from the way conference schedules are constructed where teams alternate hosting from year-to-year.
```r
scores2018 <- read_csv('../data/scores/scores2018.csv', col_names =FALSE)
names(scores2018) = headers[, "Header"]
scores2017 <- read_csv('../data/scores/scores2017.csv', col_names =FALSE)
names(scores2017) = headers[, "Header"]
scores2016 <- read_csv('../data/scores/scores2016.csv', col_names =FALSE)
names(scores2016) = headers[, "Header"]
```

When running similar code on the other seasons, the 2018 dataset had a home field winning percentage in the 40s. Turns out, the [NCAA stats page](stats.ncaa.org) (which is where all this data is coming from) flipped the home and away teams on the scoreboards for the 2018 season. I adjusted the headers accordingly and combined the datasets:
```r
names(scores2018) <- c("away", "away_abb", "away_score", "home", "home_abb", "home_score", "location", "innings", "date")
scores <- bind_rows(scores2019, scores2018, scores2017, scores2016)
```

I ran similar code on the full season data to get home winning percentages by conference:
```r
scores %>%
  mutate(win = ifelse(home_score > away_score, 1, 0)) -> scores
scores %>%
  filter(is.na(location)) -> scores_home
teams <- read_csv('../data/master_ncaa_teams.csv')
teams %>%
  filter(division == 1) -> d1teams
scores_home %>%
  mutate(year = strtoi(substr(date,0,4))) -> scores_home

conf <- cbind(d1teams["school"], d1teams["conference"], d1teams["year"])
names(conf) <- c("home", "home_conf", "year")
scores_home %>%
  left_join(conf, by = c("year", "home")) -> scores_conf
names(conf) <- c("away", "away_conf", "year")
scores_conf %>%
  left_join(conf, by = c("year", "away")) -> scores_conf

scores_conf %>%
  filter(home_conf == away_conf) %>%
  filter(is.na(location)) -> scores_conf

scores_conf %>%
  group_by(home_conf) %>%
  summarise(win.pct = round(mean(win),3)) -> hfa_conf
hfa_conf
```

| Conference | Home Win % |
|------------|-------|
| ACC        | .555  |
| Big 12     | .577  |
| Big Ten    | .544  |
| Pac 12     | .583  |
| SEC        | .572  |

I also broke it down by year, and found that home teams across all of Division 1 have won about 55% of games every year.

```r
scores_conf %>%
  group_by(year) %>%
  summarise(win.pct = round(mean(win),3)) -> hfa_year
hfa_year
```

| Year | Home Win % |
|------|------------|
| 2016 | .554       |
| 2017 | .552       |
| 2018 | .546       |
| 2019 | .558       |

### Next Steps
* Does home field advantage vary significantly between conferences?
* Is the RPI's weighting system justified? And how can we convert this data into a better ranking system?
* What schools have the greatest home field advantage? - This would likely require more seasons worth of data to determine the difference between a team's talent and the advantage given by the fans. 


*The files used for this project can be downloaded [here](https://github.com/milesok/milesok.github.io/tree/master/files/home_field_files)
