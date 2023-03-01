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