Google Cloud has five core storage product solution: Cloud Storage, Cloud SQL, Spanner, Firestore, and Bigtable.

#### Cloud Storage 
1) Cloud Storage offers globally located, uniquely named buckets for storing data as immutable objects (packaged binary data like videos and photos). 
2) Objects are immutable any changes result in a new version.
3) You can track object changes with versioning and control access using IAM. Life cycle policies help manage storage costs automatically. It's ideal for online content and large binary files.
4) Cloud Storage offers four classes: **Standard** (frequent access), **Nearline** (infrequent, < monthly), **Coldline** (very infrequent, < quarterly), and **Archive** (rare, < yearly, higher access costs). 
5) All offer unlimited storage, global access, low latency, high durability, uniform experience (security, tools, APIs), and Geo-redundancy in multi/dual-regions.

#### Cloud SQL
1) Cloud SQL offers fully managed relational databases, including MySQL, PostgreSQL, and SQL Server as a service.
2) Cloud SQL encrypts customer data when on Google’s internal networks and when stored in database tables, temporary files, and backups.

#### Spanner
1) Spanner is a fully managed relational database service that scales horizontally, is strongly consistent, and speaks SQL.
2)  suited for applications that require a SQL relational database management system with joins and secondary indexes, built-in high availability, strong global consistency, and high numbers of input and output operations per second.

#### Firestore
1)  Firestore is a flexible, horizontally scalable, NoSQL cloud database for mobile, web, and server development.
2) With Firestore, data is stored in documents and then organized into collections.
3) Documents can contain complex nested objects in addition to sub-collections.
4) Each document contains a set of key-value pairs.

#### Bigtable
1) Bigtable is Google's NoSQL big data database designed for massive workloads with consistent low latency and high throughput.
2) It's suitable for applications needing to store and access large amounts of semi-structured or structured NoSQL data that may be fast-changing or time-series oriented.

![[Pasted image 20250509185155.png]]

