# Code instructions and snippets

## Sections

Python
- [Create a pipenv](#create-a-pipenv)
- [Install dependencies for a Python project](#install-dependencies-for-a-python-project)

AWS
- [Build and Deploy AWS Lambda function in Python](#build-and-deploy-aws-lambda-function-in-python)

Bash scripts
- [Linux commands cheatsheet](#linux-commands-cheatsheet)
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

### File and Directory Navigation commands
```
$ cd ~ # move to home directory
$ cd PATH # move to specified path
$ ls -a DIR_NAME # list all files in directory
$ ls -l FILE_NAME or DIR_NAME # list access permissions
$ chmod <subcommand> FILENAME or DIR_NAME # change access permissions of file or directory
$ find FILENAME # find file in the directory

```

### File and String Manipulation commands
```
$ echo "body string" > OUTPUT_FILE_NAME
$ cp SOURCE_DIR_PATH TARGET_DIR_PATH # copy all files from source to target directories
$ cat 1.txt 2.txt 3.txt # merge 3 columns from each file
$ mkdir DIRECTORY_NAME
$ touch FILE_NAME
$ less FILE_NAME # show file within scrollable view
$ more FILE_NAME # show file
$ grep PATTERN FILE_NAME/DIR_NAME # outputs file or directory given a pattern to search


```

### Networking commands
```
$ ping TARGET_URL
$ wget FILE_LOCATION
$ curl TARGET_URL
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
