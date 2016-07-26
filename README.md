# Visualizing Fligh Paths of Airlines In US
## Udacity Data Analyst Nanodegree
## Data Visualization Course Final Project

### Summary

This final project visualizes the data of US Airports flight information in 2008. The data is visualized by nodes and edges where nodes represent the airports and the edges represent the possible flight paths. 

### Design

I used the US map as a background to show readers where exactly the airports are. The map is scaled so that the readers can see the nodes and edges clearly.

When the project starts, it shows the nodes and their edges one at a time. The order of the appearance is based on the activity of the airports (The most active airpots is shown first). Readers can see that the activity of the airports is not proportional to the number of edges in the visualization because not all of the US airports are shown on the map.

The nodes have different sizes of circles and the radius of circles are based on the number of edges of the nodes. I used only 30 airports because too many airports will only confuse readers without providing clear insights of the data.

**Changes after feedback**

After Thomas's feedback, I included the number of flights between two airports in the **edges.csv** file. The number of flights is visualized by the widths of lines. When a user clicks a circle, the lines with different widths are shown where widths represent the number of flights between two nodes (airports).

After Seungmin's feedback, I removed other non-US countries from the map. Her suggestion made the visualization have more clarity. I will ignore her second suggestion about adding pictures of the airports because I don't think it will improve the visualization. 

After Yuri's feedback, I added a feature so that when the mouse is over a circle then a tip box appears showing the name of the airport and the number of the edges.

**Changes after the first submission**

I wrote the introduction and summary that enable readers to understand the visualization more clearly. The legends are now at the right hand side indicating the the number of edges represented by the size of the circle and the number of flights represented by the width of the edge. I wrote more comments on **index.html** to be more readable.

### Feedbacks

#### Thomas Lee ####
The visualization shows the flight paths of the airports in the U.S. Clicking on each airport shows which airports the you can get to from the clicked aiport. It would be interesting to see the size of the circle representing each airport to expand, shrink, or even disappear depending on the number of flights.

#### Seungmin Lee ####
*What i like about*: good visual representation where the planes are heading to.

*Suggestions*: since its only deals with american flight, i think it's better to erase surrounding countries. Add pictures of the airport!

#### Yuri Choi ####
Nice visualization showing the routes of flights.

It would be great if there is a pop up box that indicates the information of the airport when the mouse is on the circle.

### Resources

I used the 2008 flight data from *http://stat-computing.org/dataexpo/2009/the-data.html*. The data don't contain the coordinate information of each airport so I used the second data from *https://gist.githubusercontent.com/tdreyno/4278655/raw/7b0762c09b519f40397e4c3e100b097d861f5588/airports.json*. To create a US map, I used *world_countries.json* from Visualization Course.

In order to create the visualization I mainly used the project from lesson 4 of **Data Visualization** course. I used *http://jsdatav.is/visuals.html?id=11560633* to create nodes and edges netweork visualization and used *http://bl.ocks.org/biovisualize/1016860* to create tooltips.