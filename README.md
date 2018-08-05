# ubuntu-16.04.box:
vagrant 虚拟开发环境，和线上环境保持一致，已预装git, vim, python, go, docker, mysql 等, 并完成以下配置：

- docker 采用阿里云镜像加速
- apt-get 采用 aliyun source
- miniconda-python-3.6.6，pip采用阿里云镜像加速
- vim 语法高亮等常用配置
- git 快捷命令等常用配置
- 系统编码已设置为zh_CN.UTF-8
- mysql-5.7 已开启外网访问权限，user: root, password: 123123。默认编码已设置为utf-8
- 命令搜索自动前缀匹配，历史命令保存100000个
- host文件夹和vm文件夹映射

开箱即用，轻松避免开发环境和生产环境不一致的问题。
使用vagrant的好处：
- 业界流行的开发方式 
- 避免折腾环境问题，提升开发效率
- 用windows和mac的用户可以在享受原生系统的易用性的同时，享受使用linux的快感。
- 占用资源少，和原生linux速度相当
- 与host系统隔离，vm被搞坏了可以随时删掉重建，还可以创建多个虚拟环境。系统崩了，不用担心重要文件丢失。
- 方便本地调试，端口无限制，软件安装无需申请权限
- 避免在一台机器上测试通过，在另一台机器上无法通过的问题

# 使用方式
2. 到“钉钉-企业文件-常用软件”中相应的 virtualbox，vagrant并安装
2. 创建 ubuntu-16.04 环境目录
3. 到“钉钉-企业文件-常用软件” 中下载 ubuntu-16.04.box
4. 执行命令

```bash
vagrant box add ubuntu-16.04 ./ubuntu-16.04.box
vagrant init ubuntu-16.04
```

此时会在目录下生成配置文件 Vagrantfile，修改以下配置：

```ruby
# 添加私有IP，和host机通信
config.vm.network "private_network", ip: "192.168.33.10"
# 映射文件夹，可自行修改
config.vm.synced_folder "../workspace", "/home/vagrant/workspace"
config.vm.provider "virtualbox" do |vb|
  # Display the VirtualBox GUI when booting the machine
  vb.gui = false
  # Customize the amount of memory on the VM:
  vb.memory = "2048"
  vb.cpus = "2"
  vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
end
```

继续执行以下命令即可

```bash
vagrant up # 启动虚拟机
vagrant ssh # ssh到虚拟机
vagrant halt  # 关闭虚拟机，通常情况下，无需关闭
```

详细使用方法可参考 https://segmentfault.com/a/1190000008729625 。

