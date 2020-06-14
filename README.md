# CourseraCourse4 (Week 4 and Week 5)

## Idea
The Garden District Residents Association is worried about improving their resources. They want to organize a local festival with University of Toronto in Down Town Toronto and they are looking to avoid competition with local nightlife. They wish to have information about nightlife in the area of University of Toronto to decide the festival venue location. 

### Objective
To find the number and location of nightlife spots 2km from University of Toronto.

### Audience
All the festival organisers.


## Technology Stack 

1. Python 3.6
2. [Jupyter Notebook](https://jupyter.org/).
3. [Folium](https://github.com/python-visualization/folium)
4. [Foursquare API](https://developer.foursquare.com/)

## Data sources
Foursquare offers a developer API and we'll be using its venue search functionality. I will use the [Foursquare API](https://developer.foursquare.com/) to solve this problem. Particularly, I will use the `search` endpoint, [listed here](https://developer.foursquare.com/docs/api-reference/venues/search/), querying by the query parameter `categoryId` and its value `4d4b7105d754a06376d81259`, corresponding to Nightlife Spots (see more [here](https://developer.foursquare.com/docs/build-with-foursquare/categories/)).

I did a quick test for nightlife spots near Toronto University (2km) and find it to be fairly accurate.

I select the University of Toronto neighbourhood:

```python
# I will focus on one particular neighbourhood:  University of Toronto

sjt = toronto[toronto['Postcode'] == 'M5S']
lat = sjt.iloc[0,3]
lon = sjt.iloc[0,4]
```
In that way I am able to get the Nightlife Spots near a latitude and longitude.

```python
#Explore neighborhoods using foursquare
CLIENT_ID = 'SZGXOPD3CHNLEHNOMD21VHKHW34PC3IR2VVGUNZKJ5E4GJBB' # your Foursquare ID
CLIENT_SECRET = 'EL2DIGEUCMYRNUIQWNVF4KIRRONTCKFYUPHEMPUOCNS0Y0GK' # your Foursquare Secret
VERSION = '20180605' # Foursquare API version
LIMIT = 200
radius = 2000

# categoryId 56aa371ce4b08b9a8d57356c corresponding to Beer venues according to Foursquare API
url = 'https://api.foursquare.com/v2/venues/search?categoryId=4d4b7105d754a06376d81259&client_id={}&client_secret={}&ll={},{}&v={}&radius={}&limit={}'.format(CLIENT_ID, CLIENT_SECRET, lat, lon, VERSION, radius, LIMIT)
print(url)
```

Notice:
 1. `categoryId` is `4d4b7105d754a06376d81259`, passed as a query parameter.
 2. I'm using a `radius` of `2000`.

### Data cleaning

I remove `NaN` values from the `location.address` column coming from the [Foursquare API](https://developer.foursquare.com/).

```python
results = requests.get(url).json()
import requests # library to handle requests
from pandas.io.json import json_normalize # tranform JSON file into a pandas dataframe
all_venues = results['response']['venues']
nearby_venues = json_normalize(all_venues)

chosen_columns = ['name', 'location.lat', 'location.lng','location.address']

venues = nearby_venues.loc[:,chosen_columns]
venues.rename(columns={'location.lat':'lat', 'location.lng':'long', 'location.address':'address'}, inplace=True)
venues = venues.dropna(subset=['address'])
venues.iloc[3,:]
```

## Conclusions and results
Now we have answered the question we set out for. If the client wants to avoid nightlife spots, the festival can be set on the north part of the neighbourhood, particularly in the north of Bloor West Street. We can easily [use the map](https://dataplatform.cloud.ibm.com/analytics/notebooks/v2/394d3b27-605e-485e-a6cb-1cdd8f36cd04/view?access_token=629fc1d44403006657f3695949206b9c65433ef488c06f12e929f5eda717c2fb) to make this decision.
 

### Visualizating the results
[A clickable map at the bottom of my Jupter Notebook](https://dataplatform.cloud.ibm.com/analytics/notebooks/v2/394d3b27-605e-485e-a6cb-1cdd8f36cd04/view?access_token=629fc1d44403006657f3695949206b9c65433ef488c06f12e929f5eda717c2fb) is a good way to see our results: number and location of nightlife spots.

## Observations
The provided results do not take into account nightlife spots outside of 2 km limit from University of Toronto.

## Future directions

In future iterations, the organisers of this event (or other events) can use the existing data to match it with other activities around University of Toronto.

They can also use this entire project to query other kinds of venues, around other kind of areas (anywhere in the world) by replacing the area in the Jupyter Notebook and the type of venue in the FourSquare API.


