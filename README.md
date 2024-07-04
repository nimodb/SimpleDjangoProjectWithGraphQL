# Creating a Simple Django Project with GraphQL

In this tutorial, we'll create a simple Django project with GraphQL integration. We'll create a project called `core` and an app called `books`. The `books` app will have a model with title and author fields. We'll set up GraphQL to query the books. Finally, we'll add some sample data to the database and test the GraphQL endpoint.

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
Create a new Django project named `core` and a new app named `books`.
```bash
# Create Django project
django-admin startproject core .

# Create Django app
python manage.py startapp books
```

## Step 3: Set Up Models and Database
In the `books` app, create a model with title and author fields.
```python
# models.py (books app)

from django.db import models

class Books(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)

    def __str__(self):
        return self.title
```

Add the `books` app and Graphene to the `INSTALLED_APPS`.
```python
# settings.py (core project)

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
Create a new file called `schema.py` in the `books` app and set up the GraphQL schema.
```python
# schema.py (books app)

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

## Step 5: Set Up URLs
Link the `books` app to the main project and create a new `urls.py` in the `books` app to access GraphQL.
```python
# urls.py (core project)

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

```python
# urls.py (books app)

from django.urls import path
from graphene_django.views import GraphQLView
from .schema import schema

urlpatterns = [
    path('graphql', GraphQLView.as_view(graphiql=True, schema=schema)),
]
```

## Step 6: Add Sample Data
Create a JSON file with sample book data and load it into the database.
```json
// books_data.json

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
Run the Django server and test the combined GraphQL endpoint:
```bash
python manage.py runserver
```
Navigate to `http://localhost:8000/graphql` and running the following query:
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


## Step 8: Freeze the requirements
After setting up the project, freeze the installed packages to a `requirements.txt` file.
```bash
pip freeze > requirements.txt
```


## Next Steps
In Part 2, we will create another app called `quiz` to manage quizzes, categories, questions, and answers. We will extend our GraphQL schema to handle complex querying and filtering for these new models.

[Go to Part 2: Queries with GraphQL](https://github.com/nimodb/QueriesWithGraphQL_Part2)

Stay tuned!