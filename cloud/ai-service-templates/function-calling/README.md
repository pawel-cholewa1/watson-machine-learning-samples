# A LangGraph LLM app template with function calling capabilities  

Table of Contents:  
* [Short introductory description](#deployable-on-ibm-cloud-powered-by-watsonxai)  
* [Prerequisites](#prerequisites)  
* [Cloning and setting up the template locally](#cloning-and-setting-up-the-template-locally)  
* [Modifying and configuring the template](#modifying-and-configuring-the-template)  
* [Unit testing the template](#unit-testing-the-template)  
* [Playing with the template locally](#playing-with-the-template-locally)  
* [Deploying on Cloud](#deploying-on-cloud)
* [Querying the deployment](#querying-the-deployment)  


## Deployable on IBM Cloud, powered by Watsonx.ai

The repository should serve as an easily extensible skeleton for LLM apps deployable as an _ai-service_ on IBM Cloud
environment.

This particular example presents a simple calculator app with external tools covering more advanced maths and statistics
problems and use cases that the model itself might have problems getting right.

The high level structure of the repository is as follows (included are only the most important files):

function-calling  
 ┣ examples  
 ┣ src  
 ┃ ┣ ai_service_function_calling [(1)]  
 ┃ ┃ ┣ \_\_init\_\_.py  
 ┣ tests  
 ┃ ┗ \_\_init\_\_.py   
 ┣ **ai_service.py**  (2)  
 ┣ config.toml  (4)  
 ┣ pyproject.toml  

(1) any auxiliary files used by the deployed function. Packaged and sent to IBM Cloud during deployment as [package extension](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/ml-create-custom-software-spec.html?context=wx&audience=wdp#custom-wml)  
(2) includes the core logic of the app --- the function to be deployed on IBM Cloud,   
(4) a configuration file storing deployment metadata and tweaking the model  

We advise following these steps to quickly have the app up & running on IBM Cloud.  

### Prerequisites  
This template uses [Poetry](https://python-poetry.org/) package manager. Due to its recommended [installation procedure](https://python-poetry.org/docs/#installation) a [Pipx](https://github.com/pypa/pipx) should be **installed and available** on the system.  

For tips on how to ensure _Pipx_ on your system please follow [its official docs](https://github.com/pypa/pipx?tab=readme-ov-file#install-pipx).  


### Cloning and setting up the template locally  

In order not to clone the whole `IBM/watson-machine-learning-samples` repository we'll use git's shallow and sparse cloning feature to checkcout only the template's directory:  

```sh
git clone --no-tags --depth 1 --single-branch --filter=tree:0 --sparse git@github.com:IBM/watson-machine-learning-samples.git
cd watson-machine-learning-samples
git sparse-checkout add cloud/ai-service-templates/
```

From now on it'll be considered that the working directory is `watson-machine-learning-samples/cloud/ai-service-templates/function-calling`  


### Ensure Poetry installation  
```sh
pipx install --python 3.11 poetry
```

### Installing the template  
Running the below commands will install the repository in a separate virtual environment  

```sh
poetry install
```

### Activating the newly created environment in your shell  

```sh
source $(poetry -q env use 3.11 && poetry env info --path)/bin/activate
```

### Exporting PYTHONPATH
Adding working directory to PYTHONPATH is necessary for the next steps. In your terminal execute:  
```sh
export PYTHONPATH=$(pwd):${PYTHONPATH}
```

## Modifying and configuring the template  

### Config file  
There is a [config.toml](config.toml) file that should be filled in before deploying the template on Cloud. It can also be used to customise the model for local runs.  
Possible config parameters are given in the provided file and explained using comments (when necessary).  


### Core app logic  

The [ai_service.py](ai_service.py) file encompasses the functions to be deployed on IBM Cloud environment.

First and foremost they consist of LangGraph model graph definition, app logic as well as input schema definition for `/ai_service` endpoint query.  
They also include code responsible for authenticating the user to the IBM Cloud, ensure the deployment environment and store its metadata.


### Adding new tools  

[tools.py](src/ai_service_function_calling/tools.py) file stores the definition for tools enhancing the chat model's capabilities.  
In order to add new tool create a new function, wrap it with the `@tool` decorator and add to the `TOOLS` list in the `extensions` module's [__init__.py](src/ai_service_function_calling/__init__.py)

For more sophisticated use cases (like async tools), please refer to the [langchain docs](https://python.langchain.com/docs/how_to/custom_tools/#creating-tools-from-runnables).  

### Enahncing unit tests suite  
The `tests/` directory's structure resembles the repository. Adding new tests should follow this convention.  
Currently, for exemplary purposes, tools and some general utility functions are covered with unit tests.  

## Unit-testing the template  
Running the below command will execute the whole tests suite:
```sh
pytest -r 'fEsxX' tests/
```


## Playing with the template locally  
It is possible to run (or even debug) the ai-service locally, however it still requires creating the connection to the IBM Cloud.  
In order to execute the function to be deployed within your local environment:  
1) populate the `config.toml` file with your necessary credentials,  
2) run the `examples/execute_ai_service_locally.py` script using:  
    ```sh
    python examples/execute_ai_service_locally.py
    ```
3) choose from some pre-defined questions or ask the model your own  
  Please bear in mind that in order for the model to invoke its tools the questions should revolve around fitting Linear Regression to some user-defined data  


## Deploying on Cloud  

1) populate the `config.toml` file with your necessary credentials (if not already done for the purpose of local testing)  
2) run the `scripts/deploy.py` file using:  
```sh
python scripts/deploy.py
```

## Querying the deployment  

Can be done in at least a couple of ways. The easiest one is to use the `watsonx.ai` library.  
The exemplary file querying an exisitng deployment [query_existing_deployment.py](examples/query_existing_deployment.py) is a perfect place to start exploring the said possibilities.  
