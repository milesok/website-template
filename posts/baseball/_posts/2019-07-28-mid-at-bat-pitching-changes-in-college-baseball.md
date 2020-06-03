---
layout: post
published: false
title: Mid At Bat Pitching Changes in College Baseball
---
## A New Post

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.

```SQL
SELECT * FROM (SELECT `PitchNo`, `Date`, `Inning`, `PAofInning`, `PitchofPA`, `Pitcher`, `Batter`, `Balls`, `Strikes`, `PitcherTeam`,`BatterTeam`, CONCAT(`GameID`, `Inning`, `Top/Bottom`, `PAofInning`) AS 'ABid', `PitchUID` FROM `TM19`) as TM WHERE `ABid` IN ( SELECT ABid FROM (SELECT `PitchNo`, `Date`, `Inning`, `PAofInning`, `PitchofPA`, `Pitcher`, `Batter`, `Balls`, `Strikes`, `PitcherTeam`,`BatterTeam`, CONCAT(`GameID`, `Inning`, `Top/Bottom`, `PAofInning`) AS 'ABid', `PitchUID` FROM `TM19`) as TM2 GROUP BY `ABid` HAVING COUNT(distinct `Pitcher`) > 1 ) ORDER BY `TM`.`ABid` DESC, `TM`.`PitchNo`
```
