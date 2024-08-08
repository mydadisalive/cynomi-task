# Maven Jenkins Agent Docker Image

This Docker image is based on the Jenkins inbound agent with Maven installed. It is designed to be used as an agent in Jenkins CI/CD pipelines for building Java projects with Maven.

## Features

- Based on `jenkins/inbound-agent:jdk17`
- Maven 3.9.8 installed
- Google Chrome and ChromeDriver installed for UI testing
- Common VCS tools installed (Git, Mercurial, etc.)

## Usage

To use this Docker image as a Jenkins agent, you can specify it in your Jenkins node configuration. Here’s a basic example of running the image:

```bash
docker run --rm -it --entrypoint mvn my-maven-agent --version
```

## Configuration

The Docker image can be configured with the following environment variables:

| Variable       | Default Value | Description                          |
|----------------|---------------|--------------------------------------|
| `MAVEN_HOME`   | `/opt/maven`  | Path to the Maven installation       |
| `MAVEN_VERSION`| `3.9.8`       | Version of Maven installed           |

### Environment Variables

These environment variables are set within the Dockerfile and can be overridden at runtime if necessary:

- **MAVEN_HOME**: Specifies the directory where Maven is installed. This is useful for configuring Maven settings or scripts that rely on the Maven installation path.

- **MAVEN_VERSION**: Indicates the version of Maven that is installed in the Docker image. Ensure compatibility with your project's `pom.xml`.

You can set these environment variables using the `-e` flag with `docker run`. For example:

```bash
docker run --rm -it -e MAVEN_HOME=/custom/maven/path -e MAVEN_VERSION=3.9.8 my-maven-agent
```

This configuration allows you to adjust paths or Maven versions as needed for specific projects or environments.


### Explanation

- **Table**: A table is used to succinctly present the configuration options, making it easy to read and reference.
  
- **Description**: Each variable is described in detail below the table, providing more context on what each setting does.

- **Example**: An example command is given to show how to set environment variables when running the Docker container.
