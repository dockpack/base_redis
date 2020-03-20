Vagrant.configure("2") do |config|

  # Install required plugins
  required_plugins = %w( vagrant-hostmanager )
  plugin_installed = false
  required_plugins.each do |plugin|
    unless Vagrant.has_plugin?(plugin)
      system "vagrant plugin install #{plugin}"
      plugin_installed = true
    end
  end

  # If new plugins installed, restart Vagrant process
  if plugin_installed === true
    exec "vagrant #{ARGV.join' '}"
  end
  config.vm.box = "dockpack/centos7"
  config.vm.box_check_update = true
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = true
  end
  config.vm.graceful_halt_timeout=15

  config.ssh.insert_key = false
  config.ssh.forward_agent = true
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.vm.box = "dockpack/centos7"
  config.vm.provider "virtualbox" do |virtualbox|
    virtualbox.gui = false
    virtualbox.customize ["modifyvm", :id, "--memory", 1024]
    virtualbox.customize ["modifyvm", :id, "--vram", "64"]
  end

  config.vm.define :ansible, autostart: true, primary: true do |host_config|
    host_config.vm.hostname = "ansible"
    host_config.vm.network "private_network", ip: "192.168.10.80"
    host_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2286, auto_correct: false
    host_config.vm.synced_folder ".", "/home/vagrant", id: "vagrant-root", disabled: false
    host_config.vm.provider "virtualbox" do |vb|
      vb.name = "ansible"
      vb.customize ["modifyvm", :id, "--memory", 2048]
      vb.customize ["modifyvm", :id, "--vram", "64"]
    end
  end

  N = 3
  (1..N).each do |server_id|
    config.vm.define "server#{server_id}" do |server|
      server.vm.hostname = "server#{server_id}"
      server.vm.provider "virtualbox" do |vb|
        vb.name = "server#{server_id}"
      end
      server.vm.network "private_network", ip: "192.168.10.8#{server_id}"
      server.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
    end
  end
end
