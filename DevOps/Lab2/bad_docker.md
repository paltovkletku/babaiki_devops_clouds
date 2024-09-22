FROM ubuntu:latest

RUN apt-get update

RUN apt-get install bash

RUN apt-get install -y vim

RUN apt-get install -y curl

ADD https://raw.githubusercontent.com/paltovkletku/babaiki_devops_clouds/main/DevOps/Lab2/happy.bash .

CMD ["bash", "happy.bash"]
