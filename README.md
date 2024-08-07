# Code instructions and snippets

## Sections

Python
- [Create a pipenv](#create-a-pipenv)
- [Install dependencies for a Python project](#install-dependencies-for-a-python-project)

AWS
- [Build and Deploy AWS Lambda function in Python](#build-and-deploy-aws-lambda-function-in-python)


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

3. Add Your Code: Copy your Python files (e.g., lambda_function.py, llm_service.py, etc.) into the deployment package directory. Can put Python files into a folder such as "lambda" then use the secodn command.
```
$ cp lambda_function.py llm_service.py deployment_package/
# OR 
$ cp lambda/ deployment_package/
```
4. Zip the Deployment Package: Navigate to the deployment package directory and create a ZIP file.
```
$ cd deployment_package
$ zip -r ../lambda.zip .
$ cd ..
```

5. Upload ZIP file using AWS console in Lambda dashboard 
