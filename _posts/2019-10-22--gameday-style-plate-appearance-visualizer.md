---
layout: post
published: true
title: '"Gameday" Style Plate Appearance Visualizer'
---
### The Idea
Looking through rows of Trackman data on a CSV file to find out what the pitch characteristics for a particular at bat typically requires a lot of work to navigate through tons of rows and columns to find each data point, remembering the values you've just found, and being able to process what the numbers mean. Because of this, last season, I created an R Shiny app to create "MLB Gameday"-like summaries for each plate appearance that we have Trackman data for, connected to our SQL database so it will dynamically update as more games are added to the database.

### The Process
The main component of this app is the visualization. Here is an example of what the visual looks like for a generic at bat:
![Visualization of at bat](https://raw.githubusercontent.com/milesok/milesok.github.io/master/files/ab_viz_files/abplots.PNG)

From here, I created an R Shiny App that can dynamically update the visual, giving users the option to select the game, team, pitcher, and at bat that they would like to view. Here is the finished product:

![Screenshot of at bat summary](https://raw.githubusercontent.com/milesok/milesok.github.io/master/files/ab_viz_files/abplots.PNG)

### Next Steps
* Project visualizations to see pitches from hitter perspective
* Animate the ball flight
I'm currently working on creating a similar tool using Three.js, which will allow for a true three-dimensional visualization and create the potential for animating spin and much more.

[Here](https://github.com/milesok/milesok.github.io/tree/master/files/ab_viz_files) are the R files I used in creating the visualization.
