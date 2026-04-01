GitHub and Python on Windows
=============================

This is a quick tutorial on windows for getting GitHub working in your terminal. After this guide,
you should be able to run Python scripts, clone repositories, and use GitHub from the command line. 

Getting GitHub Working on Windows
---------------------------------
Here's a recommended guide on how to set up GitHub on Windows:
`GitHub in VS Code <https://code.visualstudio.com/docs/sourcecontrol/github>`_

Getting Python Working on Windows
---------------------------------
Here's a good guide on getting python working on windows:
`Python on Windows <https://docs.python.org/3/using/windows.html>`_

Using PipFile on Windows
---------------------------------
Here's a guide on using PipFile on Windows:

1. Install pipenv using pip:

   .. code-block:: console

      py -m pip install pipenv

2. To run a script with the PipFile dependencies, navigate to the directory containing the PipFile and run:

   .. code-block:: console

      pipenv run python your_script.py

3. Alternatively, you can run a custom command prespecified using: 
    .. code-block:: console
    
        pipenv run your_command

Getting Set Up on MacOS
-----------------------
The process for getting GitHub and Python working on MacOS is similar to Windows, but with some differences in installation. Here are the steps:

1. Install python3.13 which can be found here: `Python 3.13 for MacOS <https://www.python.org/downloads/macos/>`_

2. Install GitHub Desktop for MacOS: `GitHub Desktop for MacOS <https://desktop.github.com/>`_

3. Go to the repository you want to clone on GitHub, and click "Open in GitHub Desktop". This will open the repository in GitHub Desktop, where you can clone it to your local machine.

4. Then run these commands to set up pipenv and install dependencies:

.. code-block:: console

   python3 -m pip install pipenv
   python3 -m pipenv install
   
Now you should be able to run your Python scripts with the dependencies specified in the PipFile using:

.. code-block:: console

   python3 -m pipenv run python your_script.py

or
.. code-block:: console

   python3 -m pipenv run your_command