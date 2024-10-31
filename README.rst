============= 
SONIC producer tutorial
=============

Project description: An tutorial of writing a producer for a ML model to make inference with SONIC interface in the CMSSW. It uses ParticleTransformer as an example for with Run3 2023 AOD->MiniAOD data processing step.

.. note:: 
    The tutorial is under construction...


CMSSW standard workflow for data processing
===============

Configuration file for AOD->MiniAOD
------------

You need to confirm a workflow that involves the ML model making inference before hand. An example for particle transformer is the centrally produced Run3 TTTo2L2Nu Monte-Carlo sample in 2023. It can be found on `McM <https://cms-pdmv-prod.web.cern.ch/mcm/chained_requests?contains=TOP-Run3Summer23MiniAODv4-00001&page=0&shown=15>`_

By clicking the MiniAODv4 step in the chain, then click on ``Get setup command`` icon, you can check its ``cmsDriver.py`` command for generating a cms configuration file, that takes an AOD root file as input, and outputs a MiniAODv4 root file. 

.. code-block:: bash

    # cmsDriver command
    cmsDriver.py  --python_filename TOP-Run3Summer23MiniAODv4-00001_1_cfg.py --eventcontent MINIAODSIM --customise Configuration/DataProcessing/Utils.addMonitoring --datatier MINIAODSIM --fileout file:TOP-Run3Summer23MiniAODv4-00001.root --conditions 130X_mcRun3_2023_realistic_v14 --step PAT --geometry DB:Extended --filein "dbs:/TTto2L2Nu_HT-500_NJet-7_TuneCP5_13p6TeV_powheg-pythia8/Run3Summer23DRPremix-130X_mcRun3_2023_realistic_v14-v1/AODSIM" --era Run3_2023 --no_exec --mc -n $EVENTS || exit $? ;

Please note that for locally generate a cmsConfig, we don't have environmental variable ``$EVENTS`` and we don't have to terminate the process. The number of events in this command can be filled with a random number just for the purpose of generating the configuration file. 

To briefly understand the structure of a CMS configuration file, you can read `CMSConfigFileIntro <https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookConfigFileIntro>`_

Process the workflow in Purdue Analysis Facility
------------

- Step 1: Set up your grid certificate ``voms-proxy-init``.
- Step 2: Use Python3 kernel and load ``cmsset_default.sh``

.. code-block: bash

    conda activate /depot/cms/kernels/python3
    source /cvmfs/cms.cern.ch/cmsset_default.sh
    
- Step 3: Setup ``CMSSW_14_1_0_pre0`` in a proper file system location.

.. code-block:: bash

    cmsrel CMSSW_14_1_0_pre0
    cd CMSSW_14_1_0_pre0/src/
    cmsenv
    git cms-init
    scram b -j 10

- Step 4: Generate the cmsConfig file.

.. code-block:: bash

    mkdir test_sonic_2023
    cd test_sonic_2023/
    cmsDriver.py --python_filename miniaod_2023_cfg.py --eventcontent MINIAODSIM --customise Configuration/DataProcessing/Utils.addMonitoring --datatier MINIAODSIM --fileout file:miniaod_2023.root --conditions 130X_mcRun3_2023_realistic_v14 --step PAT --geometry DB:Extended --filein file:/depot/cms/users/yao317/datasets/TTto2L2Nu_HT-500_NJet-7_TuneCP5_13p6TeV_powheg-pythia8_Run3Summer23DRPremix-130X_mcRun3_2023_realistic_v14-v3/0055c37c-9761-494d-83a8-e7820258686b.root --era Run3_2023 --no_exec --mc -n 10

- Step 5: ``cmsRun`` the cmsConfig file, and get the miniAOD file generated. You can modify the input/output file name and number of events that you want to process.

.. code-block:: bash

    cmsRun miniaod_2023_cfg.py


Extract the inference results
------------
Copy and run the a python script ``plotParTAK4.py`` that is provided by this repo. Make sure the MiniAOD root file name is correct in the python script. 

.. code-block:: bash

    python3 plotParTAK4.py

Please check the script and see how it extract information from MiniAOD file and creates histograms of the inference results.

.. note:: 

How to confirm if an algorithm is being called in AOD -> MiniAOD process?
It is usually under ``PhysicsTools/PatAlgos/`` in `cmssw <https://github.com/cms-sw/cmssw/blob/CMSSW_14_1_0_pre0/PhysicsTools/PatAlgos/python/slimming/applyDeepBtagging_cff.py>`_

.. note::
Do you know where the producer is defined? See the next section... 

Original Producer
=============
- Step 1: Check out the following packages under ``$CMSSW_BASE/src/`` and compile.

.. code-block:: bash

    git cms-addpkg RecoBTag/ONNXRuntime
    git cms-addpkg RecoBTag/Combined
    scram b -j 10

- Step 2: Add models to ``RecoBTag/Combined``. First, fork `RecoBTag model repo <https://github.com/cms-data/RecoBTag-Combined>`_. Then git clone your forked model repo. 

.. code-block:: bash

    git clone <ssh clone your RecoBTag-Combined repo>  RecoBTag/Combined/data/

Now take a look at the structure of the two packages. 

``ONNXRuntime/plugins/`` defines the producers.

``ONNXRuntime/python/`` make producer part of a CMS Process. This is what being called in the cmsConfig.

``ONNXRuntime/interface/`` header files for utilities that are used by plugins.

``ONNXRuntime/src/`` C files for definition of utilities that are used by the plugins.

``Combined/data/models/`` It should be identical to what is loaded by the SONIC triton server. For new models, we need to move the model to this folder, and create a link in the original folder, such that the original workflow is not interupted.


- Step 3: Let's edit the SONIC producer, and see what happens. Go to ``ONNXRuntime/plugins/ParticleTransformerAK4ONNXJetTagsProducer.cc`` and add some printouts in function: ``ParticleTransformerAK4ONNXJetTagsProducer::produce``. You can also add some printouts for input size, or output inference scores.


.. code-block:: cpp

    std::cout << "In ParT ONNX producer" << std::endl;

- Step 4: Compile. Go to the ``RecoBTag/`` and 

.. code-block:: bash

    scram b -j 10

- Step 5: Try to run the AOD->MiniAOD step again with cmsRun cmsConfig, and see if it prints out what you expect. This is important in debugging.


SONIC Producer
=============

Set up a server
------------




Set up client
------------

