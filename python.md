# python操作
1. 查看当前运行python环境
```python
import sys
python_path = sys.executable
print(python_path)
```
# 安装anaconda
主要参考罗通笔记
参考网址1：https://blog.csdn.net/KIK9973/article/details/118772450
参考网址2：https://blog.csdn.net/weixin_44330367/article/details/131826923

稳定版本有：Anaconda3-5.2.0(python 3.6)
本次安装：anaconda版本Anaconda3-5.3.0-Linux-x86_6，python版本3.77，conda 4.5.11

```bash
#从清华镜像下载源
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.0-Linux-x86_64.sh
#或是从自己电脑下载然后拖进去
sudo apt-get install lrzsz
# 下载完成后运行此sh
bash Anaconda3-5.3.0-Linux-x86_64.sh
#在此处我们安装在了/home/用户名/anaconda3中，同时我们默认自动配置了环境变量
#后续也可以自己配置环境变量，方法如下
#安装过程中可选不安装vscode，其他的就是yes或enter
#安装完成后重启系统
```
重启之后查看是否安装成功(如果是自动配置环境变量需要重启查看)
```bash
#终端输入python查看是否更换成了anaconda内的python解释，如下图所示：python版本信息后面带了anaconda的标识即安装成
python
Python 3.7.0 (default, Jun 28 2018, 13:15:42) 
[GCC 7.2.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
conda --version
conda 4.5.11

```
如果查看发现还是Linux自带的python可以自己添加环境变量
```bash
#目录是/home/用户名/.bashrc
sudo vim /home/sinet/.bashrc
#将export PATH="/你的安装位置/anaconda3/bin:$PATH" 添加在该文件的末尾保存退出即可
source /home/sinet/.bashrc
```
至此，anaconda3会被安装在`/home/[用户名]/anaconda3`中

```bash
#安装好后，以后每次进入服务器终端都会默认在conda 的base环境里
sinet@sinet165:~/anaconda3$ echo $PATH
/home/sinet/.local/bin:/home/sinet/anaconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
#需要离开此环境使用`conda deactivate` 退出虚拟环境
sinet@sinet165:~/anaconda3$ conda deactivate
sinet@sinet165:~/anaconda3$ python
Python 3.8.10 (default, Nov 22 2023, 10:22:35) 
[GCC 9.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
sinet@sinet165:~/anaconda3$ vim ~/.bashrc
#我使用`conda deactivate` 退出虚拟环境后，再次echo $PATH，发现没有conda没有了
sinet@sinet165:~/anaconda3$ echo $PATH
/home/sinet/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
#此时进入pyhton发现成为了默认的linux环境，后续可以通过再次进入conde环境
sinet@sinet165:~/anaconda3$ conda activate
(base) sinet@sinet165:~/anaconda3$ echo $PATH
/home/sinet/anaconda3/bin:/home/sinet/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
(base) sinet@sinet165:~/anaconda3$ conda deactivate
sinet@sinet165:~/anaconda3$ python
Python 3.8.10 (default, Nov 22 2023, 10:22:35) 
[GCC 9.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

### 安装之后默认进入Anaconda3环境问题
参考网址1：https://blog.csdn.net/weixin_43698781/article/details/124154268
参考网址2：https://blog.csdn.net/weixin_45205745/article/details/130836123

方法一：每次在命令行通过conda deactivate退出base环境回到系统自动的环境

方法二：通过将auto_activate_base参数设置为false实现：`conda config --set auto_activate_base false`
后续可通过下面的语句恢复：`conda config --set auto_activate_base true`

但是可能有的Anaconda3并没有这个参数，可以通过以下语句查看：`conda config --describe` `conda config --show`

方法三：修改.condarc配置文件

```bash
channels:
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
ssl_verify: true
show_channel_urls: true
```
参考官方文档关于.condarc配置文件的说明，在里面添加一句：auto_activate_base: false 即可
**注**：实际上，方法二也是修改的.condarc文件，可以在使用方法二的同时观察此文件内容的变化

方法四：修改.bashrc文件
对于没有`auto_activate_base`这个参数的，可以通过以下方式修改

打开.bashrc文件，在conda加入的语句里,找到 “ CONDA_CHANGEPS1=false conda activate base”这个语句，删除“conda activate base”，保存之后source，再次打开终端就是默认了conda关闭了。
但是在实际操作中，我不确定是需要`CONDA_CHANGEPS1=false conda activate base`整句删除还是只需要删除`conda activate base`。我选择了全部删除，成功了。
注意，删除之后需要重新source，并且需要重开一个终端（在原终端不知道为什么查看不了）
```bash
# added by Anaconda3 5.3.1 installer
# >>> conda init >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$(CONDA_REPORT_ERRORS=false '/home/yourname/anaconda3/bin/conda' shell.bash hook 2> /dev/null)"
if [ $? -eq 0 ]; then
    \eval "$__conda_setup"
else
    if [ -f "/home/yourname/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/youename/anaconda3/etc/profile.d/conda.sh"
        CONDA_CHANGEPS1=false conda activate base（这里要删除）
    else
        \export PATH="$PATH:/home/wjj/anaconda3/bin"
    fi
fi
unset __conda_setup
# <<< conda init <<<
```

```bash
source /home/sinet/.bashrc
```


### 注意(罗通复制过来的，实际并没有使用，具体原因参照上面)

安装anaconda后将linux的默认python改成`conda base`环境的python，无论`/usr/bin/python`软链接是怎么指的。我们PATH 来看下

``` bash
echo $PATH

/home/ubt/anaconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin
```

可以看到系统去找python的时候先在conda环境找到了，就不会去找系统的python了。

因为安装conda的时候安装程序把conda环境的顺序写在最前面了，`vim ~/.bashrc` 打印看看

``` bash
export PATH="/home/sinet/anaconda3/bin:$PATH"
```

解决方法就是在改文档最后加上python别名`alias python="/usr/bin/python"`，让它一直指向系统python


### 卸载Anaconda（网上复制的，没有使用过）

如果安装anaconda时不小心安装到了在普通用户访问不到的目录下，例如/root、/home/root，可以卸载anaconda重新进行安装。
```bash
#（1）删除安装目录：
rm -rf /你的目录/anaconda3
#（2）编辑环境变量文件:
sudo vim /home/sinet/.bashrc
#注释或删除anaconda3的路径
#（3）使修改后的环境变量立即生效：
source /home/sinet/.bashrc
```
### 其他
#### Python和Anaconda的版本对应关系如下

```bash
Packages included in Anaconda 2022.10 for 64-bit Linux on x86_64 CPUs with Python 3.10
Packages included in Anaconda 2022.10 for 64-bit Linux on ARMv8 CPUs with Python 3.10
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Power CPUs with Python 3.10
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Z CPUs with Python 3.10
Packages included in Anaconda 2022.10 for macOS on x86_64 with Python 3.10
Packages included in Anaconda 2022.10 for macOS on Apple M1 with Python 3.10
Packages included in Anaconda 2022.10 for 64-bit Windows with Python 3.10
Packages included in Anaconda 2022.10 for 64-bit Linux on x86_64 CPUs with Python 3.7
Packages included in Anaconda 2022.10 for 64-bit Linux on ARMv8 CPUs with Python 3.7
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Z CPUs with Python 3.7
Packages included in Anaconda 2022.10 for macOS on x86_64 with Python 3.7
Packages included in Anaconda 2022.10 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2022.10 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2022.10 for 64-bit Linux on x86_64 CPUs with Python 3.8
Packages included in Anaconda 2022.10 for 64-bit Linux on ARMv8 CPUs with Python 3.8
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Z CPUs with Python 3.8
Packages included in Anaconda 2022.10 for macOS on x86_64 with Python 3.8
Packages included in Anaconda 2022.10 for macOS on Apple M1 with Python 3.8
Packages included in Anaconda 2022.10 for 32-bit Windows with Python 3.8
Packages included in Anaconda 2022.10 for 64-bit Windows with Python 3.8
Packages included in Anaconda 2022.10 for 64-bit Linux on x86_64 CPUs with Python 3.9
Packages included in Anaconda 2022.10 for 64-bit Linux on ARMv8 CPUs with Python 3.9
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Power CPUs with Python 3.9
Packages included in Anaconda 2022.10 for 64-bit Linux on IBM Z CPUs with Python 3.9
Packages included in Anaconda 2022.10 for macOS on x86_64 with Python 3.9
Packages included in Anaconda 2022.10 for macOS on Apple M1 with Python 3.9
Packages included in Anaconda 2022.10 for 32-bit Windows with Python 3.9
Packages included in Anaconda 2022.10 for 64-bit Windows with Python 3.9
Packages for 64-bit Linux on x86_64 CPUs with Python 3.7
Packages for 64-bit Linux on ARMv8 CPUs with Python 3.7
Packages for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages for 64-bit Linux on IBM Z CPUs with Python 3.7
Packages for macOS on x86_64 with Python 3.7
Packages for 32-bit Windows with Python 3.7
Packages for 64-bit Windows with Python 3.7
Packages for 64-bit Linux on x86_64 CPUs with Python 3.8
Packages for 64-bit Linux on ARMv8 CPUs with Python 3.8
Packages for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages for 64-bit Linux on IBM Z CPUs with Python 3.8
Packages for macOS on x86_64 with Python 3.8
Packages for macOS on Apple M1 with Python 3.8
Packages for 32-bit Windows with Python 3.8
Packages for 64-bit Windows with Python 3.8
Packages for 64-bit Linux on x86_64 CPUs with Python 3.9
Packages for 64-bit Linux on ARMv8 CPUs with Python 3.9
Packages for 64-bit Linux on IBM Power CPUs with Python 3.9
Packages for 64-bit Linux on IBM Z CPUs with Python 3.9
Packages for macOS on x86_64 with Python 3.9
Packages for macOS on Apple M1 with Python 3.9
Packages for 32-bit Windows with Python 3.9
Packages for 64-bit Windows with Python 3.9
Packages included in Anaconda 2021.11 for 64-bit Linux on x86_64 CPUs with Python 3.9
Packages included in Anaconda 2021.11 for 64-bit Linux on ARMv8 Graviton2 CPUs with Python 3.9
Packages included in Anaconda 2021.11 for 64-bit Linux on IBM Power CPUs with Python 3.9
Packages included in Anaconda 2021.11 for 64-bit Linux on IBM Z CPUs with Python 3.9
Packages included in Anaconda 2021.11 for macOS on x86_64 with Python 3.9
Packages included in Anaconda 2021.11 for 32-bit Windows with Python 3.9
Packages included in Anaconda 2021.11 for 64-bit Windows with Python 3.9
Packages included in Anaconda 2021.11 for 64-bit Linux on x86_64 CPUs with Python 3.8
Packages included in Anaconda 2021.11 for 64-bit Linux on ARMv8 Graviton2 CPUs with Python 3.8
Packages included in Anaconda 2021.11 for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages included in Anaconda 2021.11 for 64-bit Linux on IBM Z CPUs with Python 3.8
Packages included in Anaconda 2021.11 for macOS on x86_64 with Python 3.8
Packages included in Anaconda 2021.11 for 32-bit Windows with Python 3.8
Packages included in Anaconda 2021.11 for 64-bit Windows with Python 3.8
Packages included in Anaconda 2021.11 for 64-bit Linux on x86_64 CPUs with Python 3.7
Packages included in Anaconda 2021.11 for 64-bit Linux on ARMv8 Graviton2 CPUs with Python 3.7
Packages included in Anaconda 2021.11 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2021.11 for 64-bit Linux on IBM Z CPUs with Python 3.7
Packages included in Anaconda 2021.11 for macOS on x86_64 with Python 3.7
Packages included in Anaconda 2021.11 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2021.11 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2021.05 for 64-bit Linux on x86_64 CPUs with Python 3.9
Packages included in Anaconda 2021.05 for 64-bit Linux on ARMv8 CPUs with Python 3.9
Packages included in Anaconda 2021.05 for 64-bit Linux on IBM Power CPUs with Python 3.9
Packages included in Anaconda 2021.05 for 64-bit Linux on IBM Z CPUs with Python 3.9
Packages included in Anaconda 2021.05 for macOS on x86_64 with Python 3.9
Packages included in Anaconda 2021.05 for 32-bit Windows with Python 3.9
Packages included in Anaconda 2021.05 for 64-bit Windows with Python 3.9
Packages included in Anaconda 2021.05 for 64-bit Linux on x86_64 CPUs with Python 3.8
Packages included in Anaconda 2021.05 for 64-bit Linux on ARMv8 CPUs with Python 3.8
Packages included in Anaconda 2021.05 for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages included in Anaconda 2021.05 for 64-bit Linux on IBM Z CPUs with Python 3.8
Packages included in Anaconda 2021.05 for macOS on x86_64 with Python 3.8
Packages included in Anaconda 2021.05 for 32-bit Windows with Python 3.8
Packages included in Anaconda 2021.05 for 64-bit Windows with Python 3.8
Packages included in Anaconda 2021.05 for 64-bit Linux on x86_64 CPUs with Python 3.7
Packages included in Anaconda 2021.05 for 64-bit Linux on ARMv8 CPUs with Python 3.7
Packages included in Anaconda 2021.05 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2021.05 for 64-bit Linux on IBM Z CPUs with Python 3.7
Packages included in Anaconda 2021.05 for macOS on x86_64 with Python 3.7
Packages included in Anaconda 2021.05 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2021.05 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2021.05 for 64-bit Linux with Python 3.6
Packages included in Anaconda 2021.05 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 2021.05 for macOS with Python 3.6
Packages included in Anaconda 2021.05 for 32-bit Windows with Python 3.6
Packages included in Anaconda 2021.05 for 64-bit Windows with Python 3.6
Packages included in Anaconda 2021.04 for 64-bit Linux on x86_64 CPUs with Python 3.9
Packages included in Anaconda 2021.04 for 64-bit Linux on ARMv8 CPUs with Python 3.9
Packages included in Anaconda 2021.04 for 64-bit Linux on IBM Power CPUs with Python 3.9
Packages included in Anaconda 2021.04 for 64-bit Linux on IBM Z CPUs with Python 3.9
Packages included in Anaconda 2021.04 for macOS on x86_64 with Python 3.9
Packages included in Anaconda 2021.04 for 32-bit Windows with Python 3.9
Packages included in Anaconda 2021.04 for 64-bit Windows with Python 3.9
Packages included in Anaconda 2021.04 for 64-bit Linux on x86_64 CPUs with Python 3.8
Packages included in Anaconda 2021.04 for 64-bit Linux on ARMv8 CPUs with Python 3.8
Packages included in Anaconda 2021.04 for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages included in Anaconda 2021.04 for 64-bit Linux on IBM Z CPUs with Python 3.8
Packages included in Anaconda 2021.04 for macOS on x86_64 with Python 3.8
Packages included in Anaconda 2021.04 for 32-bit Windows with Python 3.8
Packages included in Anaconda 2021.04 for 64-bit Windows with Python 3.8
Packages included in Anaconda 2021.04 for 64-bit Linux on x86_64 CPUs with Python 3.7
Packages included in Anaconda 2021.04 for 64-bit Linux on ARMv8 CPUs with Python 3.7
Packages included in Anaconda 2021.04 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2021.04 for 64-bit Linux on IBM Z CPUs with Python 3.7
Packages included in Anaconda 2021.04 for macOS on x86_64 with Python 3.7
Packages included in Anaconda 2021.04 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2021.04 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2020.11 for 64-bit Linux with Python 3.8
Packages included in Anaconda 2020.11 for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages included in Anaconda 2020.11 for macOS with Python 3.8
Packages included in Anaconda 2020.11 for 32-bit Windows with Python 3.8
Packages included in Anaconda 2020.11 for 64-bit Windows with Python 3.8
Packages included in Anaconda 2020.11 for 64-bit Linux with Python 3.7
Packages included in Anaconda 2020.11 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2020.11 for macOS with Python 3.7
Packages included in Anaconda 2020.11 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2020.11 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2020.07 for 64-bit Linux with Python 3.8
Packages included in Anaconda 2020.07 for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages included in Anaconda 2020.07 for macOS with Python 3.8
Packages included in Anaconda 2020.07 for 32-bit Windows with Python 3.8
Packages included in Anaconda 2020.07 for 64-bit Windows with Python 3.8
Packages included in Anaconda 2020.07 for 64-bit Linux with Python 3.7
Packages included in Anaconda 2020.07 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2020.07 for macOS with Python 3.7
Packages included in Anaconda 2020.07 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2020.07 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2020.07 for 64-bit Linux with Python 3.6
Packages included in Anaconda 2020.07 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 2020.07 for macOS with Python 3.6
Packages included in Anaconda 2020.07 for 32-bit Windows with Python 3.6
Packages included in Anaconda 2020.07 for 64-bit Windows with Python 3.6
Packages included in Anaconda 2020.02 for 64-bit Linux with Python 3.8
Packages included in Anaconda 2020.02 for 64-bit Linux on IBM Power CPUs with Python 3.8
Packages included in Anaconda 2020.02 for macOS with Python 3.8
Packages included in Anaconda 2020.02 for 32-bit Windows with Python 3.8
Packages included in Anaconda 2020.02 for 64-bit Windows with Python 3.8
Packages included in Anaconda 2020.02 for 64-bit Linux with Python 3.7
Packages included in Anaconda 2020.02 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2020.02 for macOS with Python 3.7
Packages included in Anaconda 2020.02 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2020.02 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2020.02 for 64-bit Linux with Python 3.6
Packages included in Anaconda 2020.02 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 2020.02 for macOS with Python 3.6
Packages included in Anaconda 2020.02 for 32-bit Windows with Python 3.6
Packages included in Anaconda 2020.02 for 64-bit Windows with Python 3.6
Packages included in Anaconda 2019.10 for 64-bit Linux with Python 3.7
Packages included in Anaconda 2019.10 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2019.10 for macOS with Python 3.7
Packages included in Anaconda 2019.10 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2019.10 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2019.10 for 64-bit Linux with Python 3.6
Packages included in Anaconda 2019.10 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 2019.10 for macOS with Python 3.6
Packages included in Anaconda 2019.10 for 32-bit Windows with Python 3.6
Packages included in Anaconda 2019.10 for 64-bit Windows with Python 3.6
Packages included in Anaconda 2019.10 for 64-bit Linux with Python 2.7
Packages included in Anaconda 2019.10 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 2019.10 for macOS with Python 2.7
Packages included in Anaconda 2019.10 for 32-bit Windows with Python 2.7
Packages included in Anaconda 2019.10 for 64-bit Windows with Python 2.7
Packages included in Anaconda 2019.07 for 64-bit Linux with Python 3.7
Packages included in Anaconda 2019.07 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2019.07 for macOS with Python 3.7
Packages included in Anaconda 2019.07 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2019.07 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2019.07 for 64-bit Linux with Python 3.6
Packages included in Anaconda 2019.07 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 2019.07 for macOS with Python 3.6
Packages included in Anaconda 2019.07 for 32-bit Windows with Python 3.6
Packages included in Anaconda 2019.07 for 64-bit Windows with Python 3.6
Packages included in Anaconda 2019.07 for 64-bit Linux with Python 2.7
Packages included in Anaconda 2019.07 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 2019.07 for macOS with Python 2.7
Packages included in Anaconda 2019.07 for 32-bit Windows with Python 2.7
Packages included in Anaconda 2019.07 for 64-bit Windows with Python 2.7
Packages included in Anaconda 2019.03 for 64-bit Linux with Python 3.7
Packages included in Anaconda 2019.03 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2019.03 for macOS with Python 3.7
Packages included in Anaconda 2019.03 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2019.03 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2019.03 for 64-bit Linux with Python 3.6
Packages included in Anaconda 2019.03 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 2019.03 for macOS with Python 3.6
Packages included in Anaconda 2019.03 for 32-bit Windows with Python 3.6
Packages included in Anaconda 2019.03 for 64-bit Windows with Python 3.6
Packages included in Anaconda 2019.03 for 64-bit Linux with Python 2.7
Packages included in Anaconda 2019.03 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 2019.03 for macOS with Python 2.7
Packages included in Anaconda 2019.03 for 32-bit Windows with Python 2.7
Packages included in Anaconda 2019.03 for 64-bit Windows with Python 2.7
Packages included in Anaconda 2018.12 for 32-bit Linux with Python 3.7
Packages included in Anaconda 2018.12 for 64-bit Linux with Python 3.7
Packages included in Anaconda 2018.12 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 2018.12 for macOS with Python 3.7
Packages included in Anaconda 2018.12 for 32-bit Windows with Python 3.7
Packages included in Anaconda 2018.12 for 64-bit Windows with Python 3.7
Packages included in Anaconda 2018.12 for 32-bit Linux with Python 3.6
Packages included in Anaconda 2018.12 for 64-bit Linux with Python 3.6
Packages included in Anaconda 2018.12 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 2018.12 for macOS with Python 3.6
Packages included in Anaconda 2018.12 for 32-bit Windows with Python 3.6
Packages included in Anaconda 2018.12 for 64-bit Windows with Python 3.6
Packages included in Anaconda 2018.12 for 32-bit Linux with Python 2.7
Packages included in Anaconda 2018.12 for 64-bit Linux with Python 2.7
Packages included in Anaconda 2018.12 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 2018.12 for macOS with Python 2.7
Packages included in Anaconda 2018.12 for 32-bit Windows with Python 2.7
Packages included in Anaconda 2018.12 for 64-bit Windows with Python 2.7
Packages included in Anaconda 5.3.0 for 32-bit Linux with Python 3.7
Packages included in Anaconda 5.3.0 for 64-bit Linux with Python 3.7
Packages included in Anaconda 5.3.0 for 64-bit Linux on IBM Power CPUs with Python 3.7
Packages included in Anaconda 5.3.0 for 64-bit macOS with Python 3.7
Packages included in Anaconda 5.3.0 for 32-bit Windows with Python 3.7
Packages included in Anaconda 5.3.0 for 64-bit Windows with Python 3.7
Packages included in Anaconda 5.3.0 for 32-bit Linux with Python 3.6
Packages included in Anaconda 5.3.0 for 64-bit Linux with Python 3.6
Packages included in Anaconda 5.3.0 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 5.3.0 for macOS with Python 3.6
Packages included in Anaconda 5.3.0 for 32-bit Windows with Python 3.6
Packages included in Anaconda 5.3.0 for 64-bit Windows with Python 3.6
Packages included in Anaconda 5.3.0 for 32-bit Linux with Python 2.7
Packages included in Anaconda 5.3.0 for 64-bit Linux with Python 2.7
Packages included in Anaconda 5.3.0 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 5.3.0 for macOS with Python 2.7
Packages included in Anaconda 5.3.0 for 32-bit Windows with Python 2.7
Packages included in Anaconda 5.3.0 for 64-bit Windows with Python 2.7
Packages included in Anaconda 5.2.0 for 32-bit Linux with Python 3.6
Packages included in Anaconda 5.2.0 for 64-bit Linux with Python 3.6
Packages included in Anaconda 5.2.0 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 5.2.0 for macOS with Python 3.6
Packages included in Anaconda 5.2.0 for 32-bit Windows with Python 3.6
Packages included in Anaconda 5.2.0 for 64-bit Windows with Python 3.6
Packages included in Anaconda 5.2.0 for 32-bit Linux with Python 3.5
Packages included in Anaconda 5.2.0 for 64-bit Linux with Python 3.5
Packages included in Anaconda 5.2.0 for 64-bit Linux on IBM Power CPUs with Python 3.5
Packages included in Anaconda 5.2.0 for macOS with Python 3.5
Packages included in Anaconda 5.2.0 for 32-bit Windows with Python 3.5
Packages included in Anaconda 5.2.0 for 64-bit Windows with Python 3.5
Packages included in Anaconda 5.2.0 for 32-bit Linux with Python 2.7
Packages included in Anaconda 5.2.0 for 64-bit Linux with Python 2.7
Packages included in Anaconda 5.2.0 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 5.2.0 for macOS with Python 2.7
Packages included in Anaconda 5.2.0 for 32-bit Windows with Python 2.7
Packages included in Anaconda 5.2.0 for 64-bit Windows with Python 2.7
Packages included in Anaconda 5.1.0 for 32-bit Linux with Python 3.6
Packages included in Anaconda 5.1.0 for 64-bit Linux with Python 3.6
Packages included in Anaconda 5.1.0 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 5.1.0 for macOS with Python 3.6
Packages included in Anaconda 5.1.0 for 32-bit Windows with Python 3.6
Packages included in Anaconda 5.1.0 for 64-bit Windows with Python 3.6
Packages included in Anaconda 5.1.0 for 32-bit Linux with Python 3.5
Packages included in Anaconda 5.1.0 for 64-bit Linux with Python 3.5
Packages included in Anaconda 5.1.0 for 64-bit Linux on IBM Power CPUs with Python 3.5
Packages included in Anaconda 5.1.0 for macOS with Python 3.5
Packages included in Anaconda 5.1.0 for 32-bit Windows with Python 3.5
Packages included in Anaconda 5.1.0 for 64-bit Windows with Python 3.5
Packages included in Anaconda 5.1.0 for 32-bit Linux with Python 2.7
Packages included in Anaconda 5.1.0 for 64-bit Linux with Python 2.7
Packages included in Anaconda 5.1.0 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 5.1.0 for macOS with Python 2.7
Packages included in Anaconda 5.1.0 for 32-bit Windows with Python 2.7
Packages included in Anaconda 5.1.0 for 64-bit Windows with Python 2.7
Packages included in Anaconda 5.0.1 for 32-bit Linux with Python 3.6
Packages included in Anaconda 5.0.1 for 64-bit Linux with Python 3.6
Packages included in Anaconda 5.0.1 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 5.0.1 for macOS with Python 3.6
Packages included in Anaconda 5.0.1 for 32-bit Windows with Python 3.6
Packages included in Anaconda 5.0.1 for 64-bit Windows with Python 3.6
Packages included in Anaconda 5.0.1 for 32-bit Linux with Python 3.5
Packages included in Anaconda 5.0.1 for 64-bit Linux with Python 3.5
Packages included in Anaconda 5.0.1 for 64-bit Linux on IBM Power CPUs with Python 3.5
Packages included in Anaconda 5.0.1 for macOS with Python 3.5
Packages included in Anaconda 5.0.1 for 32-bit Windows with Python 3.5
Packages included in Anaconda 5.0.1 for 64-bit Windows with Python 3.5
Packages included in Anaconda 5.0.1 for 32-bit Linux with Python 2.7
Packages included in Anaconda 5.0.1 for 64-bit Linux with Python 2.7
Packages included in Anaconda 5.0.1 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 5.0.1 for macOS with Python 2.7
Packages included in Anaconda 5.0.1 for 32-bit Windows with Python 2.7
Packages included in Anaconda 5.0.1 for 64-bit Windows with Python 2.7
Packages included in Anaconda 5.0.0 for 32-bit Linux with Python 3.6
Packages included in Anaconda 5.0.0 for 64-bit Linux with Python 3.6
Packages included in Anaconda 5.0.0 for 64-bit Linux on IBM Power CPUs with Python 3.6
Packages included in Anaconda 5.0.0 for macOS with Python 3.6
Packages included in Anaconda 5.0.0 for 32-bit Windows with Python 3.6
Packages included in Anaconda 5.0.0 for 64-bit Windows with Python 3.6
Packages included in Anaconda 5.0.0 for 32-bit Linux with Python 3.5
Packages included in Anaconda 5.0.0 for 64-bit Linux with Python 3.5
Packages included in Anaconda 5.0.0 for 64-bit Linux on IBM Power CPUs with Python 3.5
Packages included in Anaconda 5.0.0 for macOS with Python 3.5
Packages included in Anaconda 5.0.0 for 32-bit Windows with Python 3.5
Packages included in Anaconda 5.0.0 for 64-bit Windows with Python 3.5
Packages included in Anaconda 5.0.0 for 32-bit Linux with Python 2.7
Packages included in Anaconda 5.0.0 for 64-bit Linux with Python 2.7
Packages included in Anaconda 5.0.0 for 64-bit Linux on IBM Power CPUs with Python 2.7
Packages included in Anaconda 5.0.0 for macOS with Python 2.7
Packages included in Anaconda 5.0.0 for 32-bit Windows with Python 2.7
Packages included in Anaconda 5.0.0 for 64-bit Windows with Python 2.7
Packages included in Anaconda 4.4.0 for Python version 3.6
Packages included in Anaconda 4.4.0 for Python version 3.5
Packages included in Anaconda 4.4.0 for Python version 2.7
Packages included in Anaconda 4.3.1 for Python version 3.6
Packages included in Anaconda 4.3.1 for Python version 3.5
Packages included in Anaconda 4.3.1 for Python version 3.4
Packages included in Anaconda 4.3.1 for Python version 2.7
Packages included in Anaconda 4.3.0 for Python version 3.6
Packages included in Anaconda 4.3.0 for Python version 3.5
Packages included in Anaconda 4.3.0 for Python version 3.4
Packages included in Anaconda 4.3.0 for Python version 2.7
Packages included in Anaconda 4.2.0 for Python version 3.5
Packages included in Anaconda 4.2.0 for Python version 3.4
Packages included in Anaconda 4.2.0 for Python version 2.7
Packages included in Anaconda 4.1.1 for Python version 3.5
Packages included in Anaconda 4.1.1 for Python version 3.4
Packages included in Anaconda 4.1.1 for Python version 2.7
Packages included in Anaconda 4.1.0 for Python version 3.5
Packages included in Anaconda 4.1.0 for Python version 3.4
Packages included in Anaconda 4.1.0 for Python version 2.7
Packages included in Anaconda 4.0.0 for Python version 3.5
Packages included in Anaconda 4.0.0 for Python version 3.4
Packages included in Anaconda 4.0.0 for Python version 2.7
Packages included in Anaconda 2.5.0 for Python version 3.5
Packages included in Anaconda 2.5.0 for Python version 3.4
Packages included in Anaconda 2.5.0 for Python version 2.7
Packages included in Anaconda 2.4.1 for Python version 3.5
Packages included in Anaconda 2.4.1 for Python version 3.4
Packages included in Anaconda 2.4.1 for Python version 2.7
Packages included in Anaconda 2.4.0 for Python version 3.5
Packages included in Anaconda 2.4.0 for Python version 3.4
Packages included in Anaconda 2.4.0 for Python version 2.7
Packages included in Anaconda 2.3.0 for Python version 3.4
Packages included in Anaconda 2.3.0 for Python version 3.3
Packages included in Anaconda 2.3.0 for Python version 2.7
Packages included in Anaconda 2.3.0 for Python version 2.6
Packages included in Anaconda 2.2.0 for Python version 3.4
Packages included in Anaconda 2.2.0 for Python version 3.3
Packages included in Anaconda 2.2.0 for Python version 2.7
Packages included in Anaconda 2.2.0 for Python version 2.6
Packages included in Anaconda 2.1.0 for Python version 3.4
Packages included in Anaconda 2.1.0 for Python version 3.3
Packages included in Anaconda 2.1.0 for Python version 2.7
Packages included in Anaconda 2.1.0 for Python version 2.6
Packages included in Anaconda 2.0.1 for Python version 3.4
Packages included in Anaconda 2.0.1 for Python version 3.3
Packages included in Anaconda 2.0.1 for Python version 2.7
Packages included in Anaconda 2.0.1 for Python version 2.6
Packages included in Anaconda 1.9.2
Packages included in Anaconda 1.9.1
Packages included in Anaconda 1.9.0
Packages included in Anaconda 1.8.0
Packages included in Anaconda 1.7.0
Packages included in Anaconda 1.6.1
Packages included in Anaconda 1.5.0
Packages included in Anaconda 1.4.0
Packages included in Anaconda 1.3.1
Packages included in Anaconda 1.2.1
Packages included in Anaconda v.1.1
Packages included in Anaconda v.1.0
```

#### shell安装时的一些输出日志：
```bash
[/home/sinet/anaconda3] >>> 
PREFIX=/home/sinet/anaconda3
installing: python-3.7.0-hc3d631a_0 ...
Python 3.7.0
installing: blas-1.0-mkl ...
installing: ca-certificates-2018.03.07-0 ...
installing: conda-env-2.6.0-1 ...
installing: intel-openmp-2019.0-118 ...
installing: libgcc-ng-8.2.0-hdf63c60_1 ...
installing: libgfortran-ng-7.3.0-hdf63c60_0 ...
installing: libstdcxx-ng-8.2.0-hdf63c60_1 ...
installing: bzip2-1.0.6-h14c3975_5 ...
installing: expat-2.2.6-he6710b0_0 ...
installing: fribidi-1.0.5-h7b6447c_0 ...
installing: gmp-6.1.2-h6c8ec71_1 ...
installing: graphite2-1.3.12-h23475e2_2 ...
installing: icu-58.2-h9c2bf20_1 ...
installing: jbig-2.1-hdba287a_0 ...
installing: jpeg-9b-h024ee3a_2 ...
installing: libffi-3.2.1-hd88cf55_4 ...
installing: libsodium-1.0.16-h1bed415_0 ...
installing: libtool-2.4.6-h544aabb_3 ...
installing: libuuid-1.0.3-h1bed415_2 ...
installing: libxcb-1.13-h1bed415_1 ...
installing: lzo-2.10-h49e0be7_2 ...
installing: mkl-2019.0-118 ...
installing: ncurses-6.1-hf484d3e_0 ...
installing: openssl-1.0.2p-h14c3975_0 ...
installing: patchelf-0.9-hf484d3e_2 ...
installing: pcre-8.42-h439df22_0 ...
installing: pixman-0.34.0-hceecf20_3 ...
installing: snappy-1.1.7-hbae5bb6_3 ...
installing: xz-5.2.4-h14c3975_4 ...
installing: yaml-0.1.7-had09818_2 ...
installing: zlib-1.2.11-ha838bed_2 ...
installing: blosc-1.14.4-hdbcaa40_0 ...
installing: glib-2.56.2-hd408876_0 ...
installing: hdf5-1.10.2-hba1933b_1 ...
installing: libedit-3.1.20170329-h6b74fdf_2 ...
installing: libpng-1.6.34-hb9fc6fc_0 ...
installing: libssh2-1.8.0-h9cfc8f7_4 ...
installing: libtiff-4.0.9-he85c1e1_2 ...
installing: libxml2-2.9.8-h26e45fe_1 ...
installing: mpfr-4.0.1-hdf1c602_3 ...
installing: pandoc-1.19.2.1-hea2e7c5_1 ...
installing: readline-7.0-h7b6447c_5 ...
installing: tk-8.6.8-hbc83047_0 ...
installing: zeromq-4.2.5-hf484d3e_1 ...
installing: dbus-1.13.2-h714fa37_1 ...
installing: freetype-2.9.1-h8a8886c_1 ...
installing: gstreamer-1.14.0-hb453b48_1 ...
installing: libcurl-7.61.0-h1ad7b7a_0 ...
installing: libxslt-1.1.32-h1312cb7_0 ...
installing: mpc-1.1.0-h10f8cd9_1 ...
installing: sqlite-3.24.0-h84994c4_0 ...
installing: unixodbc-2.3.7-h14c3975_0 ...
installing: curl-7.61.0-h84994c4_0 ...
installing: fontconfig-2.13.0-h9420a91_0 ...
installing: gst-plugins-base-1.14.0-hbbd80ab_1 ...
installing: alabaster-0.7.11-py37_0 ...
installing: appdirs-1.4.3-py37h28b3542_0 ...
installing: asn1crypto-0.24.0-py37_0 ...
installing: atomicwrites-1.2.1-py37_0 ...
installing: attrs-18.2.0-py37h28b3542_0 ...
installing: backcall-0.1.0-py37_0 ...
installing: backports-1.0-py37_1 ...
installing: beautifulsoup4-4.6.3-py37_0 ...
installing: bitarray-0.8.3-py37h14c3975_0 ...
installing: boto-2.49.0-py37_0 ...
installing: cairo-1.14.12-h8948797_3 ...
installing: certifi-2018.8.24-py37_1 ...
installing: chardet-3.0.4-py37_1 ...
installing: click-6.7-py37_0 ...
installing: cloudpickle-0.5.5-py37_0 ...
installing: colorama-0.3.9-py37_0 ...
installing: constantly-15.1.0-py37h28b3542_0 ...
installing: contextlib2-0.5.5-py37_0 ...
installing: dask-core-0.19.1-py37_0 ...
installing: decorator-4.3.0-py37_0 ...
installing: defusedxml-0.5.0-py37_1 ...
installing: docutils-0.14-py37_0 ...
installing: entrypoints-0.2.3-py37_2 ...
installing: et_xmlfile-1.0.1-py37_0 ...
installing: fastcache-1.0.2-py37h14c3975_2 ...
installing: filelock-3.0.8-py37_0 ...
installing: glob2-0.6-py37_0 ...
installing: gmpy2-2.0.8-py37h10f8cd9_2 ...
installing: greenlet-0.4.15-py37h7b6447c_0 ...
installing: heapdict-1.0.0-py37_2 ...
installing: idna-2.7-py37_0 ...
installing: imagesize-1.1.0-py37_0 ...
installing: incremental-17.5.0-py37_0 ...
installing: ipython_genutils-0.2.0-py37_0 ...
installing: itsdangerous-0.24-py37_1 ...
installing: jdcal-1.4-py37_0 ...
installing: jeepney-0.3.1-py37_0 ...
installing: kiwisolver-1.0.1-py37hf484d3e_0 ...
installing: lazy-object-proxy-1.3.1-py37h14c3975_2 ...
installing: llvmlite-0.24.0-py37hdbcaa40_0 ...
installing: locket-0.2.0-py37_1 ...
installing: lxml-4.2.5-py37hefd8a0e_0 ...
installing: markupsafe-1.0-py37h14c3975_1 ...
installing: mccabe-0.6.1-py37_1 ...
installing: mistune-0.8.3-py37h14c3975_1 ...
installing: mkl-service-1.1.2-py37h90e4bf4_5 ...
installing: mpmath-1.0.0-py37_2 ...
installing: msgpack-python-0.5.6-py37h6bb024c_1 ...
installing: numpy-base-1.15.1-py37h81de0dd_0 ...
installing: olefile-0.46-py37_0 ...
installing: pandocfilters-1.4.2-py37_1 ...
installing: parso-0.3.1-py37_0 ...
installing: path.py-11.1.0-py37_0 ...
installing: pep8-1.7.1-py37_0 ...
installing: pickleshare-0.7.4-py37_0 ...
installing: pkginfo-1.4.2-py37_1 ...
installing: pluggy-0.7.1-py37h28b3542_0 ...
installing: ply-3.11-py37_0 ...
installing: psutil-5.4.7-py37h14c3975_0 ...
installing: ptyprocess-0.6.0-py37_0 ...
installing: py-1.6.0-py37_0 ...
installing: pyasn1-0.4.4-py37h28b3542_0 ...
installing: pycodestyle-2.4.0-py37_0 ...
installing: pycosat-0.6.3-py37h14c3975_0 ...
installing: pycparser-2.18-py37_1 ...
installing: pycrypto-2.6.1-py37h14c3975_9 ...
installing: pycurl-7.43.0.2-py37hb7f436b_0 ...
installing: pyflakes-2.0.0-py37_0 ...
installing: pyodbc-4.0.24-py37he6710b0_0 ...
installing: pyparsing-2.2.0-py37_1 ...
installing: pysocks-1.6.8-py37_0 ...
installing: pytz-2018.5-py37_0 ...
installing: pyyaml-3.13-py37h14c3975_0 ...
installing: pyzmq-17.1.2-py37h14c3975_0 ...
installing: qt-5.9.6-h8703b6f_2 ...
installing: qtpy-1.5.0-py37_0 ...
installing: rope-0.11.0-py37_0 ...
installing: ruamel_yaml-0.15.46-py37h14c3975_0 ...
installing: send2trash-1.5.0-py37_0 ...
installing: simplegeneric-0.8.1-py37_2 ...
installing: sip-4.19.8-py37hf484d3e_0 ...
installing: six-1.11.0-py37_1 ...
installing: snowballstemmer-1.2.1-py37_0 ...
installing: sortedcontainers-2.0.5-py37_0 ...
installing: sphinxcontrib-1.0-py37_1 ...
installing: sqlalchemy-1.2.11-py37h7b6447c_0 ...
installing: tblib-1.3.2-py37_0 ...
installing: testpath-0.3.1-py37_0 ...
installing: toolz-0.9.0-py37_0 ...
installing: tornado-5.1-py37h14c3975_0 ...
installing: tqdm-4.26.0-py37h28b3542_0 ...
installing: unicodecsv-0.14.1-py37_0 ...
installing: wcwidth-0.1.7-py37_0 ...
installing: webencodings-0.5.1-py37_1 ...
installing: werkzeug-0.14.1-py37_0 ...
installing: wrapt-1.10.11-py37h14c3975_2 ...
installing: xlrd-1.1.0-py37_1 ...
installing: xlsxwriter-1.1.0-py37_0 ...
installing: xlwt-1.3.0-py37_0 ...
installing: zope-1.0-py37_1 ...
installing: astroid-2.0.4-py37_0 ...
installing: automat-0.7.0-py37_0 ...
installing: babel-2.6.0-py37_0 ...
installing: backports.shutil_get_terminal_size-1.0.0-py37_2 ...
installing: cffi-1.11.5-py37he75722e_1 ...
installing: cycler-0.10.0-py37_0 ...
installing: cytoolz-0.9.0.1-py37h14c3975_1 ...
installing: harfbuzz-1.8.8-hffaf4a1_0 ...
installing: html5lib-1.0.1-py37_0 ...
installing: hyperlink-18.0.0-py37_0 ...
installing: jedi-0.12.1-py37_0 ...
installing: more-itertools-4.3.0-py37_0 ...
installing: multipledispatch-0.6.0-py37_0 ...
installing: networkx-2.1-py37_0 ...
installing: nltk-3.3.0-py37_0 ...
installing: openpyxl-2.5.6-py37_0 ...
installing: packaging-17.1-py37_0 ...
installing: partd-0.3.8-py37_0 ...
installing: pathlib2-2.3.2-py37_0 ...
installing: pexpect-4.6.0-py37_0 ...
installing: pillow-5.2.0-py37heded4f4_0 ...
installing: pyasn1-modules-0.2.2-py37_0 ...
installing: pyqt-5.9.2-py37h05f1152_2 ...
installing: python-dateutil-2.7.3-py37_0 ...
installing: qtawesome-0.4.4-py37_0 ...
installing: setuptools-40.2.0-py37_0 ...
installing: singledispatch-3.4.0.3-py37_0 ...
installing: sortedcollections-1.0.1-py37_0 ...
installing: sphinxcontrib-websupport-1.1.0-py37_1 ...
installing: sympy-1.2-py37_0 ...
installing: terminado-0.8.1-py37_1 ...
installing: traitlets-4.3.2-py37_0 ...
installing: zict-0.1.3-py37_0 ...
installing: zope.interface-4.5.0-py37h14c3975_0 ...
installing: bleach-2.1.4-py37_0 ...
installing: clyent-1.2.2-py37_1 ...
installing: cryptography-2.3.1-py37hc365091_0 ...
installing: cython-0.28.5-py37hf484d3e_0 ...
installing: distributed-1.23.1-py37_0 ...
installing: get_terminal_size-1.0.0-haa9412d_0 ...
installing: gevent-1.3.6-py37h7b6447c_0 ...
installing: isort-4.3.4-py37_0 ...
installing: jinja2-2.10-py37_0 ...
installing: jsonschema-2.6.0-py37_0 ...
installing: jupyter_core-4.4.0-py37_0 ...
installing: navigator-updater-0.2.1-py37_0 ...
installing: nose-1.3.7-py37_2 ...
installing: pango-1.42.4-h049681c_0 ...
installing: pygments-2.2.0-py37_0 ...
installing: pytest-3.8.0-py37_0 ...
installing: wheel-0.31.1-py37_0 ...
installing: flask-1.0.2-py37_1 ...
installing: jupyter_client-5.2.3-py37_0 ...
installing: nbformat-4.4.0-py37_0 ...
installing: pip-10.0.1-py37_0 ...
installing: prompt_toolkit-1.0.15-py37_0 ...
installing: pylint-2.1.1-py37_0 ...
installing: pyopenssl-18.0.0-py37_0 ...
installing: pytest-openfiles-0.3.0-py37_0 ...
installing: pytest-remotedata-0.3.0-py37_0 ...
installing: secretstorage-3.1.0-py37_0 ...
installing: flask-cors-3.0.6-py37_0 ...
installing: ipython-6.5.0-py37_0 ...
installing: keyring-13.2.1-py37_0 ...
installing: nbconvert-5.4.0-py37_1 ...
installing: service_identity-17.0.0-py37h28b3542_0 ...
installing: urllib3-1.23-py37_0 ...
installing: ipykernel-4.9.0-py37_1 ...
installing: requests-2.19.1-py37_0 ...
installing: twisted-18.7.0-py37h14c3975_1 ...
installing: anaconda-client-1.7.2-py37_0 ...
installing: jupyter_console-5.2.0-py37_1 ...
installing: prometheus_client-0.3.1-py37h28b3542_0 ...
installing: qtconsole-4.4.1-py37_0 ...
installing: sphinx-1.7.9-py37_0 ...
installing: spyder-kernels-0.2.6-py37_0 ...
installing: anaconda-navigator-1.9.2-py37_0 ...
installing: anaconda-project-0.8.2-py37_0 ...
installing: notebook-5.6.0-py37_0 ...
installing: numpydoc-0.8.0-py37_0 ...
installing: jupyterlab_launcher-0.13.1-py37_0 ...
installing: spyder-3.3.1-py37_1 ...
installing: widgetsnbextension-3.4.1-py37_0 ...
installing: ipywidgets-7.4.1-py37_0 ...
installing: jupyterlab-0.34.9-py37_0 ...
installing: _ipyw_jlab_nb_ext_conf-0.1.0-py37_0 ...
installing: jupyter-1.0.0-py37_7 ...
installing: bokeh-0.13.0-py37_0 ...
installing: bottleneck-1.2.1-py37h035aef0_1 ...
installing: conda-4.5.11-py37_0 ...
installing: conda-build-3.15.1-py37_0 ...
installing: datashape-0.5.4-py37_1 ...
installing: h5py-2.8.0-py37h989c5e5_3 ...
installing: imageio-2.4.1-py37_0 ...
installing: matplotlib-2.2.3-py37hb69df0a_0 ...
installing: mkl_fft-1.0.4-py37h4414c95_1 ...
installing: mkl_random-1.0.1-py37h4414c95_1 ...
installing: numpy-1.15.1-py37h1d66e8a_0 ...
installing: numba-0.39.0-py37h04863e7_0 ...
installing: numexpr-2.6.8-py37hd89afb7_0 ...
installing: pandas-0.23.4-py37h04863e7_0 ...
installing: pytest-arraydiff-0.2-py37h39e3cac_0 ...
installing: pytest-doctestplus-0.1.3-py37_0 ...
installing: pywavelets-1.0.0-py37hdd07704_0 ...
installing: scipy-1.1.0-py37hfa4b5c9_1 ...
installing: bkcharts-0.2-py37_0 ...
installing: dask-0.19.1-py37_0 ...
installing: patsy-0.5.0-py37_0 ...
installing: pytables-3.4.4-py37ha205bf6_0 ...
installing: pytest-astropy-0.4.0-py37_0 ...
installing: scikit-image-0.14.0-py37hf484d3e_1 ...
installing: scikit-learn-0.19.2-py37h4989274_0 ...
installing: astropy-3.0.4-py37h14c3975_0 ...
installing: odo-0.5.1-py37_0 ...
installing: statsmodels-0.9.0-py37h035aef0_0 ...
installing: blaze-0.11.3-py37_0 ...
installing: seaborn-0.9.0-py37_0 ...
installing: anaconda-5.3.0-py37_0 ...
installation finished.
Do you wish the installer to initialize Anaconda3
in your /home/sinet/.bashrc ? [yes|no]
[no] >>> yes
```

## conda入门
参考网址：https://blog.csdn.net/weixin_43400249/article/details/128169994
##### 1、查看conda版本
```bash
conda -V
conda --version
```
##### 2、查看所有环境

```bash
conda env list
conda info --envs
```
##### 3、进入某个环境
```conda activate envName```

如果想要退出环境，请使用```conda deactivate```，如果想要切换环境直接使用命令进入对应环境即可

##### 4、查看环境中所有的库

在一个环境中，可能会存在需要使用到的库或者包，想要查看一个环境中所有的库，首先需要先进入对应的环境中（步骤3所示），而后使用命令```conda list```

或者也可以使用```pip list```（如果你的环境中有pip这个包的话）

##### 5、查看某个单独库的的版本信息

查看某个库的信息的前提是一定要知道这个库的具体名称。例如，我们想要查看环境中是否存在一个名为numpy的库，如果存在的话，他的版本是多少，可以使用命令```conda list numpy```或者```pip show numpy```

##### 6、创建新的环境
若想要创建自己的Anaconda环境，需要使用命令为：```conda create --name xxx```，其中xxx为你想要创建环境的名称，该名称作为后续进入所创建环境的唯一标识。

如果想要指定所创建的Anaconda环境的python版本，则需对上述命令进行调整，修改为：```conda create --name xxx python=3.7```则可以创建带有python 3.7的环境

更为进阶的命令可以自行百度查阅，例如如何复制以后环境，创建环境时如何直接安装指定版本的库

##### 7、在某个环境中安装库
安装库的方式有三种，分别为conda，pip，wheel
conda安装的命令为：```conda install xxx```，xxx为所安装库的名字，例如conda install numpy，而后根据提示进行后续安装。
如果想要指定所安装库的版本，可以使用```conda instll xlrd==1.2.0```，安装xlrd这个库的1.2.0版本
需要注意的是这种安装的方式的源服务器可能在国外，有时候下载可能会比较慢。因此推荐下一种。

推荐：使用pip的前提是对应环境中拥有pip这个库，如果没有可以先用conda的方式安装pip。pip是一个第三方管理python或者Anaconda包的工具。
pip安装的基础命令为：```pip install xxx```，中xxx为所安装的库的名字。
这样下载同样可能会比较慢，因此推荐加入临时换源语句：```pip install numpy -i http://pypi.douban.com.simple --trusted-host pypi.douban.com``` ，其中numpy可以替换为想要安装的库的名字
指定版本同conda安装一样，在包的名字后边加上\==版本即可

#  Anaconda安装Pytorch

#### 创建虚拟环境

> conda create -n pytorch python=3.7 （pytorch 是我自己取的名字）

激活环境

使用下面这条命令，激活环境：

> conda activate pytorch

然后命令行开头都会带上`(pytorch)`了，表示在此虚拟环境中。

（在该环境下，linux服务器的python/python3环境都被改成了conda的python3.7了）

然后去选择适合自己的pytorch版本，点击下面那个链接:

> https://pytorch.org/

我只下载了：

> conda install pytorch==1.13.1 torchvision==0.14.1  -c pytorch

弹出提示，输入 y，即可完成安装，显示“done”。

#### 测试torch安装成功

在终端输入python，

输入` import torch`，没有报错就是安装成功了。

输入`torch.__version__`可查看安装的torch版本。


#### 安装过程中报错

报错内容如下：
```
SSLError(MaxRetryError('HTTPSConnectionPool(host=\'repo.anaconda.com\', port=443): Max retries exceeded with url: /pkgs/r/linux-64/repodata.json.bz2 (Caused by SSLError(SSLError("bad handshake: Error([(\'SSL routines\', \'ssl3_get_server_certificate\', \'certificate verify failed\')])")))'))
```
解决方案，添加清华源
参考网址：https://blog.csdn.net/hejp_123/article/details/98493042

```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/  
 
# 设置搜索时显示通道地址
conda config --set show_channel_urls yes
# 执行完上述命令后，会生成配置文件记录着我们对conda的配置，这个文件在/home/sinet文件夹下面
```

```bash
# 编辑.condarc注释defalts，主要是注释defaults
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
#  - defaults
show_channel_urls: true
```

后续下载torch继续报错，还是镜像的问题，参考另一个网址解决了
参考网址：https://blog.csdn.net/YOULANSHENGMENG/article/details/137255891
```bash
channels:
  - defaults
show_channel_urls: true
default_channels:
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```