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
