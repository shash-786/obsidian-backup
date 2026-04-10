
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
![[DBMS_Architecture.png]]

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

