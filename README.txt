# **Step-by-Step Guide to Implementing the Flask API with MySQL**

## **Step 1: Set Up the Environment**

### **1. Install Python and Required Libraries**

- Ensure Python3 is installed.
- Use `pip` to install the necessary libraries:
  ```bash
  pip install flask mysql-connector rich
  ```

### **2. Install MySQL Database**

- Install MySQL Server and configure it with a root user.

### **3. Create a Database and Tables**

- Log in to MySQL and create a database named `Eshop`:
  ```sql
  CREATE DATABASE Eshop
  DEFAULT CHARACTER SET = 'utf8mb4';
  ```
- Create the `Customer` and `Empt` tables with sample data:
  ```sql
  USE Eshop;
  CREATE TABLE Customer (
      id INT, 
      name VARCHAR(250), 
      mail VARCHAR(250)
  );

  INSERT INTO Customer (id, name, mail) 
  VALUES (1, 'suri', 'suri@gmail.com'), 
         (2, 'suriya', 'suriya@gmail.com');

  CREATE TABLE Empt (
      statement VARCHAR(250)
  );

  INSERT INTO Empt (statement) 
  VALUES ('NO DATA FOUND');
  ```

---

## **Step 2: Create the Backend Code**

### **1. Create ****`dbservice.py`**

- Handles database connection, data retrieval, and formatting:
  ```python
  import mysql.connector
  from rich import print

  def createConnection():
      conn = mysql.connector.connect(
          host="localhost",
          port=3306,
          user="suriya",
          password="suriya@25",
          database="Eshop",
          auth_plugin='mysql_native_password'
      )
      conn.autocommit = True
      return conn

  def formatter(cursor, data):
      result = []
      for row in data:
          row_dict = {}
          for idx, column in enumerate(cursor.description):
              row_dict[column[0]] = row[idx]
          result.append(row_dict)
      return result

  def fetchDataDB(id):
      print(id)
      conn = createConnection()
      cursor = conn.cursor()
      if int(id) in [1, 2]:
          cursor.execute("SELECT * FROM Customer WHERE id = %s", (id,))
      else:
          cursor.execute("SELECT * FROM Empt")
      data = cursor.fetchall()
      return formatter(cursor, data)
  ```

### **2. Create ****`app.py`**

- Sets up the Flask app and defines the API endpoint:
  ```python
  from flask import Flask, request
  from dbservice import fetchDataDB

  app = Flask(__name__)

  # Query Parameters
  @app.route("/fetchData", methods=["GET"])
  def fetch_data():
      id = request.args.get('id')
      print(id)
      return fetchDataDB(id)

  if __name__ == "__main__":
      app.run(port=5000, host="0.0.0.0", debug=True)
  ```

---

## **Step 3: Run the Application**

### **1. Start the Flask App**

```bash
python app.py
```

### **2. Access the API**

```
http://127.0.0.1:5000/fetchData?id=1
```

---

## **Step 4: Test Using Postman**

1. Open Postman and create a new **GET** request.
2. Enter the URL: `http://127.0.0.1:5000/fetchData?id=1`
3. Click **Send** and view the JSON response.

---

## **Step 5: Improve Security**

- Always use parameterized queries to prevent SQL injection:
  ```python
  cursor.execute("SELECT * FROM Customer WHERE id = %s", (id,))
  ```




