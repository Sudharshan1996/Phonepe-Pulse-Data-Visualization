# Data Visualization and Exploration : A User-Friendly Tool Using Streamlit and Plotly

# What is PhonePe Pulse?

The PhonePe Pulse website showcases more than 2000+ Crore transactions by consumers on an interactive map of India. With over 45% market share, PhonePe's data is representative of the country's digital payment habits. The insights on the website and in the report have been drawn from two key sources - the entirety of PhonePe's transaction data combined with merchant and customer interviews. The report is available as a free download on the PhonePe Pulse website and GitHub.

# Demo video of my project - Click here

# Libraries/Modules needed for the project!

1. Plotly - (To plot and visualize the data)
2.Pandas - (To Create a DataFrame with the scraped data)
3.pymysql & sqlalchemy - (To store and retrieve the data)
4.Streamlit - (To Create Graphical user Interface)
5.json - (To load the json files)
6.git.repo.base - (To clone the GitHub repository)

# Workflow

# Step 1:

Importing the Libraries:

Importing the libraries. As I have already mentioned above the list of libraries/modules needed for the project. First we have to import all those libraries. If the libraries are not installed already use the below piece of code to install.

    !pip install ["Name of the library"]
    
If the libraries are already installed then we have to import those into our script by mentioning the below codes.

    import streamlit as st
    import pandas as pd
    import mysql.connector as msql
    from mysql.connector import Error
    import plotly.express as px
    import geopandas as gpd
    from streamlit_option_menu import option_menu
    from PIL import Image
    import os

# Step 2:

Data extraction:

Clone the Github using scripting to fetch the data from the Phonepe pulse Github repository and store it in a suitable format such as JSON. Use the below syntax to clone the phonepe github repository into your local drive.

    from git.repo.base import Repo
    Repo.clone_from("GitHub Clone URL","Path to get the cloded files")
    
Data transformation:

In this step the JSON files that are available in the folders are converted into the readeable and understandable DataFrame format by using the for loop and iterating file by file and then finally the DataFrame is created. In order to perform this step I've used os, json and pandas packages. And finally converted the dataframe into CSV file and storing in the local drive.   

       import os
       path = "C:/Users/Sudharshan/Phonepe Pulse Data Visualization/data/aggregated/transaction/country/india/state/" 
       agg_trans_st_dir = os.listdir(path)
       agg_trans_st_dir

Looping through each and every folder and opening the json files appending only the required key and values and creating the dataframe.    
  
       agg_trans_dict = {'State':[],
                         'Year':[],
                         'Quater':[],
                         'Transacion_type':[],
                         'Transacion_count':[],
                         'Transacion_amount':[]}

     for i in agg_trans_st_dir:
           st_path = path+i+'/'
           st_year = os.listdir(st_path)
     for j in st_year:   
          st_year_path = st_path+j+'/'
          st_year_dir = os.listdir(st_year_path)
     for k in st_year_dir:
         json_path = st_year_path+k
         jsonData = open(json_path, 'r')
         Data = json.load(jsonData)
     for x in Data['data']['transactionData']:
                    Name = x['name']
                    count = x['paymentInstruments'][0]['count']
                    amount = x['paymentInstruments'][0]['amount']
                    agg_trans_dict['Transacion_type'].append(Name)
                    agg_trans_dict['Transacion_count'].append(count)
                    agg_trans_dict['Transacion_amount'].append(amount)
                    agg_trans_dict['State'].append(i)
                    agg_trans_dict['Year'].append(j)
                    agg_trans_dict['Quater'].append('Q'+k.strip('.json'))

     agg_trans_df = pd.DataFrame(agg_trans_dict)
     agg_trans_df
     
# Converting the dataframe into csv file

     agg_trans_df.to_csv('AggTransByStates.csv',index=False)

# Step 4:

# Database insertion:

To insert the datadrame into SQL first I've created a new database and tables using "mysql-connector-python" library in Python to connect to a MySQL database and insert the transformed data using SQL commands.

Creating the connection between python and mysql

   try:
    conn = msql.connect(host='localhost',
                           database='phonepe_pulse', user='root',
                           password='Dharshan1996')
    if conn.is_connected():
        cursor = conn.cursor()
        cursor.execute("select database();")
        record = cursor.fetchone()
        
        cursor.execute('DROP TABLE IF EXISTS mapTransByDistrict;')
       
        cursor.execute("CREATE TABLE mapTransByDistrict\
                       (State varchar(100),\
                        Year int,\
                        Quater varchar(5),\
                        District varchar(50),\
                        Transaction_count int,\
                        Transaction_amount float(50,3))")
                       
        print("mapTransByDistrict table is created....")
        for i,row in mapTransByDistrict.iterrows():
            sql = "INSERT INTO Phonepe_Pulse.mapTransByDistrict VALUES (%s,%s,%s,%s,%s,%s)"
            cursor.execute(sql, tuple(row))                        
            conn.commit()
        print("mapTransByDistrict values are inserted to MySQL....")
    except Error as e:
    print("Error while connecting to MySQL", e)
       
Creating tables
    
    try:
    conn = msql.connect(host='localhost',
                           database='phonepe_pulse', user='root',
                           password='Dharshan1996')
    if conn.is_connected():
        cursor = conn.cursor()
        cursor.execute("select database();")
        record = cursor.fetchone()
        
        cursor.execute('DROP TABLE IF EXISTS mapUserByDistReg;')
       
        cursor.execute("CREATE TABLE mapUserByDistReg\
                       (State varchar(100),\
                        Year int,\
                        Quater varchar(5),\
                        District varchar(50),\
                        Registered_user int,\
                        App_opening int)")
                       
        print("mapUserByDistReg table is created....")
        for i,row in mapUserByDistReg.iterrows():
            sql = "INSERT INTO Phonepe_Pulse.mapUserByDistReg VALUES (%s,%s,%s,%s,%s,%s)"
            cursor.execute(sql, tuple(row))                        
            conn.commit()
        print("mapUserByDistReg values are inserted to MySQL....")
    except Error as e:
    print("Error while connecting to MySQL", e)
    
# Step 5:

Dashboard creation:

To create colourful and insightful dashboard I've used Plotly libraries in Python to create an interactive and visually appealing dashboard. Plotly's built-in Pie, Bar, Geo map functions are used to display the data on a charts and map and Streamlit is used to create a user-friendly interface with multiple dropdown options for users to select different facts and figures to display.

# Step 6:

Data retrieval:

Finally if needed Using the "pymysql" library to connect to the MySQL database and fetch the data into a Pandas dataframe.   
