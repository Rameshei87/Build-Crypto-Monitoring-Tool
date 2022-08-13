# Build-Crypto-Monitoring-Tool

This is an ETL pipeline to pull bitcoin exchange data from CoinCap API and load it into our data warehouse. 

Architecture

![image](https://user-images.githubusercontent.com/110036451/184507210-ffff0b00-e8d7-44b6-8067-a6fcf024c500.png)

Arch

We use python to pull, transform and load data. Our warehouse is postgres. We also spin up a Metabase instance for our presentation layer.

All of the components are running as docker containers.

Setup

Pre-requisites
Docker and Docker Compose v1.27.0 or later.
AWS account.
AWS CLI installed and configured.
git.

Local

We have a Makefile with common commands. These are executed in the running container.

cd Build Crpyto Monitoring Tool

make up # starts all the containers
make ci # runs formatting, lint check, type check and python test

If the CI step passes you can go to http://localhost:3000 to checkout your Metabase instance.

You can connect to the warehouse with the following credentials

Host: warehouse
Database name: finance
The remaining configs are available in the env file.

Refer to this doc for creating a Metabase dashboard.

Production

In production we will run the instances as containers. We have helper scripts in deploy_helpers for this.

You will need to have an ubuntu x_86 EC2 instance with a custom TCP inbound rule with port 3000 open to the IP 0.0.0.0/0.

These can be set when you create an AWS EC2 instance in the configure security group section. A t2.micro (free-tier eligible) instance would be sufficient.


![image](https://user-images.githubusercontent.com/110036451/184507301-8156571d-3631-4e9d-8bec-fd36d117bfa8.png)


Sec group

You can setup a prod instance as shown below.

cd bitcoinmonitor

chmod 755 ./deploy_helpers/send_code_to_prod.sh

chmod 400 pem-full-file-location

./deploy_helpers/send_code_to_prod.sh pem-full-file-location EC2-Public-IPv4-DNS


# the above command will take you to your ubuntu instance.
# If you are having trouble connecting use method 2 from https://aws.amazon.com/premiumsupport/knowledge-center/ec2-linux-fix-permission-denied-errors/

# install docker on your Ubuntu EC2 instance

chmod 755 install_docker.sh
./install_docker.sh

# verify that docker and docker compose installed
docker --version
docker-compose --version

# start the containers

unzip crpytomonitoringtool.gzip && cd cryptomonitoringtool/
docker-compose --env-file env up --build -d
Tear down
You can spin down your local instance with.

make down
Do not forget to turn off your EC2 instance.
