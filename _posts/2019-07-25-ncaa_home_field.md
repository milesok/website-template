---
layout: post
title: Home Field Advantage in NCAA D1 Baseball
output: html_notebook
published: true
tags:
  - baseball
  - ncaa
---
### One of the most important

```{r message=FALSE, warning=FALSE}
require(tidyverse)
headers <- read.csv('../data/scores/headers.csv')
scores2019 <- read_csv('../data/scores/scores2019.csv', col_names =FALSE)
names(scores2019) = headers[, "Header"]
```


```{r}
scores2019 %>%
  mutate(win = ifelse(home_score > away_score, 1, 0)) -> scores19
head(scores19)
```

Overall home team winning percentage
```{r}
table(scores19$win)[2]/(table(scores19$win)[2]+table(scores19$win)[1])
```


```{r}
scores19 %>%
  filter(is.na(location)) -> scores2019home
head(scores2019home)
```


Calculating home team winning percentage at non-neutral site games
```{r}
mean(scores2019home$win)
```

```{r}
scores19 %>%
  filter(!is.na(location)) -> scores2019neutral
head(scores2019neutral)
```

```{r}
mean(scores2019neutral$win)
```


```{r}
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

Take out conference tournaments
```{r}
scores19 %>%
  filter(home_conf != away_conf) %>%
  filter(!is.na(location)) -> nonconf2019
mean(nonconf2019$win)
```


```{r message=FALSE, warning=FALSE}
scores2018 <- read_csv('../data/scores/scores2018.csv', col_names =FALSE)
names(scores2018) = headers[, "Header"]
scores2017 <- read_csv('../data/scores/scores2017.csv', col_names =FALSE)
names(scores2017) = headers[, "Header"]
scores2016 <- read_csv('../data/scores/scores2016.csv', col_names =FALSE)
names(scores2016) = headers[, "Header"]
scores2015 <- read_csv('../data/scores/scores2015.csv', col_names =FALSE)
names(scores2015) = headers[, "Header"]
scores2014 <- read_csv('../data/scores/scores2014.csv', col_names =FALSE)
names(scores2014) = headers[, "Header"]
```

ncaa screwed up
```{r}
names(scores2018) <- c("away", "away_abb", "away_score", "home", "home_abb", "home_score", "location", "innings", "date")
```

combine scores
```{r}
scores <- bind_rows(scores2019, scores2018, scores2017, scores2016, scores2015, scores2014)
```


```{r}
scores %>%
  mutate(win = ifelse(home_score > away_score, 1, 0)) -> scores
scores %>%
  filter(is.na(location)) -> scores_home
head(scores_home)
```

```{r}
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

```{r}
scores_conf %>%
  group_by(year) %>%
  summarise(win.pct = round(mean(win),3)) -> hfa_year
hfa_year
```

```{r}
scores_conf %>%
  group_by(year, home_conf) %>%
  summarise(win.pct = round(mean(win),3)) -> hfa_conf_year
hfa_conf_year
ggplot(hfa_year, aes(year, win.pct)) + geom_boxplot(data = hfa_conf_year, aes(year, win.pct, group = year))
```
