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