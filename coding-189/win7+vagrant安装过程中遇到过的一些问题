该文章主要是针对课程2-5的内容在win7上进行实际操作中可能遇到的问题进行汇总和我本人（一个菜鸟学员）的解决办法
1，先安装vagrant，我实验的环境是使用了vagrant的1.9.5和virtualbox的4.3.40版本。
2，如果遇到执行vagrant up的时候报404的错误，对于这个问题，我的理解是在执行这个命令的时候，我们的vagrant拉取box源的时候是从国外的源去拉取的（这只是学员个人的理解，非课程讲授者），
   解决办法就是修改拉取源，在我们的vagrantfile的这一行(Vagrant.configure("2") do |config|)前添加一行配置，指定box源：添加内容如下：
   Vagrant::DEFAULT_SERVER_URL.replace('https://vagrantcloud.com')
3，在我们执行vagrant up以前，请先确认文件：C:\Users\Administrator\.vagrant.d\boxes\centos-VAGRANTSLASH-7\1802.01\virtualbox\Vagrantfile中的以下配置
  config.vm.synced_folder ".", "/vagrant", type: "rsync"     //如果配置为这样，请修改为如下
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  该配置是在将我们的共享文件夹mount进虚拟机中。如果这里不修改，可能会造成共享目录挂载失败。
4，在执行了vagrant up后遇到如下的问题
  Vagrant was unable to mount VirtualBox shared folders. This is usually because the filesystem "vboxsf" is not available
  网上的解决办法是在CMD下面执行：
  vagrant plugin install vagrant-vbguest
  vagrant vbguest
  然后再vagrant up
5,执行vagrant ssh的时候遇到下面的问题：
  ‘ssh’ executable not found in any directories in the %PATH% variable.
  这是因为我们的系统中没有安装或者系统的PATH中没有找到ssh.exe这个可执行文件，我们的vagrant 需要这个文件来连接虚拟机
  我的解决办法是安装一个git软件包，因为git软件包就包含了ssh.exe。然后再在PATH中添加该ssh.exe所在的目录就可以了。
  还有一个办法就是使用xshell这样的工具连接我们的虚拟机。而不用理会上面的错误
6，在虚拟机中，需要切换到root用户的时候，密码也是vagrant。
