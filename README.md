# cpp-docker

## What is CPP?
Cancer Publication Portal (CPP) is a tool will help researchers find biomedical journals related to human cancer. The tool will break down the journals by genes, cancer types, drugs, and mutations. The researcher will be able to filter the journals by these categories.

AACR 2019 Abstract: https://www.abstractsonline.com/pp8/#!/6812/presentation/6247

## Docker

### Problem
RStudio Shinyapp was limited to one connection at a time to the MySQL database. If there was multiple users using CPP, each users would have to wait for the pervious query to finish before their query will start up. This would make for a poor user experience for this tool.

### Solution
By using docker to containerize CPP, we can launch multiple containers of CPP and users could all connect to the database at the same time. Users would not have to wait for each other's query to run their own query on the database.

### My Contributions
I created the docker file to containerize the MySQL database for CPP to connect to. This will reduce down time when updating the database. When updating the database, a new database container will be created with the new data. Afterward, we would only need to change the configuration file of CPP container to point at the new database container. I created a script to automate the process of updating the CPP container configuration file.
