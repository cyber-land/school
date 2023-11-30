- Make the database more **efficient**
- Prevent the same data from being stored in **more than one place** (called an “insert anomaly”)
- Prevent updates being made to **some data but not others** (called an “update anomaly”)
- Prevent data not being deleted when it is supposed to be, or from data being lost when it is not supposed to be (called a “delete anomaly”)
- Ensure the data is **accurate**
- Reduce the **storage space** that a database takes up
- Ensure the **queries** on a database run as fast as possible

# dipendenze funzionali
TODO: definizione formale di dipendenza funzionale
graph vs algorithm

# definizione
prodotto tramite decomposizione ma senza perdita di informazioni
gli attributi comuni (per fare il join devono essere chiave di almeno una delle due tabelle)

# First Normal Form
- Each table cell should contain a single value
- Each record needs to be unique

- non ha attributi composti
- non ha attributi multi-valore
- non avere relazioni annidate
- non avere tuple ripetute
molti DBMS non permettono nemmeno queste eventualità

primary key attributes:
- cannot be NULL
- must be unique
- must be given a value when a new record is inserted
A **composite key** is a primary key composed of multiple columns used to identify a record uniquely

# Second Normal Form
- Be in 1NF
- Single Column Primary Key that does not functionally dependent on any subset of candidate key relation

foreign key references the primary key of another table, used to connect tables
- A foreign key can have a different name from its primary key
- It ensures rows in one table have corresponding rows in another
- Unlike the Primary key, they do not have to be unique (most often they aren’t)
- Foreign keys can be null even though primary keys can not

# Third Normal Form
- Be in 2NF
- Has no transitive functional dependencies

A transitive functional dependency is when changing a non-key column, might cause any of the other non-key columns to change

