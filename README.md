# Analyzing crime data to explore measures for reducing crime rates in Leed
Crime has always been a concern in this society, as it relates to the safety of residents' lives. For this project, crime data of Leeds from April 2023 to March 2024 was selected, aiming to propose recommendations for enhancing the safety of the city of Leeds.
# Dataset
The data used in this project are crime recording data and monthly average temperature data for Leeds for a total of 12 months from April 2023 to March 2024, as well as geographic coordinates for Leeds
Reading Data Use the pandas.read_csv function to read data into a pandas data frame.
# Initial Visualization
To get the data in the right shape, there are 4 main steps to take:
1.Read in the data: Data will be read into a pandas dataframe using the pandas.read_csv function.
2.Pull out just the date and metric columns: We only need the date part (monthly for this dataset) and the metrics (crime total column/temperature).
3.Normalize the data: exploring and cleaning data.
4.Set the dataframe date index: Setting our date column to be the index of the pandas dataframe allows for an easier setup when using the visualize fuction. 
The code for these four steps is as follows: 

'python #read in required packages
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
#familiar with data
crime.head()
#Found that the column name is not automatically recognized, manually set the column name
crime.columns = ['Crime ID', 'Month', 'Reported by',	'Falls within',	'Longitude',	'Latitude',	'Location',	'LSOA code',	'LSOA name',	'Crime type',	'Last outcome category',	'Context']
#Delete the first line that is not automatically recognized as a title bar
crime = crime.iloc[1:].reset_index(drop=True)
crime.info()
#Found no content in the contant column, delete it
crime = crime.drop('Context', axis=1)
