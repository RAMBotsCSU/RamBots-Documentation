Running Test Scripts
=====================
After learning about the magic of PipFile, the goal is to run all of our test code from the command line and combine everything into a single test suite.  
                                                                                                                                                            
Requirements
------------

* pip, python, and pipenv must be installed to run this supercode, so get those on your path                                                               
* For windows and maybe mac use:   
                                                                                                                                         
.. code-block:: console
    
    py -m pip install pipenv                                                                                                                                   
                                                                                                                                                      
and

.. code-block:: console                                                                                                                                                     
    
    py -m pipenv run <SCRIPTNAME>
                                                                                                                                                    
* any non-python code should be run from it's respective directory                                                                                                                                     
                                                                                                                                                            
LIDAR Scripts     
-------------

Test out running the LiDAR sensor and displaying it in real-time:                                                                                          

.. code-block:: console                                                                                                                                                     
    
    py -m pipenv run lidar_sim

O-Drive Scripts   
----------------

Run RamBOT-Tool UI (Best):                                                                                                                                 

.. code-block:: console

    py -m pipenv run rambot_tool_v2
                                                                                                                                                          
                                                                                                           
Run old calibration script:                                                                                                              

.. code-block:: console

    py -m pipenv run calibrate

Run original calibration script:                                                                                                                                

.. code-block:: console

     py -m pipenv run calibrate_old

Run simple diagnostic script:                                                                                                                              

.. code-block:: console

    py -m pipenv run diagnostic                                                                                                                                      
                                                                                                                                                            
Run single motor calibration axis 0 (for the testbench hip):                                                                                               

.. code-block:: console

    py -m pipenv run calibrate_axis0                                                                                                                                 
                                                                                                                                                            
Run single motor calibration axis 1:                                                                                                                       

.. code-block:: console

     py -m pipenv run calibrate_axis1                                                                                                                                 

Plotter Code 
-----------------

Run code that plots current in real-time (O-Drive must be plugged in):                                                                                     

.. code-block:: console

    py -m pipenv run plotter                                                                                                                                         
                                                                                                                                                            
Single Leg Test Code
----------------------------

Run old code to operate single leg (old teensy code must be loaded):                                                                                       

.. code-block:: console

     py -m pipenv run com_sim
                                                                                                                                                            
Run single leg code for Linux:

.. code-block:: console

     py -m pipenv run com_sim_linux                                                                                                                                                   