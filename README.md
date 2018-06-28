
# Pyber HQ

- **Observation 1:** The number of rides per city and driver count per city are negatively correlated with the average fare. These differences are divided neatly between rural (least drivers, least rides, highest cost), suburban (between rural and urban), and urban (most drivers, most rides, lowest cost). 

- **Observation 2:** Roughly 2/3rds of all fare costs and rides take place in urban areas.

- **Observation 3:** 4/5ths of drivers drive in urban areas, while less than 3 percent drive in rural areas


```python
%matplotlib inline
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
```


```python
# File to Load (Remember to change these)
city_data_to_load = "data/city_data.csv"
ride_data_to_load = "data/ride_data.csv"

# Read the City and Ride Data
city_df = pd.read_csv(city_data_to_load)
ride_df = pd.read_csv(ride_data_to_load)

city_df.head()
ride_df.head()

# Combine the data into a single dataset
all_rides = pd.merge(ride_df, city_df, on="city", how="left")
```


```python
# Display the data table for preview
all_rides.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
      <th>driver_count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Lake Jonathanshire</td>
      <td>2018-01-14 10:14:22</td>
      <td>13.83</td>
      <td>5739410935873</td>
      <td>5</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Michelleport</td>
      <td>2018-03-04 18:24:09</td>
      <td>30.24</td>
      <td>2343912425577</td>
      <td>72</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Port Samanthamouth</td>
      <td>2018-02-24 04:29:00</td>
      <td>33.44</td>
      <td>2005065760003</td>
      <td>57</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Rodneyfort</td>
      <td>2018-02-10 23:22:03</td>
      <td>23.44</td>
      <td>5149245426178</td>
      <td>34</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>South Jack</td>
      <td>2018-03-06 04:28:35</td>
      <td>34.58</td>
      <td>3908451377344</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>



## Bubble Plot of Ride Sharing Data


```python
# Obtain the x and y coordinates for each of the three city types
urb_cit = all_rides.loc[all_rides["type"]=="Urban",:]
sub_cit = all_rides.loc[all_rides["type"]=="Suburban",:]
rur_cit = all_rides.loc[all_rides["type"]=="Rural",:]

num_rides = []
avg_fare = []
driver_count = []

for df in (urb_cit, sub_cit, rur_cit):
    city_zip = df.groupby("city")
    num_rides.append(city_zip["ride_id"].count())
    avg_fare.append(city_zip["fare"].mean())
    driver_count.append(city_zip["driver_count"].mean())
```


```python
# Build the scatter plots for each city types
urban = plt.scatter(num_rides[0], avg_fare[0], marker="o", facecolors="lightcoral", edgecolors="black", s=driver_count[0]*10, alpha=0.80, label="Urban")
suburban = plt.scatter(num_rides[1], avg_fare[1], marker="o", facecolors="lightskyblue", edgecolors="black", s=driver_count[1]*10, alpha=0.80, label="Suburban")
rural = plt.scatter(num_rides[2], avg_fare[2], marker="o", facecolors="gold", edgecolors="black", s=driver_count[2]*10, alpha=0.80, label="Rural")

# Incorporate the other graph properties
plt.grid()
plt.title("Pyber Ride Sharing Data (2016)", fontsize=12)
plt.ylabel("Average Fare ($)")
plt.xlabel("Total Number of Rides (Per City)")

# Create a legend
lgnd = plt.legend(loc="upper right", handles=[urban, suburban, rural], title="City Types", fontsize=8)
#lgnd = plt.legend(loc="lower left", scatterpoints=1, fontsize=10)
lgnd.legendHandles[0]._sizes = [30]
lgnd.legendHandles[1]._sizes = [30]
lgnd.legendHandles[2]._sizes = [30]

# Incorporate a text label regarding circle size
plt.text(42,35,"Note:\nCircle size correlates with driver count per city.")

# Save and Show Figure
plt.savefig("Pyber_Bubble_BDS.png")
plt.show()
```


![png](Pyber_BDS_files/Pyber_BDS_6_0.png)


## Total Fares by City Type


```python
# Calculate Type Percents
urban_fares = urb_cit["fare"].sum()
suburban_fares = sub_cit["fare"].sum()
rural_fares = rur_cit["fare"].sum()

fares = [urban_fares, rural_fares, suburban_fares]
labels = ["Urban", "Rural", "Suburban"]
showcolor = ["lightcoral", "gold", "lightskyblue"]
explode = [0.1, 0, 0]
# Build Pie Chart
plt.pie(fares, explode=explode, labels=labels, colors=showcolor, autopct="%1.1f%%", shadow=True, startangle=-75)
plt.title("% of Total Fares by City Type")

# Save Figure
plt.savefig("Pyber_Fares_Pie_BDS.png")
plt.show()
```


![png](Pyber_BDS_files/Pyber_BDS_8_0.png)


## Total Rides by City Type


```python
# Calculate Ride Percents
urban_rides = urb_cit["ride_id"].count()
suburban_rides = sub_cit["ride_id"].count()
rural_rides = rur_cit["ride_id"].count()

rides = [urban_rides, rural_rides, suburban_rides]
labels = ["Urban", "Rural", "Suburban"]
showcolor = ["lightcoral", "gold", "lightskyblue"]
explode = [0.1, 0, 0]

# Build Pie Chart
plt.pie(rides, explode=explode, labels=labels, colors=showcolor, autopct="%1.1f%%", shadow=True, startangle=-95)
plt.title("% of Total Rides by City Type")

# Save and Show Figure
plt.savefig("Pyber_Rides_Pie_BDS.png")
plt.show()


```


![png](Pyber_BDS_files/Pyber_BDS_10_0.png)


## Total Drivers by City Type


```python
# Calculate Driver Percents
drivers = []
types = city_df.loc[:,"type"].unique()
types
for x in range(3):
    drivers.append(city_df.loc[city_df["type"]==types[x],"driver_count"].sum())
```


```python
driver_full = [drivers[0], drivers[2], drivers[1]]
labels = ["Urban", "Rural", "Suburban"]
showcolor = ["lightcoral", "gold", "lightskyblue"]
explode = [0.1, 0, 0]

# Build Pie Charts
plt.pie(driver_full, explode=explode, labels=labels, colors=showcolor, autopct="%1.1f%%", shadow=True, startangle=-125)
plt.title("% of Total Drivers by City Type")

# Save and Show Figure
plt.savefig("Pyber_Drivers_Pie_BDS.png")
plt.show()
```


![png](Pyber_BDS_files/Pyber_BDS_13_0.png)

