# Use an official Python runtime as a parent image
ARG VARIANT="3.10"
FROM python:${VARIANT}

# Set the working directory in the container to /app
WORKDIR /app

# Install Git LFS
RUN apt-get update && \
    apt-get install -y curl git && \
    curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash && \
    apt-get install -y git-lfs && \
    git lfs install

# Create a new user 'vscode' and switch to it
RUN useradd -m vscode && chown -R vscode /app
USER vscode

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install gensim==4.3.0 pandas==2.0.2