---
layout: post
published: true
title: Win Expectations in College Baseball
date: '2019-08-01'
---
### The Idea
Bill James' original idea of relating winning percentage to runs scored and runs allowed was a very influential piece of baseball analytics history. While this concept [may not predict "true talent" well] (https://library.fangraphs.com/principles/expected-wins-and-losses/), we can use the pythagorean expectation formula to determine a conversion between runs and wins. The [pythagenpat](http://gosu02.tripod.com/id69.html) formula developed by Symth/Patriot adjusted James' original format and is currently accepted as the most elegant way to represent this relationship, so that's what I'll use for this post.

### The Process
First, after loading in the [data](https://github.com/milesok/milesok.github.io/tree/master/files/run_win_files/pt1) and required packages, I created a table of the pythagorean constants by year across all of D1 baseball using the pythagenpat formula suggested by Smyth. While this was a broad oversimplification of the college baseball landscape, it would give me a starting point.
```r
scores %>%
  mutate(year = substr(date, 0, 4)) -> scores
scores %>%
  group_by(year) %>%
  summarise(mean = mean(home_score + away_score), pythexp = mean ^ .287) -> constants
constants
```
This resulted in this table, which suggested that the original exponent of 2 from James' work was actually a  good exponent to use for the pythagorean win formula in college.  

| year | mean     | pythexp  |
|------|----------|----------|
| 2015 | 10.82605 | 1.981038 |
| 2016 | 11.06015 | 1.993239 |
| 2017 | 11.39254 | 2.010250 |
| 2018 | 11.26295 | 2.003661 |
| 2019 | 11.67152 | 2.024256 |

Next, I created a dataframe which, for the 2019 season, compiled each team's runs scored, runs allowed, and win-loss totals, and compared them to expected wins based on the formula. From here, I was also able to make a column approximating the number of runs that translate to a win for each team.
```r
scores %>%
  mutate(win = ifelse(home_score > away_score, 1, ifelse(home_score < away_score, 0, .5))) -> scores
scores %>%
  filter(year == 2019) %>%
  group_by(team_id = away_abb) %>%
  summarize(rs = sum(away_score), ra = sum(home_score), gp = n(), w = gp-sum(win)) %>%
  mutate(type = "away") -> awayscores

scores %>%
  filter(year == 2019) %>%
  group_by(team_id = home_abb) %>%
  summarize(rs = sum(home_score), ra = sum(away_score), gp = n(), w = sum(win)) %>%
  mutate(type = "home") -> homescores

awayscores %>%
  bind_rows(homescores) %>%
  group_by(team_id) %>%
  summarise(rs = sum(rs), ra = sum(ra), gp = sum(gp), w = sum(w), rundiff = rs-ra) %>%
  mutate(pythexp = (rs ^ 2.023955) / ( (rs ^ 2.023955) + (ra ^ 2.023955)), wpct = w/gp, xWins = pythexp*gp, runs_win = (rs^2+ra^2)^2/(gp*2*rs*ra^2))
```
Here are the bottom-5 teams by runs per win:  

|    team_id    |    rs    |    ra    |    gp    |    w    |       wpct       |       pythexp       |       xWins       |       runs_win       |
|---------------|----------|----------|----------|---------|------------------|---------------------|-------------------|----------------------|
| CORN          | 129      | 203      | 38       | 14      | 0.3684           | 0.2854              | 10.847            | 8.2835               |
| LBST          | 196      | 307      | 55       | 14      | 0.2545           | 0.2874              | 15.805            | 8.6614               |
| PUR           | 219      | 285      | 54       | 20      | 0.3704           | 0.3698              | 19.968            | 8.6871               |
| RUT           | 206      | 283      | 51       | 20      | 0.3922           | 0.3446              | 17.576            | 8.9209               |
| PSU           | 213      | 257      | 49       | 22      | 0.449            | 0.4061              | 19.899            | 9.0041               |

And here are the top-5 teams:  

|    team_id    |    rs    |    ra    |    gp    |    w    |    rundiff    |    wpct    |    pythexp    |    xWins    |    runs_win    |
|---------------|----------|----------|----------|---------|---------------|------------|---------------|-------------|----------------|
| NMST          | 610      | 318      | 55       | 38      | 292           | 0.6909     | 0.7889        | 43.391      | 33.003         |
| VAN           | 578      | 291      | 71       | 59      | 287           | 0.831      | 0.8004        | 56.83       | 25.231         |
| MSST          | 530      | 269      | 67       | 52      | 261           | 0.7761     | 0.7978        | 53.452      | 24.283         |
| WRST          | 501      | 289      | 59       | 42      | 212           | 0.7119     | 0.7528        | 44.415      | 22.664         |
| CMU           | 511      | 299      | 61       | 47      | 212           | 0.7705     | 0.7474        | 45.59       | 22.045         |

These tables show that, as expected, a lower amount of runs scored by the specific team leads to a lower number of runs that each win is worth, and vice versa.


### The Next Steps
This is just an introductory exploration of run-win values, so there are many different directions I could go from here:
* First, as discussed in my previous posts about [run environments](https://milesok.github.io/2019-07-30-college-baseball-run-environments-pt-1/), each conference has a different runs-per-game value, which should therefore change the exponent generated from the pythagenpat formula
* It would also be interesting to look at how these apply to a team- and even pitcher-specific level in the future
