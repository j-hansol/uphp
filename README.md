# Vagrant / VirtualBox용 PHP 게발용 Box
---

## 작성 배경
개인적으로 회사에서는 MacBook을 사용하고 있어 개발에는 문제가 없는데 집에서는 Windows 운영체제의 저가 PC라 회사와 비숫한 수준의 개발환경에 목말라 있었습니다. 그렇다고 비싼 맥북을 살 수도 없고 고심 끝에 VirtualBox와 Vagrant, 그리고 Acrylic DNS Proxy를 이용하여 맥과 유사한 환경을 구성하기로 했습니다.

---
## 환경 구성에 필요한 기능
제가 사용하는 맥북의 개발환경은 아래와 같은 환경이 구성되어 있습니다. 이와 같은 구성을 윈도우즈 PC에서도 사용하도록 했습니다.

* MariaDB 10.0, Apache 2.4 기반환경을 제공합니다.
* PHP 5.6과 7.2의 두 개의 버전이 설치되어 있습니다.
* 위 두 버전을 'sphp' 명령을 이용하여 변경할 수 있습니다.
* xDebug 를 이용하여 DEBUG가 가능합니다.
* 쉽게 멀티 사이트를 구성할 수 있습니다.
* 멀티 사이트 환경과 싱글 사이트를 svhost 명령을 이용하여 변경할 수 있습니다.
* composer 가 기본 설치되어 잇습니다.
* drupal 7 개발을 위한 drush가 기본 설치되어 있습니다.

---

## 환경 구성을 위한 기반 프로그램
위와 같은 환경을 구성하기 위해서는 아래와 같은 프로그램이 필요합니다.

* [Oracle VirtualBox](https://www.virtualbox.org/)
* [HashiCrop Vagrant](https://www.vagrantup.com/)
* [Acrylic DNS Proxy](https://mayakron.altervista.org/wikibase/show.php?id=AcrylicHome)

위의 3개의 프로그램과 Vagrant를 통해 실행될 VirtualBox용 가상머신 이미지가 있어야 합니다. 제가 만든 Vagrant Box(가상머신 이미지)는 아래 링크를 통해 다운로드 받고 설치할 수 있습니다.

* [pig482/uphp](https://app.vagrantup.com/pig482/boxes/uphp)

아래 부분에서 3개의 프로그램 설정부분을 포함한 설치 과정을 설명하겠습니다.

---

## 기반 프로그램 설치 및 설정
3개의 프로그램을 다운로드하여 설치마법사를 통해 설치합니다. 설치 과정은 기본값으로 설치하면 됩니다. 그래서 설치 과정은 생략하도록 하겠습니다. 모두 설치되었다면 아래 추가적인 설정을 진행합니다.

추가적인 설정이 필요한 부분은 [Acrylic DNS Proxy](https://mayakron.altervista.org/wikibase/show.php?id=AcrylicHome)로 이 부분에 대한 내용 설명합니다.

1. Acrylic UI 프로그램 실행합니다.
2. File 메뉴의 Open Acrlic Host 를 클릭하여 DNS 관련 설정합니다.
설정 내용 최 하단에 아래와 같이 DNS 설정을 추가합니다.
~~~
# 개발 환경에서 사용할 DNS 서버스를 위한 도매인 페턴을 설정합니다.
# 제가 만든 box는 'public.admin.wd' 형태로 설정되어 있습니다.
# 형식은 IP주소 도매인 패턴
192.168.56.10 *.*.wd
~~~
3. Actions 메뉴의 Install Acrylic Service 를 클릭하여 DNS Proxy 서비스를 실행합니다.
4. 제어판의 '네트워크 공유 센터'의 '어답터 설정 변경' 링크를 클릭합니다.
5. 현재 사용중인 네트워크 어답의 '속성' 메뉴를 클릭합니다.
6. 'Internet Protocol Version 4 (IPv4)'의 '속성' 메뉴를 클릭합니다.
7. 'DNS 서버' 부분에서 '다음 DNS 서버 주소 사용'을 체크한 후 '가본 설정 DNS 서버'에 '127.0.0.1'를 설정하고 '보조 DNS 서버'에 기존에 사용하던 서버 주소를 입력한 후 '확인' 버튼을 연속으로 클릭하여 설정을 마무리합니다.

특히 7번은 매우 중요합니다. DNS 서버 주소를 모를 경우 '명령 프롬프트'에셔 아래의 명령을 입력하여 DNS 주소를 확인합니다.

```bash
ipconfig /all
```

---

## Vagrant Box(uphp) 설치

[pig482/uphp](https://app.vagrantup.com/pig482/boxes/uphp) 링크를 클릭하여 페이지에 접속하면 tjfcl 방법이 간단하게 나와 있습니다. 너무 간단하게 나와 추가적인 설명이 필요할 듯하여 아래와 같이 설명을 하겠습니다.

### vagrant box를 저장할 폴더를 생성합니다.
생성된 폴더에서 다음 명령을 수행합니다.

### '명령 프롬프트' 를 통해 아래의 명령을 실행합니다.
```Windows
vagrant init pig482/uphp --box-version 1.0
```
### 'vagrantfile'을 편집기를 이용하여 아래와 같이 편집합니다.
1항의 명령을 실행하면 'vagrantfile' 파일이 생성되었을 것입니다. 여기에 추가적인 설정이 필요합니다. 그래서 편집기를 이용하여 파일을 열어 아래와 같이 주석이 해제된 부분을 본인의 설정에 맞게 조정합니다.
~~~
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
  config.vm.box = "pig482/uphp"
  config.vm.box_version = "1.0"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

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
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "private_network", ip: "192.168.56.10"

  config.vm.synced_folder "../../sites", "/home/vagrant/sites", group: "vagrant", owner: "vagrant"
  config.vm.synced_folder "../../site", "/home/vagrant/site", group: "vagrant", owner: "vagrant"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = "2048"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
~~~

추가되거나 변경된 부분을 살펴보면 아래와 같습니다.
~~~
config.vm.network "private_network", ip: "192.168.56.10"
~~~
위 코드는 네트워크 인터페이스를 사설 네트워크를 추가 설정한 것입니다. IP 주소는 네트워크 어답터 중 'VirtualBox Host Only Network'의 설정을 참조하여 설정합니다.

~~~
config.vm.synced_folder "../../sites", "/home/vagrant/sites", group: "vagrant", owner: "vagrant"
config.vm.synced_folder "../../site", "/home/vagrant/site", group: "vagrant", owner: "vagrant"
~~~

위 코드는 개발 환경에서 중요한 부분으로 Windows PC의 각종 개발 소스가 저장되어 있는 폴더를 가상 머신에 마운트(Mount)하여 웹 서비스, 디버깅 등의 개발 작업을 수행하기 위한 폴더 마운트 설정입니다.

* '/home/vagrant/sites' 폴더는 멀티 사이트 개발을 위한 마운트 폴더입니다.
* '/home/vagrant/site' 폴더는 싱글 사이트 개발을 위한 마운트 폴더입니다.

원활한 개발 환경을 위해 위 두 폴더는 마운트하는 것이 좋습니다.
위 예에서 두 마운트 폴더의 실재 연결 폴더는 'vagrantfile'을 기준으로 상위 폴더의 상위 폴더에 있는 것으로 설정되어 있습니다.

또 하나 두 마운트 폴더는 사용자, 그룹 모두 'vagrant'로 설정하였습니다.

> Vagrant 를 이용해 가상머신을 힐행하기에 앞서 소스가 저장될 루터 폴더를 생성해두고 위 설정을 수정하여 마운트해주십시오.

~~~
config.vm.provider "virtualbox" do |vb|
  # Display the VirtualBox GUI when booting the machine
  # vb.gui = true

  # Customize the amount of memory on the VM:
  vb.memory = "2048"
end
~~~
위 코드는 가상 머신 실행 시 메모리 크기를 2048MB(2GB)로 설정하였습니다. 메모리가 부족하면 composer 등의 실행에 문제가 있기 때문입니다.

### Vagrant를 이용한 가상 머신 실행과 종료
Vagrant를 이용하여 가상머신을 실행하기 위해서는 'vagrantfile' 이 저장된 폴더에서 아래와 같이 명령을 실행합니다.

```bash
vagrant up
```

종료하기 위해서는 아래와 같이 명령을 힐행합니다.

```bash
vagrant halt
```

VirtualBox 목록에서 가상머신 제거를 위해서는 아래와 같이 명령을 실행합니다.

```bash
vagrant destroy
```

철치된 가상머신(vagrant box 아님)을 디스크에서 제거하려면 아래의 명령을 실행합니다.

```bash
vagrant box remove pig482/uphp
```

### Vagrant를 이용한 가상머신 SSH 접속
가상머신에 SSH 서비스를 이용하여 접속하기 위해서 기존 SSH 클라이언트를 이용해도 되나 네트워크 설정이 올바르지 않은 경우 기존 SSH 클라이언트로는 접속할 수 없습니다. 한편 vagrant를 이용하면 이 와중에서도 접속 가능합니다.

아래의 명령으로 접속할 수 있습니다.
```bash
vagrant ssh
```

접속을 종료할 경우 아래의 명령을 입력합니다.
```bash
exit
```
