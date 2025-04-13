# Code instructions and snippets

## Sections

GitHub
- [Connect to GitHub using SSH key](#connect-to-github-using-ssh-key)

Python
- [Create a pipenv](#create-a-pipenv)
- [Install dependencies for a Python project](#install-dependencies-for-a-python-project)

AWS
- [Build and Deploy AWS Lambda function in Python](#build-and-deploy-aws-lambda-function-in-python)
- [NAT Gateway](#nat-gateway)
- [Domain integration: GoDaddy to AWS S3](#domain-integration-godaddy-to-aws-s3)
- Problem: [AWS API Gateway CORS issue](#aws-api-gateway-cors-issue)
- Problem: [Unable to import module into Lambda function](#unable-to-import-module-into-lambda-function)
- Problem: [Unable to import psycopg2 connecting Python Lambda to Postgresql database](#unable-to-import-psycopg2-connecting-python-lambda-to-postgresql-database)

Linux shell scripts
- [Linux commands cheatsheet](#linux-commands-cheatsheet)
- [Shell scripting](#shell-scripting)
- [Bash scripts for ETL](#bash-scripts-for-etl)
- [Bash scripts for Python project](#bash-scripts-for-python-project)
- [SSH](#ssh)


Docker
- [Docker commands cheatsheet](#docker-commands-cheatsheet)
- [Build a Docker Image](#build-a-docker-image)

Apache Airflow
- [Airflow DAG example](#airflow-dag-example)

Apache Kafka
- [Apache Kafka setup](#apache-kafka-setup)

Apache Hadoop
- Refer to Google doc "data-engineering-notes"

Apache Spark
- Refer to Google doc "data-engineering-notes"

Arduino
- [Arduino Basics](#arduino-basics)

React
- Problem: [HTTP polyfill error](#http-polyfill-error)

IPFS
- [IPFS Resources](#ipfs-resources)
- [Setup local IPFS node](#setup-local-ipfs-node)
- [Setup IPFS node cluster in the cloud](#setup-ipfs-node-cluster-in-the-cloud)
- [Integrate React app with IPFS](#integrate-react-app-with-ipfs)

## GitHub
### Connect to GitHub using SSH key
1. Configure GitHub identity
```
$ git config --global user.name "Your Name"
$ git config --global user.email "your.email@example.com"
```
2. Generate SSH key (RSA shown)
```
$ ssh-keygen -t rsa -b 4096 -C "your.email@example.com"
```
3. Start SSH agent and add key (MacOS)
```
$ eval "$(ssh-agent -s)"
$ ssh-add --apple-use-keychain ~/.ssh/id_rsa
```

4. Copy SSH key to add to GitHub account
```
$ pbcopy < ~/.ssh/id_rsa.pub
```
5. Add key to GitHub account
- Go to GitHub.com account -> Settings -> SSH and GPG keys -> Add SSH key -> paste SSH public key



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

### NAT Gateway
Since NAT Gateways are expensive, add it only shortly before prod launch, if at all

Use case: For resources such as Lambda functions in private subnets to access the Internet: Lambda function in private subnet → private route table → NAT Gateway → auto points to Internet Gateway

Have private route table with ingress relevant private subnet point to NAT Gateway


### Domain integration: GoDaddy to AWS S3
GoDaddy -> AWS Route53 -> S3 bucket (deployed React app) <br/>
Without CloudFront (CDN), so no HTTPS <br/>
GPT guide: https://chatgpt.com/c/66db4379-1894-800f-a105-2c5c7ce5bcd0	<br/>


#### 1. Set Up Your React Frontend in S3
Ensure your React application is deployed to an S3 bucket:

##### 1a. Create an S3 Bucket:

	- The bucket name should match your domain name (e.g., mydomain.com) if possible, to make DNS integration straightforward.
	- Enable static website hosting for the bucket under the Properties tab.
	- Set the Index document to index.html and optionally the Error document to index.html.
##### 1b. Upload Your Build Files:
	
	- Run npm run build or yarn build in your React project.
	- Upload the contents of the build/ directory to your S3 bucket.

##### 1c. Set Permissions:
	- Go to the Permissions tab of your S3 bucket and ensure the files are publicly accessible:
	Attach a Bucket Policy like this:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::mydomain.com/*"
        }
    ]
}
```
Replace mydomain.com with your bucket name.

##### 1d. Obtain the S3 Website Endpoint:

	- Go to the Properties tab, find the Static website hosting section, and copy the Bucket website endpoint (e.g., http://mydomain.com.s3-website-us-east-1.amazonaws.com).

#### 2. Set Up Route 53 as Your DNS Manager
Route 53 will handle DNS management for your domain.

##### 2a. Create a Hosted Zone:

	- Go to the Route 53 Console in AWS.
	- Click Create Hosted Zone.
	- Enter your domain name (e.g., mydomain.com).
	- Choose Public Hosted Zone and click Create.

##### 2b. Note the Route 53 Nameservers:

After creating the hosted zone, AWS provides a list of nameservers (NS records). These will be used to configure GoDaddy.

#### 3. Update GoDaddy Nameservers to Point to Route 53

##### 3a. Login to GoDaddy:
	- Go to your GoDaddy account, select your domain, and go to the DNS Management section.
##### 3b. Change Nameservers:

	- Find the section for Nameservers.
	- Choose Custom Nameservers and add the 4 nameservers provided by Route 53.
	- Save the changes.
	- Note: DNS changes can take up to 48 hours to propagate, but they often update much sooner.

#### 4. Configure DNS Records in Route 53
In Route 53, set up DNS records to point your domain to the S3 bucket.

##### 4a. Add an A Record for the Root Domain (mydomain.com):

	- Go to the Hosted Zone you created.
	- Click Create Record.
	- Set the following values:
		Record Name: Leave blank to use the root domain (mydomain.com).
		Record Type: A (Address).
		Alias: Yes.
		Alias Target: Choose the S3 website endpoint from the list.
  
##### 4b. Add a CNAME Record for the Subdomain (www.mydomain.com):

	- Click Create Record again.
	- Set the following values:
		Record Name: www.
		Record Type: CNAME.
		Value: Enter the S3 website endpoint (e.g., mydomain.com.s3-website-us-east-1.amazonaws.com).

#### 5. Test the Integration
##### 5a. Open a browser and navigate to:
	http://mydomain.com
	http://www.mydomain.com
 
##### 5b. Ensure the React application is loading properly.

#### 6. (Optional) Redirect www to Root Domain
If you want all traffic to www.mydomain.com to redirect to mydomain.com (or vice versa), configure the S3 bucket for redirection:

##### 6a. Create a Second S3 Bucket:

	- Name the bucket www.mydomain.com.
	- In the Properties tab, enable Static website hosting.
	- Select the Redirect requests for an object option and set the target bucket to mydomain.com.

##### 6b. Update DNS in Route 53:

	- In the CNAME record for www, change the value to the new bucket’s endpoint (www.mydomain.com.s3-website-region.amazonaws.com).



### AWS API Gateway CORS issue

AWS CORS issue doc: https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-cors-console.html

Client → API Gateway → Preflight request (OPTIONS method) → Server → Preflight response (with 3 Access-Control-Headers) → API Gateway → Actual request (GET/POST/etc method) → Server → actual response to API Gateway → actual response to Client

**Approach (non-proxy integration)**

1. An OPTIONS method must be created for every resource level (every endpoint). OPTIONS should be of Integration Type ‘Mock’.
![Screenshot 2024-09-26 at 11 05 30 AM](https://github.com/user-attachments/assets/401083b1-fd68-4219-b90d-f24990a8f288)

2. For each OPTIONS method, under Method Response, add the three request headers Access-Control-Allow-Origin, Access-Control-Allow-Headers, Access-Control-Allow-Methods (no quotations).  If unable to edit existing response, may have to delete then create new response.
![Screenshot 2024-09-26 at 11 05 47 AM](https://github.com/user-attachments/assets/7281c5ac-afbc-4c25-8b02-e9f3460bfc3c)
    
3. For OPTIONS method, under Integration Response, for Response 200, set Content Handling to ‘Passthrough’. Then assign the string values with *quotations* to each Access-Control request header. If unable to edit existing response, may have to delete then create new response.
    1. Access-Control-Allow-Origin = ‘*’
    2. Access-Control-Allow-Headers = 'Content-Type,Authorization’
    3. Access-Control-Allow-Methods = 'GET,POST,PUT,DELETE,OPTIONS’
![Screenshot 2024-09-26 at 11 05 57 AM](https://github.com/user-attachments/assets/7386eeb5-b863-44f4-87bc-8dd027cbc492)

4. Within the response in your API service source code, include the attribute ‘Access-Control-Allow-Origin’ = ‘*’.
![Screenshot 2024-09-26 at 11 10 36 AM](https://github.com/user-attachments/assets/ce6e42e6-c699-4bef-9dfe-7699e5e90ad3)

### Unable to import module into Lambda function

Error: 
"Unable to import module 'lambda_function': No module named 'pydantic_core._pydantic_core'"

Problem: Incompatible dependencies for AWS Lambda runtime environment

could be due to different runtime between local machine runtime architecture (arm) and lambda function runtime architecture (x86)

Solution 1: https://stackoverflow.com/questions/76650856/no-module-named-pydantic-core-pydantic-core-in-aws-lambda-though-library-is-i

adapted approach from Alena to pip install dependencies for proper x86 architecture runtime 

- clear pip cache so that previously incorrectly versioned dependencies are not reinstalled
- add the pip install line, can add to init.sh script

```
VIRTUALENV_DEPENDENCIES_PATH=./virtualenv/lib/python3.12/site-packages
# arm64 (e.g., Macbook M1)
pip install -r requirements.txt --platform manylinux2014_aarch64 --target $VIRTUALENV_DEPENDENCIES_PATH --only-binary=:all:	
# x86 (e.g., Macbook Intel)
pip install -r requirements.txt --platform manylinux2014_x86_64 --target $VIRTUALENV_DEPENDENCIES_PATH --only-binary=:all:
```
- alternative: after installing all dependencies, delete the pydantic libraries and install the pydantic wheel by executing the following command:
```
# arm64
pip install pydantic --platform manylinux2014_aarch64 -t .
$ x86
pip install pydantic --platform manylinux2014_x86_64 -t .
```

- build the lambda package to deploy → build.sh script
- upload lambda.zip to AWS Lambda console, hope it works…
- if not, try adding the Lambda layer from the StackOverflow answer by user659xxxx

### Unable to import psycopg2 connecting Python Lambda to Postgresql database

Error in AWS Lambda function: Unable to import module 'psycopg2'

Trouble import psycopg2 module in Python Lambda function due to inconsistent dependencies built on local machine compared with AWS Lambda runtime architecture

Instructions:
1. Create AWS Lambda with the correct Python version runtime (e.g., Python 3.11 and other versions)
2. Download corresponding psycopg library for AWS given Python version (I used Python 3.11) from GitHub repo awslambda-psycopg2 (https://github.com/jkehler/awslambda-psycopg2)
3. Find corresponding psycopg2-PYTHONVERSION (e.g., psycopg2-3.11) folder in downloaded libraries and rename entire folder to “psycopg2”.
4. Initialize virtualenv and install dependencies ($bash init.sh - make sure correct Python version; e.g., may have to change first instruction in Bash script from python -m venv virtualenv to python3.11 -m venv virtual)
5. Move the psycopg2 folder and its contents to the Lambda function’s virtualenv/lib/python-VERSION/site-packages/ (e.g., virtualenv/lib/python-3.11/site-packages/)
6. Created zipped deployment package ($bash build.sh)



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

# find process on port and kill process
$ sudo lsof -i :PORT_NUM	# output: will show PID of process on this port
$ kill -9 PID	

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
echo "created virtual environment"

# Activate the virtual environment
source virtualenv/bin/activate
echo "activated virtual env"

# Install dependencies
pip3 install -r requirements.txt
echo "installed dependencies from requirements.txt"

# Create deployment directory for Python files and installed dependencies site-packages
mkdir deployment_package/
echo "created deployment package directory"

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

## SSH
### SSH key generation 
example: for Github SSH cloning
$ ssh-keygen -t rsa -b 4096 -C "hnryzhng@gmail.com"
$ cat /Users/USERNAME/.ssh/id_rsa.pub


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
$ docker rmi IMAGE # delete images and dependencies on machine
$ docker push USER/IMAGE  # pushes image within user account to Docker registry
$ docker login  # logs in to user account so can push to corresponding user repo in Docker registry

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
1. Define a Dockerfile

```
# Sample Dockerfile for running a Flask web app
FROM Ubuntu  # install a base OS image

RUN apt-get udpate && apt-get -y install python  # install dependencies for OS

RUN pip install flask  # install app dependencies

COPY . /opt/project-repo  # copy source code in project repo to the image 

ENTRYPOINT FLASK_APP=/opt/project-repo/app.py flask run  # Run web server using "flask" command
```

2. Build the image (containerize the app) from the Dockerfile
```

$ docker build . -f Dockerfile -t hnryzhng/my-app  # your username and image name

$ docker push hnryzhng/my-app  # pushes to Docker registry, may need to authenticate using $ docker login

```

Additional: General steps to construct a Dockerfile
  - Go through the commands in the shell to install dependencies and run the application
  - Then get and modify the relevant commands to put in the Dockerfile for building the image.
  - Build the image by containerizing the project repo with the Dockerfile.
 
Dockerfile snippets
```
FROM Ubuntu
CMD sleep 5
# equal to $ docker run ubuntu sleep 5

FROM Ubuntu
ENTRYPOINT ["sleep"]
# equal to docker run ubuntu sleep, but no argument to command

FROM Ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
# equal to $ docker run ubuntu sleep 5, PREFERRED CONSTRUCTION

```

## Arduino
### Arduino Basics

#### Tools
- Arduino IDE
- TinkerCad - simulator
- Arduino physical set (microcontroller board, USB cable, breadboard, sensors, etc.)
- Resistor Color Code (Digikey)

#### Workflow
- Build circuit in simulator and write code in simulator or IDE -> test -> if it works, then build it using physical Arduino board and circuits (go through working with hardware safety checklist)

#### Code illustrations
**Setup**
```
void setup() {
	// initialize code
}


void loop() {
	// main code here that runs repeatedly in loop
 Serial.println("loop iteration");
}
```

**Debugging**
```
void setup() {
 // debugging: logging to serial monitor
 
 // initialize serial communication (baud rate here should match that of serial monitor)
 Serial.begin(9600);
 Serial.println("Hi");
}
void loop() {
 Serial.println("loop iteration");
}
```

**Program: Basic LED blinking**
```
void setup() {
 // put your setup code here, to run once:
 // Set LED pin 13 to output
 pinMode(13, OUTPUT);


}

void loop() {
 // Main code to run on loop
 blink();
}


void blink() {
 digitalWrite(13, HIGH);	// turn on
 delay(1000);  // Wait for 1000 millisecond(s)
 digitalWrite(13, LOW);	// turn off
 delay(1000);
}
```

**Programming basics**
- Reference: https://drive.google.com/file/d/1Nh-YW7C1sSA8IkyHgRe54GGsAuEr-kWD/view?usp=drive_link 

```
#define LED_PIN 12  // define constant


int a = 1;  // initialize and define variable
int b;  // initialize variable
int temperatureArray[2] = {23, 45}; // static array


// primitive data types: bool, String, int, double (float), long
// int: max 2 bytes, range -32,768 to 32,767, else overflow
// long: max 4 bytes, range -2,147,483,648 to 2,147,483,647, else overflow
// double (float): practically unlimited


void setup() {
  Serial.begin(9600);


 int temp = 0;


 if ((temp >= 0) && (temp <= 30)) {
   Serial.println("low temp");
 }
 else if (temp >= 30) {
   Serial.println("high temp");
 }
 else {
   Serial.println("temp is outside range");
 }


 for (int i = 0; i < 10; i++) {
   Serial.println("Hi";
 }


}


void loop() {


}

```

## React

### HTTP Polyfill error
If getting this after 'npm start', add the following snippet in the file 'webpack.config.js' (path: node-modules/react-scripts/config/webpack.config.js) within the 'resolve' attribute. Then run 'npm start' again.
```
resolve: {
	...
	fallback: {
		"http": false
	}
}
```

## IPFS

### IPFS Resources

- IPFS comprehensive concepts (official docs): https://docs.ipfs.tech/concepts/

- IPFS basic concepts + BlockFrost usage: https://blockfrost.dev/start-building/ipfs/


### Setup local IPFS node

1. On Mac:
   ```
   brew install ipfs
   ```

2. Initialize IPFS repo in directory ```~/.ipfs```, which also sets up node's unique identity.
   ```
   ipfs init
   ```
3. Configure CORS to allow requests via different ports and HTTP methods from client such as a React app to access the local node.
   ```
   ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["http://localhost:3000"]'	# if React is on localhost 3000, or 5173 for Vite
   ipfs config --json API.HTTPHeaders.Access-Control-Allow-Methods '["PUT", "GET", "POST"]'
   ipfs config --json API.HTTPHeaders.Access-Control-Allow-Headers '["Authorization", "Content-Type"]'

   ```

4. Run local IPFS node using daemon. Starts background service, make it accessible on http://localhost:PORTS -> ports 5001 (API), 8080 (Gateway), 4001 (Swarm for peer-to-peer communication)
   ```
   ipfs daemon
   ```

5. Can view active IPFS node via Web UI (link available in terminal after running "ipfs daemon")
   ```
   http://127.0.0.1:5001/webui
   ```

### Setup IPFS node cluster in the cloud

https://chatgpt.com/c/67b93b9e-d320-800f-998e-e160b379198b


### Integrate React app with IPFS 
1. In React project directory, install IPFS HTTP client
   ```
   npm install ipfs-http-client
   ```

2. Upload a file to IPFS in the React component via API localhost port (here: http://localhost:5001)
   	```
	
 	// Sample React component (TypeScript)
	
	import React, { useState, ChangeEvent, FormEvent } from 'react';
	import { create, IPFSHTTPClient } from 'ipfs-http-client';
	
	// Create an IPFS client instance
	const client: IPFSHTTPClient = create({
	  host: 'localhost',
	  port: 5001,
	  protocol: 'http',
	});
	
	const IPFSFileUploader: React.FC = () => {
	  const [file, setFile] = useState<File | null>(null);
	  const [cid, setCid] = useState<string>('');
	  const [uploading, setUploading] = useState<boolean>(false);
	  const [error, setError] = useState<string>('');
	
	  // Handle file selection
	  const onFileChange = (event: ChangeEvent<HTMLInputElement>) => {
	    if (event.target.files && event.target.files.length > 0) {
	      setFile(event.target.files[0]);
	    }
	  };
	
	  // Upload the file and pin it
	  const onFileUpload = async (event: FormEvent<HTMLFormElement>) => {
	    event.preventDefault();
	    if (!file) {
	      setError('Please select a file to upload.');
	      return;
	    }
	    setUploading(true);
	    setError('');
	    try {
	      // Upload the file to IPFS
	      const added = await client.add(file);
	      setCid(added.path);
	
	      // Pin the file to ensure it's persisted on your node
	      await client.pin.add(added.path);
	    } catch (err) {
	      console.error('Error uploading or pinning file:', err);
	      setError('File upload failed.');
	    }
	    setUploading(false);
	  };
	
	  return (
	    <div>
	      <h2>Upload File to IPFS</h2>
	      <form onSubmit={onFileUpload}>
	        <input type="file" onChange={onFileChange} />
	        <button type="submit" disabled={uploading}>
	          {uploading ? 'Uploading...' : 'Upload'}
	        </button>
	      </form>
	      {cid && (
	        <div>
	          <p>File uploaded and pinned successfully!</p>
	          <p>
	            CID: <code>{cid}</code>
	          </p>
	          <p>
	            View file at:{' '}
	            <a
	              href={`https://ipfs.io/ipfs/${cid}`}
	              target="_blank"
	              rel="noopener noreferrer"
	            >
	              https://ipfs.io/ipfs/{cid}
	            </a>
	          </p>
	        </div>
	      )}
	      {error && <p style={{ color: 'red' }}>{error}</p>}
	    </div>
	  );
	};
	
	export default IPFSFileUploader;

 
   	```
   




