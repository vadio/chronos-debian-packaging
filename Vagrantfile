# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.provider "virtualbox" do |virtualbox|
    virtualbox.memory = "2048"
    virtualbox.cpus = 8
  end

  config.vm.provision "shell" do |bootstrap|
    @script = <<-SCRIPT
      add-apt-repository -y ppa:webupd8team/java

      curl -s https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add -
      echo 'deb https://deb.nodesource.com/node_5.x trusty main' > /etc/apt/sources.list.d/nodesource.list
      echo 'deb-src https://deb.nodesource.com/node_5.x trusty main' >> /etc/apt/sources.list.d/nodesource.list

      apt-get update

      /bin/echo debconf shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections
      /bin/echo debconf shared/accepted-oracle-license-v1-1 seen true | /usr/bin/debconf-set-selections
      DEBIAN_FRONTEND=noninteractive apt-get install -y --force-yes oracle-java8-installer
      DEBIAN_FRONTEND=noninteractive apt-get install -y --force-yes oracle-java8-set-default

      apt-get install -y packaging-dev maven nodejs ruby-dev gcc make

      gem install fpm
    SCRIPT

    bootstrap.privileged = true
    bootstrap.inline = @script
  end

  config.vm.provision "shell" do |compile|
    @script = <<-SCRIPT
      git clone https://github.com/vadio/chronos-pkg.git /vagrant/chronos-pkg --branch 2.4.2
      cd /vagrant/chronos-pkg && git submodule init && git submodule update && cd ~vagrant
      cd /vagrant/chronos-pkg && make PKG_REL=0.1.20160614000000 deb
    SCRIPT

    compile.inline = @script
  end
end
