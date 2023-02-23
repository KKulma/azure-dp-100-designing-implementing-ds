# [Introduction to the Azure Machine Learning SDK](https://learn.microsoft.com/en-gb/training/modules/intro-to-azure-machine-learning-service/)

Azure Machine Learning enables you to manage:

- Scalable on-demand compute for machine learning workloads.
- Data storage and connectivity to ingest data from a wide range of sources.
- Machine learning workflow orchestration to automate model training, deployment, and management processes.
- Model registration and management, so you can track multiple versions of models and the data on which they were trained.
- Metrics and monitoring for training experiments, datasets, and published services.
- Model deployment for real-time and batch inferences.

## Azure Machine Learning workspaces
A workspace is a context for the experiments, data, compute targets, and other assets associated with a machine learning workload.

### Workspaces for Machine Learning Assets

The assets in a workspace include:

- Compute targets for development, training, and deployment.
- Data for experimentation and model training.
- Notebooks containing shared code and documentation.
- Experiments, including run history with logged metrics and outputs.
- Pipelines that define orchestrated multi-step processes.
- Models that you have trained.


![azure-ml-workspace](/Users/user/MEGAsync/projects/azure-dp-100-designing-implementing-ds/img/azure-ml-workspace.png)

### Role-Based Access Control
You can assign role-based authorization policies to a workspace, enabling you to manage permissions that restrict what actions specific Azure Active Directory (AAD) principals can perform. For example, you could create a policy that allows only users in the IT Operations group to create compute targets and datastores, while allowing users in the Data Scientists group to create and run experiments and register models.

### Creating a Workspace
You can create a workspace in any of the following ways:

In the Microsoft Azure portal, create a new Machine Learning resource, specifying the subscription, resource group and workspace name.

- Use the Azure Machine Learning Python SDK to run code that creates a workspace. For example, the following code creates a workspace named aml-workspace (assuming the Azure ML SDK for Python is installed and a valid subscription ID is specified):

```python
from azureml.core import Workspace
    
    ws = Workspace.create(name='aml-workspace', 
                      subscription_id='123456-abc-123...',
                      resource_group='aml-resources',
                      create_resource_group=True,
                      location='eastus'
                     )
```

- Use the Azure Command Line Interface (CLI) with the Azure Machine Learning CLI extension. For example, you could use the following command (which assumes a resource group named aml-resources has already been created):

```shell
az ml workspace create -w 'aml-workspace' -g 'aml-resources'
```
    
- Create an Azure Resource Manager template. For more information the template format for an Azure Machine Learning workspace, see the [Azure Machine Learning documentation](- Create an Azure Resource Manager template. For more information the template format for an Azure Machine Learning workspace, see the [Azure Machine Learning documentation](https://aka.ms/AA70rq4).

## Azure Machine Learning tools and interfaces

Azure Machine Learning provides a cloud-based service that offers flexibility in how you use it. There are user interfaces specifically designed for Azure Machine Learning, or you can use a programmatic interface to manage workspace resources and run machine learning operations.

### Azure Machine Learning studio

Azure Machine Learning studio is a web-based tool for managing an Azure Machine Learning workspace (you can also do it in Azure Portal but the Studio is more focused and limited to AML).
It enables you to create, manage, and view all of the assets in your workspace and provides the following graphical tools:

- Designer: A drag and drop interface for "no code" machine learning model development.
- Automated Machine Learning: A wizard interface that enables you to train a model using a combination of algorithms and data preprocessing techniques to find the best model for your data.

To use Azure Machine Learning studio, use a a web browser to navigate to https://ml.azure.com and sign in using credentials associated with your Azure subscription. You can then select the subscription and workspace you want to manage.

### The Azure Machine Learning SDK (software development kit)

By writing scripts to create and manage resources, you can:

- Run machine learning operations from your preferred development environment.
- Automate asset creation and configuration to make it repeatable.
- Ensure consistency for resources that must be replicated in multiple environments (for example, development, test, and production)
- Incorporate machine learning asset configuration into developer operations (DevOps) workflows, such as continuous integration / continuous deployment (CI/CD) pipelines.


**Installing the Azure Machine Learning SDK for Python**

You can install the Azure Machine Learning SDK for Python by using the pip package management utility, as shown in the following code sample:

```shell
pip install azureml-sdk
```

The SDK is installed using the Python pip utility, and consists of the main azureml-sdk package as well as numerous other ancillary packages that contain specialized functionality. For example, the azureml-widgets package provides support for interactive widgets in a Jupyter notebook environment. To install additional packages, include them in the pip install command:

```shell
pip install azureml-sdk azureml-widgets
```

**Connecting to a Workspace**

After installing the SDK package in your Python environment, you can write code to connect to your workspace and perform machine learning operations. The easiest way to connect to a workspace is to use a workspace configuration file, which includes the Azure subscription, resource group, and workspace details as shown here:

```json
{
    "subscription_id": "1234567-abcde-890-fgh...",
    "resource_group": "aml-resources",
    "workspace_name": "aml-workspace"
}
```

To connect to the workspace using the configuration file, you can use the from_config method of the Workspace class in the SDK, as shown here:

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```
By default, the from_config method looks for a file named config.json in the folder containing the Python code file, but you can specify another path if necessary.

As an alternative to using a configuration file, you can use the get method of the Workspace class with explicitly specified subscription, resource group, and workspace details as shown here - though the configuration file technique is generally preferred due to its greater flexibility when using multiple scripts:

```python
from azureml.core import Workspace

ws = Workspace.get(name='aml-workspace',
                   subscription_id='1234567-abcde-890-fgh...',
                   resource_group='aml-resources')

```
Whichever technique you use, if there is no current active session with your Azure subscription, you will be prompted to authenticate.


**Working with the Workspace Class**
The Workspace class is the starting point for most code operations. For example, you can use its compute_targets attribute to retrieve a dictionary object containing the compute targets defined in the workspace, like this:

```python
for compute_name in ws.compute_targets:
    compute = ws.compute_targets[compute_name]
    print(compute.name, ":", compute.type)
```

The SDK contains a rich library of classes that you can use to create, manage, and use many kinds of asset in an Azure Machine Learning workspace.

*The Azure Machine Learning CLI Extension*

The Azure command-line interface (CLI) is a cross-platform command-line tool for managing Azure resources. The Azure Machine Learning CLI extension is an additional package that provides commands for working with Azure Machine Learning.

To install the Azure Machine Learning CLI extension, you must first install the Azure CLI. See the full installation instructions for all supported platforms for more details.

After installing the Azure CLI, you can add the Azure Machine Learning CLI extension by running the following command:

```shell
az extension add -n azure-cli-ml
```
To use the Azure Machine Learning CLI extension, run the az ml command with the appropriate parameters for the action you want to perform. For example, to list the compute targets in a workspace, run the following command:

```shell
az ml computetarget list -g 'aml-resources' -w 'aml-workspace'
```

### Compute Instances
Azure Machine Learning includes the ability to create Compute Instances in a workspace to provide a development environment that is managed with all of the other assets in the workspace.

Compute Instances include Jupyter Notebook and JupyterLab installations that you can use to write and run code that uses the Azure Machine Learning SDK to work with assets in your workspace.

You can choose a compute instance image that provides the compute specification you need, from small CPU-only VMs to large GPU-enabled workstations. Because compute instances are hosted in Azure, you only pay for the compute resources when they are running; so you can create a compute instance to suit your needs, and stop it when your workload has completed to minimize costs.

You can store notebooks independently in workspace storage, and open them in any compute instance.


### Visual Studio Code

The Azure Machine Learning Extension for Visual Studio Code provides a graphical interface for working with assets in an Azure Machine Learning workspace. You can combine the capabilities of the Azure Machine Learning and Python extensions to manage a complete end-to-end machine learning workload in Azure Machine Learning from the Visual Studio Code environment.


## Azure Machine Learning experiments
An experiment can be run multiple times, with different data, code, or settings; and Azure Machine Learning tracks each run, enabling you to view run history and compare results for each run.

### The Experiment Run Context
When you submit an experiment, you use its run context to initialize and end the experiment run that is tracked in Azure Machine Learning, as shown in the following code sample:

```python
from azureml.core import Experiment

# create an experiment variable
experiment = Experiment(workspace = ws, name = "my-experiment")

# start the experiment
run = experiment.start_logging()

# experiment code goes here

# end the experiment
run.complete()
```

### Logging Metrics and Creating Outputs
Experiments are most useful when they produce metrics and outputs that can be tracked across runs.

Logging Metrics
Every experiment generates log files that include the messages that would be written to the terminal during interactive execution. This enables you to use simple print statements to write messages to the log. However, if you want to record named metrics for comparison across runs, you can do so by using the Run object; which provides a range of logging functions specifically for this purpose. These include:

- `log`: Record a single named value.
- `log_list`: Record a named list of values.
- `log_row`: Record a row with multiple columns.
- `log_table`: Record a dictionary as a table.
- `log_image`: Record an image file or a plot.

For example, following code records the number of observations (records) in a CSV file:

```python
from azureml.core import Experiment
import pandas as pd

# Create an Azure ML experiment in your workspace
experiment = Experiment(workspace = ws, name = 'my-experiment')

# Start logging data from the experiment
run = experiment.start_logging()

# load the dataset and count the rows
data = pd.read_csv('data.csv')
row_count = (len(data))

# Log the row count
run.log('observations', row_count)

# Complete the experiment
run.complete()
```

### Retrieving and Viewing Logged Metrics
You can view the metrics logged by an experiment run in Azure Machine Learning studio or by using the RunDetails widget in a notebook, as shown here:

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
```


You can also retrieve the metrics using the Run object's get_metrics method, which returns a JSON representation of the metrics, as shown here:

```python
import json

# Get logged metrics
metrics = run.get_metrics()
print(json.dumps(metrics, indent=2))
```

### Experiment Output Files
In addition to logging metrics, an experiment can generate output files. Often these are trained machine learning models, but you can save any sort of file and make it available as an output of your experiment run. The output files of an experiment are saved in its outputs folder.

The technique you use to add files to the outputs of an experiment depend on how you're running the experiment. The examples shown so far control the experiment lifecycle inline in your code, and when taking this approach you can upload local files to the run's outputs folder by using the Run object's upload_file method in your experiment code as shown here:

```python
run.upload_file(name='outputs/sample.csv', path_or_stream='./sample.csv')
```

When running an experiment in a remote compute context (which we'll discuss later in this course), any files written to the outputs folder in the compute context are automatically uploaded to the run's outputs folder when the run completes.

Whichever approach you use to run your experiment, you can retrieve a list of output files from the Run object like this:
```python
import json

files = run.get_file_names()
print(json.dumps(files, indent=2))
```

### Running a Script as an Experiment
You can run an experiment inline using the start_logging method of the Experiment object, but it's more common to encapsulate the experiment logic in a script and run the script as an experiment. The script can be run in any valid compute context, making this a more flexible solution for running experiments as scale.

An experiment script is just a Python code file that contains the code you want to run in the experiment. To access the experiment run context (which is needed to log metrics) the script must import the azureml.core.Run class and call its get_context method. The script can then use the run context to log metrics, upload files, and complete the experiment, as shown in the following example:

```python
from azureml.core import Run
import pandas as pd
import matplotlib.pyplot as plt
import os

# Get the experiment run context
run = Run.get_context()

# load the diabetes dataset
data = pd.read_csv('data.csv')

# Count the rows and log the result
row_count = (len(data))
run.log('observations', row_count)

# Save a sample of the data
os.makedirs('outputs', exist_ok=True)
data.sample(100).to_csv("outputs/sample.csv", index=False, header=True)

# Complete the run
run.complete()
```
To run a script as an experiment, you must define a script configuration that defines the script to be run and the Python environment in which to run it. This is implemented by using a ScriptRunConfig object.

For example, the following code could be used to run an experiment based on a script in the experiment_files folder (which must also contain any files used by the script, such as the data.csv file in previous script code example):

```python

from azureml.core import Experiment, ScriptRunConfig

# Create a script config
script_config = ScriptRunConfig(source_directory=experiment_folder,
                                script='experiment.py') 

# submit the experiment
experiment = Experiment(workspace = ws, name = 'my-experiment')
run = experiment.submit(config=script_config)
run.wait_for_completion(show_output=True)
```

> **Note**
> 
> An implicitly created RunConfiguration object defines the Python environment for the experiment, including the packages available to the script. If your script depends on packages that are not included in the default environment, you must associate the ScriptRunConfig with an Environment object that makes use of a CondaDependencies object to specify the Python packages required. Runtime environments are discussed in more detail later in this course.