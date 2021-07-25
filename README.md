# python-api-challenge

# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import time
import api_keys
from datetime import date
from pprint import pprint
from scipy.stats import linregress
import seaborn as sns

# Import API key (from weather and google api)
import api_keys


# Incorporated citipy to determine city based on latitude and longitude
from citipy import citipy

# Output File (CSV)
output_data_file = "output_data/cities.csv"

# Range of latitudes and longitudes
lat_range = (-90, 90)
lng_range = (-180, 180)



# generate Cities list 
lat_lngs = []
cities = []

# Create a set of random lat and lng combinations
lats = np.random.uniform(lat_range[0], lat_range[1], size=1500)
lngs = np.random.uniform(lng_range[0], lng_range[1], size=1500)
lat_lngs = zip(lats, lngs)

# Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs:
    city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name
    
    # If the city is unique, then add it to a our cities list
    if city not in cities:
        cities.append(city)

# Print the city count to confirm sufficient count
len(cities)



# API Calls on a couple of citites to validate
url_main = "http://api.openweathermap.org/data/2.5/weather?"
query_url = url_main + "appid=" + weather_api_key + "&q=" + 'New York City'
weather = requests.get(query_url).json()
weather


# Define url
url_main = "http://api.openweathermap.org/data/2.5/weather?units=Imperial&"
#http://api.openweathermap.org/data/2.5/weather?

# Create counters
record_count = 1
set_count = 1
columns = ["City", "Cloudiness", "Country", "Date", "Humidity", "Lat", "Lng", "Max Temp (F)", "Wind Speed"]
city_data = pd.DataFrame(columns = columns)
print("-----------------------------")
print("Beginning Data Retrieval.")
print("-----------------------------")

# Set iteration
for i, city in enumerate(cities):
    if (i % 50 == 0 and i >= 50):
        set_count += 1
        record_count = 0

# Set index and row in df.interrows():
    print(f"Processing Record {record_count} of Set {set_count} | {city}")
    record_count +=1

# Define url for query
    query_url = url_main + "appid=" + weather_api_key + "&q=" + city
    
    try:
        weather = requests.get(query_url).json()
        city_clouds = weather['clouds']['all']
        country = weather['sys']['country']
        date = weather['dt']
        city_humidity = weather['main']['humidity']
        #city_temp = weather['main']['temp']
        lat = weather['coord']['lat']
        lon = weather['coord']['lon']
        city_max_temp = weather['main']['temp_max']
        city_wind_speed = weather['wind']['speed']    
        
        # Append city into city_info
        city_data = city_data.append({"City": city,
                                      "Cloudiness" : city_clouds,
                                      "Country" : country,
                                      "Date" : date,
                                      "Humidity" : city_humidity,
                                      "Lat" : lat,
                                      "Lng" : lon,
                                      "Max Temp (F)" : city_max_temp,
                                      "Wind Speed" : city_wind_speed,
                                     }, ignore_index=True)
        
    except:
        print("City not found. Skipping...")
        pass
    
print("-----------------------------")
print("Data Retrieval Complete.")
print("-----------------------------")


