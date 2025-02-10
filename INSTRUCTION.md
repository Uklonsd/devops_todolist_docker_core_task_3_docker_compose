# INSTRUCTION.md

## Prerequisites

## Step 1: Update Django Database Settings

In `settings.py`, configure the MySQL database:

```python
DATABASES = {
    'default': {
        'ENGINE': 'mysql.connector.django',
        'NAME': 'app_db',
        'USER': 'app_user',
        'PASSWORD': '1234',
        'HOST': 'mysql',  # You can use a different host if your MySQL server is on a remote machine.
        'PORT': '',  # Leave this empty to use the default MySQL port (3306).
    }
}

```
## Step 2: Dockerfile
Create a Dockerfile to build the Django container:
````
# Build and run Django app
FROM python:3.8

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

EXPOSE 8080

ENTRYPOINT ["sh", "-c", "python manage.py migrate && python manage.py runserver 0.0.0.0:8080"]
````

## Step 3: Docker Compose Setup
Create docker-compose.yml for Django and MySQL services:

````
version: '3'

networks:
  db-data-net:
    driver: bridge

services:
  todoapp:
    build: .
    ports:
      - "8080:8080"
    environment:
      - ENV=local
      - DB_HOST=mysql
    depends_on:
      - mysql
    networks:
      - db-data-net

  mysql:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=1234
      - MYSQL_DATABASE=app_db
      - MYSQL_USER=app_user
      - MYSQL_PASSWORD=1234
    ports:
      - "3306:3306"
    networks:
      - db-data-net

volumes:
  db-data:
````
## Step 4: Run Containers
To build and start the containers:
````
docker-compose up --build
````

## Step 5: Stop Containers
To stop and remove the containers:
````
docker-compose down
````