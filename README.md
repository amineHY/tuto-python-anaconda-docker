# How to Setup A Python Project with Anaconda in Docker

Quick tutorial for packaging Python project using Anaconda with Docker

I write this quick tutorial for those who want to create a python app with anaconda packages and package the app using Docker.
amineHY/tuto-python-anaconda-docker

A project to quickly setup a python project using anaconda packages and Docker GitHub is home to over 50 million…
github.com

# Project Structure

```
.
├── Dockerfile
├── environment.yaml
├── main.py
├── README.md
└── requirements.txt
```

# Create a YAML file for Conda Environment

_environment.txt_

```yaml
name: app

channels:
  - conda-forge
  - anaconda

dependencies:
  - flask
  - gunicorn
  - pandas
  - matplotlib
  - scikit-learn
  - pip:
      - -r file:requirements.txt
```

- Name your conda environment, for instance app
- Add a list of channels where the desired packages are hosted
- List packages you want to install such as flask, gunicorn, numpy… under dependencies
- You can also install pip packages from the YAML file. One way to it is by adding a pip command to install (pip) packages from a requirements.txt, (-r in the command stands for requirement)

For instance:

_requirements.txt_

```
numpy
```

# Create a Python Script

Here we have created a test file main.py for testing the application.

_main.py_

```python
import numpy as np


print('Hello World !')
print('Numpy version is ', np.__version__)
```

# Dokerization

Create a file named `Dockerfile` (without extension) from which we build a docker image for our Python application.

_Dockerfile_

```docker
FROM  continuumio/miniconda3
LABEL Author, Amine HadjYoucef

ENV APP_HOME /app
WORKDIR $APP_HOME
COPY . $APP_HOME

#---------------- Prepare the envirennment
RUN conda update --name base conda &&\
    conda env create --file environment.yaml
SHELL ["conda", "run", "--name", "app", "/bin/bash", "-c"]

ENTRYPOINT ["conda", "run", "--name", "app", "python", "main.py"]
```

- For simplicity we start from the base image \* of miniconda3 (line1)
- Copy the files from the folder to docker \* (line 6)
- Create a conda environment and install the _ in it the packages defined in YAML file _ (line 9)
- Edit the shell command so we can launch _ python scripts from within the newly _ created conda environment (line 11)
- Define an entry-point for the docker image _ such that it execute the main.py python _ script of the application (line 13)

## Build a docker image

We build a docker image, named app, with the following commands

    docker build --tag myapp .

… then wait for docker to build an image for your app (it might tight a take for downloading and update it the packages list).

**Note:**

    The Dockerfile must be located in the root directory of the application
    Make sure Docker is installed on your machine (www.docker.com).

## Run the docker image

To run the App from the docker image we created

    docker run --rm -ti myapp

This should outputs:

    Hello World !
    Numpy version is  1.19.4

There you have it.

You can find all the files on Github:
[amineHY/tuto-python-anaconda-docker](amineHY/tuto-python-anaconda-docker)

PS: If you encounter a problem please create an issue on GitHub or comment below.
Resources
