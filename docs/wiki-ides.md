---
author: "Yiyan Hao"
date: 2026-08-20
---

# Using IDEs on Cluster

## Integrated development environments (IDEs)

An IDE is a software application that allows us to write, test, and debug code in a user-friendly environment. Commonly used IDEs include RStudio, Jupyter Lab, and Visual Studio Code (VS Code); the choice of which one to use depends on the programming language being used and the preferences of the user. While RStudio is popular for R users, Jupyter Lab is particularly convenient for exploratory data analysis, visualization, and writing demos, using notebooks written in Python. VS Code supports multiple programming languages and has a wide range of extensions available. 


!!! note
    All three IDEs are run through login nodes, and therefore should only be used for exploratory tasks. **DO NOT run large jobs in these sessions!** It will quickly crash your home directory and may cause issues for other users. 

Each software has its own advantages and limitations when it comes to cluster computing. Sometimes we may encounter issues with connection due to software updates or changes to the cluster, and the best point of contact in those situations is the PMACS support team. In the following sections, we will provide instructions on how to **set up the bash startup file** and how to **connect to the cluster** using each IDE.

## Bash setup

Before moving on to the software-specific instructions, let's check which shell your local terminal is using and determine the appropriate configuration file to set up aliases. You can do this by running the following command in your terminal:

```bash
echo $SHELL
```

If the output is `/bin/bash`, then you are using the Bash shell, and you should edit the `.bash_profile` file. If the output is `/bin/zsh`, then you are using the Zsh shell, and you should edit the `.zshrc` file.

Now, make sure that you've completed all steps in the [Bash](environment_setup.md) page. Add the corresponding aliases to your local computer: copy the following chunk from the `.env` file from the cluster

```bash
CBICA_USERNAME="your_cbica_username"
PMACS_USERNAME="your_pmacs_username"
PREFERRED_PORT="your_preferred_port"
PREFERRED_PORT2=$(($PREFERRED_PORT + 1))
```

and paste it in your local `.bash_profile` or `.zshrc` file. Then, add the following lines:

```bash
alias sciget="ssh ${PMACS_USERNAME}@sciget.pmacs.upenn.edu"
alias scisub="ssh ${PMACS_USERNAME}@scisub.pmacs.upenn.edu"
alias takim2="ssh ${PMACS_USERNAME}@takim2"
alias scisub9="ssh ${PMACS_USERNAME}@scisub9.pmacs.upenn.edu"
alias takim2http="ssh -L ${PREFERRED_PORT}:127.0.0.1:${PREFERRED_PORT} -q ${PMACS_USERNAME}@takim2"
alias takim2http2="ssh -L ${PREFERRED_PORT2}:127.0.0.1:${PREFERRED_PORT2} -q ${PMACS_USERNAME}@takim2"
alias ccebsub2="ssh ${PMACS_USERNAME}@ccebsub2.pmacs.upenn.edu"

```

Once the edits are made, save the file and run `source ~/.bash_profile` or `source ~/.zshrc` to apply the changes. Here, `takim2http` and `takim2http2` are aliases for connecting to the cluster with ssh tunneling, which allows you to forward a local port on your machine to a port on the cluster (e.g., 123 for RStudio, 124 for Jupyter Lab) in order to open the IDE on a browser.

## Using RStudio

After logging into the cluster, follow the two steps below to set everything up (Don't forget to source the file if any changes are made. These need to be done once only):

1. Check that the `.bash_aliases` file has the updated definition for the rstudio alias. Locate the following line under the "#program aliases" section, inside the `elif` statement: `alias rstudio='docker run --rm -d -v $PWD:/data -w /data -p 80:8787 pennsive/rstudio:4.1'`. Also make sure that `R` and `apptainer` are loaded. 

2. Set up a temporary directory for RStudio by adding `export TMPDIR=/scratch` to the `.bashrc` file. 

You may log into the cluster and start an RStudio session via the following steps each time:

1. Connect to the PMACS VPN, and log into the cluster by typing an alias with ssh tunneling in your terminal (e.g., `takim2http`).
2. Start the RStudio apptainer container on the login node by running `rstudio`.
3. Open a browser on your local machine and navigate to `http://localhost:preferred_port` (you may find it by `echo $PREFERRED_PORT`, or recall from the `.bash_profile` or `.zshrc` file). 

Now, you should be able to use RStudio as you normally would on your local machine. Note that you will only be able to open / edit files in your home directory (e.g., not `/project`) from RStudio, so you may want to copy files to `~/` for editing.

For more details on using RStudio, please refer to the [RStudio documentation](https://docs.posit.co/ide/user/).

## Using Jupyter Lab

To use Jupyter Lab, follow similar steps as RStudio to set up the bash files and start a session:

1. (Do this once) Open the `.bash_aliases` file and check that the definition for the jupyterlab alias is updated. Locate the following line under the "#program aliases" section, inside the `elif` statement: `alias jlab='jupyter-lab --no-browser --port=${PREFERRED_PORT2}'`

2. Connect to the PMACS VPN, and log into the cluster by typing an alias with ssh tunneling in your terminal (e.g., `takim2http2`).

3. Start the Jupyter Lab session by running `jlab` in the terminal. Wait a few seconds for the session to start, and you should see a message in the terminal that includes a URL with a token. Copy the link that starts with `http://localhost:`.

4. Open a browser on your local machine and paste the copied URL. You should now be able to use Jupyter Lab as you normally would on your local machine. Similar to RStudio, you will only be able to open / edit files in your home directory (e.g., not `/project`) from Jupyter Lab, so you may want to copy files to `~/` for editing.

Sometimes we might want to create a conda environment for management of Python packages. We can conveniently create and use such environments in Jupyter Notebook as well. To create an environment called `myenv` with the `scikit-learn` package, run the following commands in the login node terminal:

```bash
conda create --prefix path_to_env/myenv -c conda-forge scikit-learn
conda activate myenv
pip install ipykernel
python -m ipykernel install --user --name myenv
```

Here, `path_to_env` is the path to store conda environments, which by default is set to `~/.conda/envs/`. You can specify it to a different path, such as inside a project directory, to share with others.

Then, when you open a notebook from a Jupyter Lab session (by running `jlab`), you should be able to select `myenv` as the kernel from the upper right corner and use the packages installed in that environment. 

For more details on using Jupyter Lab, please refer to the [Jupyter Lab documentation](https://docs.jupyter.org/en/latest/).


## Using VS Code

Before connecting, you'll need to install the `Remote - SSH` extension in VS Code (search for ms-vscode-remote.remote-ssh in the Extensions Marketplace). Also make sure that you have completed all steps in the [Bash Setup](environment_setup.md) page.

!!! note "Optional path configuration"
    Running notebooks in VS Code may take up a lot of memory, and the default path for VS Code Server is in the home directory. If you want to change the path to a different location (e.g., `/scratch`), open a new VS Code window. Press `Ctrl+Shift+P` / `Cmd+Shift+P` to open the Command Palette, and type "Preferences: Open User Settings (JSON)". Add the following entry to the exisiting json object:
    ```json
    {
    "remote.SSH.serverInstallPath": 
      {
        "my-server": "/scratch/YOUR_USERNAME/.vscode-server"
      }
    }
    ```

    Make sure that `/scratch/YOUR_USERNAME` exists. You can verify the setup is successful by checking if `/home/YOUR_USERNAME/.vscode-server` is created after connecting VS Code to the host (see details below). If it still exists, try closing the current VS Code windows, deleting the remote folder and reconnecting to the cluster. 

Now, open VS Code on your local machine, open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and type "Remote-SSH: Connect to Host…", and select your cluster. A new VS Code window will open and automatically install the VS Code Server on the remote machine (this only happens during the first connection or after the server is rebooted). After a few seconds, you should see the green indicator in the bottom-left corner of the window and the remote host name. You are now connected!

From here, click "File", "Open Folder" to browse and open any directory on the cluster's filesystem. You can now edit files, run notebooks, and use the terminal just as if you were working on your local machine! Note that you will only be able to access files in your home directory (e.g., not `/project`) from VS Code, so you may want to copy files to `~/` for editing.

!!! note "Optional environment setup"
    If you want to use a specific conda environment in VS Code (e.g., when running a Jupyter notebook), you can set it up by following these steps:

    - On the remote host, make sure your conda environment has the `ipykernel` package installed. If not, activate the environment and run

    ```bash
    python -m ipykernel install --user \
    --name myenv \
    --display-name "Python (myenv)"
    ```

    - In vscode, open an `.ipynb` file, click "Select Kernel" on the top right, and see if the environment appears. If not, press `Cmd+Shift+P`, type “Python: Select Interpreter”, and then choose `Enter interpreter path` to where the python interpreter corresponding to your conda environment is located. For example, `/home/YOUR_USERNAME/.conda/envs/myenv/bin/python`.

    You should only need to do this once per environment, and should be able to use it from any `.ipynb` files.

The official [VS Code documentation](https://code.visualstudio.com/docs) provides more details on its usage and features that you may find useful.