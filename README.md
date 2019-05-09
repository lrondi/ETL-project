# ETL-project: Deep sea corals database
!['coral'](https://deepseacoraldata.noaa.gov/images/Mushroom%20coral)

The aim of this project was to gather data, transform it and load it into a database.
We used Jupyter notebooks with Python to code, Pandas to analyze the data, BeautifulSoup for scraping, MySQL Workbench to load it onto a database and Gmaps library to visualize it.

<b>Data acquisition:</b></br>

- We chose a csv file from kaggle.com (https://www.kaggle.com/noaa/deep-sea-corals) that consisted on records of more than 500,000 deep sea corals locations from the National Oceanic and Atmospheric Administration (NOAA: https://deepseacoraldata.noaa.gov/).
- We complemented that information with taxonomy classification for each species from the World Register of Marine Species (WORM: http://www.marinespecies.org/index.php). WORMS gives taxanomic classification for each species via its scientific name or its AphiaID. Even though WORMS offers an API to get the taxonomy of each species, we wanted to scrape each website to practice this technique.
- We used Flickr's API to look for pictures of different types of corals (https://www.flickr.com/services/api/ and https://www.flickr.com/services/api/flickr.photos.search.html).


<b>Transformation:</b></br>

- We first loaded up the csv file as a dataframe on a jupyter notebook using Pandas. We selected the columns that were of interest to us and got rid of the rows with NA values.
- We created a list of the unique species on the dataframe.
- We used this list to make API requests to the WORMS server (http://www.marinespecies.org/rest/) to get the AphiaID for each species.
- With this information, we looped through each species website (the url for each species is composed of a fixed path plus the AphiaID of each species), scraped the taxonomy and created a dictionary with the results.
- Most of the species taxonomy is composed of 8 ranks: Kingdom, Phyllum, Class, Subclass, Family, Order, Genus and Species. But for some of the species there was an extra category, which wasn't always the same (sometimes it was a Suborder, or Subspecies, etc). We decided to work with the species that had only the 8 ranks, to simplify the analysis. Even though we eliminated some records, we ended up with a dataframe comprising more than 200,000 rows.
- We then merged the different dataframes into a final one containing all the selected information from the NOAA csv file and the taxonomy from WORMS. This dataframe contains, for each species, its scientific name, AphiaID, all its taxonomic information, the vernacular name of its category, its location (latitude and longitude) and its locality.


<b>Loading:</b></br>

- We created a database on MySQL Workbench, and in it we created two tables: one for the corals NOAA data, and one for the taxonomy of each species.
- On the coral information table, we imported the csv file that we created that contained the original NOAA data, plus the AphiaID for each sp.
- On the taxonomy table, we imported the csv file that we created with the WORMS data for each species, plus the AphiaID. This id will work as the relational key between the two tables.


<b>Visualization:</b></br>

- We created a visualization widget on a Jupyter notebook using ipywidgets and gmaps libraries. The idea was to visualize the localization of all records of a specific species on the map, while also being able to visualize where its related species were located, selecting the level of relationship by choosing the taxonomic level to compare (same Kingdom, or Phyllum, or Oder, etc).
- We used Flickr's API to look for photos of the vernacular categories of each species, because looking up pictures of each specific species wasn't possible. For this, we first made an API request looking up the tags of the pictures (i.e. 'stony+coral'). This gaves us back the information to recreate the picture's url (as explained here: https://www.flickr.com/services/api/misc.urls.html). We limited the url's construction to 5 urls per category, because then we needed to manually visit each url to verify if the picture was correct/good.
- Some of the categories didn't give back any result on the API call, so we looked manually for those on Wikipedia.
- We then set up the widgets for dropdown menus and gmaps visualization using this tutorial: https://towardsdatascience.com/bring-your-jupyter-notebook-to-life-with-interactive-widgets-bc12e03f0916. We set up two dropdown menus with the species names and the level of taxa to compare, so that when the map is plotted, we get markers for the localization of each record for that specific species, and a heatmap layer for the ocurrence of all the species that belong to the selected taxonomic level. 
- For example, choosing <i>Aaptos apptos</i> as a species and "Order" as a taxonomic level to compare, would give us the following map, in which there's a marker for every record of <i>Aaptos aaptos</i> in the dataframe, and a heatmap for every record of the Order "Suberitida":

<img src='https://github.com/lrondi/ETL-project/blob/master/Images/map.png'>

- We also added an info box for each marker that contains the information for that species in that locality: name of the species, latitude and longitud, depth at which it was found, name of the locality, and name and picture of the vernacular category to which it belongs:
<img src='https://github.com/lrondi/ETL-project/blob/master/Images/markers.png'>

We had a lot of fun and learned a lot doing this project!
