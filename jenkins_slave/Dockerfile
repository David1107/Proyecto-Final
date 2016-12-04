FROM evarga/jenkins-slave
run apt-get update
run apt-get install -y wget
run apt-get install -y git
run apt-get install -y python
run wget https://bootstrap.pypa.io/get-pip.py
run python get-pip.py
USER root
workdir /home/jenkins/workspace/test
run chmod -R 777 /home/jenkins/workspace/test
copy requeriments.txt /home/jenkins/workspace/test
run pip install -r requeriments.txt
