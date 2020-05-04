# Step by step installation of Ubuntu 18.04

1. Make sure you have backup from your data

2. Download ubuntu 18.04 LTS ISO file , desktop version

3. Create a bootable USB Disk with downloaded file

   1. Insert the USB disk

   2. Open the Start Disk Creator application in Ubuntu or in Etcher Mac

   3. Make Startup Disk

   4. Test your Bootable Disk by installing a utility called QEMU

      ````
      $ sudo apt-get install qemu
      $ sudo qemu-system-x86_64 -hda /dev/sdc1
      ````

      Your booting process is successfull if you see a virtual machine booting from your USB disk

4. Boot from USB Drive

5. Follow steps

   - You can check to update during installation and install 3rd party to save time after installation
   - You can check partitions and make sure you have at least 8 GB swap area
     mount point set to / on your ssd hard drive with swao area and 500 MB EIFT format
     and /mnt on your second hard drive

6. Eject USB drive and reboot

7. Install Essentials first

   ```
   sudo apt-get update
   sudo apt-get upgrade
   
   sudo apt-get install git
   
   sudo apt-get install vim
   
   #Status Bar: Indicator Multiload
   sudo apt install indicator-multiload
   
   # Typora
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
   sudo add-apt-repository 'deb https://typora.io ./linux/'
   sudo apt-get update
   sudo apt-get install typora
   
   # Chrome, GitKraken, Pycharm, Slack
   ```

8. Install conda
   Download Miniconda from [here](https://docs.conda.io/en/latest/miniconda.html)

   ```
   bash Miniconda3-latest-Linux-x86_64.sh
   
   # Test your installation in new terminal windows
   conda list 
   ```

9. Install nvidia driver

   1. Add the official Nvidia PPA to Ubuntu:
      `sudo add-apt-repository ppa:graphics-drivers/ppa`

   2. Update and Install Nvidia Drivers:
      (Make sure you select correct Nvidia Driver version)

      ```
      sudo apt update	
      sudo apt-get install nvidia-driver-440
      sudo reboot
      ```

      If it doesn't work, try this:

      ```
      sudo apt-get remove libdpkg-perl
      sudo apt-get install libdpkg-perl=1.19.0.5ubuntu2
      sudo apt-get install dpkg-dev
      sudo apt-get install dkms
      sudo apt-get install nvidia-dkms-440
      sudo apt-get install nvidia-driver-440
      sudo reboot
      ```

   3. Checking install status of NVIDIA packages:

      ```
      dpkg -l | grep nvidia
      nvidia-smi
      ```

   

9. Install CUDA 10:

   1. Follow instructions from [here](https://developer.nvidia.com/cuda-10.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=runfilelocal)
      Download the runfile instead of deb file. When running the run file, need to disable the driver installation. 

      ```
      Do you accept the previously read EULA? accept
      Install NVIDIA Accelerated Graphics Driver for linux? no
      Install the CUDA 10.0 Toolkit? yes
      Do you want to install a symbolic link at /usr/local/cuda? yes
      ...
      ```

      

   2. Post installation actions

      Environment setup, add these to `.bashrc` file

      ```
      export PATH=$PATH:/usr/local/cuda-10.0/bin
      export CUDADIR=/usr/local/cuda-10.0
      export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-10.0/lib6
      
      export PATH=$PATH:/usr/local/cuda/bin
      export CUDADIR=/usr/local/cuda
      export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64
      ```

      

10. install cudnn:

    ```
    # Install CuDNN 7 and NCCL 2
    wget https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
    
    sudo dpkg -i nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
    sudo apt update
    sudo apt install -y libcudnn7 libcudnn7-dev libnccl2 libc-ares-dev
    sudo apt autoremove
    sudo apt upgrade
    
    # Link libraries to standard locations
    sudo mkdir -p /usr/local/cuda-10.0/nccl/lib
    sudo ln -s /usr/lib/x86_64-linux-gnu/libnccl.so.2 /usr/local/cuda/nccl/lib/
    sudo ln -s /usr/lib/x86_64-linux-gnu/libcudnn.so.7 /usr/local/cuda-10.0/lib64/
    
    echo 'If everything worked fine, reboot now.'
    ```

11. Create a new conda env and test GPU on Tensorflow

    ```
    pip install tensorflow-gpu==1.15.2
    python
    >> import tensorflow as tf
    >> with tf.device('/gpu:0'):
          a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
          b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
          c = tf.matmul(a, b)
    >> with tf.Session() as sess:
        	print (sess.run(c))
    ```

    Congratulations if you can see the results without any problem.

13. Install Docker
    Simply follow instructions [here](https://docs.docker.com/engine/install/ubuntu/)

    ```
    $ sudo apt-get update
    
    $ sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common
        
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    
    $ sudo apt-key fingerprint 0EBFCD88
    
    $ sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
    
    $ sudo docker run hello-world
    
    ```

    Add the docker group if it doesn't already exist:

    ```
    $ sudo groupadd docker
    
    # Add the connected user to the docker group
    sudo gpasswd -a $USER docker
    ```

    Now, having the user logout then login again to test if you can use docker without sudo.
    `docker images`. Sometimes you need restart to see the result.
    
14. PubKey Authentication
    Permanently added the RSA host key for this IP address to the list of known hosts.

    ```
    $ ssh-keygen
    $ vim ~/.ssh/config
    ```

    Find instructions [here](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

    

15. Minikube

    ```
    $ sudo apt-get update
    $ sudo apt-get install apt-transport-https
    $ sudo apt-get upgrade
    
    $ sudo apt install virtualbox virtualbox-ext-pack
    
    $ wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    $ chmod +x minikube-linux-amd64
    $ sudo mv minikube-linux-amd64 /usr/local/bin/minikube
    
    $ minikube version
    ```

16. Install kubectl

    ```
    curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
    
    chmod +x ./kubectl
    
    sudo mv ./kubectl /usr/local/bin/kubectl
    
    kubectl version --client
    ```

17. Configure kubectl

    ```
    minikube start
    ```

    it will create the config file in home directory `~/.kube/config` that you can replace with your own config file.

    