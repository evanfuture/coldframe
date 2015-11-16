# -*- mode: ruby -*-
# vi: set ft=ruby :

# Specify Vagrant provider as Virtualbox
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'

require 'yaml'

ANSIBLE_PATH = 'ansible'

# Set Ansible roles_path relative to Ansible directory
ENV['ANSIBLE_ROLES_PATH'] = File.join(ANSIBLE_PATH, 'vendor', 'roles')

vault_pass = './.coldframe_vault_pass'
ansible_hosts = File.join(ANSIBLE_PATH, 'hosts')

Vagrant.require_version '>= 1.7.4'

Vagrant.configure('2') do |config|
  config.vm.box = 'scotch/box'
  config.ssh.forward_agent = true

  # next lines are commands to retrieve the latest ip address used of our prefered pattern from your etc/hosts file.
  if !Vagrant.has_plugin? 'vagrant-hostsupdater'
    puts 'vagrant-hostsupdater missing, please install the plugin:'
    puts 'vagrant plugin install vagrant-hostsupdater'
  end

  require 'ipaddr'
  hostsfile = "/etc/hosts"

  ip = File.read(hostsfile).scan(/192\.168\.42\.\d{1,3}/).sort_by! {|ip| ip.split('.').map{ |octet| octet.to_i} }.last or ip = "192.168.42.1"
  ips = [ip]
  ips << IPAddr.new(ips.last).succ.to_s
  latest_ip = ips.last

  hostname, *aliases = File.read(ansible_hosts).scan(/([\da-z\.-]+\.[a-z\.]{2,6})/).flatten

  config.vm.network :private_network, ip: latest_ip
  config.hostsupdater.remove_on_suspend = true
  config.vm.hostname = hostname
  config.hostsupdater.aliases = aliases

  config.vm.synced_folder ".", "/var/www", :mount_options => ["dmode=777", "fmode=666"]

  if !Vagrant.has_plugin? 'vagrant-triggers'
    puts 'vagrant-triggers missing, please install the plugin:'
    puts 'vagrant plugin install vagrant-triggers'
  end

  config.trigger.before :up do
    requirements = File.join(ANSIBLE_PATH, 'vendor/')
    if !File.exists?(requirements)
      system "ansible-galaxy install -r #{ANSIBLE_PATH}/requirements.yml -p #{ANSIBLE_PATH}/vendor/roles"
    end
  end
  config.trigger.after :destroy do
    system "rm -Rf #{ANSIBLE_PATH}/vendor"
  end

  config.vm.provision :ansible do |ansible|
    if File.exists?(vault_pass)
      ansible.vault_password_file = vault_pass
    else
      ansible.ask_vault_pass = true
    end
    ansible.playbook = File.join(ANSIBLE_PATH, "coldframe.yml")
  end

  config.vm.provider 'virtualbox' do |vb|
    # Give VM access to all cpu cores on the host
    cpus = case RbConfig::CONFIG['host_os']
      when /darwin/ then `sysctl -n hw.ncpu`.to_i
      when /linux/ then `nproc`.to_i
      else 2
    end

    # Customize memory in MB
    vb.customize ['modifyvm', :id, '--memory', 1024]
    vb.customize ['modifyvm', :id, '--cpus', cpus]

    # Fix for slow external network connections
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    vb.customize ['modifyvm', :id, '--natdnsproxy1', 'on']
  end
end
