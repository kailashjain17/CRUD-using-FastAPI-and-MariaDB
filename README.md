
# CRUD using FastAPI and MariaDB Docker Container 

Explanation and Step by Step Process of achieving CRUD application using FastAPI and MariaDB Docker Container.

This file contains the following operations 

    1. CREATES A TABLE
    2. READ VALUES FROM THE TABLE
    3. UPDATE VALUES IN THE TABLE
    4. DELETE A PARTICULAR VALUE FROM THE TABLE


### Code:

    from fastapi import HTTPException, FastAPI
    from pydantic import BaseModel
    from databases import Database

    #Creates an instance of FastAPI
    app = FastAPI() 

    #Database url for connection
    database_url = "mysql+async://root:my-secret-pw@localhost:3307/mydatabase"

    #Create a Database instance
    database = Database(database_url)

    #Class to define the variable of the expected data in a structured way
    class UserData(BaseModel):
        name: str
        age: int
        age_condition: int

    async def connect_to_database():
        await database.connect()

    async def close_database_connection():
        await database.connect()

    #API Endpoint to create a new table
    @app.post("/create_table")
    async def create_and_insert(table_name: str, user_data: UserData):
        # Construct SQL query to create table and insert data
        query = f"CREATE TABLE IF NOT EXISTS {table_name} (name VARCHAR(255), age INT);"
        values = {"name": user_data.name, "age": user_data.age}

        try:
            # Connect to the database
            await connect_to_database()

            # Create the table if it doesn't exist
            await database.execute(query)

            return {"message": "Table created and data inserted successfully"}
        except Exception as e:
            raise HTTPException(status_code=500, detail=str(e))
        finally:
            # Disconnect from the database
            await close_database_connection()

    #API Endpoint to Add an Entry into a Specific Table
    @app.post("/insert_table_info")

    async def add_info(table_name: str, user_data: UserData):
        insert_query = f"INSERT INTO {table_name} (name, age) VALUES (:name, :age);"
        values = {"name": user_data.name, "age": user_data.age}

        try:
            # Connect to the database
            await connect_to_database()

            await database.execute(insert_query, values)

            return {"message": "Data Inserted Successfully"}
        
        except Exception as e:
            raise HTTPException(status_code=500, detail=str(e))
        finally:
            # Disconnect from the database
            await close_database_connection()


    #API Endpoint to delete an entry from a table based on name and age
    @app.delete("/delete_entry")
    async def delete_entry(table_name: str, user_data: UserData):
        # Construct SQL query to delete entry based on name and age
        query = f"DELETE FROM {table_name} WHERE name = :name AND age = :age;"
        values = {"name": user_data.name, "age": user_data.age}

        try:
            # Connect to the database
            await connect_to_database()

            # Execute the query to delete the entry based on name and age
            await database.execute(query, values)

            return {"message": f"Entry with name '{user_data.name}' and age {user_data.age} deleted successfully from table {table_name}"}
        except Exception as e:
            raise HTTPException(status_code=500, detail=str(e))
        finally:
            # Disconnect from the database
            await close_database_connection()

    #API Endpoint to Read Data from a Table
    @app.get("/get_users")
    async def get_users(table_name: str):
        query = f"SELECT * FROM {table_name}"

        try:
            await connect_to_database()
            users = await database.fetch_all(query)
            return {"users": users}
        except Exception as e:
            raise HTTPException(status_code=500, detail=str(e))
        finally:
            await close_database_connection()

    #API endpoint to Update Data from a Table
    @app.put("/update_value")
    async def update_value(table_name: str,user_data: UserData, age_condition: int):
        # Construct SQL query to update value
        query = f'''UPDATE {table_name}
    SET name = :name, age = :age
    WHERE age = :age_condition'''
        values = {"name": user_data.name, "age": user_data.age, "age_condition": user_data.age_condition}
        try:
            # Connect to the database
            await connect_to_database()

            # Execute the query to update the value
            await database.execute(query, values)

            return {"Updated!!! "}
        except Exception as e:
            raise HTTPException(status_code=500, detail=str(e))
        finally:
            # Disconnect from the database
            await close_database_connection()


   

### Explanation of the Code:
    from fastapi import HTTPException, FastAPI
    from pydantic import BaseModel
    from databases import Database

   HTTPException from fastapi is used for giving HTTP errors in a meaningful     way 
    
   BaseModel from pydantic is used for data validation which gives some advantages like editor support, helps in data  serialization/deserialization
   
   Database from database is used for database connection and executing queries

---
Creates an instance of FastAPI
    
    app = FastAPI() 

Database url for connection
    
    database_url = "mysql+async://  
    root:my-secret-pw@localhost:3307/ mydatabase"

Create a Database instance
    
    database = Database(database_url)

---
Creates an instance of FastAPI
    
    app = FastAPI() 

Database url for connection
    
    database_url = "mysql+async://
    root:my-secret-pw@localhost:3307/ mydatabase"

Create a Database instance
    
    database = Database(database_url)
---

API Endpoint to Create a New Table

    @app.post("/create_table")
    async def create_and_insert(table_name: str, user_data: UserData):
        # Construct SQL query to create table and insert data
        query = f"CREATE TABLE IF NOT EXISTS {table_name} (name VARCHAR(255), age INT);"
        values = {"name": user_data.name, "age": user_data.age}

        try:
            # Connect to the database
            await connect_to_database()

            # Create the table if it doesn't exist
            await database.execute(query)

            return {"message": "Table created and data inserted successfully"}
        except Exception as e:
            raise HTTPException(status_code=500, detail=str(e))
        finally:
            # Disconnect from the database
            await close_database_connection()
In the above code snippet we are creating a table in our database.   
For that we are taking inputs 'table_name' as 'str' and 'user_data" as UserData.    These parameters basically help in taking the table name and the expected data type of the value to be inserted in it.

We have used a simple mySQL query to achieve this. 
    
    query = f"CREATE TABLE IF NOT EXISTS {table_name} (name VARCHAR
    (255), age INT);"
This creates a table with columns name and age taking datatype as VARCHAR and INT respectively. 

We have used try-except-finally block here to structurised the flow and for database connection monitoring. 

    
    try:
            # Connect to the database
            await connect_to_database()

            # Create the table if it doesn't exist
            await database.execute(query)

            return {"message": "Table created and data inserted successfully"}

This particular try block connects to the database, executes the query using 'database.execute(query) and returns a message that it has been updated in the request body.

    
    except Exception as e:
            raise HTTPException(status_code=500, detail=str(e))
    finally:
            # Disconnect from the database
            await close_database_connection()

Moving ahead, the except block helps structuring the error by giving a defined status_code, and detail. This helps understanding the errors better. And finally, the finally block is used for disconnecting the database.

#### This particular try-except-finally block is used throughout the code across endpoints.
#### Below are the queries used in the code

    1. In @app.delete("/delete_entry")
    query = f"DELETE FROM {table_name} WHERE name = :name AND age = :age;"

    2. In @app.get("/get_users")
    query = f"SELECT * FROM {table_name}"
    
    Here, we have used 'database.fetch_all(query)'
    to get all the data from the table

    3. In @app.put("/update_value")
    query = f'''UPDATE {table_name}
    SET name = :name, age = :age
    WHERE age = :age_condition'''

    Here, we have used an age_condition to access the particular 
    values for updation

    4. In @app.post("/insert_table_info")

    query = f"INSERT INTO {table_name} (name, age) VALUES 
    (:name, :age);"





for running the code --> go to terminal and write

        uvicorn yourfilename:app --reload


---
