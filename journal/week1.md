# Week 1 — App Containerization
# Required Homework/Tasks

## Containeraized backend

<img width="1092" alt="Screenshot 2023-02-22 at 6 33 10 PM" src="https://user-images.githubusercontent.com/50529535/221424466-63d9f430-a38b-46bf-be75-535dc5b3c3cd.png">

## Added docker file in backend flask
Dockerfile saved in backend-flask folder

```
# For more information, please refer to https://aka.ms/vscode-docker-python
FROM python:3.10-slim-buster

# Keeps Python from generating .pyc files in the container
ENV PYTHONDONTWRITEBYTECODE=1

# Turns off buffering for easier container logging
ENV PYTHONUNBUFFERED=1
# Inside Container
# make a new folder inside container
WORKDIR /backend-flask
# Outside Container -> Inside Container
# contains the libraries want to install on Flask app
COPY requirements.txt requirements.txt
# Install pip requirements

RUN pip3 install -r requirements.txt
# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-python-configure-containers
# RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
# # Inside Container 
# RUN pip3 install -r requirements.txt
# USER appuser

COPY . .

ENV FLASK_ENV=development
# During debugging, this entry point will be overridden. For more information, please refer to https://aka.ms/vscode-docker-python-debug
# CMD ["gunicorn", "--bind", "0.0.0.0:undefined", "backend-flask.app:app"]

EXPOSE ${PORT}
# CMD 
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

## Build container backend
docker build -t backend-flask ./backend-flask

## Open (unlock) ports on Gitpod
<img width="1093" alt="Screenshot 2023-02-26 at 6 45 58 PM" src="https://user-images.githubusercontent.com/50529535/221427131-be5c4bbf-16a2-4f80-8280-fe9531f02bdd.png">

## Run container
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='' -e BACKEND_URL='' backend-flask

## List container images
<img width="978" alt="Screenshot 2023-02-26 at 6 47 46 PM" src="https://user-images.githubusercontent.com/50529535/221427221-36405e69-c271-478a-9db3-e969079bc9d3.png">

## Containerise frond-end and cd to the front end directory. installed npm using npm i command there also created docker file
<img width="1084" alt="Screenshot 2023-02-26 at 6 49 21 PM" src="https://user-images.githubusercontent.com/50529535/221427275-ce26a992-8c37-4128-9339-876ae6f13c79.png">

## Build Frontend
```docker build -t frontend-react-js ./frontend-react-js```
 ## Docker Compose
 <img width="958" alt="Screenshot 2023-02-26 at 7 02 44 PM" src="https://user-images.githubusercontent.com/50529535/221428020-cf2157c1-f67b-4d68-9e1f-5331adb35d3f.png">
 ### added Postgres and DynamoDB services to yml file
 ```
 services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local
To install the postgres client into Gitpod

  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
 ```
 ### Docker compose up
 <img width="1090" alt="Screenshot 2023-02-26 at 6 43 22 PM" src="https://user-images.githubusercontent.com/50529535/221428058-67f11109-1192-40b6-b944-cbd63154089a.png">

### Postgres
<img width="1394" alt="Screenshot 2023-02-26 at 6 44 52 PM" src="https://user-images.githubusercontent.com/50529535/221428855-5c41184c-c402-462c-9550-94a7bd38179a.png">
### Test Postgres 
<img width="765" alt="Screenshot 2023-02-26 at 7 41 53 PM" src="https://user-images.githubusercontent.com/50529535/221430413-04c691a6-c008-4f32-aaea-e3e0aae4cff4.png">

### Create table
```
aws dynamodb create-table \
--endpoint-url http://localhost:8000 \
--table-name Music \
--attribute-definitions \
    AttributeName=Artist,AttributeType=S \
    AttributeName=SongTitle,AttributeType=S \
--key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
--provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
--table-class STANDARD
```
# Create item
```
aws dynamodb put-item \
--endpoint-url http://localhost:8000 \
--table-name Music \
--item \
    '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
--return-consumed-capacity TOTAL  
```
### DynamoDB list tables
aws dynamodb list-tables --endpoint-url http://localhost:8000

### Get Records
aws dynamodb scan --table-name cruddur_cruds --query "Items" --endpoint-url http://localhost:8000


