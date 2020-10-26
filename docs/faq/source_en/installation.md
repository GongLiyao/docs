﻿# Installation

`Linux` `Windows` `Ascend` `GPU` `CPU` `Environment Preparation` `Basic` `Intermediate`

<!-- TOC -->

- [Installation](#Installation)
    - [Installing Using pip](#installing-using-pip)
    - [Source Code Compilation Installation](#source-code-compilation-installation)
    - [Uninstall](#uninstall)
    - [Environment Variables](#environment-variables)
    - [Verifying the Installation](#verifying-the-installation)

<!-- /TOC -->

<a href="https://gitee.com/mindspore/docs/blob/master/docs/faq/source_en/installation.md" target="_blank"><img src="./_static/logo_source.png"></a>

## Installing Using pip

Q: What can I do if an error message `cannot open shared object file:file such file or directory` is displayed when I install MindSpore of the GPU, CUDA 10.1, 0.5.0-beta, or Ubuntu-x86 version?

A: The error message indicates that the cuBLAS library is not found. Generally, the cause is that the cuBLAS library is not installed or is not added to the environment variable. Generally, cuBLAS is installed together with CUDA and the driver. After the installation, add the directory where cuBLAS is located to the `LD_LIBRARY_PATH` environment variable.

<br/>

Q: What should I do if an error message `SSL:CERTIFICATE_VERIFY_FATLED` is displayed when I use pip to install MindSpore?

A: Add the `--trusted-host=ms-release.obs.cn-north-4.myhuaweicloud.com` parameter to the pip installation command and try again.

<br/>

Q: Any specific requirements for Python version when pip install MindSpore?

A: MindSpore utilizes many of the new features in Python3.7+，therefore we recommend you add Python3.7.5 develop environment via `conda`.

<br/>

Q: What should I do when error `ProxyError(Cannot connect to proxy)` prompts during pip install?

A: It is generally a proxy configuration problem, you can using `export http_proxy={your_proxy}` on Ubuntu environment, and using `set http_proxy={your_proxy}` in cmd on Windows environment to config your proxy.

<br/>

Q: What should I do when error prompts during pip install?

A: Please execute `pip -V` to check if pip is linked to Python3.7+. If not, we recommend you
use `python3.7 -m pip install` instead of `pip install` command.

<br/>

Q: What should I do if I cannot find whl package for MindInsight or MindArmour on the installation page of MindSpore website?

A: You can download whl package from the official [MindSpore Website download page](https://www.mindspore.cn/versions) and manually install it via `pip install`.

## Source Code Compilation Installation

Q: A sample fails to be executed after I installed MindSpore 0.6.0 beta on Ascend 910 using Ubuntu_aarch64 and Python 3.7.5 and manually downloaded the .whl package of the corresponding version, compiled and installed GMP6.1.2, and installed other Python library dependencies. An error message is displayed, indicating that the .so file cannot be found. What can I do?

A: The `libdatatransfer.so` dynamic library is in the `fwkacllib/lib64` directory. Find the path of the library in the `/usr/local` directory, and then add the path to the `LD_LIBRARY_PATH` environment variable. After the settings take effect, execute the sample again.

<br/>

Q: What should I do if the compilation time of MindSpore source code takes too long or the process is constantly interrupted by errors?

A: MindSpore imports third party dependencies through submodule mechanism, among which `protobuf` v3.8.0 might not have the optimal or steady download speed, it is recommended that you perform package cache in advance.

<br/>

Q: How to change installation directory of the third party libraries?

A: The third party libraries will be installed in build/mindspore/.mslib, you can change the installation directory by setting the environment variable MSLIBS_CACHE_PATH, eg. `export MSLIBS_CACHE_PATH = ~/.mslib`.

<br/>

Q: What should I do if the software version required by MindSpore is not the same with the Ubuntu default software version?

A: At the moment some software might need manual upgrade. (**Note**: MindSpore requires Python3.7.5 and gcc7.3，the default version in Ubuntu 16.04 are Python3.5 and gcc5，whereas the one in Ubuntu 18.04 are Python3.7.3 and gcc7.4)

<br/>

Q: What should I do if there is a prompt `tclsh not found` when I compile MindSpore from source code?

A: Please install the software manually if there is any suggestion of certain `software not found`.

## Uninstall

Q: How to uninstall MindSpore?

A: Using `pip uninstall mindspore` to uninstall MindSpore.

## Environment Variables

Q: Some frequently-used environment settings need to be reset in the newly started terminal window, which is easy to be forgotten, What should I do?

A: You can write the frequently-used environment settings to `~/.bash_profile` or `~/.bashrc` so that the settings can take effect immediately when you start a new terminal window.

## Verifying the Installation

Q: After MindSpore is installed on a CPU of a PC, an error message `the pointer[session] is null` is displayed during code verification. The specific code is as follows. How do I verify whether MindSpore is successfully installed?

```python
import numpy as np
from mindspore import Tensor
import mindspore.ops as ops
import mindspore.context as context

context.set_context(device_target="Ascend")
x = Tensor(np.ones([1,3,3,4]).astype(np.float32))
y = Tensor(np.ones([1,3,3,4]).astype(np.float32))
print(ops.tensor_add(x,y))
```

A: After MindSpore is installed on a CPU hardware platform, run the `python -c'import mindspore'` command to check whether MindSpore is successfully installed. If no error message such as `No module named'mindspore'` is displayed, MindSpore is successfully installed. The verification code is used only to verify whether a Ascend platform is successfully installed.