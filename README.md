# Data Visualization and Exploration : A User-Friendly Tool Using Streamlit and Plotly

# What is PhonePe Pulse?

The PhonePe Pulse website showcases more than 2000+ Crore transactions by consumers on an interactive map of India. With over 45% market share, PhonePe's data is representative of the country's digital payment habits. The insights on the website and in the report have been drawn from two key sources - the entirety of PhonePe's transaction data combined with merchant and customer interviews. The report is available as a free download on the PhonePe Pulse website and GitHub.

Demo video of my project - https://www.linkedin.com/posts/activity-7065317438770319360-DJ7V?utm_source=share&utm_medium=member_desktop

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

    import pymysql
    import pandas as pd
    import sqlalchemy
    from sqlalchemy import text
    import socket
    import os
    from os import walk
    from pathlib import Path
    import pandas as pd
    from git.repo.base import Repo

# Step 2:

Data extraction:

Clone the Github using scripting to fetch the data from the Phonepe pulse Github repository and store it in a suitable format such as JSON. Use the below syntax to clone the phonepe github repository into your local drive.

    from git.repo.base import Repo
    Repo.clone_from("GitHub Clone URL","Path to get the cloded files")
    
Data transformation:

In this step, dataframe has been converte into the readeable and understandable dataframe format by using this syntax.And finally converted the dataframe into CSV file and storing in the local drive.

      Data_Aggregated_Transaction_Table = pd.DataFrame({}) 
      Data_Aggregated_Transaction_Summary_Table = pd.DataFrame({}) 


Looping through each and every folder and opening the json files appending only the required key and values and creating the dataframe.    
        
       def Aggregated_Transaction_Table_fun(state,year,quarter,path):
       global Data_Aggregated_Transaction_Table
       global Data_Aggregated_Transaction_Summary_Table
       dft = pd.read_json(path)
    
       dataFrom=dft['data']['from']
       dataTo=dft['data']['to'] 
       T_row={'State':state,'Year': year,'Quarter':quarter,'Data From':dataFrom,'Data To':dataTo}
       Data_Aggregated_Transaction_Summary_Table=Data_Aggregated_Transaction_Summary_Table.append(T_row,ignore_index = True)
    
       DAT_temp=dft['data']['transactionData']
       if DAT_temp:      
        for i in DAT_temp:
            DAT_row={ 'Payment Mode':i['name'], 'Total Transactions count':i['paymentInstruments'][0]['count'], 'Total Amount':i['paymentInstruments'][0]                             ['amount'],'Quarter':quarter,'Year': year,'State':state}  
            Data_Aggregated_Transaction_Table = Data_Aggregated_Transaction_Table.append(DAT_row, ignore_index = True)
            
PATH FOR ALL STATES IN AGGREGATED TRANSACTIONS
       t_s= r"C:/Users/Sudharshan/Phonepe Pulse Data Visualization\data\aggregated\transaction\country\india\state"
       t_path = r"C:/Users/Sudharshan/Phonepe Pulse Data Visualization\data\aggregated\transaction\country\india\state"
       t_states = os.listdir(t_path) # NAMES OF ALL DIRECTORIES IN STATES (36 STATES)

      for i in t_states:
             #print(i)                  
             p=t_s+'\\'+i                      
             states_year=os.listdir(p)        
      for j in states_year:             
             #print(j)
             pt=p+'\\'+j                    
             f=[]
      for (dirpath, dirnames, filenames) in walk(pt):
            f.extend(filenames)         
            break
      for k in f:                    
            fp=pt+'\\'+k               
            fn=Path(fp).stem           
            #print(i,j,fn)
            Aggregated_Transaction_Table_fun(i,j,fn,fp) 
            #print(fp)             
                
# Converting the dataframe into csv file

      Data_Aggregated_Transaction_Table.to_csv('Data_Aggregated_Transaction_Table.csv',index=False)

# Step 4:

# Database insertion:

To insert the datadrame into SQL first I've created a new database and tables using "mysql-connector-python" library in Python to connect to a MySQL database and insert the transformed data using SQL commands.

Creating the connection between python and MySQL

       user = 'root'
       password = 'Dharshan1996'
       host = 'localhost'
       port = 3306
       database = 'phonepe_pulse'
       connection = sqlalchemy.create_engine("mysql+pymysql://{0}:{1}@{2}:{3}/{4}".format(user, password, host, port, database))
    
Creating tables
         
         sql = text('CREATE TABLE Data_Aggregated_Transaction_Table1 (MyIndex INT NOT NULL AUTO_INCREMENT,Payment_Mode VARCHAR(50),Total_Transactions_count                            BIGINT,Total_Amount BIGINT,Quater INT,Year INT,State INT,PRIMARY KEY (MyIndex))')
         connection.execute(sql)
         sql = text("use phonepe_pulse")
         connection.execute(sql)
         df = pd.read_csv("C:/Users/Sudharshan/Phonepe Data Extraction/Data_Aggregated_Transaction_Table1.csv")
         df.to_sql('data_aggregated_transaction_table',con=connection, if_exists= "replace",index=False, chunksize=10000)
   
# Step 5:

Dashboard creation:

To create colourful and insightful dashboard I've used Plotly libraries in Python to create an interactive and visually appealing dashboard. Plotly's built-in Pie, Bar, Geo map functions are used to display the data on a charts and map and Streamlit is used to create a user-friendly interface with multiple dropdown options for users to select different facts and figures to display.

# Step 6:

Data retrieval:

Finally if needed Using the "pymysql" library to connect to the MySQL database and fetch the data into a Pandas dataframe.   
