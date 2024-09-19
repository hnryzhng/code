# Code instructions and snippets

## Sections

Python
- [Create a pipenv](#create-a-pipenv)
- [Install dependencies for a Python project](#install-dependencies-for-a-python-project)

AWS
- [Build and Deploy AWS Lambda function in Python](#build-and-deploy-aws-lambda-function-in-python)

Linux shell scripts
- [Linux commands cheatsheet](#linux-commands-cheatsheet)
- [Shell scripting](#shell-scripting)
- [Bash scripts for ETL](#bash-scripts-for-etl)
- [Bash scripts for Python project](#bash-scripts-for-python-project)

Apache Airflow
- [Airflow DAG example](#airflow-dag-example)

Apache Kafka
- [Apache Kafka setup](#apache-kafka-setup)

Docker
- [Docker commands cheatsheet](#docker-commands-cheatsheet)

## Python
### Create a pipenv 
(Python virtual environment)

```
# Create a virtual environment
$ python -m venv myenv

# Activate the virtual environment

# On Unix or MacOS
$ source myenv/bin/activate

```

### Install dependencies for a Python project

```
# 1. Activate the virtual environment according to the above

# 2a. Installing a single package
$ pip install PACKAGE_NAME

# 2b. Install packages in requirements.txt
$ pip install -r requirements.txt

# file: requirements.txt
# mypackage==PACKAGE_VERSION

```


## AWS 

### Build and Deploy AWS Lambda function in Python
Note: Can put commands below in a bash script "build.sh", then execute using $bash build.sh

1. Create a Deployment Directory: Create a directory to store the packages and code.
```
mkdir deployment_package
```

2. Copy Installed Packages: Copy the site-packages from the virtual environment into the deployment package directory.
```
# Replace python3.x with your Python version.

cp -r myenv/lib/python3.x/site-packages/* deployment_package/

```

3. Add Your Code: Copy your Python files (e.g., lambda_function.py, llm_service.py, etc.) into the deployment package directory. Or can add Python files into src/ folder, so use second command
```
$ cp lambda_function.py llm_service.py deployment_package/
OR
$ cp -r src/* deployment_package/
```
4. Zip the Deployment Package: Navigate to the deployment package directory and create a ZIP file. 
```
$ cd deployment_package
$ zip -r9 ../lambda.zip .
$ cd ..
```

5. Upload ZIP file using AWS console in Lambda dashboard




## Linux commands cheatsheet

[Linux commands cheatsheet from Coursera](https://author-ide.skills.network/render?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJtZF9pbnN0cnVjdGlvbnNfdXJsIjoiaHR0cHM6Ly9jZi1jb3Vyc2VzLWRhdGEuczMudXMuY2xvdWQtb2JqZWN0LXN0b3JhZ2UuYXBwZG9tYWluLmNsb3VkL0lCTS1MWDAxMTdFTi1Ta2lsbHNOZXR3b3JrL2xhYnMvdjRfbmV3X2NvbnRlbnQvbGFicy9yZWFkaW5ncy9NMl9DaGVhdF9TaGVldF9JbnRyb190b19MaW51eF9Db21tYW5kcy5tZCIsInRvb2xfdHlwZSI6Imluc3RydWN0aW9uYWwtbGFiIiwiYWRtaW4iOmZhbHNlLCJpYXQiOjE3MTE2Mzg2Mzl9.hPGxtLz4kFonOfMyXgeNXoj225Ow7P2-kil58GtJQKM)

### General OS commands
```
$ whoami # prints current username
$ id # list users and user groups in system
$ uname # print basic system info
$ df # check system disk usage
$ date # current date and time
$ man COMMAND # read manual for command

# monitor processes
$ ps
$ top # more detailed ps info

```

### File and Directory Navigation commands
```

$ pwd  # absolute path to current directory (present working directory)
$ cd ~ # move to home directory
$ cd PATH # move to specified path
$ ls -a DIR_NAME # list all files in directory
$ ls -l FILE_NAME or DIR_NAME # list access permissions
$ ls -r # lists recursively all directories, subdirectories, and files in a tree-like representation
$ find FILENAME # find file in the directory

$ chmod <subcommand> FILENAME or DIR_NAME # change access permissions of file or directory
$ chmod +x FILENAME # make file executable
$ chmod +(w/r/x) FILENAME # add write, read or executable permissions
$ chmod -(w/r/x) FILENAME # take away write, read or executable permissions


```

### File and String Manipulation commands
```

$ cp SOURCE_DIR_PATH TARGET_DIR_PATH # copy all files from source to target directories
$ mv SOURCE_DIR_PATH TARGET_DIR_PATH # move all files from source to target directories
$ echo "body string" > OUTPUT_FILE_NAME # create file initialized with string
$ echo "body string 2" >> OUTPUT_FILE_NAME # append to existing file
$ cat <<EOF>> OUTPUT_FILE_NAME # multi-line write to a file, end with "EOF"
$ cat 1.txt 2.txt 3.txt # merge 3 columns from each file
$ mkdir DIRECTORY_NAME
$ touch FILE_NAME
$ less FILE_NAME # show file within scrollable view
$ more FILE_NAME # show file
$ grep STRING_PATTERN FILE_NAME # outputs line within file given a pattern to search

```

### File archiving commands
```
# create a .tar file to bundle a directory and its contents (subdir and files) into one file 

$ ls -r # list files and subdirectories within directory
$ tar -cf FILE.tar SOURCE_DIRECTORY # creates a .tar file for the directory (contains files and subdirectories)
$ tar -czf FILE.tar.gz SOURCE_DIRECTORY # creates a compressed (gzipped) .tar.gz file

$ tar -tf FILE.tar # lists all files and directories within .tar file

$ tar -xf FILE.tar TARGET_DIRECTORY # de-archive/extract/unpack contents of .tar file into a destination directory, preserving the directory hierarchy
$ tar -xzf FILE.tar.gz DESTINATION_DIRECTORY # de-archive compressed .tar file

```

### File compression commands
```
# zip vs tar.gz -> zip compresses then bundles, tar bundles then compresses

$ zip -r FILE.zip SOURCE_DIRECTORY # creates zipped file from source directory
$ unzip FILE.zip # unzip -> unpacks and decompresses files


$ tar -czf FILE.tar.gz SOURCE_DIRECTORY # creates a compressed (gzipped) .tar.gz file
$ tar -xzf FILE.tar.gz DESTINATION_DIRECTORY # de-archive compressed .tar file


```



### Networking commands
```
$ ping TARGET_URL
$ wget FILE_LOCATION
$ curl TARGET_URL
```


## Shell scripting
Filters are shell commands

### Shell scripting basics
```
# Pipes
$ COMMAND_1 | COMMAND_2 # output of command 1 input to command 2
$ ls | sort -r # lists files in dir and reverse sorts 

# Shell variables
(scope limited to shell)
$ set # lists all shell variables
$ SHELL_VAR=VALUE # define shell variable
$ echo $SHELL_VAR # prints value for shell variable
$ unset SHELL_VAR # deletes shell variable
$ VAR1 = $(command) # executes command then stores output in the variable
# example: NOW = $(date)

# Environment variables
(scope extends beyond shell session)
$ env # lists all env vars
$ export VAR_NAME # exports var to env variable
$ env | grep "GREE" # can chain commands using pipe to filter for env var(s) using grep


```

### Shell scripting logic
[Coursera's shell scripting logic cheatsheet](https://author-ide.skills.network/render?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJtZF9pbnN0cnVjdGlvbnNfdXJsIjoiaHR0cHM6Ly9jZi1jb3Vyc2VzLWRhdGEuczMudXMuY2xvdWQtb2JqZWN0LXN0b3JhZ2UuYXBwZG9tYWluLmNsb3VkL0lCTS1MWDAxMTdFTi1Ta2lsbHNOZXR3b3JrL2xhYnMvdjRfbmV3X2NvbnRlbnQvbGFicy9yZWFkaW5ncy9NM19DaGVhdF9TaGVldF9JbnRyb190b19TaGVsbF9TY3JpcHRpbmcubWQiLCJ0b29sX3R5cGUiOiJpbnN0cnVjdGlvbmFsLWxhYiIsImFkbWluIjpmYWxzZSwiaWF0IjoxNzExNjM4NjQxfQ.a5rAy3qDmGekAMX0CfgHEIBiX3QDzC9e4UuP9s2idzg)
```
# Conditionals
if [[ $# == 2 ]]
then
  echo "number of arguments is equal to 2"
else
  echo "number of arguments is not equal to 2"
fi

# Logical operators
==	is equal to
!=	is not equal to
<	is less than
>	is greater than
<=	is less than or equal to
>=	is greater than or equal to

# Arithmetic calculations
# Integer arithmetic notation: $(())
# example: echo $((-1*-2))

# Arrays
$ my_array=(1 2 "three" "four" 5)
$ my_array+=7
$ my_array+="six"
$ my_array=($(echo $(cat column.txt))) # Declare an array and load it with lines of text from a file

# Loops

for item in ${my_array[@]}; do
  echo $item
done

for i in {0..6}; do
    echo ${my_array[$i]}
done


```

### Scheduling jobs
- Cron: service that runs jobs
- Crond: interpretese crontab files
- Crontab: file that contains jobs and schedule data
```

$ crontab -e # open crontab text file in CLI text editor
# crontab - l # list all cron jobs and their schedules
# syntax for cron jobs
# m h dom mon dow command 

# example crontab file
# m h dom mon dow command
30 15  *   *   0  date >> path/sundays.txt # on Sundays at 15:30, execute command to write date to the target file

0 0 * * *         /cron_scripts/load_data.sh # at midnight every day, execute load_data.sh
```

## Bash scripts for ETL

### Shell scripting ETL example: Earthquake data
```
#!/bin/bash

# Extract
extracted_file_name="earthquakes.csv"
source_data_url=https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.csv
echo "retrieving csv data from url: $source_data_url"
wget -O $extracted_file_name $source_data_url

# Transform
# get columns (time, latitude, longitude, magnitude, place)
staging_file_name="earthquakes-staging.csv"
delimiter=","
fields=1,2,3,5,14
echo "getting columns into staging: $(head -1 earthquakes.csv | cut -d $delimiter -f $fields)"
cut -d $delimiter -f $fields $extracted_file_name > $staging_file_name # note: delimiter comma cuts off place because field value has commas within string

# Load
# load into target data destination, such as database or just another csv file
# note: not applicable here, since staging and destination are the same csv file
output_file_name="earthquakes-transformed.csv"
echo "transforming into output file: $output_file_name"
cp $staging_file_name $output_file_name


```

### Shell scripting ETL example: CSV to DB
```
#!/bin/bash

# Course: ETL | Module 2 lab: https://www.coursera.org/learn/etl-and-data-pipelines-shell-airflow-kafka/ungradedLti/YhMiA/hands-on-lab-etl-using-shell-scripts

# Extracts data (field columns 1,3,6) from /etc/passwd file into a CSV file.
filename=/etc/passwd
target_file=extracted-data.txt
echo "extracting columns from $filename into $target_file"
cut -d":" -f1,3,6 $filename > $target_file

# The target csv data file contains the user name, user id and home directory of each user account defined in /etc/passwd
# Transforms the text delimiter from ":" to ",".
echo "transforming colons to commas in txt for csv"
tr ":" "," < extracted-data.txt  > transformed-data.csv


# Loads the data from the CSV file into a table in PostgreSQL database.
echo "Loading data"
# Set the PostgreSQL password environment variable.
# Replace <yourpassword> with your actual PostgreSQL password.
export PGPASSWORD=<PG_PASSWORD>; # TODO: for teaching only, do not expose in production
# Send the instructions to connect to 'template1' and
# copy the file to the table 'users' through command pipeline.
echo "\c template1;\COPY users  FROM '/home/project/transformed-data.csv' DELIMITERS ',' CSV;" | psql --username=postgres --host=postgres

# Validation: print users in table to ensure it's populated from CSV file
echo "SELECT * FROM users;" | psql --username=postgres --host=postgres template1

```

### Shell scripting ETL example: Weather report
Adapted from Coursera's Linux commands course, module: "Hands-on Lab: Historical Weather Forecast Comparison to Actuals"
```
#! /bin/bash

# Lab goal: Create a Bash script to extract, transform, and load weather data, then write a script to measure forecast accuracy.
# temperatures.txt contain weather report data, rx_poc.log contains records that compares the day's temperature with the next day's forecasted temperature

# create datestamped filename for the raw wttr data
today=$(date +%Y%m%d)   # store today's date 
weather_report=raw_data_$today

# download today's weather report for the specified city
city=Casablanca
domain=wttr.in
curl $domain/$city --output $weather_report # output crawled response data to weather report shell variable

# use command substitution to store the current day, month, and year in corresponding shell variables
hour=$(TZ='Morocco/Casablanca' date -u +%H) 
day=$(TZ='Morocco/Casablanca' date -u +%d) 
month=$(TZ='Morocco/Casablanca' date +%m)
year=$(TZ='Morocco/Casablanca' date +%Y)

# extract all lines containing temperatures from the raw weather report and write to file temperatures.txt
file=temperatures.txt
grep °C $weather_report > $file

# extract the current temperature 
obs_tmp=$(head -1 $file | tr -s " " | xargs | rev | cut -d " " -f2 | rev)   # string processing

# extract the forecast for noon tomorrow
fc_temp=$(head -3 $file | tail -1 | tr -s " " | xargs | cut -d "C" -f2 | rev | cut -d " " -f2 |rev) # more string processing

# Create records for rx_poc.log, which compares current temperature with tomorrow's forecasted temperature
# Create header for rx_poc.log
header=$(echo -e "year\tmonth\tday\thour_UTC\tobs_tmp\tfc_temp")
echo $header>rx_poc.log

# Create a tab-delimited record for rx_poc.log
record=$(echo -e "$year\t$month\t$day\t$obs_tmp\t$fc_temp")
echo $record>>rx_poc.log    # append to existing header
```


## Bash scripts for Python project

```
$ bash BASH_SCRIPT_FILE_NAME
```

a. Initialize Python project: $ bash init.sh
 ```
 #!/bin/bash

# init.sh

# Create a virtual environment
python3 -m venv virtualenv

# Activate the virtual environment
source virtualenv/bin/activate

# Install dependencies
pip3 install -r requirements.txt

# Create deployment directory for Python files and installed dependencies site-packages
mkdir deployment_package/
 ```

b. Build Python Lambda function: $ bash build.sh 
```
#!/bin/bash

# build.sh

cp -r virtualenv/lib/python3.12/site-packages/* deployment_package/
echo "copy files from installed package dependencies into deployment package"

cp -r src/* deployment_package/
echo "copy Python files into deployment package"

cd deployment_package
echo "cd into deployment_package dir"

zip -r9 ../lambda.zip .
echo "zipped lambda package for deployment"
```

c. Run Python file in virtual environment: $ bash run.sh VIRTUAL_ENV_DIR_NAME PYTHON_FILE

```
#!/bin/bash

# run.sh

# Check if the correct number of arguments is provided
if [ "$#" -ne 2 ]; then
  echo "Usage: $0 <path_to_virtualenv> <path_to_python_file>"
  exit 1
fi

# Get the arguments
VENV_PATH=$1
PYTHON_FILE=$2

# Check if the virtual environment directory exists
if [ ! -d "$VENV_PATH" ]; then
  echo "Error: Virtual environment directory not found at $VENV_PATH"
  exit 1
fi

# Check if the Python file exists
if [ ! -f "$PYTHON_FILE" ]; then
  echo "Error: Python file not found at $PYTHON_FILE"
  exit 1
fi

# Activate the virtual environment
source "$VENV_PATH/bin/activate"

# Run the Python file
python3 "$PYTHON_FILE"

# Deactivate the virtual environment
deactivate
```

## Apache Airflow
Batch processing platform

DAG defines the workflow
DAG -> Tasks -> Operators

### Airflow DAG example

```
# my_first_dag.py

# Import the libraries
from datetime import timedelta


from airflow.models import DAG  # instantiate DAG object


from airflow.operators.python import PythonOperator # import operators for tasks


from airflow.utils.dates import days_ago    # scheduling help


# Define the path for the input and output files in pipeline
input_file = '/etc/passwd'
extracted_file = 'extracted-data.txt'
transformed_file = 'transformed.txt'
output_file = 'data_for_analytics.csv'


# Define functions for operators
def extract():
   """extract fields or columns"""
   global input_file
   print("Inside Extract")
   # Read the contents of the file into a string
   with open(input_file, 'r') as infile, \
           open(extracted_file, 'w') as outfile:
      
       for line in infile:
           fields = line.split(':')
           if len(fields) >= 6:
               field_1 = fields[0]
               field_3 = fields[2]
               field_6 = fields[5]
               outfile.write(field_1 + ":" + field_3 + ":" + field_6 + "\n")
def transform():
   global extracted_file, transformed_file
   print("Inside Transform")
   with open(extracted_file, 'r') as infile, \
           open(transformed_file, 'w') as outfile:
       for line in infile:
           processed_line = line.replace(':', ',')
           outfile.write(processed_line + '\n')


def load():
   global transformed_file, output_file
   print("Inside Load")
   # Save the array to a CSV file
   with open(transformed_file, 'r') as infile, \
           open(output_file, 'w') as outfile:
       for line in infile:
           outfile.write(line + '\n')


def check():
   """Prints out final file in pipeline"""
   global output_file
   print("Inside Check")
   # Save the array to a CSV file
   with open(output_file, 'r') as infile:
       for line in infile:
           print(line)




# Define default properties for DAG
default_args = {
   'owner': 'Your name',
   'start_date': days_ago(0),
   'email': ['your email'],
   'retries': 1,
   'retry_delay': timedelta(minutes=5),
}


# Define the DAG
dag = DAG(
   'my-first-python-etl-dag',  # DAG name
   default_args=default_args,
   description='My first DAG',
   schedule_interval=timedelta(days=1),
)


# Define tasks
execute_extract = PythonOperator(
   task_id='extract',
   python_callable=extract,
   dag=dag,
)
execute_transform = PythonOperator(
   task_id='transform',
   python_callable=transform,
   dag=dag,
)
execute_load = PythonOperator(
   task_id='load',
   python_callable=load,
   dag=dag,
)
execute_check = PythonOperator(
   task_id='check',
   python_callable=check,
   dag=dag,
)


# Define Task Pipeline
execute_extract >> execute_transform >> execute_load >> execute_check


```

## Apache Kafka
### Apache Kafka setup
Refer to Google doc "data-engineering-notes"


## Docker
### Docker commands cheatsheet

- Overview: Dockerfile -> Image -> Container
- Detailed: Dockerfile -> ($ docker build ROOT_PROJECT_PATH) ->  Docker Image -> ($ docker run IMAGE_NAME; if not on machine, $ docker pull IMAGE_NAME) -> Docker Container    

```
# Images
$ docker images   # lists images on machine
$ docker pull IMAGE  # pulls Docker image from DockerHub registry, latest version of image if unspecified (e.g., node, ubuntu)
$ docker rmi IMAGE $ delete images and dependencies on machine

# Containers
$ docker ps # lists running containers
$ docker ps -a # lists all containers (including stopped/exited containers)
$ docker inspect CONTAINER_NAME/CONTAINER_ID  # shows detailed info for the container

$ docker run IMAGE # create and run container from image, pulls image from DockerHub registry if not on machine
$ docker run --name NAME IMAGE  # runs container with a specified name
$ docker stop CONTAINER_NAME  # stops particular container
$ docker rm CONTAINER_NAME # delete container from machine

$ docker run IMAGE <COMMANDS_FOR_IMAGE> # runs commands for container created from image upon starting container (e.g., docker run ubuntu sleep 100 -> runs Ubuntu container with command "sleep 100")
$ docker run -d IMAGE # runs container in background (detached mode)
$ docker run -it IMAGE # runs container with an interactive terminal where you can input your arguments to the commands of your container
$ docker run -p DOCKER_HOST_PORT:CONTAINER_PORT IMAGE  # port mapping: maps Docker Host port to Container port so that a user can access the container through a network (such as a Node container on a web server port 3001, but user has to connect to Docker Host via DOCKER_HOST_IP:DOCKER_HOST_PORT)
$ docker run -v DOCKER_HOST_VOLUME_PATH:CONTAINER_VOLUME_PATH IMAGE # persistent volume mapping: maps Docker Host volume path to Container volume path so that even if container is destroyed, the data such as the config or other files will persist in the Docker Host, and a new container can map their volume to the host volume so that the data will persist in the new container
![Screenshot 2024-09-18 at 6 54 32 PM](https://github.com/user-attachments/assets/180e7926-34fe-4feb-81f1-fae05225703d)

$ docker logs CONTAINER_NAME/CONTAINER_ID  # shows the logs or the output of the running container

$ docker execute CONTAINER_NAME <COMMANDS_FOR_IMAGE>   # executes commands for running container (e.g., docker execute ubuntu_container_name cat file.txt)


```

### Build a Docker image
![Screenshot 2024-09-19 at 3 02 26 PM](https://github.com/user-attachments/assets/0e637a7b-0361-4e0a-a365-7fe70159bc53)
![oDcTS](https://github.com/user-attachments/assets/c2c64f8c-df28-4f8f-b8e1-4b0e99a3f70a)

