# Deploying a Streamlit App on Docker

## TLDR
This is a guide on how to deploy a Streamlit app via Docker container

## Outline
- Introduction
- Streamlit
- What is Docker?
- Why Containerize a ML app with Docker?
- Installing Docker
- About the Streamlit ML App
- Creating a Dockerfile
- Building a Docker Image
- Push Image to Docker Hub
- Test: Pulling a Docker Image
- Conclusion or Final thoughts

### Introduction
Are you excited about a new machine learning model you have built? Whether it is making movie recommendations or trying to predict the occurrence of a disease in patients, the idea is to share the model with people in order to make it useful. In this guide, we will explore how to:
1) Wrap the model code in Streamlit to create a beautiful, interactive interface
2) Deploy/share the model using Docker containers

### Streamlit
So you have built a machine learning model. It is producing reasonably accurate recommendations/predictions for a fixed set of input parameters. However, in order to make the model useful, anyone should be able to interact and play around with the input parameters without having to read through a wall of code. This is where Streamlit comes in.

Streamlit is a framework that creates an interactive user interface for a machine learning model. By wrapping the model code in simple Streamlit functions, the data scientist is able to create a simple web application that provides users the option to select or enter model parameters and observe the results.
Some of the user interface elements that Streamlit provides include dropdown menus, sliders, checkboxes, text input, and many others.


 Ref: https://streamlit.io/

A detailed guide for basic Streamlit functions can be found in this article.

Once you have an interactive front-end web interface for general users to interact with, there are multiple ways of sharing the ML application with the world. The easiest and quickest method to do this would be to use Streamlit Sharing - a service that connects to your publicly accessible Streamlit code on Github and makes the application available via a shareable link.

​​


The above method works great for personal applications where you can make the code publicly available on Github and the app link can be distributed without any security measures. But what if you need to deploy the machine learning product application to several customers, each with their own system configurations? Since the product would be developed internally within the organization, the code cannot be made publicly available on Github. 

In such cases, it is essential for the model code wrapped in Streamlit functionality to be recreated in a platform-agnostic manner with its specific package versions, configurations, and dependencies.

Imagine if you could put all the ingredients of the model code into a jar, and then share it with your client. They need not know the exact contents of the jar but could simply apply them together to recreate your Streamlit ML application. There exists a service that provides these jars to anyone who wishes to use them - this service is named ‘Docker’.

### What is Docker?
Docker is a service that provides you with the ‘jar’ or container mentioned in the above example. It is a container management tool that packages the code, configurations, and dependencies of any application, and is a method of deployment for Streamlit ML apps.

### Why Containerize a ML app with Docker?
Let us suppose you made a scrumptious spaghetti dish that one of your friends really enjoyed. She wants to recreate the dish for herself. You are allowed to share the recipe with her in two ways:
Take a container and add all the ingredients of the dish in their specific quantities to give to your friend
Write down the recipe and store it in a ‘Hub’. Now anytime a friend of yours wants to make your spaghetti dish, they can access the recipe stored in the Hub, and recreate their ingredients jar to make the dish


Ref: https://www.itzgeek.com/how-tos/linux/working-with-docker-images-building-docker-images.html


In terms of software applications, 

Ingredients Jar → Docker Container <br>
Recipe → Docker Image <br>
Hub → Docker Hub, AWS Cloud, Google Cloud Platform Registry, and other platforms <br>


Example: Named Entity Recognition
To demonstrate the process of containerizing a Streamlit Machine Learning app, we will consider a simple app for Named Entity Recognition (NER). 

Named-entity recognition is a part of information extraction that identifies named entities in a piece of text. The names are then classified into predefined categories such as names of individuals, places, organizations, and others. In this model, we will be using the spaCy library to create a simple Natural Language Processing (NLP) model.

Here is an example of how a NER app works:



spaCy is an advanced open-source NLP library that is commonly used in information extraction tasks. It offers a set of functionalities such as named entity recognition and parts-of-speech tagging that makes it a popular NLP library among data scientists. 

In this demo, we will be using the ‘en_core_web_sm’ statistical model within the spaCy library which is a Convolutional Neural Network (CNN) trained to identify components of the English language. Further information on the spaCy statistical models can be found in the spaCy documentation.


### Installing Docker
Docker Desktop provides a user-friendly interface to start working with Docker.

Download Docker Desktop: https://www.docker.com/products/docker-desktop
Sign in to Docker Desktop 



Open the Command Line app on your computer (Terminal for MacOS or Command Prompt for Windows). Use the command docker login to login to Docker.


### Creating a Dockerfile
Now that the Artificial Intelligence model is ready and wrapped in Streamlit, the next step is to create a file called the ‘Dockerfile' that tells Docker how to create an image for the app.



Here is a breakdown of each command in the Dockerfile above:
- The FROM command indicates to Docker to create a base layer. In this case, we have used the Python v3.7 image available in Docker Hub as the base image on which our application will be built
- EXPOSE specifies the port number on the network where our application will be deployed. The Streamlit library is particularly compatible with the Port 8501 as set above
- WORKDIR sets the Working Directory within the Docker image. In this case, it is creating an ‘app’ folder where all files will be stored
- COPY command is duplicating the local requirements.txt file (which contains package names and versions required by the model app) into the Docker image’s ‘app’ folder
- The RUN command specifies what Command Line Interface (CLI) commands to run within the Docker container. In this Dockerfile, it is installing all packages and dependencies in the requirements.txt file, and running some additional commands to download the spaCy ‘en_core_web_sm’ model correctly from the web
- Lastly, CMD is used to specify the commands to be run when the Docker container is started

Building a Docker Image
Keep Docker Desktop open and signed in
Within the Command Line Interface (CLI), navigate inside the project directory using cd
In this case, the project directory is in Documents folder which leads to the command: 
```cd Documents/named_entity_recognition```

**Note**
Docker requires the project folder to be completely in lowercase letters and/or underscores. It is important to ensure the name is in lowercase before building the app in the next step.

Build a Docker image for the NER app with the command:
docker build -f Dockerfile -t named_entity_recognition:latest .   

The -f  flag is used to specify the name of the Dockerfile to be used for building the image
The -t  flag is used to name and/or tag the image. In this case, we are tagging the Docker image with the ‘latest’ tag
	
For a complete list of flags available for the docker build command, you may refer to the Docker documentation.

Note:
The dot at the end of the command must be present. It indicates that Docker should use files from the current directory to build the image.




The image will appear in Docker Desktop:




4) To ensure the image has been built correctly, we can run the application from the Docker container with the command:

docker run -p 8501:8501 named_entity_recognition:latest

The -p  flag is used to map the container’s port to the host port i.e. Port 8501

For a complete list of flags available for the docker run command, you may refer to the Docker documentation.




This is how it will appear in the ‘Containers/Apps’ section of Docker Desktop:




Push to Docker Hub
Docker Hub is a platform for storing container images, where it can be accessed by anyone on the Internet (at least the public repositories). It is the world’s largest repository of container images.

While there are a plethora of options to store a container image (e.g. AWS, GCP, etc.), we will be storing our container in the Docker Hub registry for others to access from.

The following steps outline how to push your local Docker image onto Docker Hub:

Login to Docker Hub with the account you created earlier and create a new repository as shown.





Tag the locally built image to the empty repository

	docker tag named_entity_recognition ishaterdal/streamlit_app_docker


Push the Docker image into the Hub repository
docker push ishaterdal/streamlit_app_docker



This is what the Docker image looks like on Docker Hub:


### Test: Pulling a Docker Image
At this point, your ML model has been created, wrapped in Streamlit for a user-friendly interface, and stored on Docker Hub registry for any individual or organization to access. Let us now see how anyone can pull the image from Docker Hub registry and start working with it.

Pull Image from Docker Hub onto local machine
docker pull ishaterdal/streamlit_app_docker

Run Docker Container locally with the same command as above
`docker run -p 8501:8501 ishaterdal/streamlit_app_docker`
Open a browser and go to localhost:8501 to run the app on any machine




Here is how the deployment looks like:



### Conclusion
This is how you can share your ML application wrapped in Streamlit using Docker. Docker containers are a common way of deploying ML applications built for various business purposes. Happy Sharing!



### Additional Information
https://maelfabien.github.io/project/Streamlit/#
https://www.geeksforgeeks.org/a-beginners-guide-to-streamlit/
https://www.analyticsvidhya.com/blog/2021/06/a-hands-on-guide-to-containerized-your-machine-learning-workflow-with-docker/
https://www.analyticsvidhya.com/blog/2020/03/spacy-tutorial-learn-natural-language-processing/
https://docs.docker.com/engine/reference/commandline/pull/
