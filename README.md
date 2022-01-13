# Azure AI Vision Tecnun

## Prerequisites

### Azure Subscription for students

More information in https://azure.microsoft.com/en-us/free/students/, you can start free following the instructions: click on **Start Free** and use your UNAV email account.

### Windows OS
You can either use your local computer or [create an Azure Virtual Machine](https://github.com/unaihuete-org/AzureVisionTecnun/blob/main/CreateAzureVM.md#create-azure-vm)

### Python (and required packages)

1. Download version 3.8 from https://docs.conda.io/en/latest/miniconda.html 
![image](https://user-images.githubusercontent.com/64772417/149216837-6cb338a3-9bc4-456c-9f0b-5fe853f0f1a4.png)
1. Run setup to install - **Important**: Select the options to add Miniconda to the PATH variable and to register Miniconda as the default Python environment.
1. After installation, open the Anaconda prompt and enter the following commands to install packages: 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```
### VS Code
1. In your browser, navigate to [Visual Studio Code download page](https://code.visualstudio.com/Download)
1. Select and download the Windows installer. Most browsers give you the option to either save the file to your local computer (usually in your Downloads folder) or immediately run the installer file.
1. Double-click the installer file to start the installation process.

After the installation is complete, Visual Studio Code launches automatically.

#### Install Visual Studio Code extensions

Install the following Visual Studio Code extensions for the lab:

1. In the Visual Studio Code, from the menu bar select View > Extensions to open the Extensions view.

    ![image](https://user-images.githubusercontent.com/64772417/149222523-375e3d9d-3da3-497f-b93f-2ba0605af18b.png)
    The Extensions view lists the Visual Studio Code extensions that are installed, recommended extensions from the most popular on Marketplace, and the extensions that are installed and enabled.

1. Filter the list of available extensions by entering **python** in the search box at the top of the Extensions view.

1. Select the 'Python' extension published by Microsoft, in the either Extensions panel, or in the main panel, select Install.
    ![image](https://user-images.githubusercontent.com/64772417/149222737-a6dfd960-ddcf-4f89-aa31-f3e041e420de.png)

1. Search the extension **Indent on Paste** by **gazugafan** and Install as mentioned before.
