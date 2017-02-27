本页面涉及的**gem5_utils**脚本暂只支持**ALPHA**体系结构下**PARSEC2.1**的**全系统模拟**实验，如需模拟其他体系结构或自己编写或选择的目标程序，请先根据gem5官方文档自行研究并对脚本进行适当修改，或可先用上述配置进行实验。

无论是在虚拟机还是真实机器上进行本实验，请保证本实验过程中系统中至少有**2G空闲内存**可用。

在实验过程中，如碰到任何自己无法解决的问题可通过邮件或其他方式与任课教师联系：**min.cai.china@bjut.edu.cn**，祝一切顺利!

### 目录层次说明
按以下步骤，实验环境搭建完毕后，最终目录层次如下：

`gem5` - gem5模拟器本身

`gem5_utils` - gem5模拟器实验实用脚本

`gem5_extras` - 用于运行基于gem5的全系统模拟实验所需的文件

### 实验环境搭建
推荐实验环境为**Ubuntu Linux 15.10(64位)桌面版**。注意，其他Linux发行版未做测试，因Linux平台上各个软件包各个版本之间的相互兼容性非常难以把控，如用户熟悉Linux发行版的差异及相关操作，可尝试用其他发行版，否则最好以推荐的配置为准。以下以上述推荐的Ubuntu 15.10为例，对实验细节进行说明。

安装好操作系统后，修改`/etc/apt/sources.list`的内容为:

```
deb http://mirrors.aliyun.com/ubuntu/ wily main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ wily-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ wily-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ wily-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ wily-backports main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ wily main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ wily-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ wily-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ wily-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ wily-backports main restricted universe multiverse
```
 
联网进行源的更新，执行以下命令以便获取和安装运行**gem5**所需软件包。

```
sudo apt-get update && sudo apt-get dist-upgrade
```

执行以下命令，安装运行**gem5**模拟器及**gem5_utils**工具所需的软件包。

```
sudo apt-get install git mercurial scons build-essential vim geany swig zlib1g-dev libgoogle-perftools-dev protobuf-compiler libprotobuf-dev m4 python-dev python3-pip python3-lxml python3-pydot python3-matplotlib python3-pandas python3-seaborn
```

修改**PyPI**(Python Package Index, Python包索引)镜像，将`~/.pip/pip.conf`的内容修改为：

```ini
[global]
index-url = https://pypi.mirrors.ustc.edu.cn/simple
```

执行以下命令，安装运行**gem5_utils**工具所需的Python3软件包。

```
pip3 install objectpath yattag pytz
```

执行以下命令，以从[gem5的github项目网站](https://github.com/gem5/gem5)下载最新版本的**gem5**模拟器源码。

```
git clone https://github.com/gem5/gem5.git --verbose
```

执行以下命令，以从[mcai的github项目网站](https://github.com/mcai/gem5_utils)下载最新版本的**gem5_utils**实用工具源码。

```
git clone https://github.com/mcai/gem5_utils.git --verbose
```

以下提供两种方法下载支持本课程gem5实验用的**硬盘镜像文件和其他必要文件**。

**方法1**：从[gem5官网](http://www.gem5.org/)和[cart的parsec-m5网站](http://www.cs.utexas.edu/~cart/parsec_m5/)分别下载以下文件并解压。

```
mkdir -p gem5_extras; cd gem5_extras
curl http://www.gem5.org/dist/current/m5_system_2.0b3.tar.bz2 | tar -xj
mv m5_system_2.0b3 system
cd system/binaries
wget http://www.cs.utexas.edu/~parsec_m5/tsb_osfpal
wget http://www.cs.utexas.edu/~parsec_m5/vmlinux_2.6.27-gcc_4.3.4
cd ../disks
curl http://www.cs.utexas.edu/~parsec_m5/linux-parsec-2-1-m5-with-test-inputs.img.bz2 | bzip2 > linux-parsec-2-1-m5-with-test-inputs.img
```

**方法2**：下载以下文件: [gem5_extras.tar.bz2](https://github.com/mcai/public_ftp/raw/master/gem5_extras.tar.bz2)并解压。该网站有月流量限制，请同学们不要同一人多次下载，可下完线下互相传递，谢谢：

```
wget https://github.com/mcai/public_ftp/raw/master/gem5_extras.tar.bz2
```

通过上述两种方法中的任何一种完成下载解压操作后，请核对生成的`gem5_extras`目录层次如下所示：

```
system
├── binaries
│   ├── console
│   ├── tsb_osfpal
│   ├── vmlinux_2.6.27-gcc_4.3.4
└── disks
    ├── linux-bigswap2.img
    ├── linux-parsec-2-1-m5-with-test-inputs.img
```

### 实验过程与结果重现

**实验时间可能会比较长(单个实验耗时大约几小时，时间不等，其中facesim程序模拟时间特别长，至少一天以上，在课程实验中可以不予考虑。)，如果实验过程中没有报错，请耐心等待结束**。

进入`gem5`目录，需要对**gem5**模拟器源码打补丁以满足本次实验条件。

```
patch -p0 < ../gem5_utils/two_phase_simulation.patch
```

进入`gem5_utils`目录，编译**gem5**模拟器:

```
./compile_gem5_alpha.sh
```

运行统计gem5实验用法举例。请熟悉以下两脚本内容后，根据需要自行修改脚本内容，然后再运行实验。

进入`gem5_utils`目录，运行**gem5**模拟器：

```
./run_gem5_alpha_parsec2.1.py
```

实验结束后，实验结果会存放在`gem5_utils/results`目录下。

进入`gem5_utils`目录，汇总上述`gem5_utils/results`目录中的实验结果并生成图表文件：

```
./analyze_gem5_alpha_parsec2.1_results.py
```

上述脚本生成的实验结果图表文件同样会存放在`gem5_utils/results`目录下。