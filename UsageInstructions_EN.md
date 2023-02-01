# Development environment usage instructions


## 1  General Development
After the environment is started, if Jupyterlab or SSH is installed in the environment image, resources will be dispatched to complete the creation of the Jupyter environment and SSH channel.

### 1.1  Using JupyterLab
After the task status of the development environment is "running", users can start the Jupyterlab window, one development environment can only start one window, running on the master node (if the application is a distributed multi-node resource), please develop in the Jupyterlab working window, you can upload files to the development environment.

### 1.2  Using SSH
SSH access information can be seen after the task status of the development environment is "running", if you add a distributed multi-node, only the master node access channel will be provided. You can add the SSH key in the personal account management.

### 1.3  Interactive Port
Interactive ports are used to open additional ports to the development environment for other access.

### 1.4  Directory Description
The development environment generates 6 persistable directories (users are asked to follow directory usage conventions).
- `code`: a local copy of the project code development directory, the code is managed using the git versioning tool, and the directory will be deleted when the project is deleted. When the development environment is started for the first time, it will be automatically cloned from the external code repository to this folder.
- `outputs`: stores the project user's private output files, which will be deleted if the project is deleted.
- `userdata`: store user private files, project deletion will not affect the userdata directory.
- `teamdata`: store user group shared files, project deletion will not affect the teamdata directory.
- `adhub/{name}/{Vn}`: stores the list of ADHub datasets, it is a read-only directory.
- `models/{name}/{Vn}`: stores the models imported from the model factory for secondary development, project deletion will not affect the models directory.


## 2  Inference Development
For trained inference models, if you want to develop their pre and post-processing code, you can choose inference development when starting the development environment, or choose the platform pre-built inference development image.
The inference development image provides the Triton inference framework, sample pre and post-processing code, and inference debugging plug-ins for quick writing and debugging of inference code.
The inference development environment is the same as for generic development, with JupyterLab, SSH, and interactive ports also available.

### 2.1  Write pre-processing and post-processing code
Please refer to the mirror for sample pre and post processing code (in Python/C) and instructions.

### 2.2  Debug pre-processing and post-processing code
To debug the compiled pre and post processing code, you can open the inference debugging tool, start the inference service, predict images and text online, visually see if the prediction results are correct, and view log messages.


## 3  Save Image
After debugging the generic development code or inference pre and post processing code, you can save the entire development environment on the master node as a mirror, which will be saved to the space belonging to the mirror center for direct use next time. If inference development environment is used, the saved image supports Triton inference.


## 4  Publish Model
To publish the developed model to the Model Factory APWorkshop for subsequent training, inference, automatic annotation, and other purposes, you need to:
1. write and store the configuration file according to the following instructions, otherwise there will be errors when using the model.
2. git commit the code before publishing.
```
git add .
git commit -m "Remarks"
git push
```
3. Follow the model release command line provided by the platform, otherwise the model will not be released correctly.
``` /start/aplab -trainOutPutPath {training output path} -modelName {model name} ```


### 4.1  Configuration file description
When you create a new development environment, there are 3 configuration file templates in the code directory, please fill them in correctly according to the content.
1. manifest.yaml: Configure the parameters for model training and evaluation.
2. serve_desc.yaml: Configure the general parameters of model inference.
3. serve.yaml: Configure the specific parameters of the inference model, if there are n inference models, there are n serve.yaml correspondingly.

### 4.2  Configuration file storage directory
Please store the configuration file to the specified directory, otherwise it cannot be published.
1. manifest.yaml: `code` folder
2. serve_desc.yaml: `{training output path}`
3. serve.yaml: `{training output path}/{model name}`, there is a serve.yaml in each inference model directory.

- Directory example
```
code/
|   manifest.yaml
|
outputs/
|   my_training_outputs/
|   |   my_plugin_1/
|   |   |   serve.yaml
|   |   |
|   |   my_plugin_2/
|   |   |   serve.yaml
|   |   |
|   |   my_plugin_3/
|   |   |   serve.yaml
|   |   |
|   |   serve_desc.yaml
|   |   class_name.txt
```
The training output path in the example is the `my_training_outputs` folder, or you can specify it yourself.

### 4.3  Automatic annotation
Once the inference model is published, it can be used for automatic annotation on the data annotation platform ADMagic.
If you want to map the model inference output labels to ADMagic annotation labels for automatic annotation, you need to complete the following before publishing the model.
1. under `{training output path}`, create a new text file `class_names.txt`, encoded in UTF-8.
2. fill the labels of the model output into `class_names.txt`, one label for each row, separated by newlines.
   For example, if the dog and cat target detection model outputs the labels "dog" and "cat", then the contents of `class_names.txt` are
   ```
   dog
   cat
   ```