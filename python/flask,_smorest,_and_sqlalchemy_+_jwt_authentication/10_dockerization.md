# Dockerization

This section covers:  

* Setting up the Dockerfile
* Setting up necessary support files

## The Dockerfile

The Dockerfile is used by Docker to build an image that can be deployed and spun up into containers.  A full Docker tutorial is outside the scope of this documentation, but we will take a look at the features being set up in this file.  Here is the complete file:

```dockerfile
FROM python:3.10
EXPOSE 5000
WORKDIR /app
RUN pip install flask
COPY . .
CMD["flask", "run", "--host", "0.0.0.0"]
```

## Notes

1. Use the latest python version 
1. Set the port as appropriate

5. Here, the first `.` is the current directory on your machine and the second `.` is the `WORKDIR` set in line 3
This should be usable as boilerplate for any of our API containers as all API specific code has been isolated away from it in either the `src` directory or in environment variables.  Here's a breakdown of what this is doing:

## Support Files

There are several batch files which can help make development run more smoothly:
| File         | Purpose                                                      |
| ------------ | ------------------------------------------------------------ |
| .env         | Contains all environment variables needed by the container.  |
| build.bat    | Run the docker image build.                                  |
| push.bat     | Pushes the docker image out to our artifact repository.      |
| run.bat      | Stops the running container, deletes it, and then recreates it from the image. |
| runtests.bat | Described in the testing section, this batch file sets up Pytest's environment so that tests can be easily discovered and ran from the command line. |

### `.env`

```
CORS_RESOURCES={"/api/*": {"origins": "http://localhost:4200"}}
```

While these values can be passed in via the environment or command line, using a .env file makes it simpler to run on your local environment.  Additionally, passing JSON in (for CORS_RESOURCES) is unaccountably tricky from the command line, so doing that in a .env file is far easier.

### `build.bat`

```
docker build -t my_api .
```

Modify this to change the name of the image to suit your project.

### `run.bat`

```
docker stop my_api_container
docker rm my_api_container
docker run -p 8080:8080 --env-file .env -d --name my_api_container my_api
```

A lot of changes will be needed here to fit your project.

* Change `my_api_container` to meet your container name (as opposed to the image name!)
* Change the ports in `docker run -p 8080:8080` to fit your needs (or the needs of the deployment.)
  * Port order is host_port:container_port
  * Example:  I want my host to listen on 80 and pass traffic to the container's port 5000, it would look like this: 80:5000
* `--name` needs to be set to your image name.
