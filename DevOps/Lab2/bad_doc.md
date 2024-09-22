FROM ubuntu:latest

RUN apt-get update
RUN apt-get install bash

ADD https://raw.githubusercontent.com/Darrifus/itmo-cloud-labs/main/DevOps%20%D0%9B%D0%B0%D0%B1%D0%B0%202/MyScript.bash](https://github.com/paltovkletku/babaiki_devops_clouds/blob/main/DevOps/Lab2/happy.bash .

CMD ["bash", "happy.bash"]
