---
layout: post
published: false
title: Creating an NCAA Play-by-play Database Part 1
---
### The Idea
NCAA publishes game-by-game college baseball statistics on their [stats website](https://stats.ncaa.org/contests/scoreboards?utf8=%E2%9C%93&sport_code=MBA), but summary statistics are very limited, and they do not provide functionality to export play-by-play logs. The goal of this project is to create a [Retrosheet](https://www.retrosheet.org/)-like database of NCAA statistics.

### The Process
The resources for each game include two templates that we are interested in. The first is the Box Score page, which allowed me to scrape the lineups for each game:
![]({{site.baseurl}}/files/pbp_files/box_score.png)

The Play by Play page contains a log of plays and substitutions for the game, as inputted by school SIDs. These generally follow a template based on the software used to input games, which means they are mostly standardized but are not error-free:
![]({{site.baseurl}}/files/pbp_files/pbp_log.png)

I started this project in Python, primarily using the requests library to make http requests and the lxml package to parse the responses. I started with a simple function to scrape all elements contained within table rows on a webpage, and saved it in a module called *scrape.py*.

*scrape.py*
```python
import requests
import lxml.html as lh

def get_table(url) -> list:
    return lh.fromstring(requests.get(url).content).xpath('//tr')
```  
This function simply collects the contents of all **\<tr\>** tags from a webpage. We'll use this to scrape tables from both pages.

*game.py*
```python
import scrape
import pandas as pd
  
def get_pbp(game_id):
    table = scrape.get_table('https://stats.ncaa.org/game/play_by_play/' + str(game_id))
```
I started by using the **get_pbp** function above to collect data from the play-by-play page.

```python
skip = True
score = False
plays = []
game = []
none = 0
i = 0
half = 1
```
Here, I initialized variables to streamline getting only the data I wanted from the page. The **skip** variable will be false until the loop reaches the second to last element in the header, which is titled "Score," and tell the loop to skip over the values until then. At that point, the **score** variable will be changed to true. The purpose of this variable is to build in one more iteration before values will be collected since the name of the home team will be in the element immediately after score. The empty list **plays** will contain plays for each half inning, and these will become the elements for the **game** list which will ultimately be the output of the function. The variable **none** will count the empty cells between plays, which can tell us where the top half of an inning ends, and the **i** counter ensures that we are looking in the correct column for data. Finally, the **half** counter keeps track of the half-inning within the game.
  
Now, we loop through each row in the table, and each cell in each row, collecting all of the plays in the game and ignoring all other values in the table (specifically the end-of-inning R/H/E recaps).
```python
for element in table:
   for e in element:
       if e.text == "Score":
           score = True
       elif score:
           skip = False
           score = False
           i = -1
       elif not skip:
           i += 1
           if e.text is None:
               none += 1
          elif e.text[0:3] == " R:":
               skip = True
               none = 0
               half += 1
               game.append(clean_plays(plays))
               plays = []
          elif half % 2 != 0:
               if i % 3 == 0:
               plays.append(e.text)
                   none = 0
               if none > 2:
                   half += 1
                   none = 0
                   game.append(clean_plays(plays))
                   plays = []
         if half %2 == 0:
               if (i-2) % 3 == 0:
                   plays.append(e.text)
                   none = 0
return game
```

### Next Steps
