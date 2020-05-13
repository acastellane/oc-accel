# HLS_HELLOWORLD_PYTHON EXAMPLE

* Provides a simple base allowing to discover AC-ACCEL
* Python code is changing characters case of a user phrase
  * code can be executed on the CPU (will transform all characters to lower case)
  * code can be simulated (will transform all characters to upper case in simulation)
  * code can then run in hardware when the FPGA is programmed (will transform all characters to upper case in hardware)
* The example code uses the copy mechanism to get/put the file from/to system host memory to/from DDR FPGA attached memory

* Some packages you may need to install on your system

```
# Installing python development library, swig and curl
sudo apt-get install python3-dev python3-venv swig curl
```


* To get started use the following steps:
```
git clone git@github.com:diamantopoulos/oc-accel.git
git clone https://github.com/OpenCAPI/ocse
cd oc-accel
git checkout add_action_hls_helloworld_python
make snap_config        #select (X) HLS Action - manually set ACTION ...
vim snap_env.sh -> export ACTION_ROOT=${SNAP_ROOT}/actions/hls_helloworld_python/
# Check ocse PATH if not default ~/ocse
. snap_env.sh
make software
cd actions/hls_helloworld_python/sw
python3 -m venv env_sim
source env_sim/bin/activate
pip3 install -r requirements.txt  # note you may see errors (eg pyzmq)
make pywrap  # to compile the appropriate libraries for SWIG
```

### To launch a Python shell and use the OCSE (RTL simulation)

* Run action simulation

```
cd ${SNAP_ROOT}
make sim 
```

* On the xTerm that pops-up:

```
oc_maint -vvv

LD_LIBRARY_PATH=$OCSE_ROOT/libocxl/ python3

import sys
import os

snap_action_sw=os.environ['SNAP_ROOT'] + "/actions/hls_helloworld_python/sw"
print(snap_action_sw)
sys.path.append(snap_action_sw)

import snap_helloworld_python
 
input = "Hello world. This is my first CAPI SNAP experience with Python. It's extremely fun"
output = "11111111111111111111111111111111111111111111111111111111111111111111111111111111111111"

out, output = snap_helloworld_python.uppercase(input)

print("Output from FPGA:"+output)

print("Output from CPU :"+input.upper())

exit() # from python shell

exit # from xTerm
```


### To launch a Jupyter Notebook and use the OCSE (RTL simulation)

* Run action simulation

```
cd ${SNAP_ROOT}
make sim 
```

* On the xTerm that pops-up:
```
oc_maint -vvv

cp ../../../../actions/hls_helloworld_python/sw/snap_helloworld_python.py .
cp ../../../../actions/hls_helloworld_python/sw/trieres_helloworld_cosim.ipynb .

# For Jupyter:
jupyter notebook trieres_helloworld_cosim.ipynb # and follow the instructions: Select every cell and run it with Ctrl+d

# For Jupyter Lab:
jupyter-lab trieres_helloworld_cosim.ipynb # and follow the instructions: Select every cell and run it with Ctrl+d

Ctrl+c # kill Jupyter Notebook / Jupyter Lab

exit # from xTerm

```




### To launch a Jupyter Notebook on P9 (on the FPGA card)

* Install required tools
 ```
 sudo apt-get install python3-dev python3-venv swig curl
```

* Ensure you have compiled oc-accel's software on P9
```
git clone git@github.com:diamantopoulos/oc-accel.git oc-accel-python
cd oc-accel-python/
git checkout add_action_hls_helloworld_python
vim snap_env.sh -> 
  export SNAP_ROOT=/home/capiteam/your_user/oc-accel-python
  export ACTION_ROOT=${SNAP_ROOT}/actions/hls_helloworld_python/
. snap_env.sh
make software
cd actions/hls_helloworld_python/sw
python3 -m venv env_P9
source env_P9/bin/activate
pip3 install -r requirements.txt    # note you may see errors (eg pyzmq)
make pywrap  # to compile the appropriate libraries for SWIG
```

* Continue as any action (you may need sudo when execution jupyter to have valid access rights for the card):

```
# optional step: it ensured you see the action in the FPGA

# either your have set sudo rights rule
. $SNAP_ROOT/snap_path.sh        # to source SNAP_ROOT and ACTION_ROOT
oc_maint -vvv  
cd ${SNAP_ROOT}/actions/hls_helloworld_python/sw/

# or if you need to be sudo to run ocmaint :
sudo ${SNAP_ROOT}/software/tools/oc_maint -vvv   


LD_LIBRARY_PATH=$OCSE_ROOT/libocxl/ python3

import sys
import os

snap_action_sw=os.environ['SNAP_ROOT'] + "/actions/hls_helloworld_python/sw"
print(snap_action_sw)
sys.path.append(snap_action_sw)

import snap_helloworld_python
 
input = "Hello world. This is my first CAPI SNAP experience with Python. It's extremely fun"
output = "11111111111111111111111111111111111111111111111111111111111111111111111111111111111111"

out, output = snap_helloworld_python.uppercase(input)

print("Output from FPGA:"+output)

print("Output from CPU :"+input.upper())

exit() # from python shell

exit # from xTerm


# then you can use the jupyter env to run the helloworld case with python
sudo jupyter notebook trieres_helloworld.ipynb # and follow the instructions: Select every cell and run it with Ctrl+d

Ctrl+c # kill Jupyter Notebook

exit # from xTerm

```
