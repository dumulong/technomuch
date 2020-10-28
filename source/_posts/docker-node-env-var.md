---
title: Passing environment variables from Docker to Nodejs
tags:
  - Node.js
  - Docker containers
categories: 'Docker containers'
date: 2020-10-28 06:54:03
---

One of the main issue when creating and releasing docker images is making sure that we do not reveal any secret information (password, etc).

In Node, we often use a .env file to achieve this goal.  Sadly, we would need to include this file in our Docker image which would make it unsecure. Indeed, if such file is added to the image, anybody who get the image would be able to see the content of that file.

So instead, we will want to pass our secret information when we are running the image.  There are 2 ways to pass environment variables to Docker:


- __Using the option -e__

  ```
  docker run  [...] -e my_connection_string="xxxxx" -e my_password="xxx" my_node_container
  ```

- __Using the env-file docker option__


  For this method, you'll need to create a file containing the list of ```KEY=Value``` pairs.
  Example:
  __my_env.list__
  ```
  my_connection_string=xxxxxx
  my_password=yyyyyyyy
  my_secret=zzzzz
  ```
  Then, run the container using the ```--env-file``` option:
  ```
  docker run [...] --env-file ./my_env.list my_node_container
  ```
See this [document](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file) for more details.

From there, you will be able access these variables from Node by using ```process.env.{KEY}```.

Please note that, as a general rule, you should always follow the motto "batteries included but removable".  Meaning that you should code your Node application to have default values (when possible) so the software will run without providing these environment variables.  So it can run "straight out of the box".

## Simple example:

Here's an example to get you started

File: __env_test.js__
```
console.log(process.env);

```
File: __my_env.list__
```
my_connection_string=xxxxxx
my_password=yyyyyyyy
my_secret=zzzzz
```

File: __Dockerfile__
```
FROM node:12.19.0-alpine3.10

RUN mkdir -p /usr/app/src

WORKDIR /usr/app/src

COPY env_test.js .

CMD [ "node", "env_test" ]
```

Then build your container:
```
docker image build -t node-test .

```
Run it with the __-e__ flag:
```
docker container run -e my_test="this is a test" node-test
```
Or, run it with the __--env-file__ flag:
```
docker container run --env-file ./my_env.list node-test
```
