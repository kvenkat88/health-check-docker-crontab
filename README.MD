#### For flask or any web framework
docker run --rm -it -d --name health_checker -p 4888:5000 health_checker:<name>

#### Docker build image
docker build -t health_checker:<name> --build-arg HOST_INFO=<Docker_host or Container IP> .

#### Example Docker Build Command
docker build -t health_checker:0.1 --build-arg HOST_INFO=0.0.0.0 .

#### Docker Run Command
docker run --rm -it -d -v /var/log/health_checker:/var/log/health_checker -v $HOME/health_checker_config/configs:/app/configs --env HOST_INFO=<Docker_host or Container IP> --name health_checker health_checker:<name>

  ` -v /var/log/health_checker:/var/log/health_checker  - volume mount path for redirecting the log files from docker container to docker hosted machine
     where first part before : is path from host machine and part after is path for logs in docker container running.
  `
  
  ` -v $HOME/health_checker_config/configs:/app/configs  - volume mount path for redirecting the files from docker container to docker hosted machine
     where first part before : is path from host machine and part after is path in docker container running. $HOME expansion is 
     /home/{configured_linux_user}/health_checker_config/configs. Inside of this path, base.env would be available.
  `
  
  `
    Under the path - /home/{configured_linux_user}/health_checker_config/configs of host machine, actual values of environment specific host URLs are mapped.
    For example, INFEC_DISEASE_API_HOST=http://localhost:8082 would be mapped in base.env
  `
#### Example Docker Run Command
docker run --rm -it -d -v /var/log/health_checker:/var/log/health_checker -v $HOME/health_checker_config/configs:/app/configs --env HOST_INFO=0.0.0.0 --name health_checker health_checker:0.1

#### Notes to Remember::
1. Currently http requests for the environment **specific URLs are crawled for every 2 minutes**. This can be configurable by changing in **crontab.txt**

   `*/2 * * * * python /app/request_executor.py >> /var/log/health_checker/http_request_executor.log 2>&1`

2. **HOST_INFO** value mentioned in **docker build build-arg and docker run --env** is the docker host ip or docker container ip.

3. **request_executor.py** file holds methods related with HTTP Request and Response crawling.

4. Add the python related library in requirements.txt and while building docker, libraries are automatically installed.

5. **entrypoint.sh** is the entry for starting the docker run and to run the crontab job in background.

6. Generalized host urls are mentioned in **configs/base.env**. To run the docker container for specific environment(say dev), then mount the path using docker
   run using -v flag.
