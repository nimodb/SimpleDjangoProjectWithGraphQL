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

## Step 3: Set Up Models and Database
In the "books" app, create a model with title and author fields.

**models.py (books app)**

```python
    from django.db import models

    class Books(models.Model):
        title = models.CharField(max_length=100)
        author = models.CharField(max_length=100)

        def __str__(self):
            return self.title
```

**settings.py (core project)**

Add the "books" app and Graphene to the "INSTALLED_APPS".
```python
    INSTALLED_APPS = [
        ...
        'books',
        'graphene_django',
    ]
```

**Database Migration**

Run the migrations to set up the database.
```bash
    python manage.py makemigrations
    python manage.py migrate
```

## Step 4: Set Up GraphQL Schema
Create a new file called schema.py in the books app and set up the GraphQL schema.

**schema.py (books app)**
```python
    import graphene
    from graphene_django import DjangoObjectType
    from .models import Books


    class BooksType(DjangoObjectType):
        class Meta:
            model = Books
            fields = ("id", "title", "author")


    class Query(graphene.ObjectType):
        all_books = graphene.List(BooksType)

        def resolve_all_books(root, info):
            return Books.objects.all()


    schema = graphene.Schema(query=Query)
```