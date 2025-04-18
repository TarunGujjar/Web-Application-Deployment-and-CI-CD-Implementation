# Containerized web application using Docker
1. Creating a Docker image and pushing it to DockerHub:
- Ensure that Docker is installed on our machine and create an account on https://hub.docker.com/.
- In your eclipse environment, create a Dockerfile without any extension (this name is specific for Docker's use).
- Compile your project files within the code editor, ensuring the resultant war file is inthe same directory as your Dockerfile. Assume "survey" as your Tomcat application's name.
- Write the following in your Dockerfile:
  FROM tomcat:latest
  COPY survey.war /usr/local/tomcat/webapps/
- Open the command prompt and set the path of the directory to where the Dockerfile resides.
- Build the Docker image with the command docker build --tag survey:0.1.
- In order to check if the image has been created, run the command docker images
- Check the image's correctness by running docker run -it -p 8182:8080 survey:0.1
- Visit http://localhost:8182/survey via a web browser.
- Log into Docker Hub through the terminal:
  docker login user_name.
- Rename your Docker image to align with the docker tag command
  docker tag survey:0.1 tarungujjar/survey:0.1. (where survey is the docker image name)
- The docker image must be pushed to the docker hub using the command
  docker push user_name/survey:0.1.
- The Docker image is publicly accessible and ready for use in Rancher Deployment.


2. Orchestrated deployment using Kubernetes
3. Built a CI/CD pipeline using Jenkins

