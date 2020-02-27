# Homestead  
## 背景
```
    在日常的团队开发中，由于开发环境的不一致，往往会导致出现各种各样的问题。即便是经验丰富的工程师，在遇到这种问题时也会特别头疼。为了解决这种问题，Vagrant 顺势而生！Vagrant 是一个用于创建和部署虚拟化开发环境的工具，其依赖于 VirtualBox 虚拟机，致力于帮助开发者快速构建一个环境统一的虚拟系统。Vagrant 最强大的地方是在于它在构建虚拟系统时的快捷简便，使开发者可以在短短几分钟内完成一个虚拟系统的删除与构建。
    
    Laravel 希望在 Vagrant 的基础上让开发环境更加统一，让开发者都能在指定的具体开发环境下使用 Laravel，这时便有了 Homestead。Homestead 是一个基于 Ubuntu 构建的虚拟机，它包含了所有 Laravel 开发时需要用到的东西，你可以很轻松的通过指定的 Laravel 版本来找到相对应版本的 Homestead 并进行安装。Homestead 提供的默认开发环境还会装上很多常用的开发工具来辅助 Laravel 进行项目开发，包括 PHP7, Nginx, Redis, Memcached, MySQL, Git, Node.js, NPM 等等。或许你对这里提到的一些专有词汇所涉及到的知识并不太了解，但是别担心，这些我们在后面章节都会作相关介绍。
    
    Laravel Homestead 是一个官方预封装的 Vagrant box ，它为你提供了一个完美的开发环境，你不需要在本地机器安装 PHP、 web 服务器和其他的服务器软件。你再也不用担心会弄乱你的操作系统了！ Vagrant boxes 是一次性的。 如果出现问题， 你可以在几分钟内销毁并重新创建 box !

    Homestead 可以运行在任何 Windows、 Mac、 或 Linux 系统， 它包括了 Nginx web 服务器， PHP 7.3、 PHP 7.2、 PHP 7.1、 MySQL、PostgreSQL、Redis、 Memcached、 Node 以及你开发 Laravel 所需要的东西。
```
```
注意：如果你使用的是 Windows, 你可能需要通过 BIOS 来启用硬件虚拟化 (VT-x)。如果你在 Hyper-V 系统上使用 UEFI 你可能还需要禁用 Hyper-V 以便访问 VT-x.
```
## [大陆网站-详细文档介绍](https://learnku.com/laravel/wikis/26268)
## 安装&设置
### 第一步
* 在安装Homestead之前，需要先安装VirtualBox，Vagrant。
### 安装 Homestead Vagrant Box
* 使用以下命令，将 laravel/homestead box 添加到 Vagrant 中
```
    vagrant box add laravel/homestead
```
* 如果这个命令失败了，请确保你安装的是最新版的 Vagrant。
### 安装 Homestead
* 你可以通过克隆代码来安装 Homestead。 建议将代码克隆到你的 "home" 目录下的 Homestead 文件夹中， 这样 Homestead box 就可以作为你的所有 Laravel 项目的主机：
```
    git clone https://github.com/laravel/homestead.git ~/Homestead
```
* 默认是在根目录下建立一个‘~’文件夹，里面是 Homestead
* 因为 Homestead 的 master 分支并不是稳定的，你应该使用打过标签的稳定版本。你可以在 GitHub 发行页: 上找到最新的稳定版本:
```
    cd ~/Homestead

    // 克隆期望的发行版本...
    git checkout v8.0.1
    
    // 或者直接切换到release分支
    git checkout release
```
* 在 Homestead 目录中使用 bash init.sh 命令来创建 Homestead.yaml 配置文件。 Homestead.yaml 文件将被放在 Homestead 目录中:
```
    // Mac / Linux...
    bash init.sh

    // Windows...
    init.bat
```
## 配置 Homestead
### 配置 Homestead.yaml 文件
* 设置提供器，统一使用virtualbox
```
    provider: virtualbox
```
* 配置共享文件夹
```
    folders:
    - map: C:/project/psi
      to: /home/vagrant/psi
      // 开启 NFS 解决站点响应缓慢，
      type: "nfs"
```
* 如果开启 nfs，需要安装 Vagrant WinNFSd 
```
    // 安装 Vagrant WinNFSd
    vagrant plugin install vagrant-winnfsd
```
* 并在 Vagrantfile 文件中添加以下代码
```
    class VagrantPlugins::ProviderVirtualBox::Action::Network
        def dhcp_server_matches_config?(dhcp_server, config)
            true
        end
    end
    
    config.vm.network "private_network", type: "dhcp"
```
#### 如何使用NFS[大陆网站-详情看链接](https://learnku.com/laravel/wikis/25550)
* 配置 Nginx 站点
```
    sites:
    // 域名地址
    - map: homestead.test
      // 网站的根目录
      to: /home/vagrant/laravel/public
      // 设置PHP版本
      php: '7.2'
```
* 设置 Hosts 文件  
在 Mac 和 Linux 中，该文件的地址在 /etc/hosts，在 Windows 中位于 C:\Windows\System32\drivers\etc\hosts。添加新站点的格式如下所示：
```
    192.168.10.10  homestead.test
```
* 端口转发  
```
    SSH：2222 -> 转发到 22
    ngrok UI：4040 -> 转发到 4040
    HTTP：8000 -> 转发到 80
    HTTPS：44300 -> 转发到 443
    MySQL：33060 -> 转发到 3306
    PostgreSQL：54320 -> 转发到 5432
    MongoDB：27017 -> 转发到 27017
    Mailhog：8025 -> 转发到 8025
    Minio：9600 -> 转发到 9600
```
## 启动 Vagrant Box
* 设置好 Homestead.yaml 后，在Homestead文件夹运行
```
    vagrant up

    启动虚拟机，并自动配置共享文件夹和 Nginx 站点
```
* 通过 SSH 登录虚拟机
```
    vagrant ssh
```
* 在虚拟机里退出登录
```
    logout
```
* 关闭虚拟机
```
    vagrant halt
```
* 一旦更新 Homestead.yaml 文件，则在启动前需要先运行
```
    vagrant reload --provision
```
## 更新 Homestead
* 查看所有的 vagrant 主机
```
    vagrant global-status --prune
```
* 若要删除虚拟机，只需要运行
```
    vagrant destroy --force

    // 或者根据主机 id 标识删除
    vagrant destroy 1a2b3c4d
```
* 更新 vagrant 盒子
```
    vagrant box update
```
* 更新 Homestead 源代码
```
    git fetch
    git checkout release
```
## 特殊问题
* 如果遇到以下问题，请运行 vagrant plugin install vagrant-vbguest 命令
```
    NFS requires a host-only network to be created.
    Please add a host-only network to the machine (with either DHCP or a
    static IP) for NFS to work.
```
## 统一环境  
开发之前统一配置好 Homestead.yaml 文件，每个开发人员只需要本地安装好需要的工具，然后运行
```
    vagrant up 

```





















