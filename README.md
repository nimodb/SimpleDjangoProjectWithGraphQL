# Creating a Simple Django Project with GraphQL

In this tutorial, we'll create a simple Django project with GraphQL integration. We'll create a project called "core" and an app called "books". The "books" app will have a model with title and author fields. We'll set up GraphQL to query the books. Finally, we'll add some sample data to the database and test the GraphQL endpoint.

## Step 1: Set Up Virtual Environment and Install Packages
First, create a virtual environment and install the necessary packages.
```bash
    # Create virtual environment
    python -m venv env

    # Activate virtual environment
    # On Windows
    env\Scripts\activate
    # On macOS/Linux
    source env/bin/activate

    # Install Django and Graphene
    pip install django graphene-django
```

## Step 2: Create New Django Project and App
Create a new Django project named "core" and a new app named "books".
```bash
# Create Django project
django-admin startproject core .

# Create Django app
python manage.py startapp books
```