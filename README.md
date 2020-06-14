# CourseraCourse4Week4

# Final Report: Data section

## Introduction/Business Problem

### Idea
My idea/problem of choice is to focus on a particular neighbourhood in Toronto, Garden District, Ryerson.

### Data sources
I will use the [Foursquare API](https://developer.foursquare.com/) to solve this problem. Particularly, I will use the `search` endpoint, [listed here](https://developer.foursquare.com/docs/api-reference/venues/search/), querying by the query parameter `categoryId` and its value `4d4b7105d754a06376d81259`, corresponding to Nightlife Spots (see more [here](https://developer.foursquare.com/docs/build-with-foursquare/categories/).

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

#### Data cleaning

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


### Audience
My audience is any particular stakeholder that will like to use the data on Garden District, Ryerson to identify different kind of venues to solve a particular problem (social, mobility, marketing, other) within this neighbourhood. My audience would care about this problem because it's interesting and useful to have more accurata data on this neighbourhood.
