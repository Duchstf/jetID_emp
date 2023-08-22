# JetID on EMP

JetID setup for EMP framework for CMS L1 Trigger.

# L1 resources links

1. https://serenity.web.cern.ch/serenity/emp-fwk/
    * Specifically how to build the firmware here: https://serenity.web.cern.ch/serenity/emp-fwk/firmware/instructions.html

2. Building 904 setup (optical links connections): https://cms-l1t-phase2.docs.cern.ch/904/overview.html#optical-links

# EMP Simulation Notes

1. First you need to activate the environment and enable ipbb.

```
conda activate jetID
```

```
source ipbb-dev-2022f/env.sh
```

2. Then:

```
ipbb proj create vivado jet-sim correlator-layer2:jet_seededcone top_sim.dep
cd proj/jet-sim
ipbb vivado generate-project --enable-ip-cache -1
```


# EMP Building Notes

1. Go to this website to start:

https://gitlab.cern.ch/p2-xware/firmware/emp-fwk/-/tree/v0.7.4?ref_type=tags

2. You want to make sure you install the prerequisites

- Xilinx Vivado: 2021.2
- Python 2.7 - available on most linux distributions, natively or as miniconda distribution.
- ipbb: dev/2022f pre-release or greater - the IPbus Builder Tool. Note: a single ipbb installation is not work area specific and suffices for any number of projects

In my case I set up a miniconda environment:

```
conda-env create -f environment.yml
```

Then,

```
conda activate jetID
```

After that,

```
curl -L https://github.com/ipbus/ipbb/archive/dev/2022f.tar.gz | tar xvz
source ipbb-dev-2022f/env.sh
```

3. After you have the ipbb software running, you can follow the instructions on `emp-fwk`, I documented the commands to set it up in `jetid_setup.sh`. 

4. For seededcone, note that you need to run all the corresponding tcl files in `correlator-common`, as of the current version, we need `jetmet/jec`, `jetmet/seededcone`, `htmht`, to be converted from hls to vhdl in order for seededcone to run properly.

Note that you have to setup cmssw for it: 


```
source /cvmfs/cms.cern.ch/cmsset_default.sh
```

```
./utils/setup_cmssw.sh -run CMSSW_12_5_5_patch1 p2l1pfp:L1PF_12_5_X lict-125x-v1.15
```

```
export CMSSW_VERSION=CMSSW_12_5_5_patch1
```

5. After that you need to follow the instructions to create a build for seededcone, this is what I did:

```
ipbb proj create vivado btag correlator-layer2:jet_seededcone top_serenity.dep
cd proj/btag
```

then build the package

```
ipbb ipbus gendecoders
ipbb vivado generate-project
ipbb vivado synth -j4 impl -j4
ipbb vivado package
```

6. Potential errors:

 ```
 Error: 'gen_ipbus_addr_decode' script not found.
 ```

 and missing files:

 ```
 cms-tcds2-firmware/components/tcds2_interface/firmware/hdl/ipbus_decode_ipbus_tcds2_interface_accessor.vhd
 ```

Basically you need to follow the presequisite here: https://serenity.web.cern.ch/serenity/emp-fwk/firmware/instructions.html#prerequisites

Just to quote from the website, "in order for the TCDS2 functionality to be correctly implemented and for the command ipbb ipbus gendecoders to run, uHAL needs to be installed." 

