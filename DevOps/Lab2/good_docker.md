FROM ubuntu:22.04

WORKDIR /lab2

COPY happy.bash .

ENTRYPOINT ["bash"]

CMD ["happy.bash"]
