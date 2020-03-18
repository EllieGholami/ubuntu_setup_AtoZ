# Install-nvidia-cuda-cudnn

Fresh install nvidia driver and CUDA on Ubuntu:

1. I recommend Ubuntu 16 as it is compatible with CUDA9

2. Add the official Nvidia PPA to Ubuntu: 

   `sudo add-apt-repository ppa:graphics-drivers/ppa`

3. Update and Install Nvidia Drivers:

    `sudo apt update` 
    
   `sudo apt install nvidia-410` 
   
    `sudo reboot`

4. Make sure you select correct Nvidia Driver version, I use 410.129

5. Checking install status of NVIDIA packages: 

   `dpkg -l | grep nvidia`

   `nvidia-smi`

6. Install CUDA 9: 

   Follow instructions from [here](https://developer.nvidia.com/cuda-90-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1604)

7. Verify cuda installation:

    `$ locate cupti | grep "libcupti.so." | tail -n1 | sed -r 's/^.*\.so\.//'`

8. Install cudnn:

   Download package from [here](https://developer.nvidia.com/rdp/cudnn-download)
   
   You will need to register
   
   `sudo dpkg -i libcudnn7_7.0.5.15–1+cuda9.0_amd64.deb`

   Navigate to your <cudnnpath> directory containing the cuDNN Tar file.

   `sudo cp cuda/include/cudnn.h /usr/local/cuda/include`

   `sudo cp cuda/include/cudnn.h /usr/local/cuda/include`

   `sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64`

   `sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*`

9. Configure the CUDA and cuDNN library paths:

   Put the following line in the end or your `.bashrc` file

   `export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64"`
   
   OR

   `export PATH="PATH=/usr/local/cuda-9.0/bin:$PATH"`

   `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-9.0/lib64/`

10. Verify all:

    ```
    $ nvidia-smi | grep "Driver Version" | awk '{print $6}'
    410.129
    $  nvcc --version | grep "release" | awk '{print $6}' | cut -c2-
    7.5.17
    $ locate cupti | grep "libcupti.so." | tail -n1 | sed -r 's/^.*\.so\.//' 
    9.0.176
    $ locate cudnn | grep "libcudnn.so." | tail -n1 | sed -r 's/^.*\.so\.//' 
    7.2.1
    ```