============= 
SONIC_producer_tutorial
=============

Project description: An tutorial of writing a producer for a ML model to make inference with SONIC interface in the CMSSW. It uses ParticleTransformer as an example for with Run3 2023 AOD->MiniAOD data processing step.

.. note:: 
    The tutorial is under construction...


CMSSW standard workflow for data processing
===============

Configuration file for AOD->MiniAOD
------------

You need to confirm a workflow that involves the ML model making inference before hand. An example for particle transformer is the centrally produced Run3 TTTo2L2Nu Monte-Carlo sample in 2022. It can be found on `McM <https://cms-pdmv-prod.web.cern.ch/mcm/chained_requests?contains=TOP-Run3Summer22MiniAODv4-00001&page=0&shown=15>`_

By clicking the MiniAODv4 step in the chain, then click on ``Get setup command`` icon, you can check its ``cmsDriver.py`` command for generating a cms configuration file, that takes an AOD root file as input, and output a MiniAODv4 root file. 

.. code-block:: bash

    # cmsDriver command
    cmsDriver.py  --python_filename TOP-Run3Summer22MiniAODv4-00001_1_cfg.py --eventcontent MINIAODSIM --customise Configuration/DataProcessing/Utils.addMonitoring --datatier MINIAODSIM --fileout file:TOP-Run3Summer22MiniAODv4-00001.root --conditions 130X_mcRun3_2022_realistic_v5 --step PAT --geometry DB:Extended --filein "dbs:/TTto2L2Nu_HT-500_NJet-7_TuneCP5_13p6TeV_powheg-pythia8/Run3Summer22DRPremix-124X_mcRun3_2022_realistic_v12-v2/AODSIM" --era Run3,run3_miniAOD_12X --no_exec --mc -n $EVENTS || exit $? ;

Please note that for locally generate a cmsConfig, we don't have environmental variable ``$EVENTS`` and we don't have to terminate the process. The number of events in this command can be filled with a random number just for the purpose of generating the configuration file. 

To briefly understand the structure of a CMS configuration file, you can read `CMSConfigFileIntro <https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookConfigFileIntro>`_

Process the workflow
------------

- Step 1: Set up your grid certificate ``voms-proxy-init``.
- Step 2: Setup ``CMSSW_14_1_0_pre0`` in a proper file system location.

.. code-block:: bash

    cmsrel CMSSW_14_1_0_pre0
    cd CMSSW_14_1_0_pre0/src/
    cmsenv

- Step 3: Generate the cmsConfig file.

.. code-block:: bash

    mkdir test_sonic_2023
    cd test_sonic_2023/
    cmsDriver.py  --python_filename miniaod_2022_cfg.py --eventcontent MINIAODSIM --customise Configuration/DataProcessing/Utils.addMonitoring --datatier MINIAODSIM --fileout file:miniaod_2022.root --conditions 130X_mcRun3_2022_realistic_v5 --step PAT --geometry DB:Extended --filein "dbs:/TTto2L2Nu_HT-500_NJet-7_TuneCP5_13p6TeV_powheg-pythia8/Run3Summer22DRPremix-124X_mcRun3_2022_realistic_v12-v2/AODSIM" --era Run3,run3_miniAOD_12X --no_exec --mc -n 10

- Step 4: ``cmsRun`` the cmsConfig file, and get the miniAOD file generated. You can modify the input/output file name and number of events that you want to process.

.. code-block:: bash

    cmsRun miniaod_2022_cfg.py


Extract the inference results
------------
Copy and run the a python script ``plotParTAK4.py`` that is provided by this repo. Make sure the MiniAOD root file name is correct in the python script. 

.. code-block:: bash

    python3 plotParTAK4.py

Please check the script and see how it extract information from MiniAOD file and creates histograms of the inference results.



Usage
-----

Example of how to use the project in code:

.. code-block:: python

   from project_name import main_function
   result = main_function(argument1, argument2)
   print(result)

Requirements
------------

- Python 3.x
- Required libraries (e.g., ``requests``, ``numpy``)
  
Development
===========

To contribute to the project, follow these steps:

1. Fork the repository
2. Create a new branch
3. Make changes and test
4. Submit a pull request

Running Tests
-------------

Run tests with the following command:

.. code-block:: bash

   pytest

File Structure
==============

A brief overview of the key files and directories:

- ``project_name/``: The main project directory
- ``tests/``: Contains tests for the project
- ``README.rst``: Project documentation

.. note::
   Add any additional files and folders specific to your project.

License
=======

This project is licensed under the MIT License - see the LICENSE file for details.