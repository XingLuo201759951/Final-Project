# Analyzing crime data to explore measures for reducing crime rates in Leed
Crime has always been a concern in this society, as it relates to the safety of residents' lives. For this project, crime data of Leeds from April 2023 to March 2024 was selected, aiming to propose recommendations for enhancing the safety of the city of Leeds.
# Dataset
The data used in this project are crime recording data and monthly average temperature data for Leeds for a total of 12 months from April 2023 to March 2024, as well as geographic coordinates for Leeds
Reading Data Use the pandas.read_csv function to read data into a pandas data frame.

Crime data downloaded from downloaded https://www.police.uk/pu/your-area/west-yorkshire-police/leeds-city?tab=CrimeMap

Monthly temperature data downloaded from https://www.metoffice.gov.uk/hadobs/hadcet/data/meantemp_monthly_totals.txt

Geographical data downloaded from https://geoportal.statistics.gov.uk/maps/761ecd09b4124843b95511a242e2b1a1tab=CrimeMap

# Initial Visualization
## Data Load and Processing
To get the data in the right shape, there are 4 main steps to take:
1.Read in the data: Data will be read into a pandas dataframe using the pandas.read_csv function.
2.Pull out just the date and metric columns: We only need the date part (monthly for this dataset) and the metrics (crime total column/temperature).
3.Normalize the data: exploring and cleaning data.
4.Set the dataframe date index: Setting our date column to be the index of the pandas dataframe allows for an easier setup when using the visualize fuction. 
The code for these four steps is as follows: 

```python #read in required packages
import pandas as pd
import numpy as np
import geopandas as gpd
import matplotlib as mpl
import matplotlib.pyplot as plt
import seaborn as sns
#set seaborn plotting theme to white
sns.set_theme(style="white")
#read crime data[1], data downloaded from https://www.police.uk/pu/your-area/west-yorkshire-police/leeds-city?tab=CrimeMap
crime = pd.read_csv('.../data/crime.csv')
```
### familiar with data
```
crime.head()
#Found that the column name is not automatically recognized, manually set the column name
crime.columns = ['Crime ID', 'Month', 'Reported by',	'Falls within',	'Longitude',	'Latitude',	'Location',	'LSOA code',	'LSOA name',	'Crime type',	'Last outcome category',	'Context']
#Delete the first line that is not automatically recognized as a title bar
crime = crime.iloc[1:].reset_index(drop=True)
crime.info()
```
### Data Cleaning
```
#Found no content in the contant column, delete it
crime = crime.drop('Context', axis=1)
# Check for duplicate lines
crime[crime.duplicated(keep=False)]
# Duplicate rows found, delete duplicate rows
crime.drop_duplicates(keep="first",inplace=True)
# Keeping the index continuous after deleting duplicate rows
crime.reset_index(drop = True)

# data mapping validation
crime["Crime ID"].unique()
crime["Month"].unique()
crime["Reported by"].unique()
crime["Falls within"].unique()
crime["Longitude"].unique()
crime["Latitude"].unique()
crime["Location"].unique()
crime["LSOA code"].unique()
crime["LSOA name"].unique()
crime["Crime type"].unique()
crime["Last outcome category"].unique()
# Found that there are some "No Location" fields in the "Location" column, replace it with a null value to facilitate the subsequent data processing.
crime["Location"] = crime['Location'].replace('No Location', np.NaN)
# Remove columns not relevant to the analysis
crime = crime.drop('Crime ID', axis=1)
crime = crime.drop('Reported by', axis=1)
crime = crime.drop('Last outcome category', axis=1)
crime = crime.drop('Falls within', axis=1)
crime = crime.drop('Location', axis=1)

# Check for null values
crime.isnull().any()
# Some spatial information was found to be null, which was temporarily retained and replaced with 0 when performing the non-spatial analysis to facilitate subsequent data manipulation
crime = crime.fillna('0')
# Filtering Leeds crime data for non-spatial analysis
crime_leeds = crime[(crime['LSOA name'].str.contains('Leeds'))]
# Check data
crime_leeds
```
## Data Visualisation of Relationship between Crime and Temperature
Then we can start making visualizations.First, by visualizing and analyzing the temporal nature of the Leeds crime data and whether it is climate related or not.
The code for these four steps is as follows:
```# Count different types of crime in Leeds
crime_count = crime_leeds.groupby('Month')["Crime type"].count().rename('count').reset_index()
# Check data
crime_count
#As a seasonality reference, import the average temperature data[2] of Leeds from April 2023 to March 2024 as a basis for making seasonal judgments,data downloaded from https://www.metoffice.gov.uk/hadobs/hadcet/data/meantemp_monthly_totals.txt
monthly_temp = pd.read_csv('.../data/Monthly_Temperature.csv')
monthly_temp.info()
crime_count['Month'] = pd.to_datetime(crime_count['Month'], format='%Y-%m')
monthly_temp['Month'] = pd.to_datetime(monthly_temp['Month'], format='%Y-%m')

# Visualizing Leeds' crime data in a time series and create line-bar charts to better visualize the changes in the time series.

# Creates a new figure with a figure number of 1, a size of 6x4 inches, and a resolution of 300 dots per inch (dpi)
fig =plt.figure(1,(6,4),dpi = 300)
# Creates a subplot (axes) in the figure with one row, one column, and an index of 1. This will be the primary subplot.
ax1 =plt.subplot(111)

#Plots crime count data as a line plot and scatter plot on the primary y-axis (ax1)
ax1.plot(crime_count['Month'],crime_count['count'],'k-',crime_count['Month'],crime_count['count'],'r.',linewidth=1.5, markersize=5)
# Sets the label for the y-axis as "Crime Count"
ax1.bar(crime_count['Month'], crime_count['count'], width=5, alpha=0.6, label='Crime Count Bar')
# Sets the label for the x-axis as "Month"
ax1.set_xlabel('Month')
# Sets the label for the y-axis as "Crime Count"
ax1.set_ylabel('Crime Count')
# Sets the limits for the y-axis from 0 to 12000
ax1.set_ylim(0, 12000)
#  Sets the x-axis ticks to the months in the crime count data
ax1.set_xticks(crime_count['Month'])
ax1.set_xticklabels(crime_count['Month'], rotation='vertical')
# Adds a legend to the upper left corner of the plot
ax1.legend(loc='upper left')
# Sets the x-axis ticks again to the formatted months ("%Y-%m")
ax1.set_xticks(crime_count['Month'])
ax1.set_xticklabels(crime_count['Month'].dt.strftime('%Y-%m'), rotation=45)

# Add a line graph of temperature data
# Creates a secondary y-axis (ax2) that shares the same x-axis as ax1
ax2 = ax1.twinx()
# The temperature data is represented by blue lines and dots, labeled on the secondary y-axis with limits set between 0 and 25. A legend is added to the upper right corner for the temperature data.
ax2.plot(monthly_temp['Month'], monthly_temp['Temperature'],'b-',monthly_temp['Month'],monthly_temp['Temperature'],'b.', linewidth=1.5, markersize=5, label='Average Temperature')
ax2.set_ylabel('Average Temperature (°C)')
ax2.set_ylim(0, 25)
ax2.legend(loc='upper right')

# Sets the title of the plot
plt.title('Monthly Crime and Average Temperature in Leeds', fontsize=10)

# Displays the plot
plt.show()
```
## Discussion
From the results of the data analysis, it shows that crime in Leeds does not show a clear seasonal pattern, nor is it directly related to seasonal climatic conditions.

# Further Visualization
The results of the previous analysis showed that crime in Leeds is not seasonal, so we analyzed it again in terms of the type of crime and looked for entry points to reduce the crime rate, mapping with spatial data[3] from Leeds.
## Geographical Data Preparation
