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

## Set Up URLs
Link the "books" app to the main project and create a new "urls.py" in the "books" app to access GraphQL.

**urls.py (core project)**

```python
    from django.contrib import admin
    from django.urls import path, include
    from graphene_django.views import GraphQLView
    from books.schema import schema

    urlpatterns = [
        path("admin/", admin.site.urls),
        path("graphql/", GraphQLView.as_view(graphiql=True, schema=schema)),
        path("", include("books.urls")),
    ]
```

**urls.py (books app)**

```python
    from django.urls import path
    from graphene_django.views import GraphQLView
    from .schema import schema

    urlpatterns = [
        path('graphql', GraphQLView.as_view(graphiql=True, schema=schema)),
    ]
```

## Step 6: Add Sample Data
Create a JSON file with sample book data and load it into the database.

**books_data.json**

```json
[
    {"model": "books.books", "pk": 1, "fields": {"title": "Faust", "author": "Johann Wolfgang von Goethe"}},
    {"model": "books.books", "pk": 2, "fields": {"title": "The Trial", "author": "Franz Kafka"}},
    {"model": "books.books", "pk": 3, "fields": {"title": "Siddhartha", "author": "Hermann Hesse"}},
    {"model": "books.books", "pk": 4, "fields": {"title": "The Metamorphosis", "author": "Franz Kafka"}},
    {"model": "books.books", "pk": 5, "fields": {"title": "Steppenwolf", "author": "Hermann Hesse"}},
    {"model": "books.books", "pk": 6, "fields": {"title": "Berlin Alexanderplatz", "author": "Alfred Döblin"}},
    {"model": "books.books", "pk": 7, "fields": {"title": "The Sorrows of Young Werther", "author": "Johann Wolfgang von Goethe"}},
    {"model": "books.books", "pk": 8, "fields": {"title": "Death in Venice", "author": "Thomas Mann"}},
    {"model": "books.books", "pk": 9, "fields": {"title": "The Magic Mountain", "author": "Thomas Mann"}},
    {"model": "books.books", "pk": 10, "fields": {"title": "All Quiet on the Western Front", "author": "Erich Maria Remarque"}}
]
```

Load the data into the database.
```bash
    python manage.py loaddata books_data.json
```

## Step 7: Test GraphQL Endpoint
Run the Django server and test the GraphQL endpoint by navigating to 'http://localhost:8000/graphql' and running the following query:
```graphql
    {
        allBooks {
            id
            title
            author
        }
    }
```

You should see the list of books returned as a response.


## Conclusion
In this tutorial, we set up a simple Django project with a GraphQL endpoint to query book data. We created a project and app, set up the models and database, configured the GraphQL schema, added sample data, and tested the endpoint. This setup provides a solid foundation for building more complex applications with Django and GraphQL.