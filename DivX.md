# DivX

## Introduction

A diversity-driven and explanation-oriented solution to address the above challenges. DivX is designed to enhance any state-of-the-art single-scenario fuzzers to improve their testing completeness and provide actionable clues.

## Runtime Configuration

- OS: Ubuntu 18.04, 20.04
- CPU >= 8 cores
- GPU >= 16 GB memory 

## Current support ADS + Simulation

- SORA_SVL + Apollo 6.0
- Autoware --- + CARLA 0.9.10

## Preparation

***Apollo + LGSVL Preparation***

1. Mandatory installation package:

* For apollo to run perfectly, Ubuntu 18.04 or 20.04 is recommanded

* Install Nvidia GPU driver

  ```sudo apt-get update```

  ```sudo apt-add-repository multiverse```

  ```sudo apt-get update```

  ```sudo apt-get install nvidia-driver-455```

  __Notice:__

  If nvidia-smi does not have an output, install cuda toolkit

  ```  sudo apt install nvidia-cuda-toolkit ```

  ```reboot```

* Install Docker

  Installing using the repository.

  For details: reference https://docs.docker.com/engine/install/ubuntu/

  __set up the repository:__

  * Update the apt package index and install packages to allow apt to use a repository over HTTPS:

    ``` sudo apt-get update```

    ``` sudo apt-get install ca-certificates curl gnupg lsb-release```

  * Add Docker’s official GPG key:

    ``` curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg ```

  * Use the following command to set up the **stable** repository. To add the **nightly** or **test** repository, add the word nightly or test (or both) after the word stable in the commands below.[ ](https://docs.docker.com/engine/install/)

  * ``` 
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    
    
    ```

  

* **Install Docker Engine**

  * Update the apt package index, and install the *latest version* of Docker Engine and container

    ```sudo apt-get update ```

    ``` sudo apt-get install docker-ce docker-ce-cli containerd.io```

  * Verify that Docker Engine is installed correctly by running the hello-world image.

    ``` sudo docker run hello-world```

  __Noticed:__

  * Docker user group permission deny:

    ``` sudo groupadd docker```

    ```sudo gpasswd -a $USER docker```

    ``` newgrp docker ```

* *__Solution for not able to use GPU:__*

  For NVIDIA GPU, please update driver under the *Additional Drives* in *Software & Update*

  *For testing your GPU Performance, check Unigine_Heave benchmark*

* *__Solution for nvidia-smi with command error: Failed to initialize NVML: Driver:__*

  * Uninstall nvidia driver:

    ``` sudo apt-get purge nvidia*```

  * Check for available driver version:

    ```ubuntu-drivers devices```

  * Check kernel version:

    ```cat /proc/driver/nvidia/version```

    For example, we try to install version 440:

    ``` sudo apt-get install nvidia-driver-440 nvidia-settings nvidia-prime ```

  * Adjust System driver:

    software & update->additional Drivers

    select _nvidia-driver-440_ and click _apply changes_ and then try:

     ``` conda install -c anaconda pytorch-gpu```

  * Added Nvidia Container Toolkit for docker images:

    

    ```distribution=$(. /etc/os-release;echo $ID$VERSION_ID)```

    ```curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -```

    ```curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list```

    ```sudo apt-get -y update```

    ```sudo apt-get install -y nvidia-docker2```

    ```sudo systemctl restart docker```

    

**Install Apollo**

* Download installation package:

  https://apollo.auto/developer_cn.html

* Unpack the installation package:

  ``` tar -xvf apollo_v6.0_edu_amd64.tar.gz```

* Install:

  cd to root directory

  ``` ./apollo.sh```

**Use Dreamview to check data packages (For record data package)**

* Access into Apollo Docker:

  ```./apollo.sh```(do not use root, or you may not be able to locate qt library)

* Start Dreamview:

  ```bash scripts/bootstrap.sh```

* Dowload demostration record data packages:

  ```wget https://apollo-system.cdn.bcebos.com/dataset/6.0_edu/demo_3.5.record```

* Play the apollo record data package:

* ```cyber_recorder play -f demo_3.5.record --loop```

  __Noticed:__ if error_cyber_recorder command not found_ pops up:
  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
  try: ```source cyber/setup.bash ``` to add _cyber_recorder_ to the evironment path.

  option:--loop. Using for set loop play mode

* Use Dreamview to check the result:

  Type http://localhost:8888 in any browser(chrome recommanded), to access Apollo Dreamview:

  ![img](assets/EglphIcVpq5DCHKESJlpx2PybB8ZeRHckMpqcCfNNvl_Pc1S_7J7KqbQEnF9rRb4K3uzID1gZ-1MEKKELrEu1xzp_twoqe0mBtjI0jf3s5PeQK3DG39FAEoP-xiCjY2DU4EbQNHH)

**PythonAPI**

Before you use this tutorial, please make sure that you have downloaded Apollo and can run these six modules ['localization', 'transform', 'routing', 'prediction', 'planning', 'control', 'recorder'] in dreamview.<br/>
__Recommended minimum configuration in this part (as of January 2022):__<br/>
Rtx3080 or above (8g video memory)<br/>
16GB running memory<br/>
CPU: i7 8-core 16 thread or corresponding R7 CPU<br/>

__Process:__

1. Python API is available in separate repository: https://github.com/lgsvl/PythonAPI
2. Run the following command to install Python files and necessary dependencies(LGSVL bag):<br/>
   ```pip3 install --user -e```
3. Click the Open Browser button to open the Simulator UI, then click Sign in. Enter login credentials or click Sign Up to create an account.
4. You will need to run a simulation using the API Only runtime template to run Python API scripts on your host machine to control the simulation. See the document on Simulations for information on how to create a simulation.

5. Select the newly created Simulation and click the Run Simulation button.
6. launch the container navigate to the directory where the Apollo repository was cloned and enter:<br />
   ```./docker/scripts/dev_start.sh```<br />
   This should launch the container and mount a few volumes. Pulling the latest volumes on the first run could take a few minutes. To get into the container:<br />
   ```./docker/scripts/dev_into.sh```<br />
   Build Apollo (optimized, not debug, with GPU support):<br />
   ```./apollo.sh build_opt_gpu```<br />
   __Note__: The Apollo build may fail on machines with less than 1GB of RAM per CPU core due to aggressive parallelization in the build. If the build fails, either re-start it until it succeeds, but if it continues to fail (especially when running the linker) then you'll need to address the low memory situation by either adding more memory to your build machine or enabling or increasing available swap space. If your Apollo build is crashing on a 16GB machine with little or no swap, try setting it to 16GB.<br />
   increasing available swap space reference: https://bogdancornianu.com/change-swap-size-in-ubuntu/<br />
   start Apollo:<br />
   __Note__: you may receive errors about Dreamview not being build if you do not run the script from the /apollo directory.<br />
   ```./scripts/bootstrap_lgsvl.sh```<br />
   Launch bridge (inside docker container):<br />
   ```./scripts/bridge.sh```<br />
7. Run the script in the path /PythonAPI/Sample_Test_Case/Random-traffic-local.py<br />
   ``` python /PythonAPI/Sample_Test_Case/Random-traffic-local.py```
8. Open http://localhost:8888, you should see that your dreamview has been connected to lgsvl, and the corresponding modules are also opened.

Reference: https://www.svlsimulator.com/docs/python-api/python-api/ <br />
https://www.svlsimulator.com/docs/system-under-test/apollo-master-instructions/



To run our system, firstly, you have make sure Apollo is up, and connected to **LGSVL** bridge.

Then launch the **channel_extraction** module as following: 

```
pip install zmq
 ./bazelbin/cyber/python/cyber_py3/apollo_channel_extraction/channels_data_extraction/channels_extraction
```

Till now, the launch process of **Apollo** is finished

**Run SORA_SVL**

1. Since currently Official lgsvl is sunset now, everything about simulaor has been shifted to an open source local cloud built for **SVL** simulator.  Since we have already build and bundled the simulator in the **apollo/builds** folder, you should be able to launch the local **LGSVL** by just RUN:

   ``cd SORA-SVL``

   `` docker-compose up --build -d``

If you need to install the **SORA_SVL** Project by yourself, you need to follow the github of [**SORA-SVL** ](https://github.com/YuqiHuai/SORA-SVL)

2. Click the *simulator* icon, and you should be able to launch the simulator![Screenshot from 2022-09-04 06-16-17](assets/Screenshot from 2022-09-04 06-16-17.png)

3. Simply click the **OPEN BROWSER** button, find **Simulations** on the left hand and click **API Only**, then you are all set. 
4. ![Screenshot from 2022-09-04 06-22-47](assets/Screenshot from 2022-09-04 06-22-47.png)





***Autoware + CARLA preparation***

Autoware installation

We have already provided an image in docker repo, simply just pull:

``` docker pull jojodurrr/carla-autoware:latest ```

CARLA installation

There is no need to build Carla from the source unless you need

**requirements.**  Two Python modules: [Pygame](https://pypi.org/project/pygame/) to create graphics directly with Python, and [Numpy](https://pypi.org/project/numpy/) for great calculus.  

To install both modules using [pip](https://pip.pypa.io/en/stable/installing/), run the following commands. 

```  pip install --user pygame numpy```

Find CARLA 0.9.10 from [CARLA 0.9.10](https://github.com/carla-simulator/carla/releases/tag/0.9.10) and download it into *Home* directory                   

To use additional maps such as Town06, Town07 and Town10, download the corresponding [AdditionalMaps_0.9.10.tar.gz](https://carla-releases.s3.eu-west-3.amazonaws.com/Linux/AdditionalMaps_0.9.10.tar.gz)  and move the package to the *import* folder, and run the following script to extract them:

```  cd ~/carla && ./ImportAssets.sh```

To run CARLA:

```  ./CarlaUE4.sh```

To configure the window size and video quality:

``` ./CarlaUE4.sh -quality-level=Epic -world-port=2000 -resx=800 -resy=600```

# RUN DivX

Apollo + LGSVL

Download our repo from [google drive](https://drive.google.com/file/d/1vSSaJWqZxppRGfyiBr9tOavVsH0UrbRv/view)

```
cd DivX/DivX_test
```

**To test our DivX on Apollo-LGSVL, run**

````` python3 dynafuzzer_experiment.py --map_path="../apollo_hd_map/SanFrancisco/sim_map.txt"```

**To test our DivX on Autoware-CARLA, run**

``` git clone https://github.com/carla-simulator/carla-autoware.git ```

In *run.sh* file, Change carla-autoware:latest > jojodurrr/carla-autoware:latest, then run:

``` ./run.sh```

``` python3 -m pip install -r requirement```

We provide a script to run CARLA in **headless** mode and auto-start it when the simulation is down. Simply run

``` DISPPLAY=0```

``` python3 auto_detect_carla_down.py ```

``` python3 dynafuzzer_experiment.py --map_type="carla" --map_path="../apollo_hd_map/Carla_Town07/sim_map.txt"```

If everything works fine, you should be able to see

![Screen Shot 2023-04-07 at 1.22.06 AM](Screen Shot 2023-04-07 at 1.22.06 AM.png)