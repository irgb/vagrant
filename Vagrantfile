# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/xenial64"
  config.vm.hostname = "shannonai.local"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "/Users/shibo/shannon_workspace", "/home/vagrant/workspace"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false
    # Customize the amount of memory on the VM:
    vb.memory = "2048"
    vb.cpus = "2"
    vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    set -ex
    export DEBIAN_FRONTEND=noninteractive
    USER_HOME=/home/vagrant
    CONDA_HOME=$USER_HOME/software/conda
    GO_HOME=$USER_HOME/software
    PYTHON_VERSION=3.6.6
    # use aliyun source
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse' > /etc/apt/sources.list
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse' >> /etc/apt/sources.list
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse' >> /etc/apt/sources.list
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse' >> /etc/apt/sources.list
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse' >> /etc/apt/sources.list
    echo 'deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse' >> /etc/apt/sources.list
    echo 'deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse' >> /etc/apt/sources.list
    echo 'deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse' >> /etc/apt/sources.list
    echo 'deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse' >> /etc/apt/sources.list

    # install package
    apt-get -yqq update
    # install mysql and allow remote access, default password of root user is 123123
    echo "mysql-server mysql-server/root_password password 123123" | debconf-set-selections
    echo "mysql-server mysql-server/root_password_again password 123123" | debconf-set-selections
    apt-get install -yqq --no-install-recommends mysql-server mysql-client 
    sed -i 's/^bind-address/# bind-address/' /etc/mysql/mysql.conf.d/mysqld.cnf
    mysql -u root -p123123 -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123123' WITH GRANT OPTION; FLUSH PRIVILEGES;"
    sed -i '31i collation-server = utf8_unicode_ci' /etc/mysql/mysql.conf.d/mysqld.cnf
    sed -i '32i character-set-server = utf8' /etc/mysql/mysql.conf.d/mysqld.cnf
    sed -i '33i skip-character-set-client-handshake' /etc/mysql/mysql.conf.d/mysqld.cnf

    systemctl restart mysql
    # install common packages 
    apt-get install -yqq --no-install-recommends gcc build-essential apt-utils rsync netcat procps libssl-dev
    apt-get install -yqq --no-install-recommends vim htop git tree pv libmysqlclient-dev language-pack-zh-hans ack-grep curl
    # set git
    git config --system push.default simple
    git config --system alias.co checkout
    git config --system alias.br branch
    git config --system alias.ci commit
    git config --system alias.st status
    git config --system core.editor "vim"
    git config --system alias.unstage 'reset HEAD --'
    git config --system alias.lg 'log --graph --abbrev-commit --decorate --format=format:"%C(cyan)%h%C(reset) - %C(white)%s%C(reset) %C(yellow)%d%C(reset) %C(dim white) - %anC(reset) %C(dim green)(%c    i)%C(reset)" --all'
    # set locales
    grep -q -F 'LANG=zh_CN.UTF-8' /etc/environment ||  echo 'LANG=zh_CN.UTF-8' >> /etc/environment
    grep -q -F 'LC_ALL=zh_CN.UTF-8' /etc/environment ||  echo 'LC_ALL=zh_CN.UTF-8' >> /etc/environment
    grep -q -F 'LANGUAGE=zh_CN.UTF-8' /etc/environment ||  echo 'LANGUAGE=zh_CN.UTF-8' >> /etc/environment
    # set HOST_IP environment
    grep -q -F 'export HOST_IP=$(ifconfig enp0s8 | grep inet | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" | head -1)' /etc/profile || \
        echo 'export HOST_IP=$(ifconfig enp0s8 | grep inet | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" | head -1)' >> /etc/profile
    # set timezone 
    TZ=Asia/Shanghai
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
    # install golang
    mkdir -p $GO_HOME
    if [ ! -d $GO_HOME/go ]; then
        curl -sSL -o /tmp/golang.tar.gz https://code.aliyun.com/k9kdqvbb/files/raw/master/go1.10.3.linux-amd64.tar.gz
        tar -C $GO_HOME -xzf /tmp/golang.tar.gz
    fi
    grep -q -F "export GOROOT=$GO_HOME/go" $USER_HOME/.profile || echo "export GOROOT=$GO_HOME/go" >> $USER_HOME/.profile
    grep -q -F 'export PATH=$GOROOT/bin:$PATH' $USER_HOME/.profile || echo 'export PATH=$GOROOT/bin:$PATH' >> $USER_HOME/.profile
    grep -q -F "export GOPATH=~/workspace/go" $USER_HOME/.profile || echo "export GOPATH=~/workspace/go" >> $USER_HOME/.profile
    grep -q -F 'export PATH=$GOPATH/bin:$PATH' $USER_HOME/.profile || echo 'export PATH=$GOPATH/bin:$PATH' >> $USER_HOME/.profile
    chown -R vagrant: $GO_HOME
    rm -rf /tmp/golang.tar.gz
    # install conda
    if [ ! -d $CONDA_HOME ]; then
        curl -sS -o /tmp/conda.sh https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
        bash /tmp/conda.sh -b -p $CONDA_HOME
        $CONDA_HOME/bin/conda install -yq python==$PYTHON_VERSION
        rm -rf /tmp/conda.sh
    fi
    grep -q -F "export CONDA_HOME=$CONDA_HOME" $USER_HOME/.profile || echo "export CONDA_HOME=$CONDA_HOME" >> $USER_HOME/.profile
    grep -q -F 'export PATH=$CONDA_HOME/bin:$PATH' $USER_HOME/.profile || echo 'export PATH=$CONDA_HOME/bin:$PATH' >> $USER_HOME/.profile
    mkdir -p $USER_HOME/.pip
    mkdir -p /root/.pip
    echo '[global]' | tee $USER_HOME/.pip/pip.conf /root/.pip/pip.conf
    echo 'trusted-host = mirrors.aliyun.com' | tee --append $USER_HOME/.pip/pip.conf /root/.pip/pip.conf
    echo 'index-url = https://mirrors.aliyun.com/pypi/simple' | tee --append $USER_HOME/.pip/pip.conf /root/.pip/pip.conf
    chown -R vagrant: $USER_HOME/.pip
    $CONDA_HOME/bin/pip install --no-cache-dir --default-timeout=120 ipython glances mycli tqdm pymysql mysqlclient fabric argparse pyyaml
    chown -R vagrant: $CONDA_HOME
    # install docker-ce
    apt-get install -yqq --no-install-recommends apt-transport-https ca-certificates software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    apt-get -yqq update
    apt-get install -yqq --no-install-recommends docker-ce=18.03.1~ce-0~ubuntu
    usermod -a -G docker vagrant
    chmod 666 /var/run/docker.sock
    # aliyun speed up docker image download
    mkdir -p /etc/docker
    echo "{" > /etc/docker/daemon.json
    echo '  "registry-mirrors": ["https://ht6aappv.mirror.aliyuncs.com"]' >> /etc/docker/daemon.json
    echo "}" >> /etc/docker/daemon.json
    systemctl daemon-reload
    systemctl restart docker
    # install docker-compose
    DOCKER_COMPOSE=/usr/local/bin/docker-compose
    if [ ! -f $DOCKER_COMPOSE ]; then
        curl -sSL -o $DOCKER_COMPOSE https://code.aliyun.com/k9kdqvbb/files/raw/master/docker-compose-Linux-x86_64
        chmod +x $DOCKER_COMPOSE 
    fi
    # clean
    apt-get autoremove -yqq --purge
    apt-get clean && apt-get autoclean
    # set bashrc
    grep -q -F "HISTSIZE=1000" $USER_HOME/.bashrc || sed -i 's/^HISTSIZE=1000/HISTSIZE=100000/' $USER_HOME/.bashrc
    grep -q -F "HISTFILESIZE=2000" $USER_HOME/.bashrc || sed -i 's/^HISTFILESIZE=2000/HISTFILESIZE=200000/' $USER_HOME/.bashrc
    # set prefix sensitive command prompt
    echo '"\e[A": history-search-backward' > $USER_HOME/.inputrc
    echo '"\e[B": history-search-forward' >> $USER_HOME/.inputrc
    chown vagrant: $USER_HOME/.inputrc
    # config vimrc
    echo 'filetype plugin indent on' > $USER_HOME/.vimrc
    echo 'set nocompatible' >> $USER_HOME/.vimrc 
    echo 'filetype off' >> $USER_HOME/.vimrc
    echo 'set mouse-=a' >> $USER_HOME/.vimrc
    echo 'set fileencodings=utf-8' >> $USER_HOME/.vimrc
    echo 'set termencoding=utf-8' >> $USER_HOME/.vimrc
    echo 'set fileencoding=utf-8' >> $USER_HOME/.vimrc
    echo 'syntax on' >> $USER_HOME/.vimrc
    echo 'set nu' >> $USER_HOME/.vimrc
    echo 'set tabstop=4' >> $USER_HOME/.vimrc
    echo 'set shiftwidth=4' >> $USER_HOME/.vimrc
    echo 'set expandtab' >> $USER_HOME/.vimrc
    echo 'set cindent' >> $USER_HOME/.vimrc
    echo 'set autoindent' >> $USER_HOME/.vimrc
    echo 'set showmatch' >> $USER_HOME/.vimrc
    echo 'set cursorline' >> $USER_HOME/.vimrc
    echo 'set cursorcolumn' >> $USER_HOME/.vimrc
    echo 'set incsearch' >> $USER_HOME/.vimrc
    echo 'set hlsearch' >> $USER_HOME/.vimrc
    echo 'set ignorecase smartcase' >> $USER_HOME/.vimrc
    echo 'set nowrapscan' >> $USER_HOME/.vimrc
    echo 'set laststatus=2' >> $USER_HOME/.vimrc
    echo 'set nostartofline' >> $USER_HOME/.vimrc
    echo 'set ruler' >> $USER_HOME/.vimrc
    echo ':color desert' >> $USER_HOME/.vimrc
    chown vagrant: $USER_HOME/.vimrc
  SHELL
end
