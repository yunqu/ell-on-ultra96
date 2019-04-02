# Embedded Learning Library on Ultra96

This file documents the 
[Embedded Learning Library (ELL)](https://microsoft.github.io/ELL/) from 
Microsoft research group. Since the original documentation for Raspberry Pi 3
is pretty complete, we will just do a few tweaks to port it to Ultra96.
We will use the PYNQ image v2.4 (Ubuntu 18.04-based) as the target.

## Install ELL on x86 Ubuntu Linux

Follow the [instructions](https://github.com/Microsoft/ELL/blob/master/INSTALL-Ubuntu.md)
to build the ELL step-by-step.

For SWIG installation, you may need to make sure
you have the path `/usr/bin/swig3.0`. The default installation will perhaps
only install `swig` under `/usr/local/bin`, so you may need to symlink it:

```
sudo ln -s /usr/local/bin/swig /usr/bin/swig3.0
```

For conda environment, you will probably need to use

```
conda activate py36
```

Instead of `source activate py36` in the instruction.

## Ultra96 Setup

We can use [the page to set up Raspberry Pi 3](https://microsoft.github.io/ELL/tutorials/Raspberry-Pi-setup/)
as a reference point. However, most of the steps can be ignored for
the PYNQ image. I am summarizing only the necessary steps here:

```
sudo apt-get update
sudo apt-get install -y cmake
sudo apt-get install -y libopenblas-dev
sudo apt-get install -y curl
sudo apt-get install -y libharfbuzz-dev 
sudo apt-get install -y libgtk2.0-dev libgtk-3-dev
sudo apt-get install -y libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev libdc1394-22-dev
sudo apt-get install -y libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install -y libxvidcore-dev libx264-dev
```

Verify this in your Jupyter notebook environment that OpenCV is working:


```python
import cv2
print(cv2.__version__)
```

## Compile on Board

We can go through [this page](https://microsoft.github.io/ELL/tutorials/Getting-started-with-image-classification-on-the-Raspberry-Pi/)
for deployment on the board. You can start the steps inside a local folder on 
your host machine (e.g. `/opt/builds/tutorial`).

After 'Download a pretrained model', you can skip 
the steps for compiling and running on your host machine, and go directly to 
the sections 'Compile the model for execution on the Raspberry Pi device'. 
However, before you do anything in this section,
there is a small fix for the compiler flag:

```
cd <ELL-root>
find ./ -type f -exec sed -i 's/-mtriple=armv7-linux-gnueabihf/-mtriple=aarch64-unknown-linux-gnu/gI' {} \;
```

This replace all the armv7 flags to the aarch64 flags. After this fix,
you can run the command `python <ELL-root>/tools/wrap/wrap.py --model_file model.ell --language python --target pi3`,
as instructed on that page.

When you are asked to copy the folder over onto your board, copy 
`/opt/builds/tutorial` to `/home/xilinx/jupyter_notebooks/tutorial` via Samba.
You can then build your Python module inside that folder on board.

If you can finish the 'Build the Python module on the Raspberry Pi' section,
you are in good shape! We will now switch to the Jupyter notebook
environment.

## Run in Jupyter Notebook

I have created a notebook for this. You can check it out. Since you have a 
Jupyter notebook environment, you do not need to use the `imshow()` from 
OpenCV. 

The folder `/home/xilinx/jupyter_notebooks/tutorial` should contain the 
following items.

* pi3 - copied from host machine
* tutorial.ipynb - self-written Jupyter notebook
* categories.txt - copied from host machine
* tutorial_helpers.py - copied from host machine
* model.ell - copied from host machine
