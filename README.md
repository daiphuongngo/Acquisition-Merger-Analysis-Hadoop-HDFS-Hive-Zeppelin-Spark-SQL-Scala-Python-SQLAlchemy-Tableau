# Acquisition-Merger-Analysis-Hadoop-HDFS-Hive-Zeppelin-Spark-SQL-Scala-Python-SQLAlchemy-Tableau

## Overview:

Investigating and identifying various organizations for the most profitable merger and acquisition by examining accumulated data sets.

## Platforms, Languages and Tools:

- Big Data's Cloud Environment: Hadoop, HDFS, Zeppelin, Spark, Scala, SQL

or

- MySQL Database's Virtual Environment: MySQL, SQL, Python, SQL Alchemy

and

- Tableau

## Dataset:

Firm.csv

| Firm_ID | Firm_Name |
|-|-|

Staff.csv

| Emp_ID | Name | Prefix | First_Name | Middle_Initial | Last_Name | Gender | E_Mail | Father_Name | Mother_Name | Mother_Maiden_Name | Date_of_Birth | Time_of_Birth | Age_in_Yrs | Weight_in_Kgs | Date_of_Joining | Quarter_of_Joining | Half_of_Joining | Year_of_Joining | Month_of_Joining | Month_Name_of_Joining | Short_Month | Day_of_Joining | DOW_of_Joining | Short_DOW | Age_in_Company_Years | Salary | SSN | Phone_No | Place_Name | County | City | State | Zip | Region | User_Name | Password | Firm_ID | 
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|

Sales.csv

| Order_Number | Quantity_Ordered | Price_of_Each_Order | Line_Number | Sales | Revenue | Order_Date | Status | Quarter_ID | Month_ID | Year_ID | Product_Line | MSRP | Product_Code | Customer_Name | Phone | Address_Line 1 | Address_Line 2 | City | State | Postal_Code | Country | Territory | Contact_Last_Name | Contact_First_Name | Deal_Size | Firm_ID | 
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|

## Input data:

### Big Data's Cloud Platform - Hadoop:

#### 1. Load data into HDFS

- Upload the Firm CSV file to a folder named 'tmp' and the Sales & Staff Csv files to a sub-folder in it

![File storage 1](https://user-images.githubusercontent.com/70437668/139749698-7351df77-5f7b-4661-829a-1cd90cf5959d.png)

#### 2. Create an external tables in Hive for 'Firm' CSV file:
```
CREATE EXTERNAL TABLE IF NOT EXISTS Company (
		CompanyID INT, Name STRING)
		COMMENT 'Date of company names'
		ROW FORMAT DELIMITED
		FIELDS TERMINATED BY ','
		STORED AS TEXTFILE
```

![Hive table 2](https://user-images.githubusercontent.com/70437668/139749686-5e87c061-a40a-4900-9261-4a7b159116f2.png)

#### 3. Load data of 2 CSV files into Zeppelin:

Create a sub-folder in the main folder 'tmp' to store the remaining 2 CSV files

##### 3.a. Sales:

```
%spark2
val Sales = (spark.read
		.option("header", "true") // Use first line as header
		.option("inferSchema", "true") // Infer Schema
		.csv("/tmp/Sales and Staff/Sales.csv")
```

##### 3.b. Sales:
```
%spark2
val Staff = (spark.read
		.option("header", "true") // Use first line as header
		.option("inferSchema", "true") // Infer Schema
		.csv("/tmp/Sales and Staff/Staff.csv")
```

#### 4. Print Scheme:
```
%spark2
sales.printSchema()
```

```
%spark2
employee.printSchema()
```

#### 5. Print out the data:
```
%spark2.sql
SELECT *
FROM EmpView
```

```
 %spark2.sql
SELECT *
FROM SalesView
```

#### 5. Analysis on Zeppelin:

##### 5.1. Revenue by Product Line:
```
SELECT Product_Line, Sum(Revenue) as Cumulated_Revenue 
FROM SalesView
GROUP BY Product_Line
```

##### 5.2. Sum Sales by Product Line & Firm Name
```
%spark2.sql
SELECT SUM(SalesView.Sales), SalesView.Product_Line, Firm.Firm_Name
FROM SalesView
JOIN Firm 
ON Firm.Firm_ID = SalesView.Firm_ID
WHERE Firm.Firm_ID < 11
GROUP BY SalesView.Product_Line
```

#### 5.3 Sum Revenue by Firm Name categorized by Car
```
%spark2.sql
SELECT SUM(SalesView.Revenue), Firm.Firm_Name 
FROM SalesView, Firm 
WHERE SalesView.Firm_ID = Firm.Firm_ID 
AND SalesView.Product_Line LIKE "%Cars%" 
GROUP BY Firm.Firm_Name  
ORDER BY SUM(SalesView.Sales) DESC
```

#### 5.4 Percentage of Salary to Revenue for each Firm by name
```
%spark2.sql
SELECT  SUM(SV.Revenue), SUM(EV.Salary), SUM(EV.Salary) / SUM(SV.Revenue)  * 100 AS Percent, F.Firm_Name 
FROM Salesview SV, Firm F, EmpView EV
WHERE SV.Firm_ID = EC.Firm_ID 
AND F.Firm_ID = EV.Firm_ID  
GROUP BY F.Firm_Name
ORDER BY Percent DESC

```

#### 5.5 
```
%spark2.sql
SELECT EV.Salary, EV.First_Name, EV.Last_Name, F.Firm_name  
FROM Firm F, EmpView EV
WHERE F.Firm_ID = EV.Firm_ID 
AND F.Firm_Name = "British Leyland"  
ORDER BY EV.Salary DESC

```

### MySQL Database:

#### 1. Create 3 tables for Firm, Sales, Staff: 

##### 1.1 Firm: 
```
CREATE TABLE `firm` (
  `Firm_ID` int(10) NOT NULL AUTO_INCREMENT,
  `Firm_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  PRIMARY KEY (`Firm_ID`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=utf16 COLLATE=utf16_general_ci
```

![Firm ss 1](https://user-images.githubusercontent.com/70437668/139750232-18705797-d3f5-4ef7-a694-a3e6fe93428a.jpg)

![Firm ss 2](https://user-images.githubusercontent.com/70437668/139750239-bbca03ec-c3e4-4c87-b7be-0151eae0253f.jpg)

##### 1.2 Staff:
```
CREATE TABLE `staff` (
  `Emp_ID` int(20) NOT NULL,
  `Name_Prefix` varchar(100) CHARACTER SET utf8 NOT NULL,
  `First_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Middle_Initial` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Last_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Gender` varchar(10) CHARACTER SET utf8 NOT NULL,
  `E_Mail` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Father_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Mother_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Mother_Maiden_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Date_of_Birth` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Time_of_Birth` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Age_in_Years` float NOT NULL,
  `Weight_in_Kgs` int(20) NOT NULL,
  `Date_of_Joining` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Quarter_of_Joining` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Half_of_Joining` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Year_of_Joining` int(10) NOT NULL,
  `Month_of_Joining` int(10) NOT NULL,
  `Month_Name_of_Joining` varchar(20) CHARACTER SET utf8 NOT NULL,
  `Short_Month` varchar(20) CHARACTER SET utf8 NOT NULL,
  `DOW_of_Joining` varchar(20) CHARACTER SET utf8 NOT NULL,
  `Short_DOW` varchar(20) CHARACTER SET utf8 NOT NULL,
  `Day_of_Joining` int(10) NOT NULL,
  `Age_in_Company_Years` float NOT NULL,
  `Salary` float NOT NULL,
  `SSN` varchar(30) CHARACTER SET utf8 NOT NULL,
  `Phone_No` varchar(30) CHARACTER SET utf8 NOT NULL,
  `Place_Name` varchar(30) CHARACTER SET utf8 NOT NULL,
  `County` varchar(30) CHARACTER SET utf8 NOT NULL,
  `City` varchar(30) CHARACTER SET utf8 NOT NULL,
  `State` varchar(30) CHARACTER SET utf8 NOT NULL,
  `Zip` int(10) NOT NULL,
  `Region` varchar(30) CHARACTER SET utf8 NOT NULL,
  `User_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Password` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Firm_ID` int(4) NOT NULL,
  `ID` int(10) NOT NULL,
  UNIQUE KEY `ID` (`ID`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf16 COLLATE=utf16_general_ci
```

![Staff ss 2](https://user-images.githubusercontent.com/70437668/139750279-0501b6ee-88a8-4958-a46f-fad97c1f40b0.jpg)

![Staff ss 1](https://user-images.githubusercontent.com/70437668/139750285-d7d2d024-db5a-41bd-98b6-875a69fa13bb.jpg)

##### 1.3 Sales:
```
CREATE TABLE `sales` (
  `Order_Number` int(10) NOT NULL,
  `Quantity_Ordered` int(5) NOT NULL,
  `Price_of_Each` float NOT NULL,
  `Order_Line_Number` int(5) NOT NULL,
  `Sales` float NOT NULL,
  `Revenue` float NOT NULL,
  `Order_Date` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Status` varchar(20) CHARACTER SET utf8 NOT NULL,
  `Quarter_ID` int(10) NOT NULL,
  `Month_ID` int(10) NOT NULL,
  `Year_ID` int(4) NOT NULL,
  `Product_Line` varchar(50) CHARACTER SET utf8 NOT NULL,
  `MSRP` int(10) NOT NULL,
  `Product_Code` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Customer_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Phone` varchar(20) CHARACTER SET utf8 NOT NULL,
  `Address_Line_1` varchar(100) CHARACTER SET utf8 NOT NULL,
  `Address_Line_2` varchar(100) CHARACTER SET utf8 NOT NULL,
  `City` varchar(30) CHARACTER SET utf8 NOT NULL,
  `State` varchar(30) CHARACTER SET utf8 NOT NULL,
  `Postal_Code` varchar(20) CHARACTER SET utf8 NOT NULL,
  `Country` varchar(30) CHARACTER SET utf8 NOT NULL,
  `Territory` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Contact_Last_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Contact_First_Name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `Deal_Size` varchar(20) CHARACTER SET utf8 NOT NULL,
  `Firm_ID` int(4) NOT NULL,
  `ID` int(10) NOT NULL,
  UNIQUE KEY `ID` (`ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_general_ci
```

![Sales ss 2](https://user-images.githubusercontent.com/70437668/139750255-c716579d-122a-4e29-a596-04a226e27664.jpg)

![Sales ss 1](https://user-images.githubusercontent.com/70437668/139750265-686c8332-2239-4a6f-8f55-ece70b4b4c85.jpg)

#### 2. Insert dataset into MySQL Databse on TablePlus with SQLAlchemy in Python

#### 2.1 Start a Virtual Environment on Jupyter Lab
```
cd C:\Programming\CustomerIntention\venv\Scripts
C:\Programming\CustomerIntention\venv\Scripts>activate
cd ..
cd ..
(venv) C:\Programming\CustomerIntention>cd src\notebook
(venv) C:\Programming\CustomerIntention>cd src\notebook\jupyter lab
```

![Command Prompt SS](https://user-images.githubusercontent.com/70437668/139750836-fb142957-e22e-4cf9-81b5-0527f529077d.jpg)


#### 2.2 SQLAlchemy in Python on Jupyter Lab

##### 2.2.1 Load data

##### 2.2.2 Create an engine to access the localhost created in the Command Prompt run as administrator

```
from sqlalchemy import create_engine
engine = create_engine('mysql+mysqldb://phuongdaingo:0505@localhost:3306/customerintention?charset=utf8mb4', echo=True) 
```

##### 2.2.3 Design 3 tables

```
from sqlalchemy import Column, String, Integer, ForeignKey, DateTime, func, Boolean, MetaData, Table, Float
from sqlalchemy.dialects.mysql import TINYINT
from sqlalchemy.orm import relationship, backref
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base() 
metadata = MetaData(bind=engine) 

class Firm(Base):
    __tablename__ = Table('firm', Base.metadata,
                    autoload=True, autoload_with=engine) # metadata comes from database
    # Database (TablePlus) will regularize PK, Python won't do so (primary_key=True) since this is for mapping tables only. 
    # If Python is used for creating tables, we will need ID as a PK so 'primary_key=True' will be included.
    Firm_ID = Column(Integer, primary_key=True) 
    Firm_Name = Column(String())
    
class Staff(Base):
    __tablename__ = Table('staff', Base.metadata,
                    autoload=True, autoload_with=engine) 
    ID = Column(Integer, primary_key=True)
    Emp_ID = Column(Integer)
    Name_Prefix = Column(String())
    First_Name = Column(String())
    Middle_Initial = Column(String())
    Last_Name = Column(String())
    Gender = Column(String())
    E_Mail = Column(String())
    Father_Name = Column(String())
    Mother_Name = Column(String())
    Mother_Maiden_Name = Column(String())
    Date_of_Birth = Column(String())
    Time_of_Birth = Column(String)
    Age_in_Years = Column(Float())
    Weight_in_Kgs = Column(Integer())
    Date_of_Joining = Column(String)
    Quarter_of_Joining = Column(String())
    Half_of_Joining = Column(String())
    Year_of_Joining = Column(Integer())
    Month_of_Joining = Column(Integer())
    Month_Name_of_Joining = Column(String())
    Short_Month	= Column(String())
    DOW_of_Joining = Column(String())
    Short_DOW = Column(String())
    Day_of_Joining = Column(Integer())
    Age_in_Company_Years = Column(Float())
    Salary = Column(Float())
    SSN = Column(String())
    Phone_No = Column(String())
    Place_Name = Column(String())
    County = Column(String())
    City = Column(String())
    State = Column(String())
    Zip = Column(Integer)	
    Region = Column(String())
    User_Name = Column(String())
    Password = Column(String())
    Firm_ID = Column(Integer)	
    
class Sales(Base):
    __tablename__ = Table('sales', Base.metadata,
                    autoload=True, autoload_with=engine)
    ID = Column(Integer, primary_key=True)
    Order_Number = Column(Integer)
    Quantity_Ordered = Column(Integer)
    Price_of_Each = Column(Float)
    Order_Line_Number = Column(Integer)	
    Sales = Column(Float)
    Revenue	= Column(Float)
    Order_Date = Column(String)
    Status = Column(String)
    Quarter_ID = Column(Integer)		
    Month_ID = Column(Integer)	
    Year_ID	= Column(Integer)	
    Product_Line = Column(String)	
    MSRP = Column(Integer)
    Product_Code = Column(String)	
    Customer_Name = Column(String)
    Phone = Column(String)	
    Address_Line_1 = Column(String)
    Address_Line_2 = Column(String)
    City = Column(String)
    State = Column(String)
    Postal_Code	= Column(Integer)
    Country	= Column(String)
    Territory = Column(String)	
    Contact_Last_Name = Column(String)
    Contact_First_Name = Column(String)
    Deal_Size = Column(String)
    Firm_ID = Column(Integer)
    
# Mapping classes with tables in TablePlus's databases
# Should not create tables by Python but TablePlus
from sqlalchemy.orm import sessionmaker
#Session = sessionmaker()
#Session.configure(bind=engine)
Session = sessionmaker(bind=engine) # writing queries requires session before executing queries
session = Session() # object
#Base.metadata.create_all(engine)
```

##### 2.2.4 Insert all rows of each dataframe to database's tables in TablePlus's MySQL Database

New Method: inserting directly from data frames

Inserting takes long time means that selecting or filtering will take less time, and in reverse, due to adding IDX for a column or different columns depending on purposes of saving data into relational database only or reading the data.

I will insert dataframes in batches into session (relational database), then commit to finalize saving. If an error happen, that batch will be stopped inserting and still stay in the session and other batches will not be entered into the session as well if flush() is placed outside 'for loop'. Therefore, if flush() is placed inside the for loop, batches will be flushed into the session regarless any error might occur. But we have to set rollback() in the except case to delete any existing batches in the session causing an error.

###### 2.2.4.1 Insert 'df_Firm' dataframe into 'firm' table in the MySQL databse
```
import time
#import mysql.connector # as below mysql, not sqlite3 for this case
import traceback
from tqdm import tqdm
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String,  create_engine # use sqlalchemy with connection string for mysql
from sqlalchemy.orm import scoped_session, sessionmaker
import unicodedata 

Base = declarative_base()
DBSession = scoped_session(sessionmaker()) # the scoped_session() function is provided which produces a thread-managed registry of Session objects. It is commonly used in web applications so that a single global variable can be used to safely represent transactional sessions with sets of objects, localized to a single thread.
engine = None

def init_sqlalchemy(dbname='mysql+mysqldb://phuongdaingo:0505@localhost:3306/customerintention?charset=utf8mb4'):
    global engine
    engine = create_engine(dbname, echo=False)
    DBSession.remove()
    DBSession.configure(bind=engine, autoflush=False, expire_on_commit=False)
    Base.metadata.drop_all(engine)
    Base.metadata.create_all(engine)

def firm_sqlalchemy_orm(n=1001): 
    init_sqlalchemy()
    t0 = time.time() 
    error_i_list = [] # a new list containing i(s) of batch(es) causing errors
    
    for i in tqdm(range(n)): # use tqdm to track progress 
        try: # create custome, then add into session
            firm = Firm()
            firm.Firm_ID = df_Firm['Firm_ID'].iloc[i]
            firm.Firm_Name = df_Firm['Firm_Name'].iloc[i]
            DBSession.add(firm) # error might happen here or below
            DBSession.commit()
            
            if i % 100 == 0: # when i reachs 100 rows, it will execute by flush() to insert the batch of 100 rows into the session of the relational database
            #DBSession.flush() # should use try, except inside each 'for loop' to wrap i # error might happen here
                DBSession.commit() #2nd attempt: place commit() here, then compare the progress # commit here to insert batch without affecting other batch(es) with errors
                #text = unicodedata.normalize('NFC', text) # text: string type to fix error and replace all string texts into being wrapped by unicode 
                
        except Exception as er:
            print('Error at index {}: '.format(i))
            print(traceback.format_exc()) # print error(s)
            print('-' * 20)
            DBSession.rollback() # rollback here to delete all rows of a batch/batches causing errors to avoid being flooded or stuck with new batches coming in and then getting stuck as well
            error_i_list.append(i) # append into array the index of batch(es) causing errors 
   # DBSession.commit()  # 1st attempt: place commit() here, outside of 'for loop' # faster but will stop other batches coming in if errors happen
    
    print(
        "Firm's SQLAlchemy ORM: Total time for " + str(n) +
        " records " + str(time.time() - t0) + " secs")

if __name__ == '__main__':
    firm_sqlalchemy_orm(df_Firm.shape[0])
```

###### 2.2.4.2 Insert 'df_Sales' dataframe into 'sales' table in the MySQL database

NaN values appeared in the column State because many countries don't have states like the US so I had replaced them to 'None'.

nan values appeared in the column Territory because of 'NA' standing for 'North America' so I had replaced it with 'N.A'.

Both the Sales and Staff tables didn't have their own ID columns so I had to created one for each as the Primary Key. However, there were some rows in Sales were collapsed. Then I went to Data > Ungroup > Clear Outline to expand all collapsed rows. After that, I fill auto-increment values for ID column by rename the ID column by ROW() for cell A1. Cell A2 will be =ROW(A1) and I copied A2's formula for the rest of cells to get all ID rows filled with unique numbers.

I had set up the DateTime typed columns in both files by timestamp in the MySQL Database. However, there was a problem with doing so although this method is correct with other datasets with the same value. Then I had to change all timestamp type in MySQL into varchar and DateTime(timezone=True)) in SQLAlchemy into String().

```
import time
#import mysql.connector # as below mysql, not sqlite3 for this case
import traceback
from tqdm import tqdm
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String,  create_engine # use sqlalchemy with connection string for mysql
from sqlalchemy.orm import scoped_session, sessionmaker

Base = declarative_base()
DBSession = scoped_session(sessionmaker()) # the scoped_session() function is provided which produces a thread-managed registry of Session objects. It is commonly used in web applications so that a single global variable can be used to safely represent transactional sessions with sets of objects, localized to a single thread.
engine = None

def init_sqlalchemy(dbname='mysql+mysqldb://phuongdaingo:0505@localhost:3306/customerintention?charset=utf8mb4'):
    global engine
    engine = create_engine(dbname, echo=False)
    DBSession.remove()
    DBSession.configure(bind=engine, autoflush=False, expire_on_commit=False)
    Base.metadata.drop_all(engine)
    Base.metadata.create_all(engine)

def sales_sqlalchemy_orm(n=100000): 
    init_sqlalchemy()
    t0 = time.time() 
    error_i_list = [] # a new list containing i(s) of batch(es) causing errors
    # Index column must match with ID column of df_Firm  > indexing to the row 10th iso using loop with iterows (time consuming), but by using range(df.rows) > take out the 10th row
    for i in tqdm(range(n)): # use tqdm to track progress 
        try: # create custome, then add into session
            sales = Sales()
            sales.ID = df_Sales['ID'].iloc[i]
            sales.Order_Number = df_Sales['Order_Number'].iloc[i]
            sales.Quantity_Ordered = df_Sales['Quantity_Ordered'].iloc[i]
            sales.Price_of_Each = df_Sales['Price_of_Each'].iloc[i]
            sales.Order_Line_Number = df_Sales['Order_Line_Number'].iloc[i]
            sales.Sales = df_Sales['Sales'].iloc[i]
            sales.Revenue = df_Sales['Revenue'].iloc[i]
            sales.Order_Date = df_Sales['Order_Date'].iloc[i]
            sales.Status = df_Sales['Status'].iloc[i]
            sales.Quarter_ID = df_Sales['Quarter_ID'].iloc[i]	
            sales.Month_ID = df_Sales['Month_ID'].iloc[i]
            sales.Year_ID = df_Sales['Year_ID'].iloc[i]
            sales.Product_Line = df_Sales['Product_Line'].iloc[i]
            sales.MSRP = df_Sales['MSRP'].iloc[i]
            sales.Product_Code = df_Sales['Product_Code'].iloc[i]
            sales.Customer_Name = df_Sales['Customer_Name'].iloc[i]
            sales.Phone = df_Sales['Phone'].iloc[i]
            sales.Address_Line_1 = df_Sales['Address_Line_1'].iloc[i]
            sales.Address_Line_2 = df_Sales['Address_Line_2'].iloc[i]
            sales.City = df_Sales['City'].iloc[i]
            sales.State = df_Sales['State'].iloc[i]
            sales.Postal_Code = df_Sales['Postal_Code'].iloc[i]
            sales.Country = df_Sales['Country'].iloc[i]
            sales.Territory = df_Sales['Territory'].iloc[i]	
            sales.Contact_Last_Name = df_Sales['Contact_Last_Name'].iloc[i]
            sales.Contact_First_Name = df_Sales['Contact_First_Name'].iloc[i]
            sales.Deal_Size = df_Sales['Deal_Size'].iloc[i]
            sales.Firm_ID = df_Sales['Firm_ID'].iloc[i]
            
            DBSession.add(sales) # error might happen here or below
            if i % 100 == 0: # when i reachs 100 rows, it will execute by flush() to insert the batch of 100 rows into the session of the relational database
                DBSession.flush() # should use try, except inside each 'for loop' to wrap i # error might happen here
                DBSession.commit() #2nd attempt: place commit() here, then compare the progress # commit here to insert batch without affecting other batch(es) with errors
        except Exception as er:
            print('Error at index {}: '.format(i))
            print(traceback.format_exc()) # print error(s)
            print('-' * 20)
            DBSession.rollback() # rollback here to delete all rows of a batch/batches causing errors to avoid being flooded or stuck with new batches coming in and then getting stuck as well
            error_i_list.append(i) # append into array the index of batch(es) causing errors 
   # DBSession.commit()  # 1st attempt: place commit() here, outside of 'for loop' # faster but will stop other batches coming in if errors happen
    
    print(
        "Sales's SQLAlchemy ORM: Total time for " + str(n) +
        " records " + str(time.time() - t0) + " secs")

if __name__ == '__main__':
    sales_sqlalchemy_orm(df_Sales.shape[0]) 
```

###### 2.2.4.3 Insert 'df_Staff' dataframe into 'staff' table in the MySQL database

```
import time
#import mysql.connector # as below mysql, not sqlite3 for this case
import traceback
from tqdm import tqdm
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String,  create_engine # use sqlalchemy with connection string for mysql
from sqlalchemy.orm import scoped_session, sessionmaker
import unicodedata 

Base = declarative_base()
DBSession = scoped_session(sessionmaker()) # the scoped_session() function is provided which produces a thread-managed registry of Session objects. It is commonly used in web applications so that a single global variable can be used to safely represent transactional sessions with sets of objects, localized to a single thread.
engine = None

def init_sqlalchemy(dbname='mysql+mysqldb://phuongdaingo:0505@localhost:3306/customerintention?charset=utf8mb4'):
    global engine
    engine = create_engine(dbname, echo=False)
    DBSession.remove()
    DBSession.configure(bind=engine, autoflush=False, expire_on_commit=False)
    Base.metadata.drop_all(engine)
    Base.metadata.create_all(engine)
    
def staff_sqlalchemy_orm(n=100000): 
    init_sqlalchemy()
    t0 = time.time() 
    error_i_list = [] # a new list containing i(s) of batch(es) causing errors
    # Index column must match with ID column of df_Firm > indexing to the row 10th iso using loop with iterows (time consuming), but by using range(df.rows) > take out the 10th row
    for i in tqdm(range(n)): # use tqdm to track progress 
        try: 
            staff = Staff()
            staff.ID = df_Staff['ID'].iloc[i]
            staff.Emp_ID = df_Staff['Emp_ID'].iloc[i]
            staff.Name_Prefix = df_Staff['Name_Prefix'].iloc[i]
            staff.First_Name = df_Staff['First_Name'].iloc[i]
            staff.Middle_Initial = df_Staff['Middle_Initial'].iloc[i]
            staff.Last_Name = df_Staff['Last_Name'].iloc[i]
            staff.Gender = df_Staff['Gender'].iloc[i]
            staff.E_Mail = df_Staff['E_Mail'].iloc[i]
            staff.Father_Name = df_Staff['Father_Name'].iloc[i]
            staff.Mother_Name = df_Staff['Mother_Name'].iloc[i]
            staff.Mother_Maiden_Name = df_Staff['Mother_Maiden_Name'].iloc[i]
            staff.Date_of_Birth = df_Staff['Date_of_Birth'].iloc[i]
            staff.Time_of_Birth = df_Staff['Time_of_Birth'].iloc[i]
            staff.Age_in_Years = df_Staff['Age_in_Years'].iloc[i]
            staff.Weight_in_Kgs = df_Staff['Weight_in_Kgs'].iloc[i]
            staff.Date_of_Joining = df_Staff['Date_of_Joining'].iloc[i]
            staff.Quarter_of_Joining = df_Staff['Quarter_of_Joining'].iloc[i]
            staff.Half_of_Joining = df_Staff['Half_of_Joining'].iloc[i]
            staff.Year_of_Joining = df_Staff['Year_of_Joining'].iloc[i]
            staff.Month_of_Joining = df_Staff['Month_of_Joining'].iloc[i]
            staff.Month_Name_of_Joining = df_Staff['Month_Name_of_Joining'].iloc[i]
            staff.Short_Month = df_Staff['Short_Month'].iloc[i]
            staff.DOW_of_Joining = df_Staff['DOW_of_Joining'].iloc[i]
            staff.Short_DOW = df_Staff['Short_DOW'].iloc[i]
            staff.Day_of_Joining = df_Staff['Day_of_Joining'].iloc[i]
            staff.Age_in_Company_Years = df_Staff['Age_in_Company_Years'].iloc[i]
            staff.Salary = df_Staff['Salary'].iloc[i]
            staff.SSN = df_Staff['SSN'].iloc[i]
            staff.Phone_No = df_Staff['Phone_No'].iloc[i]
            staff.Place_Name = df_Staff['Place_Name'].iloc[i]
            staff.County = df_Staff['County'].iloc[i]
            staff.City = df_Staff['City'].iloc[i]
            staff.State = df_Staff['State'].iloc[i]
            staff.Zip = df_Staff['Zip'].iloc[i]
            staff.Region = df_Staff['Region'].iloc[i]
            staff.User_Name = df_Staff['User_Name'].iloc[i]
            staff.Password = df_Staff['Password'].iloc[i]
            staff.Firm_ID = df_Staff['Firm_ID'].iloc[i]
            
            DBSession.add(staff) # error might happen here or below
            if i % 100 == 0: # when i reachs 1000 rows, it will execute by flush() to insert the batch of 1000 rows into the session of the relational database
                DBSession.flush() # should use try, except inside each 'for loop' to wrap i # error might happen here
                DBSession.commit() #2nd attempt: place commit() here, then compare the progress # commit here to insert batch without affecting other batch(es) with errors
        except Exception as er:
            print('Error at index {}: '.format(i))
            print(traceback.format_exc()) # print error(s)
            print('-' * 20)
            DBSession.rollback() # rollback here to delete all rows of a batch/batches causing errors to avoid being flooded or stuck with new batches coming in and then getting stuck as well
            error_i_list.append(i) # append into array the index of batch(es) causing errors 
   # DBSession.commit()  # 1st attempt: place commit() here, outside of 'for loop' # faster but will stop other batches coming in if errors happen
    
    print(
        "Staff's SQLAlchemy ORM: Total time for " + str(n) +
        " records " + str(time.time() - t0) + " secs")

if __name__ == '__main__':
    staff_sqlalchemy_orm(df_Staff.shape[0]) # number of rows of df as I want --> customized function name
```

## Visualization

### Total Sales by Product Line

![Total Sales by Product Line](https://user-images.githubusercontent.com/70437668/139526107-6ee7c0f3-44e9-4165-95c0-6c6813a0b0c4.jpg)

### Total Sales by Firm Name colored by Product Line

![Total Sales by Firm Name colored by Product Line](https://user-images.githubusercontent.com/70437668/139526104-89c67f37-d8ff-4d2b-9d65-fa53e9f6c154.jpg)

### Total Sales by Firm Name and Product Line

![Total Sales by Firm Name and Product Line](https://user-images.githubusercontent.com/70437668/139526100-b9141193-da74-43d8-bdea-d8e2984db625.jpg)

### Total Revenue by Firm Name

![Total Revenue by Firm Name](https://user-images.githubusercontent.com/70437668/139526095-8d6f0f1d-5138-41b1-81f2-de4b19094cd1.jpg)

### Revenue Distribution by Product Line

![Revenue Distribution by Product Line](https://user-images.githubusercontent.com/70437668/139526093-714841ef-c2f8-4b46-b599-9c079901796c.jpg)

### Radar Chart Sum Sales by Product Line & Firm Name

![Radar Chart Sum Sales by Product Line   Firm Name](https://user-images.githubusercontent.com/70437668/139621278-a30cfe87-8b26-40a2-b738-1a8bfa89004a.jpg)

### Dashboard - Sales, Revenue by Firm, Product Line

![Dashboard - Sales, Revenue by Firm, Product Line](https://user-images.githubusercontent.com/70437668/139526090-327daecd-fd93-4ad3-a2e7-e79ff10487a2.jpg)

### Dashboard - Sum Sales by Firm, Product Line

![Dashboard - Sum Sales by Firm, Product Line](https://user-images.githubusercontent.com/70437668/139621296-820ee1d0-0314-48a9-87ec-d87ff038d324.jpg)

### Dashboard - Distribution by Sales & Revenue

![Dashboard - Distribution by Sales   Revenue](https://user-images.githubusercontent.com/70437668/139621303-d7bd0065-1456-448b-83bd-0bf9bfe7aeeb.jpg)

