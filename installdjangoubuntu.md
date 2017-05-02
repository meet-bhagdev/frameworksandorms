-   Install SQL Server

        sudo su
        curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
        curl https://packages.microsoft.com/config/ubuntu/16.04/mssql-server.list > /etc/apt/sources.list.d/mssql-server.list
        exit
        sudo apt-get update
        sudo apt-get install mssql-server
        sudo /opt/mssql/bin/mssql-conf setup
    
-   Install Python and Django
    
        sudo sudo apt-get install python python-pip django virtualenv
    
-   Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

        sudo su
        curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-tools.list
        exit
        sudo apt-get update
        sudo ACCEPT_EULA=Y apt-get install msodbcsql mssql-tools
        sudo apt-get install unixodbc-dev
        echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
        echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
        source ~/.bashrc
    
-   Install Python Driver for SQL Server

        sudo apt-get install unixodbc-dev gcc g++ build-essential
        sudo pip install pyodbc

-   Setup your first Django project

        django-admin startproject todo
        python manage.py runserver
        python manage.py startapp todoapp
        
-   Edit the todo/settings.py file with your database settings
    
        vim settings.py
        
      Replace the DATABASE section with the following
      
        DATABASES = {
            'default': {
                 'ENGINE': 'sql_server.pyodbc',
                 'NAME': 'yourdatabasename',
                 'USER': 'yourusername',
                 'PASSWORD': 'yourpassword',
                 'HOST': 'yourserver',
                 'PORT': '1433',

                 'OPTIONS': {
                      'driver': 'ODBC Driver 13 for SQL Server',
                 },
             },
        }
        
-   Create a database for your Django application

        sqlcmd -S yourserver -U yourusername -p yourpassword -Q "CREATE DATABASE SampleDB"
        
-   Run migrations

        python manage.py migrate
        
-   Create a model

        vim todo/models.py
        
     Paste the following in the models.py file. This creates a class for our todo's
     
        from __future__ import unicode_literals
        from datetime import datetime

        from django.db import models

        class Todo(models.Model):
            title = models.CharField(max_length=200)
            text = models.TextField()
            created_at = models.DateTimeField(default=datetime.now, blank=True)
            def __str__(self):
                return self.title

        
-   Create your routes
 
        vim todo/urls.py
      
     Paste the following in urls.py

        from django.conf.urls import include, url
        from django.contrib import admin

        urlpatterns = [
            #url(r'^$', include('todos.urls')),
            url(r'^todos/', include('todos.urls')),
            url(r'^admin/', admin.site.urls),
        ]
        
        vim todoapp/urls.py
        
      Paste the following in urls.py
      
        from django.conf.urls import url

        from . import views

        urlpatterns = [
            url(r'^$', views.index, name='index'),
            url(r'^details/(?P<id>\w{0,50})/$', views.details),
            url(r'^add', views.add, name='add')
        ]

-   Create super user for the Django app

        python manage.py createsuperuser --username=yourusername --email=youremail
        
-   Edit admin.py

        vim todoapp/admin.py
     
     Paste the following in admin.py
     
        from django.contrib import admin
        from .models import Todo
        admin.site.register(Todo)
        
-   Create templates for your Django application

        cd todoapp/templates
        vim  index.html
        
     Paste the following in index.html
        
        {% include 'partials/header.html' %}
            <ul>
              {% for todo in todos %}
                <li><a href="/todos/details/{{todo.id}}">{{todo.title}}</a>: {{todo.text}}</li>
              {% endfor %}
            </ul>
        {% include 'partials/footer.html' %}
        
        cd todoapp/templates
        vim  details.html
        
     Paste the following in details.html

        {% include 'partials/header.html' %}
          <a href="/todos"><< Go Back</a>
          <br>
          <h1>{{todo.title}}</h1>
          <p>{{todo.text}}</p>
          <br>
          Created on {{todo.created_at}}
        {% include 'partials/footer.html' %}
  
        cd todoapp/templates  
        mkdir partials
        vim header.html
        
     Paste the following in header.html
     
        <!DOCTYPE html>
        <html>
          <head>
            <meta charset="utf-8">
            {% load static %}
            <link rel="stylesheet" href="{% static 'css/style.css' %}">
            <title>TodoList</title>
          </head>
          <body>
            <header>
              <h1>TodoList</h1>
              <a href="/todos/add">Add Todo</a>
              <hr>
            </header>

-   Create the CSS stylesheet for your Django application

        cd todoapp
        mkdir static
        vim style.css

     Paste the following in style.css

        a {
          color: green;
        }

-   Create views 

        cd todoapp
        vim views.py
        
     Paste the following in views.py

        from django.shortcuts import render, redirect
        from django.http import HttpResponse

        from .models import Todo

        def index(request):
            todos = Todo.objects.all()[:10]

            context = {
                'todos':todos
            }
            return render(request, 'index.html', context)

        def details(request, id):
            todo = Todo.objects.get(id=id)

            context = {
                'todo':todo
            }
            return render(request, 'details.html', context)

        def add(request):
            if(request.method == 'POST'):
                title = request.POST['title']
                text = request.POST['text']

                todo = Todo(title=title, text=text)
                todo.save()

                return redirect('/todos')
            else:
        return render(request, 'add.html')
        
-   Serve the app

        python manage.py runserver
     
     Go to http://127.0.0.1:8000 from the browser of your choice. You should see the following
     
     ![alt text](https://preview.ibb.co/e98qi5/laravelcomplete.png "Logo Title Text 1")

-   Add a few tasks using the UI and verify the results using sqlcmd 

        sqlcmd -S yourserver -d yourdatabase -p yourpassword -U yourusername        
        1> select * FROM  dbo.tasks
        2> GO
         id          name           created _at                updated_at
         -----------------------------------------------------------------------------
         1           Meet's task    2017-05-02 05:51:25.000    2017-05-02 05:51:25.000                                         
         
        (1 rows affected)



