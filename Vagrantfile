# -*- mode: ruby -*-
# vi: set ft=ruby :

VM_NAME ||= "freebsd-ports-dev"

Vagrant.configure("2") do |config|
  config.vm.define VM_NAME do |subconfig|
    subconfig.vm.box = "freebsd/FreeBSD-11.3-STABLE"
    subconfig.vm.guest = :freebsd
    subconfig.vm.hostname = VM_NAME
    subconfig.vm.synced_folder ".", "/vagrant", disabled: true
    subconfig.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = ""
      libvirt.cpus = "4"
      libvirt.memory = "8192"
    end
    subconfig.vm.provider "virtualbox" do |vbox|
      vbox.gui = false
      vbox.name = VM_NAME
      vbox.cpus = "4"
      vbox.memory = "8192"
    end
    subconfig.vm.provision "shell", inline: <<-SHELL
      pkg upgrade -y
      for package in subversion vim-console; do
        pkg info "${package}" >/dev/null 2>&1 || pkg install -y "${package}"
      done
      if svn info /usr/ports; then
        svn update /usr/ports
      else
        svn checkout https://svn.freebsd.org/ports/head /usr/ports
      fi
      for option in "DEVELOPER=yes" "MAKE_JOBS_NUMBER=4"; do
        grep "${option}" /etc/make.conf >/dev/null 2>&1 || echo "${option}" >> /etc/make.conf
      done
    SHELL
    subconfig.ssh.shell = "sh"
  end
end
