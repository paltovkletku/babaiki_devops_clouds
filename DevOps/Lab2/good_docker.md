FROM ubuntu:22.04

WORKDIR /lab2

RUN apt-get update &&
apt-get install -y vim curl &&
apt-get clean &&
rm -rf /var/lib/apt/lists/*

COPY happy.bash .

ENTRYPOINT ["bash"]

CMD ["happy.bash"]
