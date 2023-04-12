# Data Visualization and Exploration : A User-Friendly Tool Using Streamlit and Plotly

# What is PhonePe Pulse?

The PhonePe Pulse website showcases more than 2000+ Crore transactions by consumers on an interactive map of India. With over 45% market share, PhonePe's data is representative of the country's digital payment habits. The insights on the website and in the report have been drawn from two key sources - the entirety of PhonePe's transaction data combined with merchant and customer interviews. The report is available as a free download on the PhonePe Pulse website and GitHub.

# Demo video of my project? - Click here

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
 
    import pandas as pd
    import json
    import os
    from PIL import Image
    import streamlit as st
    import pymysql
    import sqlalchemy
    from sqlalchemy import create_engine
    from sqlalchemy import text
    import plotly.express as px
    from streamlit_option_menu import option_menu
    import plotly.graph_objects as go
    
# Step 2:

Data extraction:

Clone the Github using scripting to fetch the data from the Phonepe pulse Github repository and store it in a suitable format such as JSON. Use the below syntax to clone the phonepe github repository into your local drive.

    from git.repo.base import Repo
    Repo.clone_from("GitHub Clone URL","Path to get the cloded files")
    
Data transformation:

In this step the JSON files that are available in the folders are converted into the readeable and understandable DataFrame format by using the for loop and iterating file by file and then finally the DataFrame is created. In order to perform this step I've used os, json and pandas packages. And finally converted the dataframe into CSV file and storing in the local drive.   

    agg_col={'State':[],'Year':[],'Quarter':[],'Transaction_type':[],'Transaction_count':[],'Transaction_amount':[]}
    path="C:/Users/Sudharshan/Phonepe Pulse Data Visualization/data/aggregated/transaction/country/india/state/"
    agg_content=os.listdir(path)

Looping through each and every folder and opening the json files appending only the required key and values and creating the dataframe.    
  
     for i in agg_content:
          state_i=os.path.join(path,i)
    for j in os.listdir(state_i):
        year_j=os.path.join(state_i,j)
        for k in os.listdir(year_j):
            quat_k=os.path.join(year_j,k)
            with open(quat_k) as f:
                data=json.load(f)
                for L in data['data']['transactionData']:
                   Name=L['name']
                   Count=L['paymentInstruments'][0]['count']
                   Amount=L['paymentInstruments'][0]['amount']
                   agg_col['State'].append(i)
                   agg_col['Year'].append(j)
                   agg_col['Quarter'].append(int(k.strip('.json')))
                   agg_col['Transaction_type'].append(Name)
                   agg_col['Transaction_count'].append(Count)
                   agg_col['Transaction_amount'].append(Amount)
                                  
       agg_dataframe=pd.DataFrame(agg_col)       
  
# Converting the dataframe into csv file

     agg_dataframe.to_csv('agg_dataframe.csv',index=False)

# Step 4:

# Database insertion:

To insert the datadrame into SQL first I've created a new database and tables using "mysql-connector-python" library in Python to connect to a MySQL database and insert the transformed data using SQL commands.

Creating the connection between python and mysql

    host='localhost'
    user='root'
    password='Dharshan1996'
    port=3306
    database='phonepe_pulse'
    engine = create_engine(f'mysql+pymysql://{user}:{password}@{host}:{port}/{database}')
    connection=engine.connect()
    
Creating tables
    
    create_table_sql = """
    CREATE TABLE `aggregated_transaction` (
        `serial_no` INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
        `State` VARCHAR(255) NOT NULL,
        `Year` INT NOT NULL,
        `Quarter` INT NOT NULL,
        `Transaction_type` VARCHAR(255) NOT NULL,
        `Transaction_count` INT NOT NULL,
        `Transaction_amount` FLOAT NOT NULL
    );
    """

    connection.execute(text(create_table_sql))
    df_agg_trans = pd.read_csv("C:/Users/Sudharshan/Phonepe Pulse Data Visualization/agg_dataframe.csv")

    df_agg_trans.to_sql(name='aggregated_transaction', con=engine, if_exists='replace', index=False)
    
# Step 5:

Dashboard creation:

To create colourful and insightful dashboard I've used Plotly libraries in Python to create an interactive and visually appealing dashboard. Plotly's built-in Pie, Bar, Geo map functions are used to display the data on a charts and map and Streamlit is used to create a user-friendly interface with multiple dropdown options for users to select different facts and figures to display.

# Step 6:

Data retrieval:

Finally if needed Using the "pymysql&sqlalchemy" library to connect to the MySQL database and fetch the data into a Pandas dataframe.   
