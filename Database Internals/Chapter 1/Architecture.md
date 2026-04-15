### DBMS taxonomy

**OLTP** (Online Transaction Processing Database)
	Handles large number of user facing requests and transactions. Queries are predefined and short lived. Eg. payment processing, checkout, inventory management, etc. Common solutions are **PostgresSQL**, **MySQL**

**OLAP** (Online Analytical Processing Databases)
	Complex aggregation. OLAP are often used for analytics and data warehousing, capable of handling complex, long-running ad hoc queries. Used for high-speed, complex analysis of massive datasets, rather than daily transaction processing. Common Solutions include **Amazon Redshift**, **Clickhouse**, **Snowflake**

**HTAP** (Hybrid Transactional and Analytical Processing)
	 Combining both OLTP and OLAP stores.


### Types of Stores

###### Row Based Store
Suppose we have a user dataset with info like *name*, *userID*, *email*, *phoneNumber*, *AccountCreationTime* etc stored. One possible way to layout the data on the disk is each record of a single user laid as is. Thought of implementing is if you need a particular feature of a particular record you might need other features as well from the same record.

###### Column Based Store
In the same dataset suppose we want to do analysis on the  *AccountCreationTime* of the user for the past year for Business analytics. If we parse each record we will have a lot of unecessary information. So one possible way to layout data could be like this. 

*Shash* *Shash1* *Shash2* *1@foo.com* *2@bar.com* *3@baz.com* 02/01/2025 x 2 04/01/2025
where the **x 2** could be RLE (run length encoding)

### DBMS Architecture
![[DBMS_Architecture.png|394]]

###### Transaction Manager
It also schedules the transactions. Basically will make sure the connection requests see a "consistent view" of the database and also that when these transactions are complete the DB is not inconsistent. 

###### Lock Manager
As name suggests a concurrency manager managing locks on the Database objects ensuring concurrent operations do not violate physical data integrity.

###### Access Layer (CRUD Layer)
These manage access and organising data on the disk. 

###### Buffer Manager
Performance Optimisation. Caches data pages accessed from the "Access layer" and also handles eviction of cached pages. 

###### Recovery Manager
Maintains the operation logs and restores the system in case of a crash. 


### Data Files and Index Files

#### **Data Files** 
Data files are essentially the data records. It contains the actual rows of the table.
Types of Data files:
	1) IOT (index organised tables) **(Clustered index read below)**
	2) HOT (heap organised tables) **(Just append records (require additional indexing))**
	3) HOT (hash organised tables) **records are stored in buckers and hash value of key determines which bucket a record belongs to**

#### **Index File** 

##### What is an Index? 
An index is an auxiliary data structure that allows to efficiently locate records without scanning the entire table on every access. Indexes are built using a subset of fields identifying the record.

Index file is an separate structure built using B+ trees. It may or may not contain the actual records but it for sure contains keys. 

Primary Index is index built on primary key or set of keys identified as primary. All other indexes are called secondary. Secondary index can point to the data record or the primary key.

*Non-clustered Index* : Basically think of it as B+ Tree based on a primary key (UID) with the leaf storing the pointer to the actual record or list of records to the data file

*Clustered Index* : The only distinction here is the leaf node will contain the actual record itself. In this case **data file = index file**


###### Primary index as Redirection 
Updates as efficient in terms of actual data pointers being updated. Since the Primary index would be `primary key -> pointer` and all other secondary indexes will only reference the primary key, the pointer is only updated at one location. But this comes at the cost of another disk seek

###### No Indirection
The additional disk seek is not needed but there during updates if there are multiple indexes then the pointer needs to be updated on all the indexes. So read efficient but not update efficient.

