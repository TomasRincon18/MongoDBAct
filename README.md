# MongoDB Atlas + Compass + Python Notebook Setup Guide

## Overview

This guide documents the setup used for `TallerMongo/mongodb_for_all.ipynb`. It covers:

- Creating a MongoDB Atlas deployment
- Configuring database access and network access
- Connecting the cluster with MongoDB Compass
- Preparing a local Python environment for the notebook
- Connecting Python to the Atlas cluster with `pymongo`
- Importing student data from a CSV file in randomized order

---

## 1. Create a MongoDB Atlas Deployment

1. Go to MongoDB Atlas and click **Create**.
2. Choose the **free tier** deployment.
3. Select a **project name**.
4. Choose a **cloud provider** such as AWS.
5. Select a cluster name and region.
6. Click **Create Deployment**.

This creates the cloud cluster that will store the database used in the notebook.

---

## 2. Set Up Database Access

Before connecting from Python or Compass, create a database user:

1. Open the **Database Access** section in Atlas.
2. Click **Add New Database User**.
3. Create a **username** and **password**.
4. Save the credentials securely.
5. Confirm by clicking **Create Database User**.

These credentials are required in the MongoDB connection string.

---

## 3. Configure Network Access

MongoDB Atlas blocks unknown IP addresses by default, so network access must be configured.

1. Open **Network Access** in Atlas.
2. Click **Add IP Address**.
3. For classwork or testing, choose:
   `Allow Access from Anywhere (0.0.0.0/0)`
4. Save the changes.

This is convenient for practice, but in production environments IP access should be restricted properly.

---

## 4. Choose a Connection Method

After the deployment is created, Atlas provides different connection options.

1. Click **Connect** on the cluster page.
2. Choose **MongoDB Compass** if you want to inspect the cluster through a GUI.
3. Choose **Drivers** if you want the connection string for Python code.

In this workflow, both MongoDB Compass and Python were relevant:

- Compass for visual inspection
- Python for programmatic access from the notebook

---

## 5. Connect MongoDB Atlas to Compass

MongoDB Compass can be used to verify that the cluster, database, and collection are visible outside the notebook.

1. Open MongoDB Compass.
2. Click **Add new connection**.
3. Copy the connection URI from Atlas.
4. Replace `<db_password>` with the real password.
5. Paste the URI into Compass.
6. Optionally assign a connection name and color.
7. Click **Save & Connect**.

Typical URI format:

```text
mongodb+srv://<username>:<db_password>@<cluster-host>/
```

---

## 6. Review the Notebook Requirements

The notebook `TallerMongo/mongodb_for_all.ipynb` was reviewed to detect the required libraries.

The notebook imports:

```python
from pymongo import MongoClient
```

To make the notebook runnable locally, the following packages were prepared:

- `pymongo`
- `jupyterlab`
- `ipykernel`

---

## 7. Create a Local Virtual Environment

A local virtual environment named `.venv` was created in the project root:

```bash
cd "/home/fabri/code/Semestre 4/Bases de Datos"
virtualenv .venv
```

Then the required tools were installed:

```bash
.venv/bin/python -m pip install --upgrade pip setuptools wheel
.venv/bin/python -m pip install pymongo jupyterlab ipykernel
```

---

## 8. Register the Jupyter Kernel

To use the environment directly from Jupyter notebooks, a custom kernel was registered:

```bash
.venv/bin/python -m ipykernel install --user --name tallermongo-venv --display-name "Python (.venv) TallerMongo"
```

After that, the notebook can run using the correct environment.

---

## 9. Open the Notebook

Use the following commands:

```bash
cd "/home/fabri/code/Semestre 4/Bases de Datos"
source .venv/bin/activate
jupyter lab
```

Then open:

`TallerMongo/mongodb_for_all.ipynb`

and select the kernel:

`Python (.venv) TallerMongo`

---

## 10. Connect Python to the MongoDB Cluster

Inside the notebook, the connection was created with `MongoClient`, following the same Atlas cluster workflow used in the setup:

```python
from pymongo import MongoClient

cliente = MongoClient("mongodb+srv://<username>:<password>@<cluster-host>/")
```

In the notebook, the cluster connection is created in the same style:

```python
from pymongo import MongoClient

cliente = MongoClient("mongodb+srv://mariabarreto6_project1:Project1.2025@cluster1.0qmkoen.mongodb.net/")
```

After creating the client, the connection can be verified with:

```python
try:
    cliente.admin.command("ping")
    print("Pinged your deployment. You successfully connected to MongoDB!")
except Exception as e:
    print(e)
```

This confirms that the Atlas cluster is reachable and the credentials are valid.

---

## 11. Create or Access the Database and Collection

Once the cluster connection is active, the notebook creates or accesses the target database and collection:

```python
db = cliente["BD_CursoMongo"]
estudiantes = db["Estudiantes"]
```

In MongoDB, the database and collection are created automatically when data is inserted if they do not already exist.

This collection is then used by helper functions such as:

- `insertar_estudiante(nombre, edad, carrera)`
- `listar_estudiantes()`
- `actualizar_edad(nombre, nueva_edad)`
- `eliminar_estudiante(nombre)`

Example insertion function from the notebook:

```python
def insertar_estudiante(nombre, edad, carrera):
    estudiante = {"nombre": nombre, "edad": edad, "carrera": carrera}
    estudiantes.insert_one(estudiante)
    print(f"Estudiante {nombre} insertado.")
```

---

## 12. Insert Data Manually with Notebook Functions

Instead of performing a bulk CSV import in the documented workflow, the student records were inserted manually through the helper functions implemented in the notebook.

The main insertion function used was:

```python
def insertar_estudiante(nombre, edad, carrera):
    estudiante = {"nombre": nombre, "edad": edad, "carrera": carrera}
    estudiantes.insert_one(estudiante)
    print(f"Estudiante {nombre} insertado.")
```

This approach allowed the collection to be populated step by step while testing the connection, document structure, and write operations directly from Python.

Example manual inserts:

```python
insertar_estudiante("Naomi Mosquera", 20, "Ciencia de datos")
insertar_estudiante("Ailyn Gomez", 19, "Ciencia de datos")
insertar_estudiante("Angie Tobar", 18, "Ciencia de datos")
```

After inserting the records, the notebook functions could also be used to review and manage the stored documents:

```python
listar_estudiantes()
actualizar_edad("Naomi Mosquera", 21)
eliminar_estudiante("Angie Tobar")
```

This manual workflow was useful for validating that each CRUD operation worked correctly on the `Estudiantes` collection.

---

## Common Issues and Notes

### 1. Connection Fails

- Verify the Atlas username and password
- Verify the cluster host
- Confirm that network access allows your IP

### 2. Compass Cannot Connect

- Recheck the copied URI
- Replace `<db_password>` correctly
- Confirm that Atlas network access is enabled

### 3. Duplicate Records

- Repeating manual insertions may create duplicate student documents

### 4. Data Consistency

- Some values may differ in capitalization, for example:
  `"Ciencia de datos"` vs `"ciencia de datos"`

---

## Verification

The following parts of the workflow were verified successfully:

- The notebook import requirement was identified correctly
- The `.venv` environment was created successfully
- `pymongo`, `jupyterlab`, and `ipykernel` were installed
- The Jupyter kernel was registered correctly
- The database operations were ready for manual testing from the notebook

---

## Goal of This Workflow

The goal of this setup is to combine:

- Cloud database access through MongoDB Atlas
- Optional GUI inspection through MongoDB Compass
- Code-based interaction through Python and Jupyter
- Manual CRUD practice on the `BD_CursoMongo.Estudiantes` collection

---

## Next Steps

- Add duplicate protection before insertion
- Normalize repeated text values
- Add query and aggregation examples
- Document update and delete operations with sample outputs
