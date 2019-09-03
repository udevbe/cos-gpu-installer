Note: This fork adds the missing X nvidia drivers. The Nvidia drivers are available in: `/var/lib/nvidia/lib64/xorg/` on the host machine. 

To make X work in a container, the following prerequisites are required:

- The container must be privileged
- The following devices must be made available to the container:

    - `/dev/nvidia0`
    - `/dev/nvidiactl`
    - `/dev/nvidia-uvm`
    - `/dev/nvidia-uvm-tools`
 - The Nvidia driver must be made available to the container ie `/var/lib/nvidia/lib64/` to `/usr/local/nvidia/lib64/`
 - The Nvidia Xorg module path must be made known to the X server in xorg.conf:
 ```
Section "Files"
    ModulePath      "/usr/local/nvidia/lib64/xorg/modules"
    ModulePath      "/usr/lib/xorg/modules"
EndSection
 ```
 - The X server must be started with `-seat 1` to trick X into not trying to open a virtual terminal.

# GPU Driver Installer containers for Container-Optimized OS from Google

Note: This is not an official Google product.

This repository contains scripts to build Docker containers that can be used to
download, compile and install GPU drivers on
[Container-Optimized OS](https://cloud.google.com/container-optimized-os/) images.

## How to use

Example command:
``` shell
gcloud compute instances create $USER-cos-gpu-test \
    --image-family cos-stable \
    --image-project cos-cloud \
    --accelerator=type=nvidia-tesla-k80 \
    --boot-disk-size=25GB \
    --maintenance-policy=TERMINATE \
    --metadata-from-file "cos-gpu-installer-env=scripts/gpu-installer-env,user-data=install-test-gpu.cfg,run-installer-script=scripts/run_installer.sh,run-cuda-test-script=scripts/run_cuda_test.sh"
```

The command above creates a GCE instance based on cos-stable image. Then it
installs GPU driver on the instance by running a container 'cos-gpu-installer'
which is implemented in this repository.

The GPU driver version and container image version are specified in
scripts/gpu-installer-env. You can edit the file if you want to install
GPU driver version or use container image other than the default.
