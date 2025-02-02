# **Linux下从源码编译**

## 环境准备

* **Linux 版本 (64 bit)**
    * **CentOS 6 (不推荐，不提供编译出现问题时的官方支持)**
    * **CentOS 7 (GPU 版本支持CUDA 10.1/10.2/11.0/11.1/11.2)**
    * **Ubuntu 14.04 (不推荐，不提供编译出现问题时的官方支持)**
    * **Ubuntu 16.04 (GPU 版本支持 CUDA 10.1/10.2/11.0/11.1/11.2)**
    * **Ubuntu 18.04 (GPU 版本支持 CUDA 10.1/10.2/11.0/11.1/11.2)**
* **Python 版本 3.6/3.7/3.8/3.9 (64 bit)**

## 选择CPU/GPU

* 如果您的计算机没有 NVIDIA® GPU，请安装CPU版本的PaddlePaddle

* 如果您的计算机有NVIDIA® GPU，请确保满足以下条件以编译GPU版PaddlePaddle

    * **CUDA 工具包10.1/10.2配合cuDNN 7 (cuDNN版本>=7.6.5, 如需多卡支持，需配合NCCL2.7及更高)**
    * **CUDA 工具包11.0配合cuDNN v8.0.4(如需多卡支持，需配合NCCL2.7及更高)**
    * **CUDA 工具包11.1配合cuDNN v8.1.1(如需多卡支持，需配合NCCL2.7及更高)**
    * **CUDA 工具包11.2配合cuDNN v8.1.1(如需多卡支持，需配合NCCL2.7及更高)**
    * **GPU运算能力超过3.5的硬件设备**

        您可参考NVIDIA官方文档了解CUDA和CUDNN的安装流程和配置方法，请见[CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/)，[cuDNN](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/)


## 安装步骤

在Linux的系统下有2种编译方式，推荐使用Docker编译。
Docker环境中已预装好编译Paddle需要的各种依赖，相较本机编译环境更简单。

* [使用Docker编译](#compile_from_docker)（不提供在CentOS 6下编译中遇到问题的支持）
* [本机编译](#compile_from_host)（不提供在CentOS 6下编译中遇到问题的支持）

<a name="ct_docker"></a>
### <span id="compile_from_docker">使用Docker编译</span>

[Docker](https://docs.docker.com/install/)是一个开源的应用容器引擎。使用Docker，既可以将PaddlePaddle的安装&使用与系统环境隔离，也可以与主机共享GPU、网络等资源

使用Docker编译PaddlePaddle，您需要：

- 在本地主机上[安装Docker](https://docs.docker.com/engine/install/)

- 如需在Linux开启GPU支持，请[安装nvidia-docker](https://github.com/NVIDIA/nvidia-docker)

请您按照以下步骤安装：

#### 1. 请首先选择您希望储存PaddlePaddle的路径，然后在该路径下使用以下命令将PaddlePaddle的源码从github克隆到本地当前目录下名为Paddle的文件夹中：

```
git clone https://github.com/PaddlePaddle/Paddle.git
```

#### 2. 进入Paddle目录下：

```
cd Paddle
```

#### 3. 拉取PaddlePaddle镜像

对于国内用户，因为网络问题下载docker比较慢时，可使用百度提供的镜像：

* CPU版的PaddlePaddle：
    ```
    docker pull registry.baidubce.com/paddlepaddle/paddle:latest-dev
    ```

* GPU版的PaddlePaddle：
    ```
    nvidia-docker pull registry.baidubce.com/paddlepaddle/paddle:latest-gpu-cuda10.2-cudnn7-dev
    ```

如果您的机器不在中国大陆地区，可以直接从DockerHub拉取镜像：

* CPU版的PaddlePaddle：
    ```
    docker pull paddlepaddle/paddle:latest-dev
    ```

* GPU版的PaddlePaddle：
    ```
    nvidia-docker pull paddlepaddle/paddle:latest-gpu-cuda10.2-cudnn7-dev
    ```

上例中，`latest-gpu-cuda10.2-cudnn7-dev` 仅作示意用，表示安装GPU版的镜像。如果您还想安装其他cuda/cudnn版本的镜像，可以将其替换成`latest-dev-cuda11.2-cudnn8-gcc82`、`latest-gpu-cuda10.1-cudnn7-gcc82-dev`、`latest-gpu-cuda10.1-cudnn7-gcc54-dev`等。
您可以访问[DockerHub](https://hub.docker.com/r/paddlepaddle/paddle/tags/)获取与您机器适配的镜像。


#### 4. 创建并进入已配置好编译环境的Docker容器：

* 编译CPU版本的PaddlePaddle：

    ```
    docker run --name paddle-test -v $PWD:/paddle --network=host -it registry.baidubce.com/paddlepaddle/paddle:latest-dev /bin/bash
    ```

    - `--name paddle-test`：为您创建的Docker容器命名为paddle-test;

    - `-v $PWD:/paddle`： 将当前目录挂载到Docker容器中的/paddle目录下（Linux中PWD变量会展开为当前路径的[绝对路径](https://baike.baidu.com/item/绝对路径/481185));

    - `-it`： 与宿主机保持交互状态;

    - `registry.baidubce.com/paddlepaddle/paddle:latest-dev`：使用名为`registry.baidubce.com/paddlepaddle/paddle:latest-dev`的镜像创建Docker容器，/bin/bash 进入容器后启动/bin/bash命令。


* 编译GPU版本的PaddlePaddle：

    ```
    nvidia-docker run --name paddle-test -v $PWD:/paddle --network=host -it registry.baidubce.com/paddlepaddle/paddle:latest-gpu-cuda10.2-cudnn7-dev /bin/bash
    ```

    - `--name paddle-test`：为您创建的Docker容器命名为paddle-test;

    - `-v $PWD:/paddle`： 将当前目录挂载到Docker容器中的/paddle目录下（Linux中PWD变量会展开为当前路径的[绝对路径](https://baike.baidu.com/item/绝对路径/481185));

    - `-it`： 与宿主机保持交互状态;

    - `registry.baidubce.com/paddlepaddle/paddle:latest-gpu-cuda10.2-cudnn7-dev`：使用名为`registry.baidubce.com/paddlepaddle/paddle:latest-gpu-cuda10.2-cudnn7-dev`的镜像创建Docker容器，/bin/bash 进入容器后启动/bin/bash命令。


注意：
请确保至少为docker分配4g以上的内存，否则编译过程可能因内存不足导致失败。

#### 5. 进入Docker后进入paddle目录下：

```
cd /paddle
```

#### 6. 切换到develop版本进行编译：

```
git checkout develop
```

注意：python3.6、python3.7版本从release/1.2分支开始支持, python3.8版本从release/1.8分支开始支持, python3.9版本从release/2.1分支开始支持

#### 7. 创建并进入/paddle/build路径下：

```
mkdir -p /paddle/build && cd /paddle/build
```

#### 8. 使用以下命令安装相关依赖：

- 安装protobuf。

```
pip3.7 install protobuf
```

注意：以上用Python3.7命令来举例，如您的Python版本为3.6/3.8/3.9，请将上述命令中的pip3.7改成pip3.6/pip3.8/pip3.9

- 安装patchelf，PatchELF 是一个小而实用的程序，用于修改ELF可执行文件的动态链接器和RPATH。

```
apt install patchelf
```


#### 9. 执行cmake：

* 对于需要编译**CPU版本PaddlePaddle**的用户：
    ```
    cmake .. -DPY_VERSION=3.7 -DWITH_GPU=OFF -DWITH_TESTING=OFF -DCMAKE_BUILD_TYPE=Release
    ```

* 对于需要编译**GPU版本PaddlePaddle**的用户：
    ```
    cmake .. -DPY_VERSION=3.7 -DWITH_GPU=ON -DWITH_TESTING=OFF -DCMAKE_BUILD_TYPE=Release
    ```
- 具体编译选项含义请参见[编译选项表](https://www.paddlepaddle.org.cn/documentation/docs/zh/develop/install/Tables.html#Compile)

- 请注意修改参数`-DPY_VERSION`为您希望编译使用的python版本,  例如`-DPY_VERSION=3.7`表示python版本为3.7

- 我们目前不支持CentOS 6下使用Docker编译GPU版本的PaddlePaddle

#### 10. 执行编译：

使用多核编译

```
make -j$(nproc)
```

注意：
编译过程中需要从github上下载依赖，请确保您的编译环境能正常从github下载代码。

#### 11. 编译成功后进入`/paddle/build/python/dist`目录下找到生成的`.whl`包：

```
cd /paddle/build/python/dist
```

#### 12. 在当前机器或目标机器安装编译好的`.whl`包：


For Python3:  
```
pip3.7 install -U [whl包的名字]
```

注意：
以上用Python3.7命令来举例，如您的Python版本为3.6/3.8/3.9，请将上述命令中的pip3.7改成pip3.6/pip3.8/pip3.9。

#### 恭喜，至此您已完成PaddlePaddle的编译安装。您只需要进入Docker容器后运行PaddlePaddle，即可开始使用。更多Docker使用请参见[Docker官方文档](https://docs.docker.com)


<a name="ct_source"></a>
### <span id="compile_from_host">本机编译</span>

#### 1. 检查您的计算机和操作系统是否符合我们支持的编译标准：

```
uname -m && cat /etc/*release
```

#### 2. 更新系统源

* Centos 环境

    更新`yum`的源：

    ```
    yum update
    ```

    并添加必要的yum源：

    ```
    yum install -y epel-release
    ```

* Ubuntu 环境

    更新`apt`的源：

    ```
    apt update
    ```


#### 3. 安装NCCL（可选）

* 如果您需要使用GPU多卡，请确保您已经正确安装nccl2，或者按照以下指令安装nccl2（这里提供的是CUDA10.2，cuDNN7下nccl2的安装指令，更多版本的安装信息请参考NVIDIA[官方网站](https://developer.nvidia.com/nccl)）:

    * **Centos 系统可以参考以下命令**

        ```
        wget http://developer.download.nvidia.com/compute/machine-learning/repos/rhel7/x86_64/nvidia-machine-learning-repo-rhel7-1.0.0-1.x86_64.rpm
        ```

        ```
        rpm -i nvidia-machine-learning-repo-rhel7-1.0.0-1.x86_64.rpm
        ```

        ```
        yum update -y
        ```

        ```
        yum install -y libnccl-2.7.8-1+cuda10.2 libnccl-devel-2.7.8-1+cuda10.2 libnccl-static-2.7.8-1+cuda10.2
        ```

    * **Ubuntu 系统可以参考以下命令**

        ```
        wget https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
        ```

        ```
        dpkg -i nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
        ```

        ```
        sudo apt install -y libnccl2=2.7.8-1+cuda10.2 libnccl-dev=2.7.8-1+cuda10.2
        ```

#### 4. 安装必要的工具

* Centos 环境

    `bzip2`以及`make`：

    ```
    yum install -y bzip2
    ```

    ```
    yum install -y make
    ```

    cmake 需要3.15以上，建议使用3.16.0:

    ```
    wget -q https://cmake.org/files/v3.16/cmake-3.16.0-Linux-x86_64.tar.gz
    ```

    ```
    tar -zxvf cmake-3.16.0-Linux-x86_64.tar.gz
    ```

    ```
    rm cmake-3.16.0-Linux-x86_64.tar.gz
    ```

    ```
    PATH=/home/cmake-3.16.0-Linux-x86_64/bin:$PATH
    ```

    gcc 需要5.4以上，建议使用8.2.0:

    ```
    wget -q https://paddle-docker-tar.bj.bcebos.com/home/users/tianshuo/bce-python-sdk-0.8.27/gcc-8.2.0.tar.xz && \
    tar -xvf gcc-8.2.0.tar.xz && \
    cd gcc-8.2.0 && \
    sed -i 's#ftp://gcc.gnu.org/pub/gcc/infrastructure/#https://paddle-ci.gz.bcebos.com/#g' ./contrib/download_prerequisites && \
    unset LIBRARY_PATH CPATH C_INCLUDE_PATH PKG_CONFIG_PATH CPLUS_INCLUDE_PATH INCLUDE && \
    ./contrib/download_prerequisites && \
    cd .. && mkdir temp_gcc82 && cd temp_gcc82 && \
    ../gcc-8.2.0/configure --prefix=/usr/local/gcc-8.2 --enable-threads=posix --disable-checking --disable-multilib && \
    make -j8 && make install
    ```

* Ubuntu 环境

    `bzip2`以及`make`：

    ```
    apt install -y bzip2
    ```
    ```
    apt install -y make
    ```

    cmake 需要3.15以上，建议使用3.16.0:

    ```
    wget -q https://cmake.org/files/v3.16/cmake-3.16.0-Linux-x86_64.tar.gz
    ```

    ```
    tar -zxvf cmake-3.16.0-Linux-x86_64.tar.gz
    ```

    ```
    rm cmake-3.16.0-Linux-x86_64.tar.gz
    ```

    ```
    PATH=/home/cmake-3.16.0-Linux-x86_64/bin:$PATH
    ```

    gcc 需要5.4以上，建议使用8.2.0:

    ```
    wget -q https://paddle-docker-tar.bj.bcebos.com/home/users/tianshuo/bce-python-sdk-0.8.27/gcc-8.2.0.tar.xz && \
    tar -xvf gcc-8.2.0.tar.xz && \
    cd gcc-8.2.0 && \
    sed -i 's#ftp://gcc.gnu.org/pub/gcc/infrastructure/#https://paddle-ci.gz.bcebos.com/#g' ./contrib/download_prerequisites && \
    unset LIBRARY_PATH CPATH C_INCLUDE_PATH PKG_CONFIG_PATH CPLUS_INCLUDE_PATH INCLUDE && \
    ./contrib/download_prerequisites && \
    cd .. && mkdir temp_gcc82 && cd temp_gcc82 && \
    ../gcc-8.2.0/configure --prefix=/usr/local/gcc-8.2 --enable-threads=posix --disable-checking --disable-multilib && \
    make -j8 && make install
    ```

#### 5. 我们支持使用virtualenv进行编译安装，首先请使用以下命令创建一个名为`paddle-venv`的虚环境：

* a. 安装Python-dev:

    (请参照Python官方流程安装）


* b. 安装pip:

    (请参照Python官方流程安装, 并保证拥有20.2.2及以上的pip3版本，请注意，python3.6及以上版本环境下，pip3并不一定对应python版本，如python3.7下默认只有pip3.7）

* c.（Only For Python3）设置Python3相关的环境变量，这里以python3.7版本示例，请替换成您使用的版本（3.6、3.8、3.9）：

    1. 首先使用
        ```
        find `dirname $(dirname $(which python3))` -name "libpython3.so"
        ```
        找到Python lib的路径，如果是3.6、3.7、3.8、3.9，请将`python3`改成`python3.6`、`python3.7`、`python3.8`、`python3.9`，然后将下面[python-lib-path]替换为找到文件路径

    2. 设置PYTHON_LIBRARIES：
        ```
        export PYTHON_LIBRARY=[python-lib-path]
        ```

    3. 其次使用
        ```
        find `dirname $(dirname $(which python3))`/include -name "python3.7m"
        ```
        找到Python Include的路径，请注意python版本，然后将下面[python-include-path]替换为找到文件路径

    4. 设置PYTHON_INCLUDE_DIR:
        ```
        export PYTHON_INCLUDE_DIRS=[python-include-path]
        ```

    5. 设置系统环境变量路径：
        ```
        export PATH=[python-lib-path]:$PATH
        ```
        （这里将[python-lib-path]的最后两级目录替换为/bin/)

* d. 安装虚环境`virtualenv`以及`virtualenvwrapper`并创建名为`paddle-venv`的虚环境：(请注意对应python版本的pip3的命令，如pip3.6、pip3.7、pip3.8、pip3.9)

    1. 安装`virtualenv`
        ```
        pip install virtualenv
        ```
        或
        ```
        pip3 install virtualenv
        ```
    2. 安装`virtualenvwrapper`
        ```
        pip install virtualenvwrapper
        ```
        或
        ```
        pip3 install virtualenvwrapper
        ```
    3.  找到`virtualenvwrapper.sh`：
        ```
        find / -name virtualenvwrapper.sh
        ```
        （请找到对应Python版本的`virtualenvwrapper.sh`）
    4.  查看`virtualenvwrapper.sh`中的安装方法：
        ```
        cat vitualenvwrapper.sh
        ```
        该shell文件中描述了步骤及命令
    5.  按照`virtualenvwrapper.sh`中的描述，安装`virtualwrapper`
    6.  设置VIRTUALENVWRAPPER_PYTHON：
        ```
        export VIRTUALENVWRAPPER_PYTHON=[python-lib-path]:$PATH
        ```
        （这里将[python-lib-path]的最后两级目录替换为/bin/)
    7.  创建名为`paddle-venv`的虚环境：
        ```
        mkvirtualenv paddle-venv
        ```

#### 6. 进入虚环境：

```
workon paddle-venv
```

#### 7. **执行编译前**请您确认在虚环境中安装有[编译依赖表](/documentation/docs/zh/install/Tables.html#third_party)中提到的相关依赖：

* 这里特别提供`patchELF`的安装方法，其他的依赖可以使用`yum install`或者`pip install`/`pip3 install` 后跟依赖名称和版本安装:

    ```
    yum install patchelf
    ```
    > 不能使用yum安装的用户请参见patchElF github[官方文档](https://gist.github.com/ruario/80fefd174b3395d34c14)

#### 8. 将PaddlePaddle的源码clone在当下目录下的Paddle的文件夹中，并进入Padde目录下：

```
git clone https://github.com/PaddlePaddle/Paddle.git
```

```
cd Paddle
```

#### 9. 切换到develop分支进行编译：

```
git checkout develop
```

#### 10. 并且请创建并进入一个叫build的目录下：

```
mkdir build && cd build
```

#### 11. 执行cmake：

>具体编译选项含义请参见[编译选项表](https://www.paddlepaddle.org.cn/documentation/docs/zh/develop/install/Tables.html#Compile)

*  对于需要编译**CPU版本PaddlePaddle**的用户：


    ```
    cmake .. -DPY_VERSION=3.7 -DPYTHON_INCLUDE_DIR=${PYTHON_INCLUDE_DIRS} \
    -DPYTHON_LIBRARY=${PYTHON_LIBRARY} -DWITH_GPU=OFF -DWITH_TESTING=OFF -DCMAKE_BUILD_TYPE=Release
    ```

    > 如果遇到`Could NOT find PROTOBUF (missing:  PROTOBUF_LIBRARY PROTOBUF_INCLUDE_DIR)`可以重新执行一次cmake指令。
    > 请注意PY_VERSION参数更换为您需要的python版本


* 对于需要编译**GPU版本PaddlePaddle**的用户：(**仅支持CentOS7（CUDA11.2/CUDA11.0/CUDA10.2/CUDA10.1)**)

    1. 请确保您已经正确安装nccl2，或者按照以下指令安装nccl2（这里提供的是CUDA10.2，cuDNN7下nccl2的安装指令，更多版本的安装信息请参考NVIDIA[官方网站](https://developer.nvidia.com/nccl)）:

    * **Centos 系统可以参考以下命令**

        ```
        wget http://developer.download.nvidia.com/compute/machine-learning/repos/rhel7/x86_64/nvidia-machine-learning-repo-rhel7-1.0.0-1.x86_64.rpm
        ```

        ```
        rpm -i nvidia-machine-learning-repo-rhel7-1.0.0-1.x86_64.rpm
        ```

        ```
        yum install -y libnccl-2.7.8-1+cuda10.2 libnccl-devel-2.7.8-1+cuda10.2 libnccl-static-2.7.8-1+cuda10.2
        ```

    * **Ubuntu 系统可以参考以下命令**

        ```
        wget https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
        ```

        ```
        dpkg -i nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
        ```

        ```
        sudo apt install -y libnccl2=2.7.8-1+cuda10.2 libnccl-dev=2.7.8-1+cuda10.2
        ```

    2. 如果您已经正确安装了`nccl2`，就可以开始cmake了：(*For Python3: 请给PY_VERSION参数配置正确的python版本*)

        ```
        cmake .. -DPYTHON_EXECUTABLE:FILEPATH=[您可执行的Python3的路径] -DPYTHON_INCLUDE_DIR:PATH=[之前的PYTHON_INCLUDE_DIRS] -DPYTHON_LIBRARY:FILEPATH=[之前的PYTHON_LIBRARY] -DWITH_GPU=ON -DWITH_TESTING=OFF -DCMAKE_BUILD_TYPE=Release
        ```

注意：以上涉及Python3的命令，用Python3.7来举例，如您的Python版本为3.6/3.8/3.9，请将上述命令中的Python3.7改成Python3.6/Python3.8/Python3.9




#### 12. 使用以下命令来编译：

```
make -j$(nproc)
```

> 使用多核编译

> 如果编译过程中显示“Too many open files”错误时，请使用指令 ulimit -n 8192 来增大当前进程允许打开的文件数，一般来说8192可以保证编译完成。

#### 13. 编译成功后进入`/paddle/build/python/dist`目录下找到生成的`.whl`包：
```
cd /paddle/build/python/dist
```

#### 14. 在当前机器或目标机器安装编译好的`.whl`包：

```
pip install -U（whl包的名字）
```
或
```
pip3 install -U（whl包的名字）
```

#### 恭喜，至此您已完成PaddlePaddle的编译安装

## **验证安装**
安装完成后您可以使用 `python` 或 `python3` 进入python解释器，输入

```
import paddle
```

再输入

```
paddle.utils.run_check()
```

如果出现`PaddlePaddle is installed successfully!`，说明您已成功安装。

## **如何卸载**
请使用以下命令卸载PaddlePaddle：

* **CPU版本的PaddlePaddle**:
    ```
    pip uninstall paddlepaddle
    ```
    或
    ```
    pip3 uninstall paddlepaddle
    ```

* **GPU版本的PaddlePaddle**:
    ```
    pip uninstall paddlepaddle-gpu
    ```
    或
    ```
    pip3 uninstall paddlepaddle-gpu
    ```

使用Docker安装PaddlePaddle的用户，请进入包含PaddlePaddle的容器中使用上述命令，注意使用对应版本的pip
