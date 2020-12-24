# Finding-Dog-Parks-in-Tampa-FL
Week - 4 Capstone Project to find places around Tampa, FL where people can let their large dogs roam off-lease
## Dog Parks in Tampa for the Larger 4-Legged Family Members
The Goal of this Capstone Project is to find Dog parks in Tampa where the 4-legged family members can run around freely and off the lease. Many people with larger dogs move to new areas and do not know where they can take their larger dogs to roam freely. Smaller dogs are often fine in city areas and roaming on the lease, however larger dogs must run freely every so often. This program will search for Dog Parks and sort out the venues that are not truely suited for dogs to roam.

### ## Step 1: Loading the tools necessary to conduct the search and sort through the excess data

Libraries for data analysis

import pandas as pd
import requests
from pandas import DataFrame
pd.setoption('display.maxcolumns', None)
pd.setoption('display.maxrows', None)
from geopy.geocoders import Nominatim
import time
from pprint import pprint
Library to handle JSON files

import json
Matplotlib and associated plotting modules

import matplotlib.cm as cm
import matplotlib.colors as colors
Import k-means from clustering stage

from sklearn.cluster import KMeans
Libraries for displaying images

from IPython.display import Image
from IPython.core.display import HTML
from tabulate import tabulate
from bs4 import BeautifulSoup
! pip install geocoder
! pip install Nominatim
Tranforming json file into a pandas dataframe library

from pandas.io.json import json_normalize
Plotting Library

! pip install folium==0.5.0
import folium
### ## Step 2:: Get the data source and load it into Python

df=pd.readhtml('Web Address'_)[0]
df.head()
##### Step 3: Load my credentials and set the search limit to 50 results

CLIENT_ID = _'Your Client ID'_ # your Foursquare ID
CLIENT_SECRET = _'Your Secret ID'_ # your Foursquare Secret
ACCESS_TOKEN = _'Your Access Token'_ # your FourSquare Access Token
VERSION = '20180604'
LIMIT = 50
##### Step 4: Get coordinates (Latitude and Longitude) for the Tampa Bay area using FourSquare

address = 'Tampa, FL'
geolocator = Nominatim(useragent="foursquareagent")
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print(latitude, longitude)
#### Step 5: Search with a radius of 700

search_query = 'Dog Park'
radius = 700
print(search_query + ' …. OK!')
#### Step 6: Define the url for your search

url = 'https://api.foursquare.com/v2/venues/search?clientid={}&clientsecret={}&ll={},{}&oauthtoken={}&v={}&query={}&radius={}&limit={}'.format(CLIENTID, CLIENTSECRET, latitude, longitude,ACCESSTOKEN, VERSION, search_query, radius, LIMIT) url
#### Step 7: Pull all the data from the url

results = requests.get(url).json()
results
#### Step 8: Get the relevant information for our purposes and put into a table

venues = results['response']['venues']
dataframe = pd.json_normalize(venues)
dataframe.head()
#### Step 9: Begin flitering the data

Keep only columns that include venue name, and anything that is associated with location

filtered_columns = ['name', 'categories'] + [col for col in dataframe.columns if col.startswith('location.')] + ['id']
dataframefiltered = dataframe.loc[:, filteredcolumns]
Function that extracts the category of the venue

def getcategorytype(row):
try:
categories_list = row['categories']
except:
categories_list = row['venue.categories']
if len(categories_list) == 0:
return None
else:
return categories_list[0]['name']
Filter the category for each row

dataframefiltered['categories'] = dataframefiltered.apply(getcategorytype, axis=1)
Clean column names by keeping only last term

dataframefiltered.columns = [column.split('.')[-1] for column in dataframefiltered.columns]
dataframe_filtered
#### Step 10: Check out the shape of my captured dataframe

dataframe_filtered.shape
#### Step 11: Get rid of the excess columns

df1 = dataframe_filtered.drop (['address', 'labeledLatLngs', 'distance', 'postalCode', 'cc', 'city', 'state', 'country', 'formattedAddress', 'crossStreet'], axis=1)
#### Step 12: Pull only results that are Parks or Dog Runs (Fenced areas for dogs)

df2 = df1[df1['categories'].str.match('Park' 'Dog Run')] df2.head()

#### Step 13: Check out the number of results in the new dataframe

df2.shape
#### Step 14: Plot the locations on a Map with Tampa, FL as the center

venuesmap = folium.Map(location=[latitude, longitude], zoomstart=13) # generate map centred around Tampa, FL
Add a red circle marker to represent Tampa FL

folium.CircleMarker(
[latitude, longitude],
radius=10,
color='red',
popup='Tampa FL',
fill = True,
fill_color = 'red',
fill_opacity = 0.6
).addto(venuesmap)
Add the Dog Parks as blue circle markers for lat, lng, label in zip(df2.lat, df2.lng, df2.categories): folium.CircleMarker( [lat, lng], radius=5, color='blue', popup='name' 'categories', fill = True, fillcolor='blue', fillopacity=0.6 ).addto(venuesmap)

Display the map

venues_map
#### Now people can know where the Dog Parks are in the Tampa, FL area so their large & small 4-legged family members can roam free off the lease.
