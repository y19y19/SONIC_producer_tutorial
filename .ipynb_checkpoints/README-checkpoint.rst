============= 
SONIC_producer_tutorial
=============

Project description: An tutorial of writing a producer for a ML model to make inference with SONIC interface in the CMSSW.

.. note::
    The tutorial is under construction...

CMSSW standard workflow for data processing
===============

Data tiers
------------

An example of centrally produced Run3 TTTo2L2Nu Monte-Carlo sample can be found on `McM <https://cms-pdmv-prod.web.cern.ch/mcm/chained_requests?contains=TOP-Run3Summer23MiniAODv4-00001&page=0&shown=15>`_

By clicking the MiniAODv4 step in the chain, then click on ``Get setup command`` icon, you can check its ``cmsDriver.py`` command for generating a cms configuration file, that takes an AOD root file as input, and output a MiniAODv4 root file. 

.. code-block:: bash

    # cmsDriver command
    cmsDriver.py  --python_filename TOP-Run3Summer23MiniAODv4-00001_1_cfg.py --eventcontent MINIAODSIM --customise Configuration/DataProcessing/Utils.addMonitoring --datatier MINIAODSIM --fileout file:TOP-Run3Summer23MiniAODv4-00001.root --conditions 130X_mcRun3_2023_realistic_v14 --step PAT --geometry DB:Extended --filein "dbs:/TTto2L2Nu_HT-500_NJet-7_TuneCP5_13p6TeV_powheg-pythia8/Run3Summer23DRPremix-130X_mcRun3_2023_realistic_v14-v1/AODSIM" --era Run3_2023 --no_exec --mc -n $EVENTS || exit $? ;

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