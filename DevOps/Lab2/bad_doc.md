FROM ubuntu:latest

RUN apt-get update
RUN apt-get install bash

ADD https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2/happy.bash .

CMD ["bash", "happy.bash"]
