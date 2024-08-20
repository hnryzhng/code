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
- [Bash scripts for Python project](#bash-scripts-for-python-project)

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

Arithmetic calculations
# Integer arithmetic notation: $(())
# example: echo $((-1*-2))

Arrays
$ my_array=(1 2 "three" "four" 5)
$ my_array+=7
$ my_array+="six"
$ my_array=($(echo $(cat column.txt))) # Declare an array and load it with lines of text from a file

Loops

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
