FROM ubuntu:22.04

COPY /lab2/happy.bash

CMD ["bash", "happy.bash"]
