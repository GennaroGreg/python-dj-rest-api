# REST API in Python
---
## Learning the Basics of Python and Django

### Main Technologies
- Python: `v3.13`
- Pip: `v25.1.1`
- Django: `v5.2.3`
- Django Rest Framework: `v3.16.0`
---
### Prerequisites
- Python installed on your local machine
- Pip installed on your local machine

### Set Up
- Clone this repository using the following command:  
`git clone https://github.com/GennaroGreg/python-dj-rest-api.git`
- Open the repository in yoru IDE
- Open a terminal instance located at the root of the repository
- Execute the following command to install Django and Django Rest Framework:  
`pip install django djangorestframework`
---
### Create a New Project
- Execute the following command in your terminal instance:  
`python -m django startproject {{PROJECT_NAME}}`  
where `{{PROJECT_NAME}}` is the desired name of your project. In this example, `{{PROJECT_NAME}}` will be `rest_api_demo`. **NOTE:** Django projects cannot contain '-' characters.
- Execute the following command to navigate the terminal instance to the newly created project:  
`cd {{PROJECT_NAME}}`
- Execute the following command to create an application within the newly created project:  
`python manage.py startapp {{NAME}}`  
where `{{NAME}}` is the name of the application. In this example, `{{NAME}}` will be `api`
- Navigate to the `settings.py` file within the project folder (**NOT** the application folder) and find the `INSTALLED_APPS` section
- Add `rest_framework` and `api` to the list of installed apps:  
```python
INSTALLED_APPS = [
    'django.contrib.admin', # Boilerplate
    'django.contrib.auth', # Boilerplate
    'django.contrib.contenttypes', # Boilerplate
    'django.contrib.sessions', # Boilerplate
    'django.contrib.messages', # Boilerplate
    'django.contrib.staticfiles', # Boilerplate
    'rest_framework', # New
    'api' # New
]
```
---
### Create and Migrate your Data Model(s)
- Within your `api` application package, find the `models.py` class.
- Create your data model
- This example will use a generic `Book` model:
    ```python
    class Book(models.Model):
        title = models.CharField(max_length=100)
        author = models.CharField(max_length=100)
        publisher = models.CharField(max_length=100)
        pages = models.IntegerField()

        def __str__(self):
            return self.title
    ```
- Execute the following command in your terminal instance:  
`python manage.py makemigrations`
- Execte the following command in your terminal instance:  
`python manage.py migrate`
- Create a file called `serializer.py` in your `api` application package
- In the new file, add the following imports:
    ```python
    from rest_framework import serializers
    from .models import Book
    ```
- In the same file, add a serializer class that corresponds to the desired model:
    ```python
    class BookSerializer(serializers.ModelSerializer):
        class Meta:
            model = Book
            fields = '__all__'
    ```
---
### Create Views / Endpoints
- Locate the `views.py` file within the `api` application package
- Clear all boilerplate content from the `views.py` file.
- Add the following lines to your `views.py` file:  
    ```python
    from rest_framework.decorators import api_view
    from rest_framework.response import Response
    from rest_framcwork import status
    from .models import Book
    from .serializer import BookSerializer
    ```

- Create two new views in the `views.py` file (basic GET & POST requests):  
    ```python
    @api_view(['GET'])
    def get_books(request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

    @api_view(['POST'])
    def create_book(request):
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)   
    ```
- Create a new file in the `api` package called `urls.py`
- Add the following imports:  
    ```python
    from django.urls import path
    from .views import get_book
    ```
- Create a route inside `urls.py` that will handle the `GET` request for the `Book` object:  
    ```python
    urlpatterns = [
        path('books/', get_books, name='get_books'),
        path('books/create/', create_book, name='create_book')
    ]
    ```
- Add the above code into `rest_api_demo/urls.py`, so that the boilerplate and new content looks like:
    ```python
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('api/', include('api.urls'))
    ]
    ```
    **NOTE:** add `include` to the second import statement
---
### Run the Server and Test the Endpoints
- Execute the following command in your terminal instance in order to start the development server:  
`python manage.py runserver`
- Navigate to the following endpoint in order to create a new `Book` entry:  
`http://localhost:8000/api/books/create/`
- In the `Content` field, enter a new book:  
    ```json
    {
        "title": "title",
        "author": "author",
        "publisher": "publisher",
        "pages": 100
    }
    ```
- You should receive a `HTTP 201 Created` response
- Navigate to the following endpoint in order to get a list of all created `Book` entries:  
`http://localhost:8000/api/books/`