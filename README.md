# Deploy Zero Downtime Application with Kubernetes

## Task
- Create Flask app
- Create an image and run the application in Docker
- Create a deployment and run the application in Kubernetes

## Prerequisites
- Docker Desktop
- A Kubernetes service - I'm using Docker Desktop with Kubernetes in this walkthrough
- Python 3 installed - It will require if you want to run the code locally in machine

## Create Flask app
For Demo purpose, I have created very simple Flask application in [main.py](./main.py)

```py
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():
    return "Hello from  Python!"


if __name__ == "__main__":
    app.run(host="0.0.0.0")
```

The [requirements.txt](./requirements.txt) file contains the list of packages needed by the [main.py](./main.py)

If you want to run this application locally then use following command in your python environment

```sh
pip install -r requirements.txt
python main.py
```

## Create a Dockerfile

```docker
FROM python:3.7

RUN mkdir /app
WORKDIR /app
ADD . /app/
RUN pip install -r requirements.txt

EXPOSE 5000
CMD ["python", "/app/main.py"]
```

[Dockerfile](./Dockerfile) is a set of instructions Docker will use to build the image. For this simple application, Docker is going to:

- Get the official Python Base Image for version 3.7 from Docker Hub.
- In the image, create a directory named app.
- Set the working directory to that new app directory.
- Copy the local directory’s contents to that new folder into the image.
- Run the pip installer (just like we did earlier) to pull the requirements into the image.
- Inform Docker the container listens on port 5000.
- Configure the starting command to use when the container starts.

**Create an image**
Start the Docker desktop and run following command
```sh
docker build -f Dockerfile -t myapp:latest .
```

**Verify the image using**
```sh
docker image ls
```

Before jumping into Kubernetes, let’s verify it works in Docker. Run the following command to have Docker run the application in a container and map it to port 5001:

```sh
docker run -p 5001:5000 myapp
```

Now navigate to http://localhost:5001, and you should see the "Hello from Python!" message

## Create a deployment and run the application in Kubernetes

- Enable Kubernetes in Docker desktop
- First verify your kubectl is configured
```sh
kubectl version
```
- Create a file named [deployment.yaml](./deployment.yaml).

This YAML file is the instructions to Kubernetes for what you want running. It is telling Kubernetes the following:

    - You want a load-balanced service exposing port 6000
    - You want four instances of the myapp container running

- Use kubectl to send the YAML file to Kubernetes by running the following command:
```sh
kubectl apply -f deployment.yaml
```
- You can see the pods are running if you execute the following command:
```sh
kubectl get pods
```

Now navigate to http://localhost:8080/, and you should see the “Hello from Python!” message.

That’s it! The application is now running in Kubernetes

Delete all the pods using following command

**All pods**
```sh
kubectl delete --all deployments
```
​
**kubectl delete all pods**
```sh
kubectl delete --all pods
```

**To check the service**
```sh
kubectl describe service myapp-service
```

## 📜 Release History

* 0.0.1
    - Testing K8's on local machine

## ✍️ Author

Ashish Kumar

[![LinkedIn](https://img.shields.io/badge/-Ashish%20Kumar-blue?style=social&logo=Linkedin&logoColor=blue&link=https://www.linkedin.com/in/ashishkrb7/)](https://www.linkedin.com/in/ashishkrb7/) 
[![Gmail](https://img.shields.io/badge/-Ashish%20Kumar-c14438?style=social&logo=Gmail&logoColor=red&link=mailto:ashishkrb7@gmail.com)](mailto:ashishkrb7@gmail.com)
