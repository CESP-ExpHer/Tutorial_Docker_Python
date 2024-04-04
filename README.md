# Practical Tutorial for Creating Python with Docker
Created by: Yazdan Asgari<br>
Creation date: 4 Apr 2024<br>
Update: Apr 2024<br>
https://cesp.inserm.fr/en/equipe/exposome-and-heredity
<br>
<br>
**IMPORTANT NOTE:** This tutorial has been tested on **CentOS 7**.

# Example 1
In this example, we want to create a Docker image including the following features
* Having a Python (last version) with pre-installed libraries (**"pandas"** and **"numpy"**)
* Copying three files (*"test.py"*, *"requirements.txt"*, and *"testfile.csv"*) in the root of the docker image.
  
**Steps:**
* Create a folder called **"test_docker_3"** in your Host machine.
* Put three files (*"test.py"*, *"requirements.txt"*, and *"testfile.csv"*) in the **"test_docker_3"** folder.
* In the **"test_docker_3"** folder, Create a file called **"dockerfile"** and write the following lines in it:
```
# Deriving the latest base image
FROM python:latest

# To COPY the remote file in the root directory of the docker container
COPY test.py ./
COPY requirements.txt ./
COPY testfile.csv ./

## To install Python Libraries from a file
RUN pip install --no-cache-dir -r requirements.txt
```
* Now it is time to build the Docker image. In the terminal, go to the **PATH** for **"test_docker_3"** folder using **cd** command
~~~
cd PATH_IN_YOUR_HOST_MACHINE/test_docker_3
~~~
* Check that you could be able to see **dockerfile** in this **PATH**:
~~~
ls
~~~
* Run the following command for building the Docker image with an arbitrary name (here we used **"docker_3"** for the name):
~~~
sudo docker build -t docker_3 .
~~~
* If all goes well without any error, you will be able to see the created Docker image (**"docker_3"**) with this command:
~~~
sudo docker images
~~~
* For running the Docker container and accessing the bash shell of the container, use this command:
~~~
sudo docker run -it docker_3 bash
~~~
* Now you are in the bash of the Docker Container and see the files by this command:
~~~
ls
~~~
* And you could run the *"test.py"* python script using this command and see the output:
~~~
python test.py
~~~
* To exit the Docker Container, run the following command:
~~~
exit
~~~


# Example 2
In this example, we want to create a Docker image including the following features
* Having a Python (last version) with pre-installed libraries (**"pandas"** and **"numpy"**)
* Creating two folders (**"input"** and **"output"**) in the root of the Docker image.
* Copying two files (*"code_reformatting.py"* and *"requirements.txt"*) in the root of the docker image.
  
**Steps:**
* Create a folder called **"test_docker_4"** in your Host machine.
* Put three files (*"code_reformatting.py"* and *"requirements.txt"*) in the **"test_docker_4"** folder.
* In the **"test_docker_4"** folder, Create a file called **"dockerfile"** and write the following lines in it:
```
# Deriving the latest base image
FROM python:latest

# To create directories
RUN mkdir -p /input
RUN mkdir -p /output

# To COPY the remote file in the working directory in the container
COPY code_reformatting.py ./
COPY requirements.txt ./

## To install Python Libraries
RUN pip install --no-cache-dir -r requirements.txt

## This command is used as the entry point, which essentially runs indefinitely and keeps the container running in the background
ENTRYPOINT ["tail", "-f", "/dev/null"]
```
* Now it is time to build the Docker image. In the terminal, go to the **PATH** for **"test_docker_4"** folder using **cd** command
~~~
cd PATH_IN_YOUR_HOST_MACHINE/test_docker_4
~~~
* Check that you could be able to see **dockerfile** in this **PATH**:
~~~
ls
~~~
* Run the following command for building the Docker image with an arbitrary name (here we used **"docker_4"** for the name):
~~~
sudo docker build -t docker_4 .
~~~
* If all goes well without any error, you will be able to see the created Docker image (**"docker_4"**) with this command:
~~~
sudo docker images
~~~
* In this step, we just want to run the Docker Container  and have access to the Docker Container's shell later. So, we want to run the Docker Container in the background. The **"-d"** flag stands for **"detached mode"** and runs the container in the background. In addition, we want to map a folder in our Host Machine (**"0_docker_4"**) to the **"output"** folder in the Docker Container. So, we could use the following command:
~~~
sudo docker run -d -v PATH_IN_YOUR_HOST_MACHINE/0_docker_4:/output docker_4
~~~
* If all goes well without any error, you will be able to see the created Docker Container (**"docker_4"**) with this command:
~~~
sudo docker ps
~~~
* Copy the **<CONTAINER_ID>** that you found after the above command. Now we want to copy two files from the Host Machine in the **"input"** folder of the Docker Container:
~~~
sudo docker cp PATH_IN_YOUR_HOST_MACHINE/0_docker_4/BCAC_2020_onco_ALL_reformatted.txt <CONTAINER_ID>:/input
sudo docker cp PATH_IN_YOUR_HOST_MACHINE/0_docker_4/formatted_invnormFT4_women_150611_invvar1.txt-QCfiltered_GC.txt <CONTAINER_ID>:/input
~~~
* **NOTE:** We want to use the two files that we had in the Host Machine (in the **"0_docker_4"** folder) as inputs for the Python script.
* Now, run the following command to access the bash shell of the Docker Container:
~~~
sudo docker exec -it <CONTAINER_ID> bash
~~~
* You could check if the copied files are available in the **"input"** folder:
~~~
cd input
ls
cd ..
~~~
* Finally, you could run the *"code_reformatting.py"* python script which reads two input files from the **"input"** folder and save the outputs in the **"output"** folder:
~~~
python code_reformatting.py
~~~
* Since we mapped the **"0_docker_4"** folder in our Host Machine to the **"output"** folder of the Docker Container, we can find the outputs in the **"0_docker_4"** folder in our Host Machine too.
* To exit the Docker Container, run the following command:
~~~
exit
~~~
