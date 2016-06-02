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
      git clone https://github.com/vadio/chronos.git /vagrant/chronos --branch 2.4.1

      cd /vagrant/chronos && mvn package -DskipTests=true && cd /vagrant

      rm -rf /vagrant/build
      cp -rf /vagrant/src /vagrant/build

      cat /vagrant/chronos/target/chronos-2.4.1.jar >> /vagrant/build/usr/bin/chronos

      fpm -s dir \
        -t deb \
        -C /vagrant/build/ \
        --name chronos \
        --after-install /vagrant/build/postinst \
        --after-remove /vagrant/build/postrm \
        --version 2.4.1-0.1.20160601000000.ubuntu1404 \
        --url https://github.com/vadio/chronos \
        --description "Fault tolerant job scheduler for Mesos which handles dependencies and ISO8601 based schedules" --vendor "Mesosphere, Inc." \
        --license "Apache-2.0" \
        --maintainer "Suhas Gaddam <suhasg@vadio.com>" \
        --depends "java8-runtime-headless | java7-runtime-headless | java6-runtime-headless, lsb-release" \
        etc usr
    SCRIPT

    compile.inline = @script
  end
end
